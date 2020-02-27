---
title: Azure AD からアプリにユーザーをプロビジョニングするための SCIM エンドポイントを開発する
description: クロスドメイン ID 管理システム (SCIM) では、自動ユーザー プロビジョニングが標準化されます。 SCIM エンドポイントを開発し、SCIM API を Azure Active Directory と統合して、クラウド アプリケーションへのユーザーとグループのプロビジョニングの自動化を開始する方法について学習します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a44cf9aa5b3287a01617be6439cd04b9a5caa73
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484232"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) を利用し、SCIM エンドポイントを構築し、ユーザー プロビジョニングを構成する

アプリケーション開発者は System for Cross-Domain Identity Management (SCIM) ユーザー管理 API を使用し、アプリケーションと Azure AD の間のユーザーとグループの自動プロビジョニングを有効にできます。 この記事では、SCIM エンドポイントを構築し、Azure AD プロビジョニング サービスと統合する方法について説明します。 SCIM 仕様では、プロビジョニングのための共通のユーザー スキーマが提供されます。 SAML や OpenID Connect などのフェデレーション標準と組み合わせて使用した場合、SCIM では エンドツーエンドの標準ベースのアクセス管理用ソリューションが管理者に提供されます。

SCIM は、/Users エンドポイントと /Groups エンドポイントという 2 つのエンドポイントの標準化された定義です。 これはオブジェクトの作成、更新、削除を行うための共通の REST 動詞を使用するほか、グループ名、ユーザー名、名、姓、電子メールなどの共通属性に対して定義済みのスキーマを使用します。 SCIM 2.0 REST API を提供するアプリでは、独自のユーザー管理 API を使用する煩わしさを軽減するか、なくすことができます。 たとえば、準拠している SCIM クライアントは、JSON オブジェクトの HTTP POST を /Users エンドポイントに送信して新しいユーザー エントリを作成する方法を認識しています。 同じ基本的なアクションに対して若干異なる API を必要とするのではなく、SCIM 標準に準拠しているアプリでは、既存のクライアント、ツール、およびコードをすぐに利用できます。 

![SCIM を使用した Azure AD からアプリへのプロビジョニング](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642)、[7643](https://tools.ietf.org/html/rfc7643)、[7644](https://tools.ietf.org/html/rfc7644)) で定義されている管理用の標準ユーザー オブジェクト スキーマと REST API を使用すると、ID プロバイダーとアプリをより簡単に相互に統合できます。 SCIM エンドポイントを構築するアプリケーション開発者は、カスタム作業を行わなくても、SCIM 準拠の任意のクライアントと統合できます。

アプリケーションへのプロビジョニングを自動化するには、SCIM エンドポイントを構築し、Azure AD SCIM に準拠するように統合する必要があります。 アプリケーションへのユーザーとグループのプロビジョニングを開始するには、次の手順を実行します。 
    
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

上記で定義されたスキーマは、以下の Json ペイロードを使用して表されます。 アプリケーションに必要な属性に加えて、JSON の表現には、必要な "id"、"externalId"、"meta" の各属性が含まれていることに注意してください。

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
| 姓 |name.familyName |
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
|/Schemas|各クライアントおよびサービス プロバイダーによってサポートされている属性のセットは、異なる場合があります。 あるサービス プロバイダーには "name"、"title"、"emails" が含まれるのに対して、別のサービス プロバイダーでは "name"、"title"、"phoneNumbers" が使用されている、といったことがあります。 スキーマ エンドポイントにより、サポートされている属性を検出できます。|
|/Bulk|一括操作を使用すると、1 回の操作でリソース オブジェクトの大きなコレクションに対する操作を実行できます (たとえば、大きなグループのメンバーシップの更新)。|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>手順 2:Azure AD SCIM の実装について
> [!IMPORTANT]
> Azure AD SCIM の実装の動作は、2018 年 12 月 18 日に最終更新が行われました。 変更点については、[Azure AD ユーザー プロビジョニング サービスの SCIM 2.0 プロトコルへのコンプライアンス](application-provisioning-config-problem-scim-compatibility.md)に関する記事をご覧ください。

お客様が SCIM 2.0 ユーザー管理 API がサポートされるアプリケーションを構築している場合、このセクションには、Azure AD SCIM クライアントの実装方法が詳細に記載されています。 また、SCIM プロトコル要求の処理と応答のモデル化方法も示されています。 SCIM エンドポイントを実装した後は、前のセクションに説明されている手順に従ってテストすることができます。

[SCIM 2.0 プロトコル仕様](http://www.simplecloud.info/#Specification)の中で、アプリケーションは次の要件を満たす必要があります。

* [SCIM プロトコルのセクション 3.3](https://tools.ietf.org/html/rfc7644#section-3.3) に従って、ユーザー、グループ (オプション) の作成をサポートする。  
* [SCIM プロトコルのセクション 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2) に従って、PATCH 要求によるユーザーまたはグループの変更をサポートする。  
* [SCIM プロトコルのセクション 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1) に従って、これまで作成したユーザーまたはグループの既知のリソースの取得をサポートする。  
* [SCIM プロトコルのセクション 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2) に従って、ユーザーまたはグループのクエリをサポートする。  既定では、ユーザーの取得には `id`、ユーザーのクエリには `username` と `externalid`、グループのクエリには `displayName` が使用されます。  
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

###### <a name="request-1"></a>要求
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>応答 (ユーザー検出)
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

##### <a name="request-2"></a>要求

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response-2"></a>応答

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

##### <a name="request-3"></a>要求

*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response-3"></a>応答

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

##### <a name="request-4"></a>要求

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

##### <a name="response-4"></a>応答

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

##### <a name="request-5"></a>要求

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

##### <a name="response-5"></a>応答

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

##### <a name="request-14"></a>要求

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

##### <a name="response-14"></a>応答

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

##### <a name="request-6"></a>要求

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>応答

*HTTP/1.1 204 No Content*

### <a name="group-operations"></a>グループ操作

* グループは、常に空のメンバーのリストで作成されます。
* グループは `displayName` 属性でクエリすることができます。
* グループ PATCH 要求を更新すると、応答に *HTTP 204 No Content* が含まれることになります。 全メンバーのリストを含めて本文を返すことはお勧めできません。
* グループの全メンバーを返すことをサポートする必要はありません。

#### <a name="create-group"></a>グループの作成

##### <a name="request-7"></a>要求

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

##### <a name="response-7"></a>応答

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

##### <a name="request-8"></a>要求

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response-8"></a>応答
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

##### <a name="request-9"></a>要求
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response-9"></a>応答

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

##### <a name="request-10"></a>要求

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

##### <a name="response-10"></a>応答

*HTTP/1.1 204 No Content*

### <a name="update-group-add-members"></a>グループの更新 [メンバーの追加]

##### <a name="request-11"></a>要求

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

##### <a name="response-11"></a>応答

*HTTP/1.1 204 No Content*

#### <a name="update-group-remove-members"></a>グループの更新 [メンバーの削除]

##### <a name="request-12"></a>要求

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

##### <a name="response-12"></a>応答

*HTTP/1.1 204 No Content*

#### <a name="delete-group"></a>グループの削除

##### <a name="request-13"></a>要求

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>応答

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


## <a name="step-3-build-a-scim-endpoint"></a>手順 3:SCIM エンドポイントを構築する

Azure Active Directory を接続して動作させる SCIM Web サービスを作成することにより、ほとんどすべてのアプリケーションまたは ID ストアの自動ユーザー プロビジョニングを実現することができます。

しくみは次のとおりです。

1. Azure AD では Microsoft.SystemForCrossDomainIdentityManagement という共通言語基盤 (CLI) ライブラリが、以下のサンプル コードと共に提供されています。 システム インテグレーターや開発者は、このライブラリを使用して、Azure AD を任意のアプリケーションの ID ストアに接続できる SCIM ベースの Web サービス エンドポイントを作成してデプロイできます。
2. 標準化されたユーザー スキーマをアプリケーションで必要なユーザー スキーマやプロトコルに対応付けるためのマッピングが Web サービスに実装されます。 
3. エンドポイント URL が、Azure AD のアプリケーション ギャラリーにカスタム アプリケーションの一部として登録されます。
4. ユーザーとグループが、Azure AD 内でこのアプリケーションに割り当てられます。 割り当ての際には、ターゲット アプリケーションと同期するためのキューに配置されます。 キューを処理する同期プロセスは、40 分ごとに実行されます。

### <a name="code-samples"></a>コード サンプル

このプロセスをわかりやすくするために、SCIM Web サービス エンドポイントを作成して自動プロビジョニングを例示する[コード サンプル](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)を用意しました。 サンプルは、ユーザーとグループを表すコンマ区切り値の行が含まれたファイルを保持するプロバイダーです。

**前提条件**

* Visual Studio 2013 以降
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/)
* SCIM エンドポイントとして使用される ASP.NET framework 4.5 をサポートしている Windows コンピューター。 このマシンにクラウドからアクセスできる必要があります。
* [無料試用版またはライセンス版の Azure AD Premium が付随した Azure サブスクリプション](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>作業の開始

Azure AD からのプロビジョニング要求を受信できる SCIM エンドポイントを実装する最も簡単な方法は、プロビジョニングするユーザーをコンマ区切り値 (CSV) ファイルに出力するコード サンプルをビルドしてデプロイすることです。

#### <a name="to-create-a-sample-scim-endpoint"></a>サンプルの SCIM エンドポイントを作成するには

1. [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) で、コード サンプル パッケージをダウンロードします。
1. パッケージを解凍し、Windows コンピューターの C:\AzureAD-BYOA-Provisioning-Samples\ などの場所に配置します。
1. このフォルダーで、Visual Studio の FileProvisioning\Host\FileProvisioningService.csproj プロジェクトを起動します。
1. **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択し、FileProvisioningService プロジェクトに対して以下のコマンドを実行して、ソリューションの参照を解決します。

   ```powershell
    Update-Package -Reinstall
   ```

1. FileProvisioningService プロジェクトをビルドします。
1. Windows のコマンド プロンプト アプリケーションを (管理者として) 起動し、**cd** コマンドを使用してディレクトリを **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug** フォルダーに変更します。
1. 次のコマンドを実行します。その際、`<ip-address>` を Windows コンピューターの IP アドレスまたはドメイン名に置き換えます。

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. Windows の **[Windows の設定]**  >  **[ネットワークとインターネット] 設定**で、 **[Windows ファイアウォール]**  >  **[詳細設定]** の順に選択し、ポート 9000 への入力方向のアクセスを許可する **[受信規則]** を作成します。
1. Windows コンピューターがルーターの内側にある場合は、インターネットに公開されているポート 9000 と Windows コンピューター上のポート 9000 の間でネットワーク アクセス変換を実行するようにルーターを構成する必要があります。 この構成は、Azure AD からクラウド上にあるこのエンドポイントにアクセスするために必要です。

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Azure AD にサンプルの SCIM エンドポイントを登録するには

1. [Azure Active Directory ポータル](https://aad.portal.azure.com)にサインインします。 
1. 左側のウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。 ギャラリーから追加されたアプリを含む、構成済みのすべてのアプリの一覧が表示されます。
1. **[+ 新しいアプリケーション]**  >  **[すべて]**  >  **[ギャラリー以外のアプリケーション]** の順に選択します。
1. アプリケーションの名前を入力し、 **[追加]** を選択してアプリ オブジェクトを作成します。 作成されるアプリケーション オブジェクトは、単なる SCIM エンドポイントではなく、シングル サインオンのプロビジョニングと実装の対象であるアプリケーションを表しています。
1. アプリの管理画面で、左側のパネルにある **[プロビジョニング]** を選択します。
1. **[プロビジョニング モード]** メニューの **[自動]** を選択します。    
1. **[テナント URL]** フィールドに、アプリケーションの SCIM エンドポイントの URL を入力します。 例: https://api.contoso.com/scim/

1. SCIM エンドポイントで、Azure AD 以外の発行者からの OAuth ベアラー トークンを必要とする場合は、必要な OAuth ベアラー トークンをオプションの **[シークレット トークン]** フィールドにコピーします。 このフィールドを空白のままにすると、Azure AD では各要求に Azure AD を発行元とする OAuth ベアラー トークンを含めます。 ID プロバイダーとして Azure AD を使用するアプリは、この Azure AD によって発行されたトークンを検証できます。
1. **[テスト接続]** を選択して、Azure Active Directory による SCIM エンドポイントへの接続を試みます。 試行に失敗した場合は、エラー情報が表示されます。  

    > [!NOTE]
    > **テスト接続**では、Azure AD 構成で照合プロパティとして選択されたランダムな GUID を使用して、存在しないユーザー用の SCIM エンドポイントのクエリが実行されます。 想定される適切な応答は、HTTP 200 OK と空の SCIM ListResponse メッセージです。

1. アプリケーションへの接続の試行に成功した場合は、 **[保存]** を選択して管理者資格情報を保存します。
1. **[マッピング]** セクションには、選択可能な 2 つの属性マッピングのセットがあります。片方はユーザー オブジェクト用であり、他方はグループ オブジェクト用です。 Azure Active Directory からアプリに同期されている属性を確認するには、それぞれを選択します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理でアプリ内のユーザーとアカウントを照合するために使用されます。 すべての変更をコミットするには、 **[保存]** を選択します。
1. **[設定]** の **[スコープ]** フィールドは、同期するユーザーまたはグループを定義します。 **[割り当てられたユーザーとグループのみを同期する]** (推奨) を選択すると、 **[ユーザーとグループ]** タブに割り当てられたユーザーとグループのみが同期されます。
1. 構成が完了したら、 **[プロビジョニング状態]** を **[オン]** に設定します。
1. Azure AD のプロビジョニング サービスを開始するには、 **[保存]** を選択します。
1. 割り当てられたユーザーとグループのみを同期する (推奨) 場合は、 **[ユーザーとグループ]** タブを選択し、同期するユーザーとグループを割り当てます。

初期サイクルが開始されたら、左側のパネルにある **[監査ログ]** を選択して進行状況を監視できます。これにより、プロビジョニング サービスによって実行されたすべてのアクションがアプリで表示されます。 Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](check-status-user-account-provisioning.md)」をご覧ください。

サンプルを確認する最後の手順として、Windows コンピューターの \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug フォルダーにある TargetFile.csv ファイルを開きます。 プロビジョニング プロセスが完了すると、割り当てられ、プロビジョニングされたすべてのユーザーとグループに関する詳細がこのファイルに表示されます。

### <a name="development-libraries"></a>開発ライブラリ

SCIM 仕様に準拠する独自の Web サービスを開発するにあたっては、開発プロセスの時間を短縮できるように、まずは Microsoft が提供する以下のライブラリについて理解してください。

* 共通言語基盤 (CLI) ライブラリは、C# など、この基盤をベースにした言語で使用できます。 これらのライブラリの 1 つ Microsoft.SystemForCrossDomainIdentityManagement.Service では、次の図に示すように、Microsoft.SystemForCrossDomainIdentityManagement.IProvider インターフェイスが宣言されています。 開発者は、このライブラリを使用して、プロバイダーと総称されるクラスにこのインターフェイスを実装できます。 開発者は、ライブラリを使用して、SCIM 仕様に準拠する Web サービスをデプロイできます。 Web サービスは、インターネット インフォメーション サービス内でホストすることも、実行可能な CLI アセンブリ内でホストすることもできます。 要求は、プロバイダーのメソッドの呼び出しに変換されます。開発者は、これを任意の ID ストアに作用するようにプログラムできます。
  
   ![内訳:プロバイダーのメソッドへの呼び出しに変換された要求](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)
  
* [Express ルート ハンドラー](https://expressjs.com/guide/routing.html)は、node.js Web サービスに対する (SCIM 仕様で定義された) 呼び出しを表す node.js 要求オブジェクトの解析に使用できます。

### <a name="building-a-custom-scim-endpoint"></a>カスタム SCIM エンドポイントの構築

開発者は CLI ライブラリを使用して、CLI の実行可能アセンブリ内、またはインターネット インフォメーション サービス内でサービスをホストできます。 アドレス http://localhost:9000: にある実行可能アセンブリ内にサービスをホストするためのサンプル コードを次に示します。 

```csharp
 private static void Main(string[] arguments)
 {
 // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
 // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

 Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
   new DevelopersMonitor();
 Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
   new DevelopersProvider(arguments[1]);
 Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
 try
 {
     webService = new WebService(monitor, provider);
     webService.Start("http://localhost:9000");

     Console.ReadKey(true);
 }
 finally
 {
     if (webService != null)
     {
         webService.Dispose();
         webService = null;
     }
 }
 }

 public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
 {
 private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
 private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

 public WebService(
   Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
   Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
 {
     this.monitor = monitoringBehavior;
     this.provider = providerBehavior;
 }

 public override IMonitor MonitoringBehavior
 {
     get
     {
         return this.monitor;
     }

     set
     {
         this.monitor = value;
     }
 }

 public override IProvider ProviderBehavior
 {
     get
     {
         return this.provider;
     }

     set
     {
         this.provider = value;
     }
 }
 }
```

このサービスには、HTTP アドレスと、ルート証明機関が次のいずれかの名前であるサーバー認証証明書が設定されている必要があります。 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

サーバー認証証明書は、ネットワーク シェル ユーティリティを使用して Windows ホスト上のポートにバインドできます。

```
netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}
```

ここで、certhash 引数に指定されている値は証明書の拇印で、appid 引数に指定されている値は任意のグローバル一意識別子です。  

インターネット インフォメーション サービス内でサービスをホストする場合は、アセンブリの既定の名前空間に、Startup という名前のクラスを使用して、CLI コード ライブラリ アセンブリをビルドします。  以下に示すのは、このクラスのサンプルです。 

```csharp
 public class Startup
 {
 // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
 // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
 // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

 Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

 public Startup()
 {
     Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
       new DevelopersMonitor();
     Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
       new DevelopersProvider();
     this.starter = 
       new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
         provider, 
         monitor);
 }

 public void Configuration(
   Owin.IAppBuilder builder) // Defined in Owin.dll.  
 {
     this.starter.ConfigureApplication(builder);
 }
 }
```

### <a name="handling-endpoint-authentication"></a>エンドポイント認証の処理

Azure Active Directory からの要求には、OAuth 2.0 のベアラー トークンが含まれます。   要求を受信するサービスでは、Microsoft Graph API サービスにアクセスするために、発行者が本来の Azure Active Directory テナントに対応する Azure Active Directory であることを認証する必要があります。  トークンでは、発行者は、"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/ " のような iss 要求によって識別されます。  この例では、要求値のベース アドレスである https://sts.windows.net では発行者である Azure Active Directory を識別し、相対アドレス セグメントである cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 は、トークンの発行対象となった Azure Active Directory テナントの一意識別子になっています。 トークンの対象は、ギャラリー内のアプリのアプリケーション テンプレート ID になります。 すべてのカスタム アプリのアプリケーション テンプレート ID は 8adf8e6e-67b2-4cf2-a259-e3dc5476c621 です。 ギャラリー内の各アプリに使用されるアプリケーション テンプレート ID は一様ではありません。 ギャラリー アプリケーションのアプリケーション テンプレート ID に関する質問は、ProvisioningFeedback@microsoft.com にお問い合わせください。 単一のテナントに登録されている各アプリケーションは、同じ `iss` 要求を SCIM 要求と共に受信する場合があります。

Microsoft が提供する CLI ライブラリを使用して SCIM サービスを作成する場合、開発者は、次の手順に従って、Microsoft.Owin.Security.ActiveDirectory パッケージを使用して、Azure Active Directory からの要求を認証できます。 

まず、プロバイダーに、Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior プロパティを実装します。そのために、サービスが開始されるたびに呼び出されるメソッドを返すように設定します。 

```csharp
  public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
  {
    get
    {
      return this.OnServiceStartup;
    }
  }

  private void OnServiceStartup(
    Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
    System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
  {
  }
```

次に、そのメソッドに次のコードを追加して、Microsoft Graph API サービスにアクセスするために、指定のテナントに対して Azure Active Directory から発行されたトークンを所有しているとして、サービスのエンドポイントに対するすべての要求を認証します。 

```csharp
  private void OnServiceStartup(
    Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
    System.Web.Http.HttpConfiguration HttpConfiguration configuration)
  {
    // IFilter is defined in System.Web.Http.dll.  
    System.Web.Http.Filters.IFilter authorizationFilter = 
      new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

    // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
    // System.IdentityModel.Token.Jwt.dll.
    SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
      new TokenValidationParameters()
      {
        ValidAudience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621"
      };

    // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
    // Microsoft.Owin.Security.ActiveDirectory.dll
    Microsoft.Owin.Security.ActiveDirectory.
    WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
      new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
      TokenValidationParameters = tokenValidationParameters,
      Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                    // identifier for this one.  
    };

    applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
  }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>ユーザーのプロビジョニングとプロビジョニング解除の処理

***例 1.一致するユーザーをサービスに照会する***

Azure Active Directory は、Azure AD 内のユーザーの mailNickname 属性値に一致する externalId 属性値を持つユーザーをサービスに照会します。 クエリは次の例のようなハイパーテキスト転送プロトコル (HTTP) 要求として表現されます。jyoung は Azure Active Directory 内のユーザーの mailNickname 例です。

>[!NOTE]
> これは一例です。 すべてのユーザーに mailNickname 属性があるわけではありません。また、ユーザーが持つ値はディレクトリ内で一意ではない場合もあります。 また、照合に使用される属性 (この場合は externalId) は、[Azure AD 属性マッピング](customize-application-attributes.md)で構成可能です。

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

サービスの作成時に SCIM サービスの実装用に Microsoft が提供する CLI ライブラリを使用した場合、要求はサービスのプロバイダーの Query メソッドの呼び出しに変換されます。  このメソッドのシグネチャを次に示します。 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
 // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
   Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
   string correlationIdentifier);
```

次に示すのは、Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters インターフェイスの定義です。 

```csharp
 public interface IQueryParameters: 
   Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
 {
     System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
     { get; }
 }

 public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
 {
   system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
   { get; }
   System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
   { get; }
   string SchemaIdentifier 
   { get; }
 }
```

```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
```

サービスの作成時に SCIM サービスの実装用に Microsoft が提供する共通言語基盤ライブラリを使用した場合、要求はサービスのプロバイダーの Query メソッドの呼び出しに変換されます。  このメソッドのシグネチャを次に示します。 

```csharp
  // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
  // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
  // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

  System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
    Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
    string correlationIdentifier);
```

次に示すのは、Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters インターフェイスの定義です。 

```csharp
  public interface IQueryParameters: 
    Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
  {
      System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
      { get; }
  }

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
  {
    system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
    { get; }
    System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
    { get; }
    string SchemaIdentifier 
    { get; }
  }

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
  {
      Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
        { get; set; }
      string AttributePath 
        { get; } 
      Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
        { get; }
      string ComparisonValue 
        { get; }
  }

  public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
  {
      Equals
  }
```

externalId 属性に特定の値を持つユーザーを照会する次の例では、Query メソッドに渡される引数の値は次のようになります。 
* parameters.AlternateFilters.Count:1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator:ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
* correlationIdentifier:System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

***例 2.ユーザーをプロビジョニングする***

ユーザーの mailNickname 属性値に一致する externalId 属性値を持つユーザーを Web サービスに照会したときに、応答でユーザーが返されなかった場合、Azure Active Directory は、Azure Active Directory 内のユーザーに対応するユーザーをプロビジョニングするようにサービスに要求します。  このような要求の例を次に示します。 

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

その要求は、SCIM サービスを実装するために Microsoft が提供する CLI ライブラリによって、サービスのプロバイダーの Create メソッドの呼び出しに変換されます。  Create メソッドには、次のようなシグネチャがあります。

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
   Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
   string correlationIdentifier);
```

ユーザーをプロビジョニングする要求では、resource 引数の値は、Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser クラスのインスタンスになります。これは Microsoft.SystemForCrossDomainIdentityManagement.Schemas ライブラリに定義されています。  ユーザーをプロビジョニングする要求が成功すると、メソッドの実装は、Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser クラスのインスタンスを返し、Identifier プロパティの値に新たにプロビジョニングされたユーザーの一意識別子を設定すると想定されています。  

***例 3.ユーザーの現在の状態を照会する*** 

SCIM によってアクセスされる ID ストアに存在することがわかっているユーザーを更新するために、Azure Active Directory は、次のような要求を使用して、そのユーザーの現在の状態をサービスに要求して処理を続行します。 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

SCIM サービスを実装するために Microsoft が提供する CLI ライブラリを使用して作成されたサービスで、要求はサービスのプロバイダーの Retrieve メソッドの呼び出しに変換されます。  Retrieve メソッドのシグネチャを次に示します。

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
 // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
 // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
    Retrieve(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
        parameters, 
        string correlationIdentifier);

 public interface 
   Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
     IRetrievalParameters
     {
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
         ResourceIdentifier 
           { get; }
 }
 public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
 {
     string Identifier 
       { get; set; }
     string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
       { get; set; }
 }
```

ユーザーの現在の状態を取得する要求の例では、parameters 引数の値として指定されたオブジェクトのプロパティ値は次のようになります。 
  
* Identifier:"54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***例 4.更新する参照属性の値を照会する*** 

参照属性を更新する場合、Azure Active Directory は、サービスによってアクセスされる ID ストア内の参照属性の現在の値が Azure Active Directory 内のその属性の値と既に一致しているかどうかを判別するために、サービスにクエリを実行します。 ユーザーの場合、この方法で現在の値を照会する属性は、manager 属性のみです。 特定のユーザー オブジェクトの manager 属性に特定の値があるかどうかを判別する要求の例を次に示します。 

サービスの作成時に SCIM サービスの実装用に Microsoft が提供する CLI ライブラリを使用した場合、要求はサービスのプロバイダーの Query メソッドの呼び出しに変換されます。 parameters 引数の値として指定されたオブジェクトのプロパティ値は次のようになります。 
  
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

要求は、SCIM サービスの実装用に Microsoft 共通言語基盤ライブラリによって、サービスのプロバイダーの Update メソッドの呼び出しに変換されます。 Update メソッドのシグネチャを次に示します。 

```csharp
  // System.Threading.Tasks.Tasks and 
  // System.Collections.Generic.IReadOnlyCollection<T>
  // are defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
  // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
  // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
  // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
  // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
  // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
  // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
  // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

  System.Threading.Tasks.Task Update(
    Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
    string correlationIdentifier);

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
  {
  Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
    PatchRequest 
      { get; set; }
  Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
    ResourceIdentifier 
      { get; set; }        
  }

  public class PatchRequest2: 
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
  {
  public System.Collections.Generic.IReadOnlyCollection
    <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
      Operations
      { get;}

  public void AddOperation(
    Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
  }

  public class PatchOperation
  {
  public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
    Name
    { get; set; }

  public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
    Path
    { get; set; }

  public System.Collections.Generic.IReadOnlyCollection
    <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
    { get; }

  public void AddValue(
    Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
  }

  public enum OperationName
  {
    Add,
    Remove,
    Replace
  }

  public interface IPath
  {
    string AttributePath { get; }
    System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
    Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
  }

  public class OperationValue
  {
    public string Reference
    { get; set; }

    public string Value
    { get; set; }
  }
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

サービスの作成時に SCIM サービスの実装用に Microsoft が提供する共通言語基盤ライブラリを使用した場合、要求はサービスのプロバイダーの Delete メソッドの呼び出しに変換されます。   このメソッドのシグネチャを次に示します。 

```csharp
  // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
  // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
  System.Threading.Tasks.Task Delete(
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
      resourceIdentifier, 
    string correlationIdentifier);
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

7. **[テナント URL]** フィールドに、アプリケーションの SCIM エンドポイントの URL を入力します。 例: https://api.contoso.com/scim/
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

複数のテナントによって使用されるアプリケーションを作成する場合は、Azure AD アプリケーション ギャラリーで使用可能にできます。 これにより、組織でアプリケーションを見つけて、プロビジョニングを構成することが簡単になります。 簡単に、Azure AD ギャラリーにアプリを発行し、他のユーザーがプロビジョニングできるようにすることができます。 手順は、 [こちら](../develop/howto-app-gallery-listing.md)で確認してください。 Microsoft はお客様と協力して、お客様のアプリケーションをギャラリーに統合し、エンドポイントをテストし、顧客が使用できるオンボード [ドキュメント](../saas-apps/tutorial-list.md)をリリースします。 

### <a name="gallery-onboarding-checklist"></a>ギャラリーのオンボードのチェックリスト
アプリケーションの迅速なオンボードと顧客のスムーズなデプロイ エクスペリエンスを確実なものとするために、以下のチェックリストに従ってください。 ギャラリーにオンボードする際に、ご自身から情報が収集されます。 
> [!div class="checklist"]
> * [SCIM 2.0 をサポートする](https://tools.ietf.org/html/draft-wahl-scim-profile-00) (必須)
> * テナントごとに少なくとも 1 秒あたり 25 個の要求をサポートする (必須)
> * スキーマ検出をサポートする (推奨)
> * 次に示すように、OAuth 承認コードの付与または有効期間が長いトークンをサポートする (必須)
> * 顧客のギャラリー オンボード後のサポートを行うエンジニアリングとサポートの連絡先を確立する (必須)
> * SCIM エンドポイントを公開文書化する (推奨) 


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>アプリケーション ギャラリーでのプロビジョニング コネクタの承認
SCIM 仕様では、SCIM 固有の認証と承認のスキームは定義されていません。 既存の業界標準の使用に依存しています。 Azure AD プロビジョニング クライアントは、ギャラリー内のアプリケーションに対して 2 つの承認方法をサポートしています。 

|承認方法|長所|短所|サポート|
|--|--|--|--|
|ユーザー名とパスワード (推奨されないか、Azure AD でサポートされていません)|簡単に実装できます|セキュリティで保護されていません - 「[パスワードは重要ではない](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)」を参照|ギャラリー アプリに対してケース バイ ケースでサポートされます。 ギャラリー以外のアプリではサポートされていません。|
|有効期間が長いベアラー トークン (現在、Azure AD でサポートされています)|有効期間が長いトークンでは、ユーザーが存在している必要はありません。 プロビジョニングを設定する場合、管理者はこれらを簡単に使用できます。|有効期間が長いトークンは、メールのようにセキュリティで保護されていない方法を使用しないと、管理者と共有するのが困難です。 |ギャラリー アプリとギャラリー以外のアプリでサポートされます。 |
|OAuth 承認コードの付与 (現在、Azure AD でサポートされています)|アクセス トークンの有効期間はパスワードよりもはるかに短く、有効期間が長いベアラー トークンにはない自動更新メカニズムがあります。  初期承認中に実際のユーザーが存在する必要があり、アカウンタビリティのレベルを追加します。 |ユーザーが存在している必要があります。 ユーザーが組織からいなくなる場合、トークンは無効になり、承認を再度完了する必要があります。|ギャラリー アプリでサポートされます。 ギャラリー以外のアプリでのサポートは作業中です。|
|OAuth クライアント資格情報の付与 (サポートされていません、ロードマップ上にあります)|アクセス トークンの有効期間はパスワードよりもはるかに短く、有効期間が長いベアラー トークンにはない自動更新メカニズムがあります。 承認コードの付与とクライアント資格情報の付与の両方で、同じ種類のアクセス トークンが作成されるため、これらの方法間の移動は API に対して透過的です。  プロビジョニングは完全に自動化することができ、ユーザーの介入なしに新しいトークンを自動的に要求することができます。 ||ギャラリー アプリとギャラリー以外のアプリでサポートされていません。 サポートはバックログに入っています。|

**OAuth 承認コード付与フロー:** プロビジョニング サービスでは、[承認コードの付与](https://tools.ietf.org/html/rfc6749#page-24)がサポートされています。 ギャラリーにアプリを発行するための要求を送信した後、私たちのチームは次の情報を収集するためにお客様と協力します。
*  承認 URL:ユーザー エージェント リダイレクトによってリソース所有者から承認を取得するためのクライアントによる URL。 ユーザーは、アクセスを承認するためにこの URL にリダイレクトされます。 
*  トークン交換 URL:アクセス トークンの承認付与を交換するためのクライアントによる URL。通常は、クライアント認証を使用します。
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

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Azure AD プロビジョニング サービスで使用される IP アドレスが SCIM 要求を行うことを許可する

特定のアプリでは、アプリへの受信トラフィックが許可されます。 Azure AD プロビジョニング サービスが期待通りに機能するためには、使用する IP アドレスが許可されている必要があります。 各サービス タグ/リージョンの IP アドレスの一覧については、「[Azure IP 範囲とサービス タグ – パブリック クラウド](https://www.microsoft.com/download/details.aspx?id=56519)」という JSON ファイルを参照してください。 これらの IP は、必要に応じて、お使いのファイアウォールにダウンロードしてプログラムすることができます。 Azure AD プロビジョニングの予約済み IP 範囲は、"AzureActiveDirectoryDomainServices" にあります。

## <a name="related-articles"></a>関連記事

* [SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](user-provisioning.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](customize-application-attributes.md)
* [属性マッピングの式の書き方](functions-for-customizing-application-data.md)
* [ユーザー プロビジョニング用のフィルターのスコープ](define-conditional-rules-for-provisioning-user-accounts.md)
* [アカウント プロビジョニング通知](user-provisioning.md)
* [SaaS アプリを統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)
