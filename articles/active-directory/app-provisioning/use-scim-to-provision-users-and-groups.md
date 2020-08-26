---
title: Azure AD からアプリにユーザーをプロビジョニングするための SCIM エンドポイントを開発する
description: クロスドメイン ID 管理システム (SCIM) では、自動ユーザー プロビジョニングが標準化されます。 SCIM エンドポイントを開発し、SCIM API を Azure Active Directory と統合して、クラウド アプリケーションへのユーザーとグループのプロビジョニングの自動化を開始する方法について学習します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 03/07/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 70938bf1dcd06ce9936767c66ffead0f8627c5a7
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235470"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-ad"></a>SCIM エンドポイントの構築と Azure AD を使用したユーザー プロビジョニングの構成

アプリケーション開発者は System for Cross-Domain Identity Management (SCIM) ユーザー管理 API を使用し、アプリケーションと Azure AD の間のユーザーとグループの自動プロビジョニングを有効にできます。 この記事では、SCIM エンドポイントを構築し、Azure AD プロビジョニング サービスと統合する方法について説明します。 SCIM 仕様では、プロビジョニングのための共通のユーザー スキーマが提供されます。 SAML や OpenID Connect などのフェデレーション標準と組み合わせて使用した場合、SCIM では エンドツーエンドの標準ベースのアクセス管理用ソリューションが管理者に提供されます。

SCIM は、/Users エンドポイントと /Groups エンドポイントという 2 つのエンドポイントの標準化された定義です。 これはオブジェクトの作成、更新、削除を行うための共通の REST 動詞を使用するほか、グループ名、ユーザー名、名、姓、電子メールなどの共通属性に対して定義済みのスキーマを使用します。 SCIM 2.0 REST API を提供するアプリでは、独自のユーザー管理 API を使用する煩わしさを軽減するか、なくすことができます。 たとえば、準拠している SCIM クライアントは、JSON オブジェクトの HTTP POST を /Users エンドポイントに送信して新しいユーザー エントリを作成する方法を認識しています。 同じ基本的なアクションに対して若干異なる API を必要とするのではなく、SCIM 標準に準拠しているアプリでは、既存のクライアント、ツール、およびコードをすぐに利用できます。 

![SCIM を使用した Azure AD からアプリへのプロビジョニング](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642)、[7643](https://tools.ietf.org/html/rfc7643)、[7644](https://tools.ietf.org/html/rfc7644)) で定義されている管理用の標準ユーザー オブジェクト スキーマと REST API を使用すると、ID プロバイダーとアプリをより簡単に相互に統合できます。 SCIM エンドポイントを構築するアプリケーション開発者は、カスタム作業を行わなくても、SCIM 準拠の任意のクライアントと統合できます。

アプリケーションへのプロビジョニングを自動化するには、SCIM エンドポイントを構築し、Azure AD SCIM クライアントと統合する必要があります。 アプリケーションへのユーザーとグループのプロビジョニングを開始するには、次の手順を実行します。 
    
  * **[手順 1:ユーザーとグループのスキーマを設計する。](#step-1-design-your-user-and-group-schema)** アプリケーションに必要なオブジェクトと属性を特定し、それらが Azure AD SCIM 実装でサポートされているユーザーとグループのスキーマにどのようにマップするかを見極めます。

  * **[手順 2:Azure AD SCIM の実装について理解する。](#step-2-understand-the-azure-ad-scim-implementation)** Azure AD SCIM クライアントがどのように実装されているかを理解し、SCIM プロトコルの要求の処理と応答をモデル化します。

  * **[手順 3:SCIM エンドポイントを構築する。](#step-3-build-a-scim-endpoint)** エンドポイントは、Azure AD プロビジョニング サービスと統合するために、SCIM 2.0 と互換性がある必要があります。 オプションとして、Microsoft 共通言語基盤 (CLI) ライブラリとコード サンプルを使用してエンドポイントを構築できます。 これらのサンプルは、参照とテストのみを目的としています。実稼働アプリに対してコーディングして、依存関係を得ることはお勧めしません。

  * **[手順 4:SCIM エンドポイントを Azure AD SCIM クライアントと統合する。](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Azure AD でサポートされる SCIM 2.0 のプロファイルが実装されているサードパーティ製のアプリケーションを組織で使用している場合、ユーザーとグループのプロビジョニングとプロビジョニング解除の両方の自動化をすぐに開始することができます。

  * **[手順 5: Azure AD アプリケーション ギャラリーにアプリケーションを発行する。](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** お客様がアプリケーションを簡単に見つけてプロビジョニングを構成できるようにします。 

![SCIM エンドポイントを Azure AD に統合する手順](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>手順 1:ユーザーとグループのスキーマを設計する

すべてのアプリケーションは、ユーザーまたはグループを作成するためのさまざまな属性を必要とします。 アプリケーションで必要なオブジェクト (ユーザー、グループ) および属性 (名前、マネージャー、役職など) を特定して、統合を開始します。 SCIM 標準では、ユーザーとグループを管理するためのスキーマが定義されています。 コア ユーザー スキーマに必要な属性は、**id** (サービス プロバイダーが定義した識別子)、**externalId** (クライアントで定義された識別子)、**meta** (サービス プロバイダーによって管理される読み取り専用のメタデータ) の 3 つだけです。 他のすべての属性は省略できます。 コア ユーザー スキーマに加えて、SCIM 標準では、エンタープライズ ユーザー拡張機能と、アプリケーションのニーズに合わせてユーザー スキーマを拡張するためのモデルが定義されています。 たとえば、アプリケーションでユーザーの管理者が必要な場合は、エンタープライズ ユーザー スキーマを使用して、ユーザーの管理者と、ユーザーのメールを収集するためのコア スキーマを収集できます。 スキーマを設計するには、次の手順のようにします。
  1. アプリケーションで必要な属性の一覧を作成します。 要件を、認証に必要な属性 (例: ログイン名やメール)、ユーザーのライフサイクルを管理するために必要な属性 (例: 状態、アクティブ)、および特定のアプリケーションが動作するために必要なその他の属性 (例: マネージャー、タグ) に分割すると便利です。
  2. これらの属性がコア ユーザー スキーマまたはエンタープライズ ユーザー スキーマで既に定義されているかどうかを確認します。 必要な属性で、コア ユーザー スキーマまたはエンタープライズ ユーザー スキーマに含まれていないものがある場合は、必要な属性をカバーする拡張機能をユーザー スキーマに対して定義する必要があります。 次の例では、ユーザーに "タグ" をプロビジョニングできる拡張機能をユーザーに追加しています。 コアおよびエンタープライズ ユーザー スキーマだけで始めて、後で追加のカスタム スキーマに展開するのが最善の方法です。  
  3. SCIM 属性を Azure AD のユーザー属性にマップします。 SCIM エンドポイントで定義した属性のいずれかに、Azure AD ユーザー スキーマに明確に対応するものがない場合、ほとんどのテナントでユーザー オブジェクトにデータがまったく格納されない可能性が高くなります。 ユーザーを作成する場合に、この属性を省略可能にできるかどうかを検討します。 アプリケーションの動作に不可欠な属性の場合は、テナント管理者にスキーマの拡張を求めるか、"tags" プロパティに関する次の例で示すように拡張属性を使用します。

### <a name="table-1-outline-the-attributes-that-you-need"></a>表 1:必要な属性の概要を明らかにする 
| 手順 1:アプリに必要な属性を決定する| 手順 2:アプリの要件を SCIM 標準にマップする| 手順 3:SCIM の属性を Azure AD の属性にマップする|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.lastName|lastName|
|workMail|Emails[type eq “work”].value|Mail|
|manager|manager|manager|
|tag|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|active|isSoftDeleted (ユーザーに格納されない計算値)|

上記で定義されたスキーマは、以下の JSON ペイロードを使用して表されます。 アプリケーションに必要な属性に加えて、JSON の表現には、必要な `id`、`externalId`、`meta` の各属性が含まれていることに注意してください。

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>表 2:既定のユーザー属性マッピング
次に、以下の表を使用して、アプリケーションが必要とする属性を Azure AD および SCIM RFC の属性にマップする方法を把握できます。 Azure AD と SCIM エンドポイントの間で属性がどのようにマップされるかを[カスタマイズ](customize-application-attributes.md)できます。 ユーザーとグループの両方、または以下に示すすべての属性をサポートする必要がないことに注意してください。 これは、Azure AD の属性が SCIM プロトコルのプロパティに通常マップされる方法を参照するためのものです。 

| Azure Active Directory ユーザー | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |active |
|department|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>表 3:既定のグループ属性マッピング

| Azure Active Directory グループ | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

SCIM RFC では複数のエンドポイントが定義されています。 /User エンドポイントで開始し、そこから展開できます。 /Schemas エンドポイントは、カスタム属性を使用する場合、またはスキーマが頻繁に変更される場合に役立ちます。 これにより、クライアントで最新のスキーマを自動的に取得できます。 /Bulk エンドポイントは、グループをサポートする場合に特に役立ちます。 次の表では、SCIM 標準で定義されているさまざまなエンドポイントについて説明します。 /Schemas エンドポイントは、カスタム属性を使用する場合、またはスキーマが頻繁に変更される場合に役立ちます。 これにより、クライアントで最新のスキーマを自動的に取得できます。 /Bulk エンドポイントは、グループをサポートする場合に特に役立ちます。 次の表では、SCIM 標準で定義されているさまざまなエンドポイントについて説明します。 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>表 4:開発するエンドポイントを決定する
|ENDPOINT|Description|
|--|--|
|/User|ユーザー オブジェクトに対して CRUD 操作が実行されます。|
|/Group|グループ オブジェクトに対して CRUD 操作が実行されます。|
|/ServiceProviderConfig|サポートされているリソースや認証方法など、サポートされている SCIM 標準の機能についての詳細が提供されます。|
|/ResourceTypes|各リソースに関するメタデータが指定されます|
|/Schemas|各クライアントおよびサービス プロバイダーによってサポートされている属性のセットは、異なる場合があります。 たとえば、あるサービス プロバイダーに `name`、`title`、`emails` が含まれ、別のサービス プロバイダーに `name`、`title`、`phoneNumbers` が使用されることがあります。 スキーマ エンドポイントにより、サポートされている属性を検出できます。|
|/Bulk|一括操作を使用すると、1 回の操作でリソース オブジェクトの大きなコレクションに対する操作を実行できます (たとえば、大きなグループのメンバーシップの更新)。|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>手順 2:Azure AD SCIM の実装について
> [!IMPORTANT]
> Azure AD SCIM の実装の動作は、2018 年 12 月 18 日に最終更新が行われました。 変更点については、[Azure AD ユーザー プロビジョニング サービスの SCIM 2.0 プロトコルへのコンプライアンス](application-provisioning-config-problem-scim-compatibility.md)に関する記事をご覧ください。

お客様が SCIM 2.0 ユーザー管理 API がサポートされるアプリケーションを構築している場合、このセクションには、Azure AD SCIM クライアントの実装方法が詳細に記載されています。 また、SCIM プロトコル要求の処理と応答のモデル化方法も示されています。 SCIM エンドポイントを実装した後は、前のセクションに説明されている手順に従ってテストすることができます。

[SCIM 2.0 プロトコル仕様](http://www.simplecloud.info/#Specification)の中で、アプリケーションは次の要件を満たす必要があります。

* [SCIM プロトコルのセクション 3.3](https://tools.ietf.org/html/rfc7644#section-3.3) に従って、ユーザー、グループ (オプション) の作成をサポートする。  
* [SCIM プロトコルのセクション 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2) に従って、PATCH 要求によるユーザーまたはグループの変更をサポートする。  
* [SCIM プロトコルのセクション 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1) に従って、これまで作成したユーザーまたはグループの既知のリソースの取得をサポートする。  
* [SCIM プロトコルのセクション 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2) に従って、ユーザーまたはグループのクエリをサポートする。  既定では、ユーザーの取得には `id`、ユーザーのクエリには `username` と `externalId`、グループのクエリには `displayName` が使用されます。  
* SCIM プロトコルのセクション 3.4.2 に従って、ID と管理者によるユーザーの照会をサポートする。  
* SCIM プロトコルのセクション 3.4.2 に従って、ID とメンバーによるグループの照会をサポートする。  
* アプリケーションに対する Azure AD の認証と承認のために、単一のベアラー トークンを受け入れる。

Azure AD との互換性を確保するために、SCIM エンドポイントの実装時は次の一般的なガイドラインに従ってください。

* `id` は、すべてのリソースの必須のプロパティです。 リソースを返すすべての応答において、メンバーが 0 の `ListResponse` を除き、各リソースにこのプロパティが含まれるようにする必要があります。
* クエリ/フィルター要求への応答は常に `ListResponse` である必要があります。
* グループはオプションですが、SCIM 実装で PATCH 要求がサポートされている場合にのみ、サポートされます。
* PATCH 応答には、リソース全体を含める必要はありません。
* Microsoft Azure AD では、次の演算子のみを使用します。  
    - `eq`
    - `and`
* [https://tools.ietf.org/html/rfc7644#section-3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2 ) に定義されているように、特に PATCH `op` 操作値の場合、SCIM 内の構造要素に対して大文字と小文字を区別した一致を要求しないでください。 Azure AD では、'op' の値が `Add`、`Replace`、`Remove` として出力されます。
* Microsoft Azure AD では、エンドポイントと資格情報が有効であることを確認するため、ランダムなユーザーとグループをフェッチする要求を行います。 [Azure portal](https://portal.azure.com) 内で、**テスト接続**フローの一部としても行われます。 
* リソースのクエリが可能な属性は、[Azure portal](https://portal.azure.com) 内でアプリケーション上の照合属性として設定される必要があります。 詳細については、[ユーザー プロビジョニング属性マッピングのカスタマイズ](customize-application-attributes.md)に関するページを参照してください。

### <a name="user-provisioning-and-deprovisioning"></a>ユーザーのプロビジョニングとプロビジョニング解除

次の図は、アプリケーションの ID ストア内にあるユーザーのライフサイクルを管理するために Azure Active Directory から SCIM サービスに送信されるメッセージを示しています。  

![ユーザーのプロビジョニングとプロビジョニング解除のシーケンスを示します](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*ユーザーのプロビジョニングとプロビジョニング解除のシーケンス*

### <a name="group-provisioning-and-deprovisioning"></a>グループのプロビジョニングとプロビジョニング解除

グループのプロビジョニングとプロビジョニング解除はオプションです。 実装され有効にされている場合、次の図は、アプリケーションの ID ストア内にあるグループのライフサイクルを管理するために Azure AD から SCIM サービスに送信されるメッセージを示しています。  それらのメッセージは、次の 2 つの点でユーザーに関するメッセージと異なっています。

* グループを取得する要求では、要求に対する応答の中で提示されるリソースから、members 属性が除外されるように指定しています。  
* 参照属性に特定の値があるかどうかを判別する要求は、members 属性に関する要求になります。  

![グループのプロビジョニングとプロビジョニング解除のシーケンスを示します](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*グループのプロビジョニングとプロビジョニング解除のシーケンス*

### <a name="scim-protocol-requests-and-responses"></a>SCIM プロトコル要求と応答
このセクションでは、Azure AD SCIM クライアントによって出力される SCIM 要求の例と、想定される応答の例を示します。 最良の結果を得るには、このような要求をこの形式で処理し、想定される応答を出力するよう、アプリをコーディングしてください。

> [!IMPORTANT]
> Azure AD ユーザー プロビジョニング サービスが以下に示す操作を出力する方法とタイミングについては、「[プロビジョニング サイクル: 初回と増分](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)」を参照してください。これは「[プロビジョニングのしくみ](how-provisioning-works.md)」に含まれるセクションです。

[ユーザー操作](#user-operations)
  - [ユーザーの作成](#create-user) ([要求](#request) / [応答](#response))
  - [ユーザーの取得](#get-user) ([要求](#request-1) / [応答](#response-1))
  - [クエリによるユーザーの取得](#get-user-by-query) ([要求](#request-2) / [応答](#response-2))
  - [クエリによるユーザーの取得 - 0 件の結果](#get-user-by-query---zero-results) ([要求](#request-3)
/ [応答](#response-3))
  - [ユーザーの更新 [複数値のプロパティ]](#update-user-multi-valued-properties) ([要求](#request-4) /  [応答](#response-4))
  - [ユーザーの更新 [単一値のプロパティ]](#update-user-single-valued-properties) ([要求](#request-5)
/ [応答](#response-5)) 
  - [ユーザーの無効化](#disable-user) ([要求](#request-14) / 
[応答](#response-14))
  - [ユーザーの削除](#delete-user) ([要求](#request-6) / 
[応答](#response-6))


[グループ操作](#group-operations)
  - [グループの作成](#create-group) ( [要求](#request-7) / [応答](#response-7))
  - [グループの取得](#get-group) ( [要求](#request-8) / [応答](#response-8))
  - [displayName でのグループの取得](#get-group-by-displayname) ([要求](#request-9) / [応答](#response-9))
  - [グループの更新 [非メンバー属性]](#update-group-non-member-attributes) ([要求](#request-10) /
 [応答](#response-10))
  - [グループの更新 [メンバーの追加]](#update-group-add-members) ( [要求](#request-11) /
[応答](#response-11))
  - [グループの更新 [メンバーの削除]](#update-group-remove-members) ([要求](#request-12) /
[応答](#response-12))
  - [グループの削除](#delete-group) ([要求](#request-13) /
[応答](#response-13))

### <a name="user-operations"></a>ユーザー操作

* ユーザーは `userName` または `email[type eq "work"]` 属性でクエリすることができます。  

#### <a name="create-user"></a>[Create User]

###### <a name="request"></a>Request

*POST /Users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Response

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>ユーザーの取得

###### <a name="request"></a><a name="request-1"></a>要求
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>応答 (ユーザー検出)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Request
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>応答 (ユーザー検出。 詳細は必要なく、状態のみであることに注意してください)。

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>クエリによるユーザーの取得

##### <a name="request"></a><a name="request-2"></a>要求

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>応答

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>クエリによるユーザーの取得 - 0 件の結果

##### <a name="request"></a><a name="request-3"></a>要求

*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response"></a><a name="response-3"></a>応答

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>ユーザーの更新 [複数値のプロパティ]

##### <a name="request"></a><a name="request-4"></a>要求

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>応答

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>ユーザーの更新 [単一値のプロパティ]

##### <a name="request"></a><a name="request-5"></a>要求

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>応答

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>ユーザーの無効化

##### <a name="request"></a><a name="request-14"></a>要求

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>応答

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>ユーザーの削除

##### <a name="request"></a><a name="request-6"></a>要求

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>応答

*HTTP/1.1 204 No Content*

### <a name="group-operations"></a>グループ操作

* グループは、常に空のメンバーのリストで作成されます。
* グループは `displayName` 属性でクエリすることができます。
* グループ PATCH 要求を更新すると、応答に *HTTP 204 No Content* が含まれることになります。 全メンバーのリストを含めて本文を返すことはお勧めできません。
* グループの全メンバーを返すことをサポートする必要はありません。

#### <a name="create-group"></a>グループの作成

##### <a name="request"></a><a name="request-7"></a>要求

*POST /Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>応答

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>グループの取得

##### <a name="request"></a><a name="request-8"></a>要求

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>応答
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>displayName でのグループの取得

##### <a name="request"></a><a name="request-9"></a>要求
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>応答

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>グループの更新 [非メンバー属性]

##### <a name="request"></a><a name="request-10"></a>要求

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>応答

*HTTP/1.1 204 No Content*

### <a name="update-group-add-members"></a>グループの更新 [メンバーの追加]

##### <a name="request"></a><a name="request-11"></a>要求

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>応答

*HTTP/1.1 204 No Content*

#### <a name="update-group-remove-members"></a>グループの更新 [メンバーの削除]

##### <a name="request"></a><a name="request-12"></a>要求

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>応答

*HTTP/1.1 204 No Content*

#### <a name="delete-group"></a>グループの削除

##### <a name="request"></a><a name="request-13"></a>要求

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>応答

*HTTP/1.1 204 No Content*

### <a name="security-requirements"></a>セキュリティ要件
**TLS プロトコルのバージョン**

許容される TLS プロトコルのバージョンは、TLS 1.2 と TLS 1.3 だけです。 他のバージョンの TLS は許可されません。 SSL のバージョンは許可されません。 
- RSA キーは、2,048 ビット以上である必要があります。
- ECC キーは 256 ビット以上で、承認された楕円曲線を使用して生成されている必要があります


**キーの長さ**

すべてのサービスでは、十分な長さの暗号化キーを使用して生成された X.509 証明書が使用されている必要があります。

**暗号スイート**

すべてのサービスは、以下の暗号スイートを、次に示す厳密な順序で使用するように、構成する必要があります。 RSA 証明書だけがある場合は、ECDSA 暗号スイートをインストールしても何の影響もないことに注意してください。 </br>

TLS 1.2 暗号スイートの最低条件:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>IP 範囲
Azure AD プロビジョニング サービスは、現在、[こちら](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all)に記載されている AzureActiveDirectory と AzureActiveDirectoryDomainServices の IP 範囲で動作します。 AzureActiveDirectory の IP 範囲のみへの統合を行う作業が進行中です。 

## <a name="step-3-build-a-scim-endpoint"></a>手順 3:SCIM エンドポイントを構築する

これでスキーマを設計し、Azure AD SCIM の実装を理解したので、SCIM エンドポイントの開発を開始できます。 最初から開始して完全に独自の実装を構築するのではなく、SCIM コミュニティによって発行された多数のオープンソース SCIM ライブラリを使用できます。

Azure AD プロビジョニング チームによって発行されたオープンソースの .NET Core [参照コード](https://aka.ms/SCIMReferenceCode)は、開発を開始するためのリソースの 1 つです。 SCIM エンドポイントを構築したら、それをテストします。参照コードの一部として提供されている [Postman テスト](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)のコレクションを使用したり、[上記](#user-operations)の要求または応答のサンプルを通じて実行したりすることができます。  

   > [!Note]
   > 参照コードは、SCIM エンドポイントの構築を開始するのに役立ち、"現状のまま" 提供されることを目的としています。 コードのビルドと保守に役立つため、コミュニティからの貢献は歓迎されます。

このソリューションは、_Microsoft.SCIM_ および _Microsoft.SCIM.WebHostSample_ の 2 つのプロジェクトで構成されています。

_Microsoft.SCIM_ プロジェクトは、SCIM 仕様に準拠する Web サービスのコンポーネントを定義するライブラリです。 これによりインターフェイス _Microsoft.SCIM.IProvider_ が宣言され、要求がプロバイダーのメソッドへの呼び出しに変換され、ID ストアで動作するようにプログラムされます。

![内訳:プロバイダーのメソッドへの呼び出しに変換された要求](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

_Microsoft.SCIM.WebHostSample_ プロジェクトは、_空の_テンプレートに基づく Visual Studio ASP.NET Core Web アプリケーションです。 これにより、サンプル コードをスタンドアロンとしてデプロイし、コンテナーまたはインターネット インフォメーション サービス内でホストすることができます。 また、_Microsoft.SCIM.IProvider_ インターフェイスを実装して、クラスをサンプル ID ストアとしてメモリに保持することができます。

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>カスタム SCIM エンドポイントの構築

SCIM サービスには、HTTP アドレスと、ルート証明機関が次のいずれかの名前であるサーバー認証証明書が設定されている必要があります。

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

.NET Core SDK には、開発時に使用できる HTTPS 開発証明書が含まれています。この証明書は、最初の実行エクスペリエンスの一部としてインストールされます。 ASP.NET Core Web アプリケーションの実行方法に応じて、異なるポートがリッスンされます。

* Microsoft.SCIM.WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

ASP.NET Core の HTTPS の詳細については、次のリンクを参照してください: 「[ASP.NET Core に HTTPS を適用する](/aspnet/core/security/enforcing-ssl)」

### <a name="handling-endpoint-authentication"></a>エンドポイント認証の処理

Azure Active Directory からの要求には、OAuth 2.0 のベアラー トークンが含まれます。 要求を受信するサービスでは、発行者が本来の Azure Active Directory テナントに対応する Azure Active Directory であることを認証する必要があります。

トークンでは、発行者は、`"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"` のような iss 要求によって識別されます。 この例では、要求値のベース アドレスである `https://sts.windows.net` では発行者である Azure Active Directory を識別し、相対アドレス セグメントである _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_ は、トークンの発行対象となった Azure Active Directory テナントの一意識別子になっています。

トークンの対象は、ギャラリー内のアプリケーションのアプリケーション テンプレート ID になります。単一のテナントに登録されている各アプリケーションは、同じ `iss` 要求を SCIM 要求と共に受信する場合があります。 すべてのカスタム アプリのアプリケーション テンプレート ID は _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_ です。 Azure AD プロビジョニング サービスによって生成されたトークンは、テストにのみ使用する必要があります。 運用環境では使用しないでください。

このサンプル コードでは、要求は Microsoft.AspNetCore.Authentication.JwtBearer パッケージを使用して認証されます。 次のコードでは、あらゆるサービスのエンドポイントに対する要求が、指定のテナントに対して Azure Active Directory から発行されたベアラー トークンを使用して認証されるようになります。

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

ベアラートークンは、提供された [Postman テスト](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)を使用し、localhost を使用してローカル デバッグを実行するためにも必要です。 このサンプル コードでは、ASP.NET Core 環境を使用し、開発段階で認証オプションを変更して、自己署名トークンを使用できるようにします。

ASP.NET Core の複数の環境の詳細については、次のリンクを参照してください: [ASP.NET Core で複数の環境を使用する](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

次のコードでは、あらゆるサービスのエンドポイントに対する要求が、カスタム キーを使用して署名されたベアラー トークンを使用して認証されるようになります。

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

GET 要求をトークン コントローラーに送信することで有効なベアラー トークンが取得されます。メソッド _GenerateJSONWebToken_ により、開発用に構成されたパラメーターに一致するトークンが作成されます。

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>ユーザーのプロビジョニングとプロビジョニング解除の処理

***例 1.一致するユーザーをサービスに照会する***

Azure Active Directory は、Azure AD 内のユーザーの mailNickname 属性値に一致する `externalId` 属性値を持つユーザーをサービスに照会します。 クエリは次の例のようなハイパーテキスト転送プロトコル (HTTP) 要求として表現されます。jyoung は Azure Active Directory 内のユーザーの mailNickname 例です。

>[!NOTE]
> これは一例です。 すべてのユーザーに mailNickname 属性があるわけではありません。また、ユーザーが持つ値はディレクトリ内で一意ではない場合もあります。 また、照合に使用される属性 (この場合は `externalId`) は、[Azure AD 属性マッピング](customize-application-attributes.md)で構成可能です。

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

このサンプル コードでは、要求はサービスのプロバイダーの QueryAsync メソッドへの呼び出しに変換されます。 このメソッドのシグネチャを次に示します。 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

`externalId` 属性に特定の値を持つユーザーのサンプル クエリでは、QueryAsync メソッドに渡される引数の値は次のようになります。

* parameters.AlternateFilters.Count:1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator:ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***例 2.ユーザーをプロビジョニングする***

ユーザーの mailNickname 属性値に一致する `externalId` 属性値を持つユーザーを Web サービスに照会したときに、応答でユーザーが返されなかった場合、Azure Active Directory は、Azure Active Directory 内のユーザーに対応するユーザーをプロビジョニングするようにサービスに要求します。  このような要求の例を次に示します。 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

このサンプル コードでは、要求はサービスのプロバイダーの CreateAsync メソッドへの呼び出しに変換されます。 このメソッドのシグネチャを次に示します。 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

ユーザーをプロビジョニングする要求では、resource 引数の値は、Microsoft.SCIM.Schemas ライブラリで定義されている Microsoft.SCIM.Core2EnterpriseUser クラスのインスタンスです。  ユーザーをプロビジョニングする要求が成功すると、メソッドの実装により、Microsoft.SCIM.Core2EnterpriseUser クラスのインスタンスが返され、Identifier プロパティの値に新たにプロビジョニングされたユーザーの一意識別子が設定されると想定されています。  

***例 3.ユーザーの現在の状態を照会する*** 

SCIM によってアクセスされる ID ストアに存在することがわかっているユーザーを更新するために、Azure Active Directory は、次のような要求を使用して、そのユーザーの現在の状態をサービスに要求して処理を続行します。 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

このサンプル コードでは、要求はサービスのプロバイダーの RetrieveAsync メソッドへの呼び出しに変換されます。 このメソッドのシグネチャを次に示します。 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

ユーザーの現在の状態を取得する要求の例では、parameters 引数の値として指定されたオブジェクトのプロパティ値は次のようになります。 
  
* Identifier:"54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***例 4.更新する参照属性の値を照会する*** 

参照属性を更新する場合、Azure Active Directory は、サービスによってアクセスされる ID ストア内の参照属性の現在の値が Azure Active Directory 内のその属性の値と既に一致しているかどうかを判別するために、サービスにクエリを実行します。 ユーザーの場合、この方法で現在の値を照会する属性は、manager 属性のみです。 ユーザー オブジェクトの manager 属性に特定の値があるかどうかを判別する要求の例を次に示します。このサンプル コードでは、要求はサービスのプロバイダーの QueryAsync メソッドへの呼び出しに変換されます。 parameters 引数の値として指定されたオブジェクトのプロパティ値は次のようになります。 
  
* parameters.AlternateFilters.Count:2
* parameters.AlternateFilters.ElementAt(x).AttributePath:"ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator:ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:"54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator:ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue:"2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0):"ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

ここでは、filter クエリ パラメーターの式の順序に応じて、インデックス x の値が 0、インデックス y の値が 1 になるか、または x の値が 1、y の値が 0 になります。   

***例 5.Azure AD から SCIM サービスに対するユーザーの更新を要求する*** 

Azure Active Directory から SCIM サービスに対するユーザーを更新する要求の例を次に示します。 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

このサンプル コードでは、要求はサービスのプロバイダーの UpdateAsync メソッドへの呼び出しに変換されます。 このメソッドのシグネチャを次に示します。 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

ユーザーを更新する要求の例では、patch 引数の値として指定されたオブジェクトのプロパティ値は次のようになります。 
  
* ResourceIdentifier.Identifier:"54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count:1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName:OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count:1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value:2819c223-7f76-453a-919d-413861904646

***例 6.ユーザーのプロビジョニングを解除する***

SCIM サービスによってアクセスされる ID ストアからユーザーのプロビジョニングを解除するために、Azure AD は次のような要求を送信します。

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

このサンプル コードでは、要求はサービスのプロバイダーの DeleteAsync メソッドへの呼び出しに変換されます。 このメソッドのシグネチャを次に示します。 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

ユーザーのプロビジョニングを解除する要求の例では、resourceIdentifier 引数の値として指定されたオブジェクトのプロパティ値は次のようになります。 

* ResourceIdentifier.Identifier:"54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>手順 4:SCIM エンドポイントを Azure AD SCIM クライアントと統合する

Azure AD は、割り当てられたユーザーとグループを、[SCIM 2.0 プロトコル](https://tools.ietf.org/html/rfc7644)の特定のプロファイルを実装したアプリケーションに自動的にプロビジョニングするように構成できます。 プロファイルの詳細は [「手順 2: Azure AD SCIM の実装について理解する](#step-2-understand-the-azure-ad-scim-implementation)」にあります。

これらの要件との互換性に関する記述については、アプリケーション プロバイダー、またはアプリケーション プロバイダーのドキュメントを確認してください。

> [!IMPORTANT]
> Azure AD SCIM 実装は、Azure AD とターゲット アプリケーション間でユーザーを常に同期するように設計された Azure AD ユーザー プロビジョニング サービスの上に構築され、固有の標準操作セットを実装しています。 Azure AD SCIM クライアントの動作を理解するには、これらの動作を把握しておくことが重要です。 詳しくは、「[プロビジョニング サイクル: 初回と増分](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)」を参照してください。これは「[プロビジョニングのしくみ](how-provisioning-works.md)」に含まれるセクションです。

### <a name="getting-started"></a>作業の開始

この記事で説明した SCIM プロファイルをサポートするアプリケーションは、Azure AD アプリケーション ギャラリーの "ギャラリー以外のアプリケーション" 機能を使用して Azure Active Directory に接続できます。 接続が完了すると、Azure AD は 40 分ごとに同期処理を実行します。この処理では、割り当て済みのユーザーとグループについてアプリケーションの SCIM エンドポイントに照会し、割り当ての詳細に従ってユーザーとグループを作成または変更します。

**SCIM をサポートするアプリケーションを接続するには:**

1. [Azure Active Directory ポータル](https://aad.portal.azure.com)にサインインします。 [開発者プログラム](https://developer.microsoft.com/office/dev-program)にサインアップすることにより、P2 ライセンスで Azure Active Directory の無料試用版にアクセスできることに注意してください
2. 左側のウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。 ギャラリーから追加されたアプリを含む、構成済みのすべてのアプリの一覧が表示されます。
3. **[+ 新しいアプリケーション]**  >  **[すべて]**  >  **[ギャラリー以外のアプリケーション]** の順に選択します。
4. アプリケーションの名前を入力し、 **[追加]** を選択してアプリ オブジェクトを作成します。 新しいアプリがエンタープライズ アプリケーションの一覧に追加され、そのアプリの管理画面が開きます。

   ![Azure AD アプリケーション ギャラリーを示すスクリーンショット](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD アプリケーション ギャラリー*

5. アプリの管理画面で、左側のパネルにある **[プロビジョニング]** を選択します。
6. **[プロビジョニング モード]** メニューの **[自動]** を選択します。

   ![例:Azure portal のアプリのプロビジョニング ページ](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Azure portal でのプロビジョニングの構成*

7. **[テナント URL]** フィールドに、アプリケーションの SCIM エンドポイントの URL を入力します。 例: `https://api.contoso.com/scim/`
8. SCIM エンドポイントで、Azure AD 以外の発行者からの OAuth ベアラー トークンを必要とする場合は、必要な OAuth ベアラー トークンをオプションの **[シークレット トークン]** フィールドにコピーします。 このフィールドを空白のままにすると、Azure AD では各要求に Azure AD を発行元とする OAuth ベアラー トークンを含めます。 ID プロバイダーとして Azure AD を使用するアプリは、この Azure AD によって発行されたトークンを検証できます。 
   > [!NOTE]
   > このフィールドを空白のままにして、Azure AD によって生成されるトークンに依存することは推奨 "***されません***"。 このオプションは、主にテスト目的で使用できます。
9. **[テスト接続]** を選択して、Azure Active Directory による SCIM エンドポイントへの接続を試みます。 試行に失敗した場合は、エラー情報が表示されます。  

    > [!NOTE]
    > **テスト接続**では、Azure AD 構成で照合プロパティとして選択されたランダムな GUID を使用して、存在しないユーザー用の SCIM エンドポイントのクエリが実行されます。 想定される適切な応答は、HTTP 200 OK と空の SCIM ListResponse メッセージです。

10. アプリケーションへの接続の試行に成功した場合は、 **[保存]** を選択して管理者資格情報を保存します。
11. **[マッピング]** セクションには、選択可能な 2 つの[属性マッピング](customize-application-attributes.md)のセットがあります。片方はユーザー オブジェクト用であり、他方はグループ オブジェクト用です。 Azure Active Directory からアプリに同期されている属性を確認するには、それぞれを選択します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理でアプリ内のユーザーとアカウントを照合するために使用されます。 すべての変更をコミットするには、 **[保存]** を選択します。

    > [!NOTE]
    > 必要に応じて [グループ] マッピングを無効にすることで、グループ オブジェクトの同期を無効にできます。

12. **[設定]** の **[スコープ]** フィールドは、どのユーザーおよびグループが同期されるかを定義します。 **[割り当てられたユーザーとグループのみを同期する]** (推奨) を選択すると、 **[ユーザーとグループ]** タブに割り当てられたユーザーとグループのみが同期されます。
13. 構成が完了したら、 **[プロビジョニング状態]** を **[オン]** に設定します。
14. Azure AD のプロビジョニング サービスを開始するには、 **[保存]** を選択します。
15. 割り当てられたユーザーとグループのみを同期する (推奨) 場合は、 **[ユーザーとグループ]** タブを選択し、同期するユーザーとグループを割り当てます。

初期サイクルが開始されたら、左側のパネルにある **[プロビジョニング ログ]** を選択して進行状況を監視できます。これにより、プロビジョニング サービスによって実行されたすべてのアクションがアプリで表示されます。 Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](check-status-user-account-provisioning.md)」をご覧ください。

> [!NOTE]
> 初期サイクルは、サービスが実行されている限り約 40 分ごとに実行される以降の同期よりも、実行に時間がかかります。

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>手順 5:Azure AD アプリケーション ギャラリーにアプリケーションを発行する

複数のテナントによって使用されるアプリケーションを作成する場合は、Azure AD アプリケーション ギャラリーで使用可能にできます。 これにより、組織でアプリケーションを見つけて、プロビジョニングを構成することが簡単になります。 簡単に、Azure AD ギャラリーにアプリを発行し、他のユーザーがプロビジョニングできるようにすることができます。 手順は、 [こちら](../azuread-dev/howto-app-gallery-listing.md)で確認してください。 Microsoft はお客様と協力して、お客様のアプリケーションをギャラリーに統合し、エンドポイントをテストし、顧客が使用できるオンボード [ドキュメント](../saas-apps/tutorial-list.md)をリリースします。 

### <a name="gallery-onboarding-checklist"></a>ギャラリーのオンボードのチェックリスト
アプリケーションの迅速なオンボードと顧客のスムーズなデプロイ エクスペリエンスを確実なものとするために、以下のチェックリストに従ってください。 ギャラリーにオンボードする際に、ご自身から情報が収集されます。 
> [!div class="checklist"]
> * [SCIM 2.0 ](#step-2-understand-the-azure-ad-scim-implementation) のユーザーおよびグループ エンドポイントをサポートする (必要なのは 1 つだけですが両方を推奨)
> * テナントごとに少なくとも 1 秒あたり 25 個の要求をサポートする (必須)
> * 顧客のギャラリー オンボード後のガイドを行うエンジニアリングとサポートの連絡先を確立する (必須)
> * アプリケーションの 3 つの無期限のテスト資格情報 (必須)
> * 次に示すように、OAuth 承認コードの付与または有効期間が長いトークンをサポートする (必須)
> * 顧客のギャラリー オンボード後のサポートを行うエンジニアリングとサポートの連絡先を確立する (必須)
> * 1 つの PATCH で複数のグループ メンバーシップの更新をサポートする (推奨) 
> * SCIM エンドポイントを公開文書化する (推奨) 
> * [スキーマ検出をサポートする](https://tools.ietf.org/html/rfc7643#section-6) (推奨)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>アプリケーション ギャラリーでのプロビジョニング コネクタの承認
SCIM 仕様では、SCIM 固有の認証と承認のスキームは定義されていません。 既存の業界標準の使用に依存しています。 Azure AD プロビジョニング クライアントは、ギャラリー内のアプリケーションに対して 2 つの承認方法をサポートしています。 

|承認方法|長所|短所|サポート|
|--|--|--|--|
|ユーザー名とパスワード (推奨されないか、Azure AD でサポートされていません)|簡単に実装できます|セキュリティで保護されていません - 「[パスワードは重要ではない](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)」を参照|ギャラリー アプリに対してケース バイ ケースでサポートされます。 ギャラリー以外のアプリではサポートされていません。|
|有効期間が長いベアラー トークン|有効期間が長いトークンでは、ユーザーが存在している必要はありません。 プロビジョニングを設定する場合、管理者はこれらを簡単に使用できます。|有効期間が長いトークンは、メールのようにセキュリティで保護されていない方法を使用しないと、管理者と共有するのが困難です。 |ギャラリー アプリとギャラリー以外のアプリでサポートされます。 |
|OAuth 認証コードの付与|アクセス トークンの有効期間はパスワードよりもはるかに短く、有効期間が長いベアラー トークンにはない自動更新メカニズムがあります。  初期承認中に実際のユーザーが存在する必要があり、アカウンタビリティのレベルを追加します。 |ユーザーが存在している必要があります。 ユーザーが組織からいなくなる場合、トークンは無効になり、承認を再度完了する必要があります。|ギャラリー アプリでサポートされていますが、ギャラリー以外のアプリではサポートされていません。 ギャラリー以外のサポートはバックログにあります。|
|OAuth クライアント資格情報の付与|アクセス トークンの有効期間はパスワードよりもはるかに短く、有効期間が長いベアラー トークンにはない自動更新メカニズムがあります。 承認コードの付与とクライアント資格情報の付与の両方で、同じ種類のアクセス トークンが作成されるため、これらの方法間の移動は API に対して透過的です。  プロビジョニングは完全に自動化することができ、ユーザーの介入なしに新しいトークンを自動的に要求することができます。 ||ギャラリー アプリとギャラリー以外のアプリでサポートされていません。 サポートはバックログに入っています。|

> [!NOTE]
> Azure AD プロビジョニング構成のカスタム アプリ UI では、トークン フィールドを空白のままにすることはお勧めしません。 生成されたトークンは、主にテスト目的で使用できます。

**OAuth 承認コード付与フロー:** プロビジョニング サービスでは、[承認コードの付与](https://tools.ietf.org/html/rfc6749#page-24)がサポートされています。 ギャラリーにアプリを発行するための要求を送信した後、私たちのチームは次の情報を収集するためにお客様と協力します。
*  承認 URL:ユーザー エージェント リダイレクトによってリソース所有者から承認を取得するためのクライアントによる URL。 ユーザーは、アクセスを承認するためにこの URL にリダイレクトされます。 現在、この URL はテナントごとに構成できないことに注意してください。
*  トークン交換 URL:アクセス トークンの承認付与を交換するためのクライアントによる URL。通常は、クライアント認証を使用します。 現在、この URL はテナントごとに構成できないことに注意してください。
*  クライアント ID: 承認サーバーは、登録されたクライアントにクライアント識別子 (クライアントによって提供される登録情報を表す一意の文字列) を発行します。  クライアント識別子はシークレットではありません。これはリソースの所有者に公開されており、クライアント認証に単独で使用**できません**。  
*  クライアント シークレット:クライアント シークレットは、承認サーバーによって生成されるシークレットです。 これは承認サーバーにのみ認識される一意の値である必要があります。 

OAuth v1 は、クライアント シークレットの露出が原因で、サポートされていないことに注意してください。 OAuth v2 はサポートされています。  

ベスト プラクティス (推奨されますが必須ではありません):
* 複数のリダイレクト URL をサポートします。 管理者は、"portal.azure.com" と "aad.portal.azure.com" の両方からプロビジョニングを構成できます。 複数のリダイレクト URL をサポートすることで、ユーザーがいずれのポータルからでもアクセスを承認できるようになります。
* 複数のシークレットをサポートして、ダウンタイムなしでスムーズにシークレットを更新できるようにします。 

**有効期間が長い OAuth ベアラー トークン:** アプリケーションで OAuth 認証コード付与フローがサポートされていない場合は、管理者がプロビジョニング統合のセットアップに使用できる、有効期間が長い OAuth ベアラー トークンを生成することもできます。 トークンは永続的である必要があり、そうでない場合、トークンの有効期限が切れるとプロビジョニング ジョブが[検疫](application-provisioning-quarantine-status.md)されます。 このトークンのサイズは 1 KB 未満である必要があります。  

追加の認証および承認方法については、[UserVoice](https://aka.ms/appprovisioningfeaturerequest) にご連絡ください。

### <a name="gallery-go-to-market-launch-check-list"></a>ギャラリーの Go-To-Market Launch チェックリスト
共同統合の認識と需要を促進するために、既存のドキュメントを更新し、お使いのマーケティング チャネルにおける統合を拡充することをお勧めします。  次に示すのは、立ち上げをサポートするために実行するようお勧めする一連のチェックリスト アクティビティです。

* **販売とカスタマー サポートの準備。** 販売とサポートのチームが統合の機能を認識し、それについて説明できるようにします。 販売とサポートのチームに概要を伝え、FAQ を提供し、統合を販売資料に含めます。 
* **ブログ投稿やプレス リリース。** 共同統合、利点、および開始方法について説明するブログ投稿やプレス リリースを作成します。 [例:Imprivata と Azure Active Directory のプレス リリース](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **ソーシャル メディア。** Twitter、Facebook、LinkedIn などのソーシャル メディアを活用して、顧客に統合を宣伝します。 投稿をリツイートできるように、必ず @AzureAD を含めるようにしてください。 [例:Imprivata の Twitter 投稿](https://twitter.com/azuread/status/1123964502909779968)
* **マーケティング Web サイト。** 共同統合が利用できるようになったことを含めるように、マーケティングのページ (統合のページ、パートナーのページ、価格のページなど) を作成または更新します。 [例:Pingboard の統合のページ](https://pingboard.com/org-chart-for)、[Smartsheet の統合のページ](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad)、[Monday.com の価格ページ](https://monday.com/pricing/) 
* **テクニカル ドキュメント。** 顧客がどのように開始できるかに関するヘルプ センターの記事またはテクニカル ドキュメントを作成します。 [例:Envoy と Microsoft Azure Active Directory の統合。](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **顧客とのコミュニケーション。** 顧客とのコミュニケーション (月次のニュースレター、メールによるキャンペーン、製品のリリース ノート) を通じて、新しい統合を顧客に通知します。 

## <a name="related-articles"></a>関連記事

* [SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](user-provisioning.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](customize-application-attributes.md)
* [属性マッピングの式の書き方](functions-for-customizing-application-data.md)
* [ユーザー プロビジョニング用のフィルターのスコープ](define-conditional-rules-for-provisioning-user-accounts.md)
* [アカウント プロビジョニング通知](user-provisioning.md)
* [SaaS アプリを統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)