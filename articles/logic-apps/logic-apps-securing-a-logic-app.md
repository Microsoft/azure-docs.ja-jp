---
title: データへのアクセスをセキュリティで保護する
description: Azure Logic Apps で、入力、出力、要求ベースのトリガー、実行履歴、管理タスク、その他のリソースへのアクセスをセキュリティで保護します
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: 883eede5296f3f280bf30c9a459c02a9243f9081
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719531"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Azure Logic Apps におけるアクセスとデータのセキュリティ保護

Azure Logic Apps では、[Azure Storage](../storage/index.yml) を利用して、データが格納され、自動的に[保存データが暗号化](../security/fundamentals/encryption-atrest.md)されます。 この暗号化によってデータが保護され、組織のセキュリティとコンプライアンスの要件を満たすことができます。 既定では、Azure Storage は Microsoft マネージド キーを使用してデータを暗号化します。 詳細については、「[保存データ向け Azure ストレージの暗号化](../storage/common/storage-service-encryption.md)」をご覧ください。

Azure Logic Apps 内の機密データへのアクセスをさらに制御し保護するために、次の領域のセキュリティを追加して設定できます。

* [要求ベースのトリガーへのアクセス](#secure-triggers)
* [ロジック アプリの操作へのアクセス](#secure-operations)
* [実行履歴の入出力へのアクセス](#secure-run-history)
* [パラメーターの入力へのアクセス](#secure-action-parameters)
* [ロジック アプリから呼び出されたサービスとシステムへのアクセス](#secure-outbound-requests)
* [特定のコネクタに対する接続の作成のブロック](#block-connections)
* [ロジック アプリの分離に関するガイダンス](#isolation-logic-apps)
* [Azure Logic Apps に対する Azure セキュリティ ベースライン](../logic-apps/security-baseline.md)

Azure でのセキュリティの詳細については、次のトピックを参照してください。

* [Azure 暗号化の概要](../security/fundamentals/encryption-overview.md)
* [保存時の Azure Disk Encryption](../security/fundamentals/encryption-atrest.md)
* [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>要求ベースのトリガーへのアクセス

着信した呼び出しや要求を受け取る要求ベースのトリガー ([Request](../connectors/connectors-native-reqres.md) トリガーや [Webhook](../connectors/connectors-native-webhook.md) トリガーなど) がロジック アプリで使用されている場合、承認されたクライアントだけがそのロジック アプリを呼び出すことができるように、アクセスを制限できます。 ロジック アプリで受信するすべての要求は、これまではSecure Sockets Layer (SSL) と呼ばれていたトランスポート層セキュリティ (TLS) プロトコルで暗号化され、セキュリティ保護されます。

この種類のトリガーへのアクセスをセキュリティで保護するためのオプションを次に示します。

* [Shared Access Signature の生成](#sas)
* [Azure Active Directory Open Authentication の有効化 (Azure AD OAuth)](#enable-oauth)
* [受信 IP アドレスの制限](#restrict-inbound-ip-addresses)
* [Azure Active Directory Open Authentication (Azure AD OAuth) またはその他のセキュリティを追加](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Shared Access Signature (SAS) の生成

ロジック アプリの各要求エンドポイントでは、エンドポイントの URL に次の形式で [Shared Access Signature (SAS)](/rest/api/storageservices/constructing-a-service-sas) を含みます。

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

次の表で説明するように、各 URL にはクエリ パラメーター `sp`、`sv`、`sig` が含まれています。

| Query parameter (クエリ パラメーター) | 説明 |
|-----------------|-------------|
| `sp` | 使用が許可された HTTP メソッドのアクセス許可を指定します。 |
| `sv` | 署名の生成に使用する SAS バージョンを指定します。 |
| `sig` | トリガーへのアクセスの認証に使用する署名を指定します。 この署名は、SHA256 アルゴリズムとシークレット アクセス キーを使用してすべての URL パスとプロパティで生成されます。 このキーは決して公開されることはなく、暗号化された状態でロジック アプリと共に格納されます。 お客様のロジック アプリでは、秘密鍵を使用して作成された有効な署名を含むそれらのトリガーのみが承認されます。 |
|||

SAS によるアクセスのセキュリティ保護の詳細については、このトピック内の次の各セクションを参照してください。

* [アクセス キーを再生成する](#access-keys)
* [有効期限付きのコールバック URL を作成する](#expiring-urls)
* [プライマリまたはセカンダリのキーを使用して URL を作成する](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>アクセス キーを再生成する

新しいセキュリティ アクセス キーを随時生成するには、Azure REST API または Azure portal を使用します。 古いキーを使用する、過去に生成された URL はすべて無効になり、ロジック アプリのトリガーが承認されなくなります。 再生成後に取得する URL は、新しいアクセス キーで署名されます。

1. [Azure portal](https://portal.azure.com) で、再生成したいキーを備えたロジック アプリを開きます。

1. ロジック アプリのメニューにある **[設定]** で、 **[アクセス キー]** を選択します。

1. 再生成したいキーを選択して、プロセスを完了します。

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>有効期限付きのコールバック URL を作成する

要求ベースのトリガーのエンドポイント URL を他のパーティと共有する場合は、特定のキーを使用する有効期限付きのコールバック URL を生成できます。 そうすることで、キーをシームレスに交換したり、ロジック アプリのトリガーに対するアクセスを特定の期間に基づいて制限したりできます。 URL の有効期限を指定するには、[Logic Apps REST API](/rest/api/logic/workflowtriggers) を使用します。その例を次に示します。

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

本文には、JSON 日付文字列を使用して `NotAfter` プロパティを含めます。 このプロパティは、`NotAfter` の日付と時刻までに限って有効なコールバック URL を返します。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>プライマリまたはセカンダリの秘密鍵を使用して URL を作成する

要求ベースのトリガーに対するコールバック URL を生成または一覧表示する場合、URL の署名に使用するキーを指定することができます。 特定のキーで署名された URL を生成するには、[Logic Apps REST API](/rest/api/logic/workflowtriggers) を使用します。その例を次に示します。

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

本文では、`KeyType` プロパティに `Primary` または `Secondary` を指定します。 このプロパティは、指定されたセキュリティ キーによって署名された URL を返します。

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-oauth"></a>Azure Active Directory OAuth の有効化

[Request トリガー](../connectors/connectors-native-reqres.md)で開始されるロジック アプリの場合は、Request トリガーへの受信呼び出しの認可ポリシーを定義または追加することによって、[Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) を有効にすることができます。 ロジック アプリが認証トークンを含む受信要求を受信すると、Azure Logic Apps は、トークンのクレームを各承認ポリシーのクレームと比較します。 トークンのクレームと、少なくとも 1 つのポリシーに含まれるすべてのクレームが一致した場合、受信要求に対して承認が成功します。 トークンは、承認ポリシーで指定された数よりも多くのクレームを持つことができます。

たとえば、ロジック アプリに、**発行者**と**対象ユーザー**という 2 つのクレームの種類が必要な認可ポリシーがあるとします。 このサンプルでデコードした[アクセス トークン](../active-directory/develop/access-tokens.md)には、次の両方のクレームの種類が含まれています。

```json
{
   "aud": "https://management.core.windows.net/",
   "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
   "iat": 1582056988,
   "nbf": 1582056988,
   "exp": 1582060888,
   "_claim_names": {
      "groups": "src1"
   },
   "_claim_sources": {
      "src1": {
         "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
    }
   },
   "acr": "1",
   "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
   "amr": [
      "rsa",
      "mfa"
   ],
   "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
   "appidacr": "2",
   "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
   "family_name": "Sophia Owen",
   "given_name": "Sophia Owen (Fabrikam)",
   "ipaddr": "167.220.2.46",
   "name": "sophiaowen",
   "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
   "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
   "puid": "1003000000098FE48CE",
   "scp": "user_impersonation",
   "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
   "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
   "unique_name": "SophiaOwen@fabrikam.com",
   "upn": "SophiaOwen@fabrikam.com",
   "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
   "ver": "1.0"
}
```

#### <a name="considerations-for-enabling-azure-oauth"></a>Azure OAuth の有効化に関する考慮事項

この認証を有効にする前に、次の考慮事項を確認してください。

* ロジック アプリへの受信呼び出しには、Azure AD OAuth または [Shared Access Signature (SAS)](#sas) のいずれか 1 つの認証スキームのみを使用できます。 一方のスキームを使用してももう一方は無効になりませんが、両方を同時に使用すると、どちらのスキームを選択するかサービスで判断できないため、エラーが発生します。 OAuth トークンに対しては[ベアラー型](../active-directory/develop/active-directory-v2-protocols.md#tokens)の承認スキームのみがサポートされ、これは Request トリガーに対してのみサポートされます。

* ロジック アプリは、承認ポリシーの最大数に制限されている必要があります。 各承認ポリシーには、[クレーム](../active-directory/develop/developer-glossary.md#claim)の最大数もあります。 詳細については、[Azure Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md#authentication-limits)に関するページを参照してください。

* 承認ポリシーには、少なくとも**発行者**要求が含まれている必要があり、その Azure AD 発行者 ID の値は `https://sts.windows.net/` または `https://login.microsoftonline.com/` (OAuth V2) で始まっています。 アクセス トークンの詳細については、「[Microsoft ID プラットフォーム アクセス トークン](../active-directory/develop/access-tokens.md)」を参照してください。

<a name="define-authorization-policy-portal"></a>

#### <a name="define-authorization-policy-in-azure-portal"></a>Azure portal で認可ポリシーを定義する

Azure portal でロジック アプリの Azure AD OAuth を有効にするには、次の手順に従って、ロジック アプリに 1 つ以上の認可ポリシーを追加します。

1. [Azure portal](https://portal.microsoft.com) のロジック アプリ デザイナーで、ロジック アプリを探して開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[認証]** を選択します。 承認ウィンドウが開いたら、 **[ポリシーの追加]** を選択します。

   ![[承認] > [ポリシーの追加] の順に選択します。](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Request トリガーへの各受信呼び出しで示される認証トークンでロジックアプリが期待する[クレームの種類](../active-directory/develop/developer-glossary.md#claim)と値を指定して、承認ポリシーに関する情報を提供します。

   ![承認ポリシーに関する情報を指定する](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **ポリシー名** | はい | 承認ポリシーに使用する名前 |
   | **請求** | はい | ロジック アプリが受信呼び出しで指定できるクレームの種類と値。 使用可能なクレームの種類は次のとおりです。 <p><p>- **発行者** <br>- **対象ユーザー** <br>- **件名** <br>- **JWT ID** (JSON Web Token ID) <p><p>**要求**の一覧には、少なくとも**発行者**の要求が含まれている必要があります。その値は、Azure AD 発行者 ID として `https://sts.windows.net/` または `https://login.microsoftonline.com/` で始まっています。 これらのクレームの種類の詳細については、「[Azure AD のセキュリティ トークンの要求](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens)」を参照してください。 独自のクレームの種類と値を指定することもできます。 |
   |||

1. 別のクレームを追加するには、次のオプションから選択します。

   * 別のクレームの種類を追加するには、 **[Add standard claim]\(標準クレームの追加\)** を選択し、クレームの種類を選択して、クレームの値を指定します。

   * 独自のクレームを追加するには、 **[Add custom claim]\(カスタム クレームの追加\)** を選択し、カスタム クレームの値を指定します。

1. 別の承認ポリシーを追加するには **[ポリシーの追加]** を選択します。 ポリシーを設定するには、前の手順を繰り返します。

1. 終了したら、 **[保存]** を選択します。

<a name="define-authorization-policy-template"></a>

#### <a name="define-authorization-policy-in-azure-resource-manager-template"></a>Azure Resource Manager テンプレートで認可ポリシーを定義する

ロジック アプリをデプロイするために ARM テンプレートで OAuth Azure AD を有効にするには、[ロジック アプリのリソース定義](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)の `properties` セクションで、`triggers` オブジェクトを含む `accessControl` オブジェクトを追加します (存在しない場合)。 `triggers` オブジェクトで、次の構文に従って 1 つ以上の認可ポリシーを定義する `openAuthenticationPolicies` オブジェクトを追加します。

```json
"resources": [
   {
      // Start logic app resource definition
      "properties": {
         "state": "<Enabled-or-Disabled>",
         "definition": {<workflow-definition>},
         "parameters": {<workflow-definition-parameter-values>},
         "accessControl": {
            "triggers": {
               "openAuthenticationPolicies": {
                  "policies": {
                     "<policy-name>": {
                        "type": "AAD",
                        "claims": [
                           {
                              "name": "<claim-name>",
                              "values": "<claim-value>"
                           }
                        ]
                     }
                  }
               }
            },
         },
      },
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
      "apiVersion": "2016-06-01",
      "dependsOn": [
      ]
   }
   // End logic app resource definition
],
```

`accessControl` セクションの詳細については、「[Azure Resource Manager テンプレートで受信 IP 範囲を制限する](#restrict-inbound-ip-template)」と [Microsoft.Logic ワークフロー テンプレートのリファレンス](/azure/templates/microsoft.logic/2019-05-01/workflows)に関する記事を参照してください。

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>受信 IP アドレスの制限

Shared Access Signature (SAS) と共に、ロジック アプリを呼び出すことができるクライアントを明確に制限したい場合があります。 たとえば、[Azure API Management](../api-management/api-management-key-concepts.md) を使用して要求エンドポイントを管理する場合は、[作成した API Management サービス インスタンス](../api-management/get-started-create-service-instance.md)の IP アドレスからの要求のみを受け入れるようお使いのロジック アプリを制限できます。

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Azure portal で受信 IP 範囲を制限する

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ワークフロー設定]** を選択します。

1. **[アクセス制御の構成]**  >  **[許可された着信 IP アドレス]** で、 **[特定の IP 範囲]** を選択します。

1. **[トリガーの IP 範囲]** で、トリガーで受け入れられる IP アドレス範囲を指定します。

   有効な IP 範囲では、*x.x.x.x/x* または *x.x.x.x-x.x.x.x* の形式が使用されます。

お使いのロジック アプリを、入れ子にされたロジック アプリとしてのみトリガーするようにしたい場合は、 **[許可された着信 IP アドレス]** の一覧で **[他のロジック アプリのみ]** を選択します。 このオプションにより、ロジック アプリのリソースに空の配列が書き込まれます。 そのため、入れ子にされたロジック アプリをトリガーできるのは、Logic Apps サービス (親ロジック アプリ) からの呼び出しだけになります。

> [!NOTE]
> IP アドレスに関わらず、[Logic Apps REST API: Workflow Triggers - Run](/rest/api/logic/workflowtriggers/run) 要求または API Management を使用することで、要求ベースのトリガーを備えたロジック アプリを引き続き実行できます。 ただし、このシナリオでも Azure REST API に対する[認証](../active-directory/develop/authentication-vs-authorization.md)が必要です。 すべてのイベントが Azure の監査ログに表示されます。 アクセス制御ポリシーを適切に設定するようにしてください。

<a name="restrict-inbound-ip-template"></a>

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Azure Resource Manager テンプレートで受信 IP 範囲を制限する

[Resource Manager テンプレートを使用してロジック アプリのデプロイを自動化する](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)場合、次の例のように、`accessControl` セクションを使用し、ロジック アプリのリソース定義に `triggers` および `actions` セクションを含めることで、*x.x.x.x/x* または *x.x.x.x-x.x.x.x* 形式で IP 範囲を指定できます。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-open-authentication-or-other-security"></a>Azure Active Directory Open Authentication またはその他のセキュリティを追加

ロジック アプリにさらに[認証](../active-directory/develop/authentication-vs-authorization.md)プロトコルを追加するには、[Azure API Management](../api-management/api-management-key-concepts.md) サービスの使用を検討してください。 このサービスでは、ロジック アプリを API として公開でき、あらゆるエンドポイント向けの監視、セキュリティ、ポリシー、ドキュメントが豊富に提供されています。 API Management では、ロジック アプリ用にパブリック エンドポイントまたはプライベート エンドポイントを公開できます。 このエンドポイントへのアクセスを承認するために、[Azure Active Directory Open Authentication](#azure-active-directory-oauth-authentication) (Azure AD OAuth)、[クライアント証明書](#client-certificate-authentication)、またはその他のセキュリティ標準を使用できます。 API Management で要求が受け取られると、サービスによって要求がお客様のロジック アプリに送信されます。さらに、必要な変換または制限もその過程で行われます。 API Management のみがロジック アプリをトリガーできるようにするには、ロジック アプリの受信 IP 範囲の設定を使用できます。

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>ロジック アプリの操作へのアクセス

ロジック アプリの管理、編集、表示など、特定のタスクの実行を特定のユーザーまたはグループのみに許可することができます。 それらのアクセス許可を制御するには、カスタマイズされたロールまたは組み込みロールを Azure サブスクリプションのメンバーに割り当てることができるように、[Azure のロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) を使用します。

* [ロジック アプリの共同作成者](../role-based-access-control/built-in-roles.md#logic-app-contributor): ロジック アプリを管理できますが、アクセス権を変更することはできません。

* [ロジック アプリのオペレーター](../role-based-access-control/built-in-roles.md#logic-app-operator):ロジック アプリの読み取り、有効化、無効化ができますが、編集または更新はできません。

他のユーザーがお客様のロジック アプリを変更したり削除したりしないようにするには、[Azure のリソース ロック](../azure-resource-manager/management/lock-resources.md)を使用できます。 この機能を使用すると、他のユーザーは運用リソースを変更または削除できなくなります。

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>実行履歴データへのアクセス

ロジック アプリが実行されている間、すべてのデータの[暗号化が転送中](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) (トランスポート層セキュリティ (TLS) を使用) と[保存時](../security/fundamentals/encryption-atrest.md)に行われます。 ロジック アプリの実行が完了したら、実行されたステップを含め、その実行の履歴を、各アクションの状態、期間、入力、出力と共に確認できます。 この豊富な詳細情報から、ロジック アプリがどのように実行されたか、発生した問題のトラブルシューティングをどこから始めるかに関する分析情報が得られます。

ロジック アプリの実行履歴を表示するときは、Logic Apps によってアクセスの認証が行われた後、各実行の要求と応答に対する入力および出力へのリンクが提供されます。 ただし、パスワード、シークレット、キーなどの秘匿性の高い情報を処理するアクションについては、他のユーザーがそのデータを表示したり利用したりできないようにします。 たとえば、ロジック アプリが HTTP アクションの認証時に使用する [Azure Key Vault](../key-vault/general/overview.md) のシークレットを取得する場合、そのシークレットが見えないようにする必要があります。

ロジック アプリの実行履歴にある入力と出力へのアクセスを制御するには、次のオプションがあります。

* [IP アドレス範囲でアクセスを制限する](#restrict-ip)。

  このオプションでは、特定の IP アドレス範囲からの要求に基づいて実行履歴へのアクセスをセキュリティで保護できます。

* [難読化を使用して実行履歴のデータをセキュリティで保護します。](#obfuscate)

  多くのトリガーおよびアクションでは、ロジック アプリの実行履歴で、入力と出力のどちらかまたは両方を保護することができます。

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>IP アドレス範囲でアクセスを制限する

ロジック アプリの実行履歴に含まれる入力と出力へのアクセスは、特定の IP アドレス範囲からの要求のみでそのデータを表示できるように制限できます。 たとえば、すべてのユーザーが入力および出力にアクセスできないようにするには、IP アドレス範囲を「`0.0.0.0-0.0.0.0`」のように指定します。 この制限を削除できるのは管理者アクセス許可を持つ人物のみであるため、自分のロジック アプリのデータに対する "ジャストインタイム" アクセスが可能になります。 制限する IP 範囲を指定するには、Azure portal を使用するか、ロジック アプリのデプロイに使用する Azure Resource Manager テンプレートで実行することができます。

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Azure portal で IP 範囲を制限する

1. Azure portal のロジック アプリ デザイナーでお客様のロジック アプリを開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ワークフロー設定]** を選択します。

1. **[アクセス制御の構成]**  >  **[許可された着信 IP アドレス]** で、 **[特定の IP 範囲]** を選択します。

1. **[コンテンツの IP 範囲]** で、入力と出力からの内容にアクセスできる IP アドレス範囲を指定します。

   有効な IP 範囲では、*x.x.x.x/x* または *x.x.x.x-x.x.x.x* の形式が使用されます。

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Azure Resource Manager テンプレートで IP 範囲を制限する

[Resource Manager テンプレートを使用してロジック アプリのデプロイを自動化する](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)場合、次の例のように、ロジック アプリのリソース定義の `accessControl` セクションで `contents` セクションを使用して、IP 範囲を指定できます。

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>難読化を使用して実行履歴のデータをセキュリティで保護する

多くのトリガーおよびアクションには、ロジック アプリの実行履歴から、入力と出力のどちらかまたは両方をセキュリティで保護するための設定があります。 これらの設定を使用してこのデータをセキュリティで保護する前に、[これらの考慮事項を確認してください](#obfuscation-considerations)。

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>デザイナーで入力と出力のセキュリティを保護する

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

   ![ロジック アプリ デザイナーでロジック アプリを開く](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. 機密データをセキュリティで保護するトリガーまたはアクションで省略記号 ( **...** ) ボタンを選択し、 **[設定]** を選択します。

   ![トリガーまたはアクションの設定を開く](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. **[セキュリティで保護された入力]** と **[セキュリティで保護された出力]** のいずれかまたは両方をオンにします。 完了したら、 **[完了]** をクリックします。

   ![[セキュリティで保護された入力] または [セキュリティで保護された出力] をオンにする](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   アクションまたはトリガーのタイトル バーにロック アイコンが表示されます。

   ![アクションまたはトリガーのタイトル バーにロック アイコンが表示される](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   前のアクションからのセキュリティで保護された出力を表すトークンにも、ロック アイコンが表示されます。 たとえば、そのような出力をアクションで使用するために動的コンテンツ リストから選択すると、そのトークンにロック アイコンが表示されます。

   ![セキュリティで保護された出力用のトークンを選択する](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. ロジック アプリの実行後、その実行の履歴を表示できます。

   1. ロジック アプリの **[概要]** ウィンドウで、表示する実行を選択します。

   1. **[ロジック アプリの実行]** ウィンドウで、確認したいアクションを展開します。

      入力と出力の両方を隠すことを選択した場合は、それらの値が非表示になります。

      ![実行履歴で非表示になっている入力と出力](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>コード ビューで入力と出力のセキュリティを保護する

基になるトリガーまたはアクションの定義で、次の値のいずれかまたは両方を含んだ `runtimeConfiguration.secureData.properties` 配列を追加または更新します。

* `"inputs"`:実行履歴における入力のセキュリティを保護します。
* `"outputs"`:実行履歴における出力のセキュリティを保護します。

ここでは、それらの設定を使用してこのデータをセキュリティで保護する際に[確認すべきいくつかの考慮事項](#obfuscation-considerations)について取り上げます。

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-securing-inputs-and-outputs"></a>入力と出力をセキュリティで保護する際の考慮事項

* トリガーまたはアクションの入力または出力を隠すと、Logic Apps から Azure Log Analytics にそのセキュリティで保護されたデータが送信されません。 また、そのトリガーまたはアクションに[追跡対象プロパティ](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)を追加して監視することもできません。

* セキュリティで保護された出力は、[ワークフロー履歴を処理するための Logic Apps API](/rest/api/logic/) から返されません。

* 入力を隠すか出力を明示的に隠すアクションからの出力をセキュリティで保護するには、そのアクションで **[セキュリティで保護された出力]** を手動で有効にします。

* ダウンストリームのアクションで、実行履歴にそのデータを隠すよう求める場合は、 **[セキュリティで保護された入力]** または **[セキュリティで保護された出力]** を確実にオンにしてください。

  **[セキュリティで保護された出力] の設定**

  トリガーまたはアクションで **[セキュリティで保護された出力]** を手動でオンにすると、Logic Apps により、実行履歴でその出力が非表示になります。 それらのセキュリティで保護された出力がダウンストリームのアクションで明示的に入力として使用されている場合、そのアクションの入力が実行履歴では非表示となりますが、アクションの **[セキュリティで保護された入力]** の設定は "*有効になりません*"。

  ![入力としてのセキュリティで保護された出力とダウンストリームによるほとんどのアクションへの影響](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  [作成]、[JSON の解析]、[応答] の各アクションには、 **[セキュリティで保護された入力]** の設定しかありません。 この設定をオンにした場合、それらのアクションの出力も非表示になります。 セキュリティで保護されたアップストリームの出力がそれらのアクションの入力として明示的に使用された場合、Logic Apps によってそれらのアクションの入力と出力は非表示となりますが、それらのアクションの **[セキュリティで保護された入力]** の設定は "*有効になりません*"。 [作成]、[JSON の解析]、[応答] のいずれかのアクションからの非表示の出力がダウンストリームのアクションで明示的に入力として使用された場合、"*そのダウンストリームのアクションの入力と出力は非表示になりません*"。

  ![入力としてのセキュリティで保護された出力とダウンストリームによる特定のアクションへの影響](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **[セキュリティで保護された入力] の設定**

  トリガーまたはアクションで **[セキュリティで保護された入力]** を手動でオンにすると、Logic Apps により、実行履歴でその入力が非表示になります。 そのトリガーまたはアクションからの表示可能な出力がダウンストリームのアクションの入力として明示的に使用されている場合、そのダウンストリームのアクションの入力が実行履歴では非表示となりますが、このアクションの **[セキュリティで保護された入力]** は "*有効にならず*"、そのアクションの出力が非表示になることもありません。

  ![セキュリティで保護された入力とダウンストリームのほとんどのアクションへの影響](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  セキュリティで保護された入力を持つトリガーまたはアクションからの表示可能な出力が、[作成]、[JSON の解析]、[応答] の各アクションで明示的に使用された場合、これらのアクションの入力と出力は非表示となりますが、そのアクションの **[セキュリティで保護された入力]** の設定は "*有効になりません*"。 [作成]、[JSON の解析]、[応答] のいずれかのアクションからの非表示の出力がダウンストリームのアクションで明示的に入力として使用された場合、"*そのダウンストリームのアクションの入力と出力は非表示になりません*"。

  ![セキュリティで保護された入力とダウンストリームの特定のアクションへの影響](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>パラメーターの入力へのアクセス

デプロイ先が複数の異なる環境にまたがる場合、環境に応じて変わる値は、ワークフロー定義内でパラメーター化することを検討してください。 そのようにすると、[Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)を使用してロジック アプリをデプロイすることでデータのハードコーディングを避け、セキュリティで保護されたパラメーターを定義することで機密データを保護し、[パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)を使用することで[テンプレートのパラメーター](../azure-resource-manager/templates/template-parameters.md)で個別の入力としてそのデータを渡すことができます。

たとえば、[Azure Active Directory Open Authentication (Azure AD OAuth)](#azure-active-directory-oauth-authentication) で HTTP アクションの認証を行う場合、認証に使用されるクライアント ID とクライアント シークレットを受け入れるパラメーターを定義し、隠すことができます。 ロジック アプリでこれらのパラメーターを定義するには、ロジック アプリのワークフロー定義内の `parameters` セクションと、デプロイ用の Resource Manager テンプレートを使用します。 ロジック アプリの編集時や実行履歴の確認時に表示させたくないパラメーター値をセキュリティで保護するには、`securestring` または `secureobject` 型を使用してパラメーターを定義し、必要に応じてエンコードを使用します。 この型のパラメーターはリソース定義と一緒に返されず、デプロイ後にリソースを表示するときにもアクセスできません。 それらのパラメーターの値に実行時にアクセスするには、ワークフロー定義内で `@parameters('<parameter-name>')` 式を使用します。 この式は実行時にのみ評価され、[ワークフロー定義言語](../logic-apps/logic-apps-workflow-definition-language.md)で記述されます。

> [!NOTE]
> 要求のヘッダーまたは本文でパラメーターを使用した場合、ロジック アプリの実行履歴や送信 HTTP 要求を表示したときに、そのパラメーターが表示されることがあります。 コンテンツ アクセス ポリシーも適切に設定するようにしてください。
> [難読化](#obfuscate)を使用して、実行履歴の入力と出力を表示されないようにすることもできます。 Authorization ヘッダーは入力や出力では表示されません。 そのため、ここでシークレットが使用された場合はそのシークレットを取得できません。

詳細については、このトピックの以下のセクションをご覧ください。

* [ワークフロー定義内のパラメーターをセキュリティで保護する](#secure-parameters-workflow)
* [難読化を使用して実行履歴のデータをセキュリティで保護する](#obfuscate)

[Resource Manager テンプレートを使用してロジック アプリのデプロイを自動化する](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)場合は、デプロイ時に評価される、セキュリティで保護された[テンプレート パラメーター](../azure-resource-manager/templates/template-parameters.md)を、`securestring` 型と `secureobject` 型を使用して定義できます。 テンプレート パラメーターを定義するには、テンプレートの最上位の `parameters` セクションを使用します。このセクションは独立しており、ワークフロー定義の `parameters` セクションとは異なります。 テンプレートのパラメーターに値を指定するには、別途[パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)を使用します。

たとえばシークレットを使用する場合、それらのシークレットをデプロイ時に [Azure Key Vault](../key-vault/general/overview.md) から取得する、セキュリティで保護されたテンプレート パラメーターを定義して使用することができます。 その後、パラメーター ファイルの中で、キー コンテナーとシークレットを参照することができます。 詳細については、以下のトピックを参照してください。

* [デプロイ時に Azure Key Vault を使用して機密性の値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)
* このトピックの後にある [Azure Resource Manager テンプレート内のパラメーターをセキュリティで保護する](#secure-parameters-deployment-template) (このトピックで後出)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>ワークフロー定義内のパラメーターをセキュリティで保護する

ロジック アプリのワークフロー定義内の秘匿性の高い情報を保護するには、その情報がロジック アプリの保存後に表示されないよう、セキュリティで保護されたパラメーターを使用します。 たとえば、基本認証を必要とする HTTP アクションがあって、ユーザー名とパスワードが使用されているとします。 ワークフロー定義の `parameters` セクションでは、`securestring` 型を使用して `basicAuthPasswordParam` パラメーターと `basicAuthUsernameParam` パラメーターを定義します。 そのうえで、これらのパラメーターをアクション定義の `authentication` セクションで参照します。

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートのパラメーターをセキュリティで保護する

ロジック アプリ用の [Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)には、複数の `parameters` セクションが存在します。 パスワード、キー、シークレットなどの秘匿性の高い情報を保護するには、テンプレート レベルおよびワークフロー定義レベルで、`securestring` または `secureobject` 型を使用して、セキュリティで保護されたパラメーターを定義します。 その後、これらの値を [Azure Key Vault](../key-vault/general/overview.md) に格納すれば、[パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)を使用して Key Vault とシークレットを参照することができます。 その後、デプロイ時にご利用のテンプレートからその情報を取得します。 詳しくは、[デプロイ時に Azure Key Vault を使用して機密性の値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)方法に関する記事をご覧ください。

次に、これらの `parameters` セクションについて詳しく説明します。

* テンプレートの最上位の `parameters` セクションには、そのテンプレートが "*デプロイ*" 時に使用する値のパラメーターを定義します。 たとえば、特定のデプロイ環境の接続文字列がそのような値として考えられます。 これらの値は、独立した[パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)に格納できるので、値の変更も容易に行うことができます。

* ロジック アプリのリソース定義内、かつワークフロー定義の外側にある `parameters` セクションでは、ワークフロー定義のパラメーターの値を指定します。 このセクションでは、テンプレートのパラメーターを参照するテンプレート式を使用して、それらの値を割り当てることができます。 これらの式は、デプロイ時に評価されます。

* ワークフロー定義内の `parameters` セクションでは、ロジック アプリによって実行時に使用されるパラメーターを定義します。 これらのパラメーターは、ロジック アプリのワークフロー内から、実行時に評価されるワークフロー定義式を使用して参照できます。

この例のテンプレートには、`securestring` 型を使用する、セキュリティで保護されたパラメーターの定義が複数存在します。

| パラメーター名 | 説明 |
|----------------|-------------|
| `TemplatePasswordParam` | パスワードを受け取るテンプレート パラメーター。パスワードはその後、ワークフロー定義の `basicAuthPasswordParam` パラメーターに渡されます。 |
| `TemplateUsernameParam` | ユーザー名を受け取るテンプレート パラメーター。ユーザー名はその後、ワークフロー定義の `basicAuthUserNameParam` パラメーターに渡されます。 |
| `basicAuthPasswordParam` | HTTP アクションで基本認証用のパスワードを受け取るワークフロー定義パラメーター |
| `basicAuthUserNameParam` | HTTP アクションで基本認証用のユーザー名を受け取るワークフロー定義パラメーター |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-outbound-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>ロジック アプリから呼び出されたサービスとシステムへのアクセス

ロジック アプリから呼び出しまたは要求を受信するエンドポイントをセキュリティで保護するためのいくつかの方法を次に示します。

* 送信要求に認証を追加します。

  HTTP など、送信呼び出しを行う HTTP ベースのトリガーまたはアクションを使用するときは、ロジック アプリによって送信される要求に認証を追加できます。 たとえば、次の認証の種類を選択できます。

  * [基本認証](#basic-authentication)

  * [クライアント証明書認証](#client-certificate-authentication)

  * [Active Directory OAuth 認証](#azure-active-directory-oauth-authentication)

  * [マネージド ID の認証](#managed-identity-authentication)

  詳しくは、後の「[送信呼び出しに認証を追加する](#add-authentication-outbound)」をご覧ください。

* ロジック アプリの IP アドレスからのアクセスを制限する。

  ロジック アプリからエンドポイントへの呼び出しはすべて、ロジック アプリのリージョンに基づいて指定される特定の IP アドレスが起点となります。 これらの IP アドレスからのみ要求を受け入れるフィルターを追加できます。 これらの IP アドレスを取得するには、[Azure Logic Apps の制限と構成](logic-apps-limits-and-config.md#configuration)に関するページを参照してください。

* オンプレミス システムへの接続のセキュリティを強化する。

  Azure Logic Apps では、以下のサービスとの統合を行って、より安全で信頼性の高いオンプレミス通信を実現できます。

  * オンプレミスのデータ ゲートウェイ

    Azure Logic Apps の多くのマネージド コネクタでは、オンプレミス システム (ファイル システム、SQL、SharePoint、DB2 など) への安全な接続が促進されます。 ゲートウェイは、オンプレミスのソースから、Azure Service Bus 経由の暗号化されたチャネルでデータを送信します。 すべてのトラフィックは、ゲートウェイ エージェントからのセキュリティで保護された送信トラフィックとして生成されます。 [オンプレミス データ ゲートウェイのしくみ](logic-apps-gateway-install.md#gateway-cloud-service)を確認してください。

  * Azure API Management での接続

    [Azure API Management](../api-management/api-management-key-concepts.md) には、オンプレミス システムへのプロキシと通信のセキュリティ保護を実現するためのサイト間仮想プライベート ネットワークと [ExpressRoute](../expressroute/expressroute-introduction.md) の統合など、オンプレミス接続オプションが用意されています。 オンプレミスのシステムへのアクセスを提供する API があり、[API Management サービス インスタンス](../api-management/get-started-create-service-instance.md)を作成することでその API を公開した場合は、ロジック アプリ デザイナーで組み込みの API Management トリガーまたはアクションを選択することにより、ロジック アプリのワークフローでその API を呼び出すことができます。

    > [!NOTE]
    > コネクタには、ユーザーが表示と接続のアクセス許可を持っている API Management サービスのみが表示され、消費量ベースの API Management サービスは表示されません。

    1. ロジック アプリ デザイナーの検索ボックスに「`api management`」と入力します。 トリガーとアクションのどちらを追加するかに基づいてステップを選択します。<p>

       * 常にワークフローの最初のステップであるトリガーを追加する場合は、 **[Azure API Management トリガーを選択する]** を選択します。

       * アクションを追加する場合は、 **[Azure API Management アクションを選択する]** を選択します。

       次の例では、トリガーを追加します。

       ![Azure API Management トリガーを追加する](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. 前に作成した API Management サービス インスタンスを選択します。

       ![API Management サービス インスタンスを選択する](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. 使用する API 呼び出しを選択します。

       ![既存の API を選択する](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>送信呼び出しに認証を追加する

HTTP および HTTPS エンドポイントでは、さまざまな種類の認証がサポートされています。 これらのエンドポイントへの送信呼び出しまたは要求の送信に使用するトリガーとアクションによっては、認証の種類を指定できます。 ロジック アプリ デザイナーでは、認証の種類の選択がサポートされているトリガーとアクションには、 **[認証]** プロパティがあります。 ただし、このプロパティが既定で常に表示されるとは限りません。 このような場合は、トリガーまたはアクションで **[新しいパラメーターの追加]** の一覧を開き、 **[認証]** を選択します。

> [!IMPORTANT]
> ロジック アプリで処理される機密情報を保護するには、セキュリティで保護されたパラメーターを使用し、必要に応じてデータをエンコードします。
> パラメーターの使用とセキュリティ保護の詳細については、「[パラメーターの入力へのアクセス](#secure-action-parameters)」を参照してください。

次の表では、認証の種類を選択できるトリガーとアクションで使用できる認証の種類を示します。

| 認証の種類 | サポートされているトリガーとアクション |
|---------------------|--------------------------------|
| [Basic](#basic-authentication) | Azure API Management、Azure App Service、HTTP、HTTP + Swagger、HTTP Webhook |
| [クライアント証明書](#client-certificate-authentication) | Azure API Management、Azure App Service、HTTP、HTTP + Swagger、HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management、Azure App Service、Azure Functions、HTTP、HTTP + Swagger、HTTP Webhook |
| [Raw](#raw-authentication) | Azure API Management、Azure App Service、Azure Functions、HTTP、HTTP + Swagger、HTTP Webhook |
| [管理対象 ID](#managed-identity-authentication) | Azure API Management、Azure App Services、Azure Functions、HTTP |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>[基本認証]

[[基本]](../active-directory-b2c/secure-rest-api.md) オプションを使用できる場合は、次のプロパティ値を指定します。

| プロパティ (デザイナー) | プロパティ (JSON) | 必須 | 値 | 説明 |
|---------------------|-----------------|----------|-------|-------------|
| **認証** | `type` | はい | Basic | 使用する認証の種類 |
| **ユーザー名** | `username` | はい | <*user-name*>| ターゲット サービス エンドポイントへのアクセスを認証するためのユーザー名 |
| **パスワード** | `password` | はい | <*password*> | ターゲット サービス エンドポイントへのアクセスを認証するためのパスワード |
||||||

たとえば、[デプロイを自動化するための Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)で、[セキュリティ保護されたパラメーター](#secure-action-parameters)を使用して機密情報を処理および保護する場合は、式を使用して実行時にこれらのパラメーター値にアクセスできます。 次の例の HTTP アクション定義では、認証の `type` として `Basic` が指定され、パラメーター値を取得するために [parameters() 関数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)が使用されています。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>クライアント証明書認証

[[クライアント証明書]](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) オプションを使用できる場合は、次のプロパティ値を指定します。

| プロパティ (デザイナー) | プロパティ (JSON) | 必須 | 値 | 説明 |
|---------------------|-----------------|----------|-------|-------------|
| **認証** | `type` | はい | **クライアント証明書** <br>or <br>`ClientCertificate` | 使用する認証の種類。 [Azure API Management](../api-management/api-management-howto-mutual-certificates.md) で証明書を管理できます。 <p></p>**注**:カスタム コネクタでは、受信と送信両方の呼び出しに対して証明書ベースの認証はサポートされていません。 |
| **Pfx** | `pfx` | はい | <*encoded-pfx-file-content*> | Base64 でエンコードされた Personal Information Exchange (PFX) ファイルのコンテンツ <p><p>PFX ファイルを Base64 でエンコードされた形式に変換するには、次の手順に従って PowerShell を使用します。 <p>1.証明書の内容を変数に保存します。 <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2.`ToBase64String()` 関数を使用して証明書の内容を変換し、その内容をテキスト ファイルに保存します。 <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **パスワード** | `password`| いいえ | <*password-for-pfx-file*> | PFX ファイルにアクセスするためのパスワード |
|||||

たとえば、[デプロイを自動化するための Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)で、[セキュリティ保護されたパラメーター](#secure-action-parameters)を使用して機密情報を処理および保護する場合は、式を使用して実行時にこれらのパラメーター値にアクセスできます。 次の例の HTTP アクション定義では、認証の `type` として `ClientCertificate` が指定され、パラメーター値を取得するために [parameters() 関数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)が使用されています。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

クライアント証明書認証を使用してサービスをセキュリティ保護する方法の詳細については、次のトピックを参照してください。

* [Azure API Management でクライアント証明書認証を使用して API のセキュリティを強化する](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Azure API Management でクライアント証明書認証を使用してバックエンド サービスのセキュリティを強化する](../api-management/api-management-howto-mutual-certificates.md)
* [クライアント証明書を使用して RESTfuL サービスのセキュリティを強化する](../active-directory-b2c/secure-rest-api.md)
* [アプリケーションを認証するための証明書資格情報](../active-directory/develop/active-directory-certificate-credentials.md)
* [Azure App Service のご自分のコードから TLS/SSL 証明書を使用する](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-open-authentication"></a>Azure Active Directory Open Authentication

Request トリガーでは、[Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) を使用して、ロジック アプリの [Azure AD 承認ポリシー](#enable-oauth)を設定した後に着信呼び出しを認証できます。 **Active Directory OAuth** の認証の種類を提供するその他のすべてのトリガーとアクションについては、次のプロパティ値を指定します。

| プロパティ (デザイナー) | プロパティ (JSON) | 必須 | 値 | 説明 |
|---------------------|-----------------|----------|-------|-------------|
| **認証** | `type` | はい | **Active Directory OAuth** <br>or <br>`ActiveDirectoryOAuth` | 使用する認証の種類。 現在、Logic Apps では [OAuth 2.0 プロトコル](../active-directory/develop/v2-overview.md)が使用されています。 |
| **機関** | `authority` | いいえ | <*URL-for-authority-token-issuer*> | 認証トークンを提供する機関の URL。 この値の既定値は `https://login.windows.net` です。 |
| **テナント** | `tenant` | はい | <*tenant-ID*> | Azure AD テナントのテナント ID |
| **対象ユーザー** | `audience` | はい | <*resource-to-authorize*> | 承認で使用するリソース (`https://management.core.windows.net/` など) |
| **クライアント ID** | `clientId` | はい | <*client-ID*> | 承認を要求しているアプリのクライアント ID |
| **資格情報の種類** | `credentialType` | はい | Certificate <br>or <br>Secret | 承認を要求するためにクライアントで使用される資格情報の種類。 このプロパティと値はロジック アプリの基になっている定義には出現しませんが、選択した資格情報の種類に対して表示されるプロパティがそれによって決まります。 |
| **シークレット** | `secret` | はい (ただし資格情報の種類が "Secret" の場合のみ) | <*client-secret*> | 承認を要求しているクライアント シークレット |
| **Pfx** | `pfx` | はい (ただし資格情報の種類が "Certificate" の場合のみ) | <*encoded-pfx-file-content*> | Base64 でエンコードされた Personal Information Exchange (PFX) ファイルのコンテンツ |
| **パスワード** | `password` | はい (ただし資格情報の種類が "Certificate" の場合のみ) | <*password-for-pfx-file*> | PFX ファイルにアクセスするためのパスワード |
|||||

たとえば、[デプロイを自動化するための Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)で、[セキュリティ保護されたパラメーター](#secure-action-parameters)を使用して機密情報を処理および保護する場合は、式を使用して実行時にこれらのパラメーター値にアクセスできます。 次の例の HTTP アクション定義では、認証の `type` として `ActiveDirectoryOAuth`、資格情報の種類として `Secret` が指定されており、パラメーター値を取得するために [parameters() 関数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)が使用されています。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>Raw 認証

**[未加工]** オプションが使用可能な場合は、[OAuth 2.0 プロトコル](https://oauth.net/2/)に従っていない[認証スキーム](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml)を使用する必要があるときに、この認証の種類を使用できます。 この種類では、送信要求で送信する Authorization ヘッダーの値を手動で作成し、トリガーまたはアクションでそのヘッダー値を指定します。

たとえば、[OAuth 1.0 プロトコル](https://tools.ietf.org/html/rfc5849)に従う HTTPS 要求のヘッダーの例を次に示します。

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

Raw 認証をサポートするトリガーまたはアクションでは、次のプロパティ値を指定します。

| プロパティ (デザイナー) | プロパティ (JSON) | 必須 | 値 | 説明 |
|---------------------|-----------------|----------|-------|-------------|
| **認証** | `type` | はい | Raw | 使用する認証の種類 |
| **Value** | `value` | はい | <*authorization-header-value*> | 認証に使用する Authorization ヘッダーの値 |
||||||

たとえば、[デプロイを自動化するための Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)で、[セキュリティ保護されたパラメーター](#secure-action-parameters)を使用して機密情報を処理および保護する場合は、式を使用して実行時にこれらのパラメーター値にアクセスできます。 次の例の HTTP アクション定義では、認証の `type` として `Raw` が指定され、パラメーター値を取得するために [parameters() 関数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)が使用されています。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>マネージド ID の認証

[特定のトリガーまたはアクション](#add-authentication-outbound)で [[マネージド ID]](../active-directory/managed-identities-azure-resources/overview.md) オプションが使用可能な場合、ロジック アプリでは、サインインせずに Azure Active Directory (Azure AD) で保護された他のリソースにアクセスするための認証用に、システム割り当ての ID、または手動で作成されたユーザー割り当ての*単一*の ID を使用できます。 この ID は、ユーザーの代わりに Azure で管理されます。ユーザーがシークレットを提供したりローテーションしたりする必要がないため、資格情報の保護に役立ちます。 [Azure AD 認証用のマネージド ID がサポートされているサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)の詳細をご覧ください。

1. ロジック アプリでマネージド ID を使用するには、その前に「[Azure Logic Apps でマネージド ID を使用して認証し、リソースにアクセスする](../logic-apps/create-managed-service-identity.md)」の手順に従います。 これらの手順により、ロジック アプリでマネージド ID が有効になり、ターゲットの Azure リソースに対するその ID のアクセスが設定されます。

1. Azure 関数でマネージド ID を使用できるようにするには、先に [Azure Functions の認証を有効](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)にします。

1. マネージド ID を使用するトリガーまたはアクションで、次のプロパティ値を指定します。

   | プロパティ (デザイナー) | プロパティ (JSON) | 必須 | 値 | 説明 |
   |---------------------|-----------------|----------|-------|-------------|
   | **認証** | `type` | はい | **Managed Identity** <br>or <br>`ManagedServiceIdentity` | 使用する認証の種類 |
   | **Managed Identity** | `identity` | はい | * **システム割り当てマネージド ID**。 <br>or <br>`SystemAssigned` <p><p>* <*user-assigned-identity-name*> | 使用するマネージド ID |
   | **対象ユーザー** | `audience` | はい | <*target-resource-ID*> | アクセスするターゲット リソースのリソース ID。 <p>たとえば、`https://storage.azure.com/` では、認証用の[アクセス トークン](../active-directory/develop/access-tokens.md)がすべてのストレージ アカウントに対して有効になります。 ただし、特定のストレージ アカウントに対する `https://fabrikamstorageaccount.blob.core.windows.net` など、ルート サービスの URL を指定することもできます。 <p>**注**: **[対象ユーザー]** プロパティは、一部のトリガーまたはアクションでは表示されない可能性があります。 このプロパティが表示されるようにするには、トリガーまたはアクションで **[新しいパラメーターの追加]** の一覧を開き、 **[対象ユーザー]** を選択します。 <p><p>**重要**:このターゲット リソース ID が、必要な末尾のスラッシュも含めて、Azure AD で予想される値と*正確に一致する*ようにします。 そのため、すべての Azure Blob Storage アカウントの `https://storage.azure.com/` リソース ID には、末尾のスラッシュが必要です。 ただし、特定のストレージ アカウントのリソース ID については、末尾のスラッシュは必要ありません。 これらのリソース ID を調べるには、「[Azure AD 認証をサポートしている Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。 |
   |||||

   たとえば、[デプロイを自動化するための Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)で、[セキュリティ保護されたパラメーター](#secure-action-parameters)を使用して機密情報を処理および保護する場合は、式を使用して実行時にこれらのパラメーター値にアクセスできます。 次の例の HTTP アクション定義では、認証の `type` として `ManagedServiceIdentity` が指定され、パラメーター値を取得するために [parameters() 関数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)が使用されています。

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>接続の作成をブロックする

Azure Logic Apps でコネクタを使用して特定のリソースに接続することが組織で許可されていない場合は、[Azure Policy](../governance/policy/overview.md) を使用して、ロジック アプリのワークフロー内の特定のコネクタについて、[接続を作成する機能をブロックする](../logic-apps/block-connections-connectors.md)ことができます。 詳細については、[Azure Logic Apps での特定のコネクタによって作成される接続のブロック](../logic-apps/block-connections-connectors.md)に関するページを参照してください。

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>ロジック アプリの分離に関するガイダンス

[Azure Government 影響レベル 5 分離ガイダンス](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps)および[米国国防総省クラウド コンピューティング セキュリティ要件ガイド (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html) に関するページで説明されているリージョンでのすべての影響レベルをサポートする Azure Logic Apps を [Azure Government](../azure-government/documentation-government-welcome.md) で使用できます。 これらの要件を満たすため、ロジック アプリで他の Azure テナントによるパフォーマンスへの影響が軽減され、コンピューティング リソースが他のテナントと共有されないよう、Logic Apps では、専用リソースを持つ環境でワークフローを作成および実行する機能がサポートされています。

* 独自のコードを実行したり、XML 変換を実行したりするには、それぞれ[インライン コード機能](../logic-apps/logic-apps-add-run-inline-code.md)を使用したり、[マップとして使用するアセンブリ](../logic-apps/logic-apps-enterprise-integration-maps.md)を提供したりするのではなく、[Azure 関数を作成して呼び出します](../logic-apps/logic-apps-azure-functions.md)。 また、分離要件に準拠するように、関数アプリのホスティング環境を設定します。

  たとえば、影響レベル 5 の要件を満たすには、[**Isolated** 価格レベル](../app-service/overview-hosting-plans.md) と共にやはり **Isolated** 価格レベルである [App Service Environment (ASE)](../app-service/environment/intro.md) を使用する [App Service プラン](../azure-functions/functions-scale.md#app-service-plan)で関数アプリを作成します。 この環境では、関数アプリは専用の Azure 仮想マシンと専用の Azure 仮想ネットワーク上で実行されます。これにより、アプリに対するコンピューティング分離の上でのネットワークの分離と、最大のスケールアウト機能が提供されます。 詳細については、[Azure Government 影響レベル 5 分離ガイダンス - Azure Functions](../azure-government/documentation-government-impact-level-5.md#azure-functions) に関するページを参照してください。

  詳細については、以下のトピックを参照してください。<p>

  * [Azure App Service プラン](../app-service/overview-hosting-plans.md)
  * [Azure Functions のネットワーク オプション](../azure-functions/functions-networking-options.md)
  * [仮想マシン用 Azure 専用ホスト](../virtual-machines/windows/dedicated-hosts.md)
  * [Azure における仮想マシンの分離性](../virtual-machines/isolation.md)
  * [仮想ネットワークに専用の Azure サービスをデプロイする](../virtual-network/virtual-network-for-azure-services.md)

* 専用リソース上で実行され、Azure 仮想ネットワークによって保護されたリソースにアクセスできるロジック アプリを作成するには、[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成できます。

  * 一部の Azure 仮想ネットワークでは、プライベート エンドポイント ([Azure Private Link](../private-link/private-link-overview.md)) を使用して、Azure でホストされている Azure Storage、Azure Cosmos DB、Azure SQL Database、パートナー サービス、カスタマー サービスなどの Azure PaaS サービスへのアクセスが提供されます。 ロジック アプリでプライベート エンドポイントを使用する仮想ネットワークにアクセスする必要がある場合は、ISE の内部でそれらのロジック アプリを作成、デプロイ、実行する必要があります。

  * Azure Storage によって使用される暗号化キーをより詳細に制御するには、[Azure Key Vault](../key-vault/general/overview.md) を使用して、独自のキーを設定、使用、管理できます。 この機能は "Bring Your Own Key" (BYOK) とも呼ばれ、キーは "カスタマー マネージド キー" と呼ばれます。 詳細については、「[Azure Logic Apps の統合サービス環境 (ISE) の保存データを暗号化するためにカスタマー マネージド キーを設定する](../logic-apps/customer-managed-keys-integration-service-environment.md)」を参照してください。

詳細については、以下のトピックを参照してください。

* [Azure パブリック クラウドでの分離](../security/fundamentals/isolation-choices.md)
* [Azure における機密性の高い IaaS アプリのセキュリティ](/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps に対する Azure セキュリティ ベースライン](../logic-apps/security-baseline.md)
* [Azure Logic Apps のデプロイを自動化する](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [ロジック アプリの監視](../logic-apps/monitor-logic-apps-log-analytics.md)
