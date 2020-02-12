---
title: Azure AD 認証を構成する
description: App Service アプリの ID プロバイダーとして Azure Active Directory 認証を構成する方法を学びます。
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 69959418c52eb7324efe19ca41481e426b822ab4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842361"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>Azure AD ログインを使用するように App Service アプリを構成する

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

この記事では、Azure Active Directory (Azure AD) を認証プロバイダーとして使用するように Azure App Service を構成する方法を示します。

アプリと認証を設定するときは、次のベスト プラクティスに従ってください。

- App Service アプリごとに独自のアクセス許可と同意を付与します。
- それぞれの App Service アプリを独自の登録で構成します。
- デプロイ スロットごとに個別のアプリ登録を使用することで、環境間でアクセス許可を共有することを回避します。 新しいコードをテストするとき、このプラクティスは、問題が運用アプリに影響を与えることを回避する上で役立つことがあります。

## <a name="express"> </a>簡単設定を構成する

1. [Azure portal] で、 **[App Services]** を探して選択してから、アプリを選択します。
2. 左側のナビゲーションから、 **[認証/承認]**  >  **[On]\(オン\)** を選択します。
3. **[Azure Active Directory]**  >  **[Express]\(簡易\)** を選択します。

   代わりに既存のアプリ登録を選択する場合は、次のようにします。

   1. **[既存の AD アプリを選択する]** を選択し、 **[Azure AD アプリ]** をクリックします。
   2. 既存のアプリ登録を選択して **[OK]** をクリックします。

3. **[OK]** を選択して、Azure Active Directory に App Service アプリを登録します。 新しいアプリ登録が作成されます。
   
    ![Azure Active Directory での簡易設定](./media/configure-authentication-provider-aad/express-settings.png)
   
4. (省略可能) App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。 Azure Active Directory で認証されたユーザーのみにアプリ アクセスを限定するには、 **[要求が認証されない場合に実行するアクション]** を **[Azure Active Directory でのログイン]** に設定します。 この機能を設定すると、お使いのアプリでは、すべての要求を認証する必要があります。 また、認証されていないものはすべて、Azure Active Directory に認証のためにリダイレクトされます。

    > [!CAUTION]
    > この方法でのアクセスの制限は、アプリへのすべての呼び出しに適用されますが、これは、多くのシングルページ アプリケーションのように、一般公開されているホーム ページが与えられているアプリには適切でない場合があります。 このようなアプリケーションの場合は、アプリが手動で自身のログインを開始する、 **[匿名要求を許可する (操作不要)]** が望ましいと考えられます。 詳細については、「[認証フロー](overview-authentication-authorization.md#authentication-flow)」をご覧ください。
5. **[保存]** を選択します。

## <a name="advanced"> </a>詳細設定を構成する

異なる Azure AD テナントからアプリの登録を使用する場合、アプリの設定を手動で構成できます。 このカスタム構成を完了するには、次の手順を実行します。

1. Azure AD で登録を作成します。
2. App Service に登録の詳細を指定します。

### <a name="register"> </a>App Service アプリに対するアプリ登録を Azure AD で作成する

App Service アプリを構成するとき、次の情報が必要になります。

- クライアント ID
- テナント ID
- クライアント シークレット (省略可能)
- アプリケーション ID URI

次の手順に従います。

1. [Azure portal] にサインインし、 **[App Services]** を探して選択してから、アプリを選択します。 アプリの **URL** をメモしておきます。 Azure Active Directory アプリの登録を構成するときにそれを使用します。
1. **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** の順に選択します。
1. **[アプリケーションの登録]** ページで、アプリの登録の **[名前]** を入力します。
1. **[リダイレクト URI]** で、 **[Web]** を選択し、「`<app-url>/.auth/login/aad/callback`」と入力します。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/aad/callback` 」のように入力します。 
1. **作成** を選択します。
1. アプリの登録が作成されたら、後のために **[アプリケーション (クライアント) ID]** と **[ディレクトリ (テナント) ID]** をコピーします。
1. **[ブランド]** を選択します。 **[ホーム ページ URL]** に App Service アプリの URL を入力し、 **[保存]** を選択します。
1. **[API の公開]**  >  **[設定]** を選択します。 App Service アプリの URL を貼り付けて、 **[保存]** を選択します。

   > [!NOTE]
   > この値は、アプリの登録の**アプリケーション ID の URI** です。 Web アプリでクラウドの API にアクセスする必要がある場合、クラウド App Service リソースの構成時、**Web アプリのアプリケーション ID URI** が必要になります。 たとえば、クラウド サービスで Web アプリにアクセスを明示的に与える場合にこれを利用できます。

1. **[Scope の追加]** を選択します。
   1. **[スコープ名]** に、「*user_impersonation*」と入力します。
   1. 同意ページでユーザーに表示する同意スコープの名前と説明をテキスト ボックスに入力します。 たとえば、「*Access my app*」と入力します。 
   1. **[スコープの追加]** を選択します。
1. (省略可能) クライアント シークレットを作成するには、 **[Certificates & secrets]\(証明書とシークレット)**  >  **[New client secret]\(新しいクライアント シークレット)**  >  **[追加]** を選択します。 ページに表示されるクライアント シークレットの値をコピーします。 二度と表示されることはありません。
1. (省略可能) 複数の**応答 URL** を追加するには、 **[認証]** を選択します。

### <a name="secrets"> </a>App Service アプリで Azure Active Directory を有効にする

1. [Azure portal] で、 **[App Services]** を探して選択してから、アプリを選択します。 
1. 左側のウィンドウの **[設定]** で、 **[認証/承認]**  >  **[On]\(オン\)** を選択します。
1. (省略可能) 既定では、App Service 認証によってアプリへの認証されていないアクセスが許可されます。 ユーザー認証を強制するには、 **[要求が認証されない場合に実行するアクション]** を **[Azure Active Directory でのログイン]** に設定します。
1. **[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。
1. **[管理モード]** で **[Advanced]\(詳細\)** を選択し、次の表に従って App Service 認証を構成します。

    |フィールド|説明|
    |-|-|
    |クライアント ID| アプリの登録の**アプリケーション (クライアント) ID** を使用します。 |
    |Issuer ID (発行者 ID)| `https://login.microsoftonline.com/<tenant-id>` を使用し、 *\<tenant-id>* をアプリの登録の**ディレクトリ (テナント) ID** に置き換えます。 |
    |クライアント シークレット (省略可能)| アプリの登録で生成したクライアント シークレットを使用します。|
    |許可されるトークン対象ユーザー| これがクラウドまたはサーバー アプリで、Web アプリからの認証トークンを許可する場合は、Web アプリの**アプリケーション ID URI** をここに追加します。 構成された **[クライアント ID]** は、*常に*、許可された対象ユーザーであると暗黙的に見なされます。 |

2. **[OK]** を選択し、 **[保存]** を選択します。

これで、App Service アプリで認証に Azure Active Directory を使用する準備ができました。

## <a name="configure-a-native-client-application"></a>ネイティブ クライアント アプリケーションを構成する

**Active Directory 認証ライブラリ**などのクライアント ライブラリを使用して認証を許可する場合は、ネイティブ クライアントを登録できます。

1. [Azure portal] で、 **[Active Directory]** 、 **[アプリの登録]** 、 **[新規登録]** の順に選択します。
1. **[アプリケーションの登録]** ページで、アプリの登録の **[名前]** を入力します。
1. **[リダイレクト URI]** で、 **[パブリック クライアント (モバイルとデスクトップ)]** を選択し、URL「`<app-url>/.auth/login/aad/callback`」を入力します。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/aad/callback` 」のように入力します。

    > [!NOTE]
    > Windows アプリケーションの場合は、代わりに[パッケージ SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) を URI として使用します。
1. **作成** を選択します。
1. アプリの登録が作成されたら、 **[アプリケーション (クライアント) ID]** の値をコピーします。
1. **[API のアクセス許可]** 、 **[アクセス許可の追加]** 、 **[自分の API]** の順に選択します。
1. App Service アプリ用に以前に作成したアプリの登録を選択します。 アプリの登録が表示されない場合は、「[App Service アプリに対するアプリ登録を Azure AD で作成する](#register)」で **user_impersonation** スコープを追加したことを確認します。
1. **user_impersonation** を選択し、 **[アクセス許可の追加]** を選択します。

これで、App Service アプリにアクセスできるネイティブ クライアント アプリケーションが構成されました。

## <a name="related-content"> </a>次のステップ

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
