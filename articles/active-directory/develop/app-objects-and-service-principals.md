---
title: Azure AD におけるアプリとサービス プリンシパル | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory におけるアプリケーション オブジェクトとサービス プリンシパル オブジェクトのリレーションシップについて説明します。
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: 0b6a6eac04711b564d602408a57b92f833fb5d5d
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782438"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト

この記事では、Azure Active Directory のアプリケーション登録、アプリケーション オブジェクト、およびサービス プリンシパルの概要、使用方法、相互関係について説明します。 アプリケーションのアプリケーション オブジェクトと対応するサービス プリンシパル オブジェクトの間の関係を示すために、マルチテナントのシナリオ例も紹介します。

## <a name="application-registration"></a>アプリケーションの登録
ID とアクセスの管理機能を Azure AD に委任するには、アプリケーションを Azure AD の[テナント](developer-glossary.md#tenant)に登録する必要があります。 アプリケーションを Azure AD に登録するときに、アプリケーションの ID 構成を作成します。これによって Azure AD との連携が可能となります。 [Azure portal][AZURE-Portal] でアプリを登録するときに、それがシングル テナント (自分のテナント内でのみアクセス可能) かマルチテナント (他のテナント内でアクセス可能) かを選択し、必要に応じてリダイレクト URI (アクセス トークンの送信先) を設定します。

:::image type="content" source="media/app-objects-and-service-principals/app-registration.png" alt-text="Azure portal の [アプリケーションの登録] ウィンドウのスクリーンショット":::

アプリの登録が完了すると、ホーム テナントまたはディレクトリ内に存在するアプリ (アプリケーション オブジェクト) のグローバルに一意なインスタンスが作成されます。  また、アプリにグローバルに一意な ID (アプリまたはクライアント ID) も割り当てられます。  ポータルでは、シークレットまたは証明書とスコープを追加してアプリを機能させることや、サインイン ダイアログでアプリのブランドをカスタマイズすることなどができます。

ポータルでアプリケーションを登録すると、ホーム テナントにアプリケーション オブジェクトとサービス プリンシパル オブジェクトが自動的に作成されます。  Microsoft Graph API を使用してアプリケーションを登録または作成する場合、サービス プリンシパル オブジェクトの作成は別の手順です。

## <a name="application-object"></a>アプリケーション オブジェクト
Azure AD アプリケーションは、その唯一のアプリケーション オブジェクトによって定義されます。アプリケーション オブジェクトは、アプリケーションの登録先である Azure AD テナント (アプリケーションの "ホーム" テナントと呼ばれます) 内にあります。  アプリケーション オブジェクトは、1 つ以上のサービス プリンシパル オブジェクトを作成するためのテンプレートまたはブループリントとして使用されます。  サービス プリンシパルは、アプリケーションが使用されるすべてのテナントに作成されます。 オブジェクト指向プログラミングのクラスと同様に、アプリケーション オブジェクトには、作成されたすべてのサービス プリンシパル (またはアプリケーション インスタンス) に適用されるいくつかの静的プロパティがあります。

アプリケーション オブジェクトには、サービスからアプリケーションにアクセスする際にトークンを発行する方法、アプリケーションからアクセスする必要があるリソース、アプリケーションで実行できるアクションというアプリケーションの 3 つの側面が記述されています。

[Azure portal][AZURE-Portal] の **[アプリの登録]** ブレードは、ホーム テナントのアプリケーション オブジェクトを一覧表示し、管理するために使用されます。

![[アプリの登録] ブレード](./media/app-objects-and-service-principals/app-registrations-blade.png)

アプリケーション オブジェクトのプロパティのスキーマは、Microsoft Graph [Application エンティティ][MS-Graph-App-Entity]によって定義されています。

## <a name="service-principal-object"></a>サービス プリンシパル オブジェクト
Azure AD テナントによってセキュリティ保護されているリソースにアクセスするためには、アクセスを必要とするエンティティをセキュリティ プリンシパルで表す必要があります。 この要件は、ユーザー (ユーザー プリンシパル) とアプリケーション (サービス プリンシパル) の両方に当てはまります。 セキュリティ プリンシパルは、その Azure AD テナント内のユーザー/アプリケーションのアクセス ポリシーとアクセス許可を定義します。 これにより、サインイン時のユーザー/アプリケーションの認証、リソースへのアクセス時の承認などのコア機能を利用できるようになります。

サービス プリンシパルは、単一のテナントまたはディレクトリ内のグローバル アプリケーション オブジェクトのローカル表現、つまりアプリケーション インスタンスです。 サービス プリンシパルは、アプリケーション オブジェクトから作成された具象インスタンスであり、そのアプリケーション オブジェクトから特定のプロパティが継承されます。  サービス プリンシパルは、アプリケーションが使用される各テナントで作成され、グローバルに一意なアプリ オブジェクトが参照されます。  サービス プリンシパル オブジェクトには、特定のテナント内でアプリが実際に実行できること、アプリにアクセスできるユーザー、アプリからアクセスできるリソースを定義します。

アプリケーションが (登録または[同意](developer-glossary.md#consent)によって) テナント内のリソースへのアクセス許可を与えられると、サービス プリンシパル オブジェクトが作成されます。 [Azure PowerShell](howto-authenticate-service-principal-powershell.md)、Azure CLI、[Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?view=graph-rest-1.0&tabs=http)、[Azure portal][AZURE-Portal]、およびその他のツールを使用して、テナントにサービス プリンシパル オブジェクトを作成することもできます。  ポータルを使用する場合、アプリケーションを登録すると、サービス プリンシパルが自動的に作成されます。

ポータルの **[エンタープライズ アプリケーション]** ブレードは、テナントのサービス プリンシパルを一覧表示および管理するために使用されます。 サービス プリンシパルのアクセス許可、ユーザーが同意したアクセス許可、その同意を行ったユーザー、サインイン情報などを確認できます。

![エンタープライズ アプリ ブレード](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

サービス プリンシパル オブジェクトのプロパティのスキーマは、Microsoft Graph [ServicePrincipal エンティティ][MS-Graph-Sp-Entity]によって定義されています。

## <a name="relationship-between-application-objects-and-service-principals"></a>アプリケーション オブジェクトとサービス プリンシパル間のリレーションシップ

アプリケーション オブジェクトは、すべてのテナントで使用するアプリケーションの "*グローバル*" 表現であり、サービス プリンシパルは、特定のテナントで使用する "*ローカル*" 表現です。

アプリケーション オブジェクトは、対応するサービス プリンシパル オブジェクトの作成に使用するために、一般的な既定のプロパティが*派生*するテンプレートとして機能します。 そのため、アプリケーション オブジェクトには、ソフトウェア アプリケーションとの間に 1 対 1 のリレーションシップがあり、対応するサービス プリンシパル オブジェクトとの間に 1 対多のリレーションシップがあります。

サービス プリンシパルは、テナントによってセキュリティ保護されているリソースにサインインまたはアクセスするための ID を確立できるように、アプリケーションが使用される各テナントで作成する必要があります。 シングルテナント アプリケーションには、アプリケーション登録中に作成され、使用が同意されたサービス プリンシパルが (そのホーム テナントに) 1 つだけあります。 マルチテナント Web アプリケーション/API には、そのテナントのユーザーが使用に同意した各テナントで作成されたサービス プリンシパルもあります。

> [!NOTE]
> アプリケーション オブジェクトに加えたすべての変更は、アプリケーションのホーム テナント (アプリケーションが登録されたテナント) にだけ存在するサービス プリンシパル オブジェクトにも反映されます。 マルチテナント アプリケーションの場合は、[アプリケーション アクセス パネル](https://myapps.microsoft.com)を通じてアクセス権を削除し、もう一度アクセス権を付与するまで、そのコンシューマー テナントのサービス プリンシパル オブジェクトにアプリケーション オブジェクトへの変更が反映されることはありません。
>
> 既定でネイティブ アプリケーションがマルチテナントとして登録されていることにも注意してください。

## <a name="example"></a>例

次の図は、 **HR アプリ**という名前のサンプル マルチテナント アプリケーションを基に、アプリケーションのアプリケーション オブジェクトと、対応するサービス プリンシパル オブジェクトの間のリレーションシップを表しています。 このサンプル シナリオには、次の 3 つの Azure AD テナントがあります。

- **Adatum** - **HR アプリ**を開発した会社が使用するテナント
- **Contoso** - **HR アプリ**のコンシューマーである Contoso という組織が使用するテナント
- **Fabrikam** - Contoso と同じく **HR アプリ**のコンシューマーである Fabrikam という組織が使用するテナント

![アプリ オブジェクトとサービス プリンシパル オブジェクトの間のリレーションシップ](./media/app-objects-and-service-principals/application-objects-relationship.svg)

このサンプル シナリオの内容:

| 手順 | 説明 |
|------|-------------|
| 1    | アプリケーションとサービス プリンシパル オブジェクトを、アプリケーションのホーム テナント内に作成するプロセスです。 |
| 2    | Contoso と Fabrikam の管理者が同意を終えると、それぞれの会社の Azure AD テナント内にサービス プリンシパル オブジェクトが作成され、それに管理者が付与したアクセス許可が割り当てられます。 HR アプリは、個々のユーザー用として、ユーザーによる同意を許可するように構成/設計することができる点にも注目してください。 |
| 3    | HR アプリケーション (Contoso と Fabrikam) のコンシューマー テナントにそれぞれ独自のサービス プリンシパル オブジェクトが作成されます。 それぞれ実行時におけるアプリケーションのインスタンスの使用を表し、それぞれの管理者によって同意されたアクセス許可によって管理されます。 |

## <a name="next-steps"></a>次のステップ

- [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を使用して、アプリケーションとサービス プリンシパル オブジェクトの両方を照会できます。
- Microsoft Graph API、[Azure portal][AZURE-Portal] のアプリケーション マニフェスト エディター、[Azure AD PowerShell コマンドレット](/powershell/azure/?view=azureadps-2.0)を使用して、その OData [Application エンティティ][MS-Graph-App-Entity]によって表されるアプリケーションのアプリケーション オブジェクトにアクセスできます。
- Microsoft Graph API または [Azure AD PowerShell コマンドレット](/powershell/azure/?view=azureadps-2.0)を通じて、その OData [ServicePrincipal エンティティ][MS-Graph-Sp-Entity]によって表されるアプリケーションのサービス プリンシパル オブジェクトにアクセスできます。

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com