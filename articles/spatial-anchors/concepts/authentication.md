---
title: 認証と権限承認
description: アプリまたはサービスが Azure Spatial Anchors に対して認証できるさまざまな方法と、Azure Spatial Anchors へのアクセスを制限する必要がある制御レベルについて説明します。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: e4d25637498bec223e294eecf2be6dc88fa2aa0d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997168"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure Spatial Anchors に対する認証と承認

このセクションでは、アプリまたは Web サービスから Azure Spatial Anchors に対して認証できるさまざまな方法と、Azure Directory (Azure AD) でロールベースのアクセス制御を使用して Spatial Anchors アカウントへのアクセスを制御する方法について説明します。

## <a name="overview"></a>概要

![Azure Spatial Anchors に対する認証の概要](./media/spatial-anchors-authentication-overview.png)

特定の Azure Spatial Anchors アカウントにアクセスするには、まずクライアントで Azure Mixed Reality セキュリティ トークン サービス (STS) からアクセス トークンを取得する必要があります。 STS から取得したトークンは、24 時間有効で、Spatial Anchors サービスがアカウントの承認決定を行うための情報が含まれており、承認されたプリンシパルのみがそのアカウントにアクセスできるようにします。

アクセス トークンは、アカウント キーまたは Azure AD が発行したトークンのいずれかと引き換えに取得できます。

アカウント キーを使用すると、Azure Spatial Anchors サービスの使用をすばやく開始することができますが、アプリケーションを運用環境にデプロイする前に、Azure AD ベースの認証を使用するようにアプリを更新することをお勧めします。

Azure AD 認証トークンは、次の 2 つの方法で取得できます。

- エンタープライズ アプリケーションをビルドしていて、会社で Azure AD を ID システムとして使用している場合は、ご使用のアプリで Azure AD のユーザーベースの認証を使用して、既存の Azure AD セキュリティ グループを使用して自分の空間アンカー アカウントにアクセス権を付与するか、組織内のユーザーに直接アクセス権を付与します。
- それ以外の場合は、ご使用のアプリをサポートしている Web サービスから Azure AD トークンを取得することをお勧めします。 サポートしている Web サービスを使用すると、Azure Spatial Anchors にアクセスするための資格情報のクライアント アプリケーションへの埋め込みを回避できるため、運用アプリケーションの認証にはこの方法が推奨されます。

## <a name="account-keys"></a>アカウント キー

アカウント キーを使用して自分の Azure Spatial Anchors にアクセスするのが、最も簡単に始める方法です。 自分のアカウント キーは、Azure portal で見つけることができます。 自分のアカウントに移動し、[キー] タブを選択します。

![Azure Spatial Anchors に対する認証の概要](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

2 つのキーが利用可能で、どちらも Spatial Anchors アカウントにアクセスするために同時に有効になっています。 アカウントにアクセスするために使用するキーは、定期的に更新することをお勧めします。2 つの異なる有効なキーを持つことで、ダウンタイムを発生させることなくこのような更新が可能になります。主キーと 2 次キーを交互に更新するだけで済みます。

SDK には、アカウント キーを使用した認証の組み込みのサポートがあり、cloudSession オブジェクトに対して AccountKey プロパティを設定するだけで済みます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

これを完了すると、SDK で、アクセス トークンのためのアカウント キーの交換と、アプリのトークンの必要なキャッシュが処理されるようになります。

> [!WARNING]
> すばやいオンボーディングにはアカウント キーの使用をお勧めしますが、開発/プロトタイプ作成時に限ります。 アプリケーションに埋め込んだアカウント キーを使用して、アプリケーションを運用環境に出荷しないことを強くお勧めします、代わりに、次に示すユーザー ベースまたはサービス ベースの Azure AD 認証の方法を使用します。

## <a name="azure-ad-user-authentication"></a>Azure AD ユーザー認証

Azure Active Directory ユーザーを対象とするアプリケーションでは、ユーザーに対して Azure AD トークンを使用することをお勧めします。これは、[MSAL ライブラリ](../../active-directory/develop/msal-overview.md)を使用して取得できます。 以下を含む、[アプリの登録のクイックスタート](../../active-directory/develop/quickstart-register-app.md)に関するページに一覧表示されている手順に従ってください。

1. Azure portal での構成
    1.  Azure AD でアプリケーションを**ネイティブ アプリケーション**として登録します。 登録の一環として、アプリケーションをマルチ テナントにする必要があるかどうかを決定し、アプリケーションに許可するリダイレクト URL を提供する必要があります。
        1.  **[API のアクセス許可]** タブに切り替えます
        2.  **[アクセス許可の追加]** を選択します
            1.  **[所属する組織で使用している API]** タブの下にある **[Microsoft Mixed Reality]\(Microsoft Mixed Reality\)** を選択します
            2.  **[委任されたアクセス許可]** を選択します
            3.  **[mixedreality]** の下にある **[mixedreality.signin]** のチェックボックスをオンにします
            4.  **[アクセス許可の追加]** を選択する
        3.  **[管理者の同意の付与]** を選択します
    2.  アプリケーションまたはユーザーにご使用のリソースへのアクセス権を付与します。
        1.  Azure portal で、ご使用の Spatial Anchors リソースに移動します
        2.  **[アクセス制御 (IAM)]** タブに切り替えます
        3.  **[ロールの割り当ての追加]** をクリックします
            1.  [ロールを選択します](#role-based-access-control)
            2.  **[選択]** フィールドに、アクセスを割り当てるユーザー名、グループ名、および/またはアプリケーション名を入力します。
            3.  **[保存]** をクリックします。
2. コードで次を行います。
    1.  利用している Azure AD アプリケーションの**アプリケーション ID** と**リダイレクト URI** を、MSAL で **client ID** パラメーターと **RedirectUri** パラメーターとして必ず使用します
    2.  テナント情報を設定します。
        1.  ご使用のアプリケーションで **[所属する組織のみ]** がサポートされている場合は、この値をご使用の **[テナント ID]** または **[テナント名]** (例: contoso.microsoft.com) に置き換えます
        2.  ご使用のアプリケーションで **[任意の組織のディレクトリ内のアカウント]** がサポートされている場合は、この値を **[組織]** に置き換えます
        3.  ご使用のアプリケーションで **[すべての Microsoft アカウント ユーザー]** がサポートされる場合は、この値を **[共通]** に置き換えます
    3.  トークンの要求で、**スコープ**を "https://sts.mixedreality.azure.com//.default" に設定します。 このスコープでは、アプリケーション上で Mixed Reality セキュリティ トークン サービス (STS) に対してトークンを要求していることを Azure AD に示します。

これで、アプリケーションで MSAL から Azure AD トークンを取得できるようになるはずです。その Azure AD トークンを、クラウド セッション構成オブジェクトで **authenticationToken** として設定できます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD サービス認証

Azure Spatial Anchors を利用してアプリを運用環境にデプロイするために推奨されるオプションは、認証要求を仲介するバックエンド サービスを利用することです。 一般的なスキームは、この図で示されているようになります。

![Azure Spatial Anchors に対する認証の概要](./media/spatial-anchors-aad-authentication.png)

ここでは、アプリが独自のメカニズム (例:Microsoft アカウント、PlayFab、Facebook、Google ID、カスタムのユーザー名/パスワードなど) を使用してそのバックエンド サービスを認証することを前提としています。 ユーザーがバックエンド サービスに対して認証されると、そのサービスで Azure AD トークンを取得して、それを Azure Spatial Anchors のアクセス トークンと交換して、それをクライアント アプリケーションに返すことができます。

Azure AD アクセス トークンは、[MSAL ライブラリ](../../active-directory/develop/msal-overview.md)を使用して取得されます。 以下を含む、[アプリの登録のクイックスタート](../../active-directory/develop/quickstart-register-app.md)に関するページに一覧表示されている手順に従ってください。

1.  Azure portal での構成:
    1.  Azure AD でアプリケーションを登録します。
        1.  Azure portal で、 **[Azure Active Directory]** に移動し、 **[アプリの登録]** を選択します
        2.  **[新しいアプリケーションの登録]** を選択します
        3.  アプリケーションの名前を入力し、アプリケーションの種類として **[Web アプリ/API]** を選択し、サービスの認証 URL を入力します。 次に **[作成]** をクリックします。
        4.  そのアプリケーションで **[設定]** をクリックし、次に **[証明書とシークレット]** タブを選択します。新しいクライアント シークレットを作成し、期間を選択し、 **[追加]** をクリックします。 シークレットの値は、Web サービスのコードに含める必要があるため、必ず保存しておいてください。
    2.  アプリケーションまたはユーザー (および両方) にリソースへのアクセス権を付与します。
        1.  Azure portal で、ご使用の Spatial Anchors リソースに移動します
        2.  **[アクセス制御 (IAM)]** タブに切り替えます
        3.  **[ロールの割り当ての追加]** をクリックします
        1.  [ロールを選択します](#role-based-access-control)
        2.  **[選択]** フィールドに、アクセス権を割り当てる作成したアプリケーションの名前を入力します。 アプリのユーザーに Spatial Anchors アカウントに対してさまざまなロールを割り当てる場合は、Azure AD で複数のアプリケーションを登録し、それぞれに別のロールを割り当てる必要があります。 次に、ユーザーに適切なロールを使用するために承認ロジックを実装します。
        3.  注 - **[ロールの割り当ての追加]** セクションで、 **[アクセスの割り当て先]** を [Azure AD のユーザー、グループ、サービス プリンシパル] に設定します。
    3.  **[保存]** をクリックします。
2.  コードで次を行います (注: GitHub に含まれているサービスのサンプルを使用できます):
    1.  利用している Azure AD アプリケーションのアプリケーション ID、アプリケーション シークレット、およびリダイレクト URI を、MSAL の client ID、secret、および RedirectUri の各パラメーターとして必ず使用します
    2.  MSAL の authority パラメーターで、tenant ID に利用している Azure ADD のテナント ID を設定します。
    3.  トークンの要求で、**スコープ**を "https://sts.mixedreality.azure.com//.default" に設定します

これでご使用のバックエンド サービスで、Azure AD トークンを取得できるようになります。 その後、クライアントに返される MR トークンに交換できます。 Azure AD トークンを使用した MR トークンの取得は、REST 呼び出しで行われます。 サンプルの呼び出しを次に示します。

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

ここで Authorization ヘッダーは、次のように書式設定されます。`Bearer <Azure_AD_token>`

応答には、MR トークンがプレーンテキストで含まれています。

その MR トークンは、その後、クライアントに返されます。 その後、クライアント アプリでそれをクラウド セッション構成でアクセス トークンとして設定できます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

アプリケーション、サービス、またはサービスの Azure AD ユーザーに付与されるアクセスのレベルを制御するため、必要に応じてご使用の Azure Spatial Anchors アカウントに対して割り当てるために次のロールが作成されています。

- **Spatial Anchors アカウント所有者**: このロールが割り当てられているアプリケーションまたはユーザーは、空間アンカーの作成、クエリの実行、および削除ができます。 アカウント キーを使用してアカウントを認証すると、認証されたプリンシパルに **Spatial Anchors アカウント所有者**ロールが割り当てられます。
- **Spatial Anchors アカウント共同作成者**: このロールが割り当てられているアプリケーションまたはユーザーは、空間アンカーの作成とクエリの実行はできますが、削除はできません。
- **Spatial Anchors アカウント閲覧者**: このロールが割り当てられているアプリケーションまたはユーザーは、空間アンカーのクエリのみを実行できますが、新しく作成したり既存のものを削除したり、空間アンカーでメタデータを更新したりすることはできません。 これは通常、一部のユーザーが環境をキュレートし、他のユーザーは以前にその環境に配置されていたアンカーの呼び戻しのみができる環境で使用されます。

## <a name="next-steps"></a>次のステップ

Azure Spatial Anchors を使用して初めてのアプリを作成します。

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
