---
title: プログラムによる Azure サブスクリプションの作成
description: 追加の Azure サブスクリプションをプログラムで作成する方法について説明します。
author: amberbhargava
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: banders
ms.openlocfilehash: 47d4454c47967d07898492176438e547b1e561b6
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198685"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>プログラムによる Azure サブスクリプションの作成 (プレビュー)

[Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)、[Microsoft 顧客契約 (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) または [Microsoft Partner Agreement (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) の課金アカウントをお持ちの Azure ユーザーは、プログラムを使用してサブスクリプションを作成できます。 この記事では、Azure Resource Manager を使用してプログラムからサブスクリプションを作成する方法について説明します。

プログラムで Azure サブスクリプションを作成した場合、そのサブスクリプションには、お客様が Microsoft または正規の販売代理店と締結した Azure サービスの契約が適用されます。 詳細については、「[Microsoft Azure の法的情報](https://azure.microsoft.com/support/legal/)」を参照してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>EA 課金アカウントのサブスクリプションを作成する

### <a name="prerequisites"></a>前提条件

サブスクリプションを作成するには、登録アカウントの所有者ロールが必要です。 そのロールを取得する方法は 2 つあります。

* 登録のエンタープライズ管理者は、ユーザーを[アカウント所有者に設定](https://ea.azure.com/helpdocs/addNewAccount)できます (サインインが必要)。これにより、ユーザーは登録アカウントの所有者になります。

* 登録アカウントの既存の所有者が、[アクセス許可を付与](grant-access-to-create-subscription.md)できます。 同様に、サービス プリンシパルを使用して EA サブスクリプションを作成する場合は、[そのサービス プリンシパルにサブスクリプションを作成する権限を付与](grant-access-to-create-subscription.md)する必要があります。

### <a name="find-accounts-you-have-access-to"></a>アクセスできるアカウントを検索します。

アカウント所有者に関連付けられた登録アカウントに追加されると、アカウントと登録の関係を使用してサブスクリプション料金の請求先が決定されます。 このアカウント内で作成されたすべてのサブスクリプションは、アカウントが含まれる EA 登録に課金されます。 サブスクリプションを作成するには、サブスクリプションを所有するための登録アカウントおよびユーザー プリンシパルに関する値を受け渡す必要があります。

次のコマンドを実行するには、サブスクリプションが既定で作成されるディレクトリである、アカウント所有者の*ホーム ディレクトリ*にログインしている必要があります。

### <a name="rest"></a>[REST](#tab/rest)

自分がアクセスできるすべての登録アカウントを一覧表示するための要求:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

API 応答で、自分がアクセスできるすべての登録アカウントが一覧表示されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

`principalName` プロパティを使用して、サブスクリプション料金の請求先アカウントを指定します。 そのアカウントの `name` をコピーします。 たとえば、登録アカウント SignUpEngineering@contoso.com でサブスクリプションを作成する場合、```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` をコピーします。 この識別子は登録アカウントのオブジェクト ID です。 次のステップでこの値を `enrollmentAccountObjectId` として使用できるよう、どこかに貼り付けておきます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[[Azure Cloud Shell]](https://shell.azure.com/) を開き、[PowerShell] を選択します。

[Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) コマンドレットを使用して、自分がアクセスできるすべての登録アカウントを一覧表示します。

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

自分がアクセスできる登録アカウントの一覧が Azure から返されます。

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
`principalName` プロパティを使用して、サブスクリプション料金の請求先アカウントを指定します。 そのアカウントの `ObjectId` をコピーします。 たとえば、登録アカウント SignUpEngineering@contoso.com でサブスクリプションを作成する場合、```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` をコピーします。 次の手順で `enrollmentAccountObjectId` として使用できるように、このオブジェクト ID をどこかに貼り付けておきます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) コマンドを使用して、自分がアクセスできるすべての登録アカウントを一覧表示します。

```azurecli-interactive
az billing enrollment-account list
```

自分がアクセスできる登録アカウントの一覧が Azure から返されます。

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

`principalName` プロパティを使用して、サブスクリプション料金の請求先アカウントを指定します。 そのアカウントの `name` をコピーします。 たとえば、登録アカウント SignUpEngineering@contoso.com でサブスクリプションを作成する場合、```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` をコピーします。 この識別子は登録アカウントのオブジェクト ID です。 次のステップでこの値を `enrollmentAccountObjectId` として使用できるよう、どこかに貼り付けておきます。

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>特定の登録アカウントの中にサブスクリプションを作成する

次の例では、前のステップで選択した登録アカウントに *Dev Team Subscription* という名前のサブスクリプションを作成します。 サブスクリプション オファーは *MS-AZR-0017P* (通常のマイクロソフトエンタープライズ契約) です。 また、必要に応じて RBAC 所有者として 2 人のユーザーをサブスクリプションに追加することもできます。

### <a name="rest"></a>[REST](#tab/rest)

次の要求を実行します。その際、`<enrollmentAccountObjectId>` を最初のステップでコピーした `name` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) と置き換えます。 所有者を指定する場合、[ユーザー オブジェクト ID の取得方法](grant-access-to-create-subscription.md#userObjectId)をご確認ください。

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| 要素名  | Required | Type   | 説明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | いいえ      | String | サブスクリプションの表示名です。 指定されていない場合は、"Microsoft Azure エンタープライズ" などのプラン名に設定されます。                                 |
| `offerType`   | はい      | String | サブスクリプションのオファーです。 EA では、[MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (運用環境用) と [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開発/テスト用。[EA ポータルを使用してオンに設定する必要があります](https://ea.azure.com/helpdocs/DevOrTestOffer)) の 2 つのオプションがあります。                |
| `owners`      | いいえ       | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーのオブジェクト ID。  |

監視対象の `subscriptionOperation` オブジェクトが返されます。 サブスクリプションの作成が完了すると、`subscriptionOperation` オブジェクトから、サブスクリプション ID を持つ `subscriptionLink` オブジェクトが返されます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

まず、`Install-Module Az.Subscription -AllowPrerelease` を実行してこのプレビュー モジュールをインストールします。 `-AllowPrerelease` が正しく動作することを確認するには、[Get PowerShellGet Module](/powershell/scripting/gallery/installing-psget) から最新バージョンの PowerShellGet をインストールします。

次の [New-AzSubscription](/powershell/module/az.subscription) コマンドを実行します。その際、`<enrollmentAccountObjectId>` を最初のステップで収集した `ObjectId` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) で置き換えます。 所有者を指定する場合、[ユーザー オブジェクト ID の取得方法](grant-access-to-create-subscription.md#userObjectId)をご確認ください。

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| 要素名  | Required | Type   | 説明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | いいえ      | String | サブスクリプションの表示名です。 指定されていない場合は、"Microsoft Azure エンタープライズ" などのプラン名に設定されます。                                 |
| `OfferType`   | はい      | String | サブスクリプションのオファーです。 EA では、[MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (運用環境用) と [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開発/テスト用。[EA ポータルを使用してオンに設定する必要があります](https://ea.azure.com/helpdocs/DevOrTestOffer)) の 2 つのオプションがあります。                |
| `EnrollmentAccountObjectId`      | はい       | String | サブスクリプションが作成された、請求先の登録アカウントのオブジェクト ID。 この値は、`Get-AzEnrollmentAccount` から取得する GUID です。 |
| `OwnerObjectId`      | いいえ       | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーのオブジェクト ID。  |
| `OwnerSignInName`    | いいえ       | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーの、メール アドレス。 `OwnerObjectId` の代わりにこのパラメーターを使用することができます。|
| `OwnerApplicationId` | いいえ       | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するサービス プリンシパルのアプリケーション ID。 `OwnerObjectId` の代わりにこのパラメーターを使用することができます。 このパラメーターを使用している場合、サービス プリンシパルには[ディレクトリへの読み取りアクセス権](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)が必要です。|

すべてのパラメーターの完全な一覧については、[New-AzSubscription](/powershell/module/az.subscription) を参照してください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

まず、`az extension add --name subscription` を実行してこのプレビュー拡張機能をインストールします。

次の [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) コマンドを実行します。その際、`<enrollmentAccountObjectId>` を最初のステップでコピーした `name` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) で置き換えます。 所有者を指定する場合、[ユーザー オブジェクト ID の取得方法](grant-access-to-create-subscription.md#userObjectId)をご確認ください。

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 要素名  | Required | Type   | 説明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | いいえ      | String | サブスクリプションの表示名です。 指定されていない場合は、"Microsoft Azure エンタープライズ" などのプラン名に設定されます。                                 |
| `offer-type`   | はい      | String | サブスクリプションのオファーです。 EA では、[MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (運用環境用) と [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開発/テスト用。[EA ポータルを使用してオンに設定する必要があります](https://ea.azure.com/helpdocs/DevOrTestOffer)) の 2 つのオプションがあります。                |
| `enrollment-account-object-id`      | はい       | String | サブスクリプションが作成された、請求先の登録アカウントのオブジェクト ID。 この値は、`az billing enrollment-account list` から取得する GUID です。 |
| `owner-object-id`      | いいえ       | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーのオブジェクト ID。  |
| `owner-upn`    | いいえ       | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーの、メール アドレス。 `owner-object-id` の代わりにこのパラメーターを使用することができます。|
| `owner-spn` | いいえ       | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するサービス プリンシパルのアプリケーション ID。 `owner-object-id` の代わりにこのパラメーターを使用することができます。 このパラメーターを使用している場合、サービス プリンシパルには[ディレクトリへの読み取りアクセス権](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)が必要です。|

すべてのパラメーターの完全な一覧については、[az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) を参照してください。

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure Enterprise のサブスクリプション作成 API の制限事項

- この API を使用して作成できるのは、Azure Enterprise サブスクリプションのみです。
- 登録アカウントあたり作成できるサブスクリプションの上限数は 200 です。 その後、アカウントの追加のサブスクリプションは、Azure portal でのみ作成することができます。 API を使用してさらにサブスクリプションを作成する場合は、別の登録アカウントを作成します。
- アカウント所有者ではないが、RBAC 経由で登録アカウントに追加されたユーザーは、Azure portal でサブスクリプションを作成することはできません。
- サブスクリプションが作成されるテナントを選択することはできません。 サブスクリプションは常に、アカウント所有者のホーム テナント内に作成されます。 サブスクリプションを別のテナントに移動する場合は、[テナントのサブスクリプションの変更](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)に関する記事を参照してください。


## <a name="create-subscriptions-for-an-mca-account"></a>MCA アカウントのサブスクリプションを作成する

### <a name="prerequisites"></a>前提条件

サブスクリプションを作成するには、請求書セクションで所有者、共同作成者、または Azure サブスクリプション作成者のロールを持っているか、課金プロファイルまたは課金アカウントで所有者または共同作成者のロールを持っている必要があります。 詳細については、「[サブスクリプションの課金ロールとタスク](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks)」を参照してください。

次に示す例では、REST API を使用します。 現時点では、PowerShell と Azure CLI はサポートされていません。

### <a name="find-billing-accounts-that-you-have-access-to"></a>アクセスできる課金アカウントを検索する

以下の要求を行って、すべての課金アカウントを一覧表示します。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API 応答で、自分がアクセスできる課金アカウントが一覧表示されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
`displayName` プロパティを使用して、サブスクリプションを作成する課金アカウントを指定します。 確実にアカウントの agreeementType を *MicrosoftCustomerAgreement* にします。 アカウントの `name` をコピーします。  たとえば、`Contoso` 課金アカウントのサブスクリプションを作成する場合は、`5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` をコピーします。 次のステップで使用できるように、この値をどこかに貼り付けておきます。

### <a name="find-invoice-sections-to-create-subscriptions"></a>サブスクリプションを作成する請求書セクションを検索する

お使いのサブスクリプションの料金は、課金プロファイルの請求書のセクションに表示されます。 次の API を使用して、自分に Azure サブスクリプションを作成するためのアクセス権がある請求書セクションと課金プロファイルの一覧を取得します。

次の要求を実行します。その際、`<billingAccountName>` を最初のステップでコピーした `name` (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) と置き換えます。

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
API 応答には、自分にサブスクリプションを作成するためのアクセス権があるすべての請求書セクションとその課金プロファイルが一覧表示されます。

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

`invoiceSectionDisplayName` プロパティを使用して、サブスクリプションを作成する請求書セクションを指定します。 請求書セクションの `invoiceSectionId`、`billingProfileId`、`skuId` をコピーします。 たとえば、`Development` 請求書セクションの `Microsoft Azure plan` 型のサブスクリプションを作成する場合は、`/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`、`/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx`、および `0001` をコピーします。 次のステップで使用できるように、これらの値をどこかに貼り付けておきます。

### <a name="create-a-subscription-for-an-invoice-section"></a>請求書セクションのサブスクリプションを作成する

次の例では、*Dev Team Subscription* という名前で種類が *Microsoft Azure プラン*であるサブスクリプションを、*Development* 請求書セクションを対象に作成します。 サブスクリプションは *Contoso finance* の課金プロファイルに請求され、請求書の *Development* セクションに表示されます。

次の要求を実行します。その際、`<invoiceSectionId>` を 2 番目のステップでコピーした `invoiceSectionId` (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```) と置き換えます。 2 番目のステップでコピーした `billingProfileId` と `skuId` を API の要求パラメーターで渡す必要があります。 所有者を指定する場合、[ユーザー オブジェクト ID の取得方法](grant-access-to-create-subscription.md#userObjectId)をご確認ください。

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| 要素名  | Required | Type   | 説明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | はい      | String | サブスクリプションの表示名です。|
| `billingProfileId`   | はい      | String | サブスクリプションの料金に対して課金される課金プロファイルの ID です。  |
| `skuId` | はい      | String | Azure プランの種類を決定する SKU ID です。 |
| `owners`      | いいえ       | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザー プリンシパルまたはサービス プリンシパルのオブジェクト ID です。  |
| `costCenter` | いいえ      | String | サブスクリプションに関連付けられているコスト センターです。 使用状況の csv ファイルに表示されます。 |
| `managementGroupId` | いいえ      | String | サブスクリプションが追加される管理グループの ID です。 管理グループの一覧を取得するには、「[管理グループ - 一覧表示](/rest/api/resources/managementgroups/list)」の API を参照してください。 API からの管理グループの ID を使用します。 |

監視対象の `subscriptionCreationResult` オブジェクトが返されます。 サブスクリプションの作成が完了すると、`subscriptionCreationResult` オブジェクトから、サブスクリプション ID を持つ `subscriptionLink` オブジェクトが返されます。

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>MPA 課金アカウントのサブスクリプションを管理する

### <a name="prerequisites"></a>前提条件

課金アカウントのサブスクリプションを作成するには、組織のクラウド ソリューション プロバイダー アカウントにグローバル管理者ロールまたは管理エージェント ロールが必要です。 詳細については、「[パートナー センター - ユーザー ロールとアクセス許可の割り当て](https://docs.microsoft.com/partner-center/permissions-overview)」を参照してください。

次に示す例では、REST API を使用します。 現時点では、PowerShell と Azure CLI はサポートされていません。

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>アクセス権のある課金アカウントを検索する

自分がアクセスできるすべての課金アカウントを一覧表示するには、以下の要求を行います。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API 応答には、課金アカウントの一覧が表示されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
`displayName` プロパティを使用して、サブスクリプションを作成する課金アカウントを指定します。 確実にアカウントの agreeementType を *MicrosoftPartnerAgreement* にします。 アカウントの `name` をコピーします。 たとえば、`Contoso` 課金アカウントのサブスクリプションを作成する場合は、`99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` をコピーします。 次のステップで使用できるように、この値をどこかに貼り付けておきます。

### <a name="find-customers-that-have-azure-plans"></a>Azure プランの顧客を検索する

次の要求を実行します。その際、`<billingAccountName>` を最初のステップでコピーした `name` (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) と置き換えます。これにより、課金アカウントの顧客のうち、自分が Azure サブスクリプションを作成できるすべての顧客が一覧表示されます。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
API 応答には、課金アカウントの顧客のうち、Azure プランを利用している顧客が一覧表示されます。 これらの顧客のサブスクリプションを作成できます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

`displayName` プロパティを使用して、サブスクリプションを作成する対象の顧客を指定します。 顧客の `id` をコピーします。 たとえば、`Fabrikam toys` のサブスクリプションを作成する場合は、`/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx` をコピーします。 後のステップで使用するために、この値をどこかに貼り付けておきます。

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>間接プロバイダー向けのオプション: 顧客のリセラーを取得する

CSP の 2 層モデルの間接プロバイダーである場合は、顧客のサブスクリプションを作成するときにリセラーを指定できます。

次の要求を実行します。その際、`<customerId>` を 2 番目のステップでコピーした `id` (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) と置き換えます。これにより、顧客が利用できるすべてのリセラーが一覧表示されます。

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
API 応答には、顧客向けのリセラーが一覧表示されます。

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
`description` プロパティを使用して、サブスクリプションに関連付けられるリセラーを指定します。 リセラーの `resellerId` をコピーします。 たとえば、`Wingtip` を関連付ける場合は、`3xxxxx` をコピーします。 次のステップで使用できるように、この値をどこかに貼り付けておきます。

### <a name="create-a-subscription-for-a-customer"></a>顧客のサブスクリプションを作成する

次の例では、*Dev Team subscription* という名前のサブスクリプションを *Fabrikam toys* 用に作成し、*Wingtip* リセラーをそのサブスクリプションに関連付けます。 T

次の要求を実行します。その際、`<customerId>` を 2 番目のステップでコピーした `id` (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) と置き換えます。 2 番目のステップでコピーしたオプションの *resellerId* を API の要求パラメーターで渡します。

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| 要素名  | Required | Type   | 説明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | はい      | String | サブスクリプションの表示名です。|
| `skuId` | はい      | String | Azure プランの SKU ID です。 種類が Microsoft Azure プランであるサブスクリプションには *0001* を使用します。 |
| `resellerId`      | いいえ       | String | サブスクリプションに関連付けられるリセラーの MPN ID です。  |

監視対象の `subscriptionCreationResult` オブジェクトが返されます。 サブスクリプションの作成が完了すると、`subscriptionCreationResult` オブジェクトから、サブスクリプション ID を持つ `subscriptionLink` オブジェクトが返されます。

## <a name="next-steps"></a>次のステップ

* .NET を使用して Enterprise Agreement (EA) サブスクリプションを作成する例については、[GitHub のサンプル コード](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)を参照してください。
* サブスクリプションを作成し終えたら、他のユーザーおよびサービス プリンシパルでその機能を利用できるようになります。 詳細については、「[Azure Enterprise サブスクリプションを作成する権限を付与する (プレビュー)](grant-access-to-create-subscription.md)」を参照してください。
* 管理グループを使用して大量のサブスクリプションを管理する方法については、「[Azure 管理グループのリソースを整理する](../../governance/management-groups/overview.md)」を参照してください。
