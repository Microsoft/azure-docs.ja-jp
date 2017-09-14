---
title: "フェデレーション シングル サインオン用に構成された、Azure AD ギャラリー アプリケーションにサインインできない問題 | Microsoft Docs"
description: "パスワード シングル サインオン用に構成された、Azure AD ギャラリー アプリケーションの使用に関する問題をトラブルシューティングする方法"
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 65bfb0a759a624c98a19f3c2c9c72aa5a6780df7
ms.contentlocale: ja-jp
ms.lasthandoff: 04/18/2017

---

# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-federated-single-sign-on"></a>フェデレーション シングル サインオン用に構成された、Azure AD ギャラリー アプリケーションにサインインできない問題

アクセス パネルは Web ベースのポータルで、ここから Azure Active Directory (Azure AD) の職場または学校アカウントを持つユーザーが、Azure AD 管理者によってアクセスを許可されたクラウドベースのアプリケーションを表示および起動することができます。 Azure AD のエディションを持つユーザーは、アクセス パネルを介してセルフサービスのグループおよびアプリ管理機能を利用することもできます。 アクセス パネルは Azure Portal から独立していて、ユーザーが Azure サブスクリプションを持っている必要はありません。

アクセス パネルでパスワード ベースのシングル サインオン (SSO) を使用するためには、ユーザーのブラウザーにアクセス パネルの拡張機能をインストールする必要があります。 このアクセス パネルの拡張機能は、ユーザーがパスワード ベースの SSO 用に構成されているアプリケーションを選択したときに、自動的にダウンロードされます。

## <a name="meeting-browser-requirements-for-the-access-panel"></a>アクセス パネルのブラウザーの要件を満たす

アクセス パネルには、JavaScript をサポートする CSS 対応のブラウザーが必要です。 アクセス パネルでパスワード ベースのシングル サインオン (SSO) を使用するためには、ユーザーのブラウザーにアクセス パネルの拡張機能をインストールする必要があります。 このアクセス パネルの拡張機能は、ユーザーがパスワード ベースの SSO 用に構成されているアプリケーションを選択したときに、自動的にダウンロードされます。

パスワードベースの SSO の場合、エンド ユーザーのブラウザーには次のいずれかを使用できます。

-   Internet Explorer 8、9、10、11 - Windows 7 以降

-   Chrome - Windows 7 以降、MacOS X 以降

-   Firefox 26.0 以降 - Windows XP SP2 以降、MacOS X 10.6 以降

>[!NOTE]
>パスワードベースの SSO の拡張機能は、ブラウザーの拡張機能が Windows 10 の Edge でサポートされるようになると、Edge で使用できるようになります。
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>アクセス パネルのブラウザー拡張機能をインストールする方法

アクセス パネルのブラウザー拡張機能をインストールするには、次の手順に従います。

1.  サポートされている任意のブラウザーで、[アクセス パネル](https://myapps.microsoft.com)を開き、Azure AD の**ユーザー**としてサインインします。

2.  アクセス パネルで、**パスワード SSO アプリケーション**をクリックします。

3.  ソフトウェアのインストールを確認するプロンプトで、**[今すぐインストール]** を選択します。

4.  お使いのブラウザーに基づいて、ダウンロード リンクが表示されます。 お使いのブラウザーに拡張機能を**追加**します。

5.  お使いのブラウザーに確認メッセージが表示されたら、拡張機能を **[有効にする]** または **[許可する]** のいずれかを選択します。

6.  インストールが完了したら、ブラウザー セッションを**再起動**します。

7.  アクセス パネルにサインインし、パスワード SSO アプリケーションを**起動**できるかどうかを確認します。

以下のダイレクト リンクから、Chrome および Firefox 対応の拡張機能をダウンロードすることもできます。

-   [Chrome アクセス パネル拡張機能](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox アクセス パネル拡張機能](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Internet Explorer のグループ ポリシーの設定

Internet Explorer 用のアクセス パネル拡張機能をユーザーのコンピューターにリモートでインストールできるようにグループ ポリシーを設定できます。

前提条件は次のとおりです。

-   [Active Directory ドメイン サービス](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)を設定し、ユーザーのコンピューターをドメインに参加させている必要があります。

-   グループ ポリシー オブジェクト (GPO) を編集するには、"設定の編集" アクセス許可が必要です。 既定では、Domain Administrators、Enterprise Administrators、Group Policy Creator Owners の各セキュリティ グループ メンバーにはこのアクセス許可があります。 [詳細情報](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)。

グループ ポリシーを構成し、ユーザーにデプロイする方法の詳しい手順については、「[グループ ポリシーを使用して Internet Explorer 用アクセス パネル拡張機能をデプロイする方法](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)」チュートリアルを参照してください。

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Internet Explorer でアクセス パネルをトラブルシューティングする

診断ツールへのアクセスと IE 対応の拡張機能を構成する方法の詳しい手順については、「[Internet Explorer 用アクセス パネル拡張機能のトラブルシューティング](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-Troubleshoot)」ガイドを参照してください。

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Azure AD ギャラリー アプリケーションのパスワード シングル サインオンを構成する方法

Azure AD ギャラリーからアプリケーションを構成するには、以下の手順を実行する必要があります。

-   [Azure AD ギャラリーからアプリケーションを追加する](#_Add_an_application)

-   [パスワード シングル サインオン (SSO) に対応するようにアプリケーションを構成する](#configure-the-application-for-password-single-sign-on)

-   [アプリケーションにユーザーを割り当てる](#assign-users-to-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Azure AD ギャラリーからアプリケーションを追加する

Azure AD ギャラリーからアプリケーションを追加するには、次の手順に従います。

1.  [Azure Portal](https://portal.azure.com) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの下部にある **[その他のサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  Azure Active Directory の左側にあるナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5.  **[エンタープライズ アプリケーション]** ブレードの右上隅にある **[追加]** ボタンをクリックします。

6.  **[ギャラリーから追加する]** セクションの **[名前を入力してください]** テキスト ボックスで、アプリケーションの名前を入力します。

7.  シングル サインオンを構成するアプリケーションを選択します。

8.  アプリケーションを追加する前に、**[名前]** テキストボックスから名前を変更できます。

9.  **[追加]** をクリックして、アプリケーションを追加します。

しばらくすると、アプリケーションの構成ブレードが表示されます。

### <a name="configure-the-application-for-password-single-sign-on"></a>パスワード シングル サインオン (SSO) に対応するようにアプリケーションを構成する

アプリケーションのシングル サインオンを構成するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの下部にある **[その他のサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  Azure Active Directory の左側にあるナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5.  **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

   * ここに表示したいアプリケーションが表示されない場合は、**[すべてのアプリケーション リスト]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

6.  シングル サインオンを構成するアプリケーションを選択します。

7.  アプリケーションの左側にあるナビゲーション メニューで **[シングル サインオン]** をクリックします。

8.  **[パスワード ベースのサインオン]** モードを選択します。

9.  [アプリケーションにユーザーを割り当てる](#_How_to_assign)

10. さらに、ユーザーの行を選び、**[資格情報の更新]** をクリックしてユーザーに代わってユーザー名とパスワードを入力すると、ユーザーに代わって資格情報を提供することもできます。 そうしないと、起動時に自分で資格情報を入力するように求めるプロンプトがユーザーに表示されます。

### <a name="assign-users-to-the-application"></a>アプリケーションにユーザーを割り当てる

1 人以上のユーザーをアプリケーションに直接割り当てるには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの下部にある **[その他のサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  Azure Active Directory の左側にあるナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5.  **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

   * ここに表示したいアプリケーションが表示されない場合は、**[すべてのアプリケーション リスト]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

6.  ユーザーを割り当てるアプリケーションを一覧から選びます。

7.  アプリケーションを読み込んだら、アプリケーションの左側にあるナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

8.  **[ユーザーとグループ]** リストの上部にある **[追加]** をクリックして、**[割り当ての追加]** ブレードを開きます。

9.  **[割り当ての追加]** ブレードの **[ユーザーとグループ]** セレクターをクリックします。

10. 割り当てる対象のユーザーの**フル ネーム**または**電子メール アドレス**を **[名前または電子メール アドレスで検索]** 検索ボックスに入力します。

11. リスト内で**ユーザー**にポインターを合わせ、**チェック ボックス**を表示します。 ユーザーのプロフィール写真またはロゴの横にあるチェック ボックスをオンにして、ユーザーを**[選択済み]** リストに追加します。

12. **省略可能:** **複数のユーザーを追加**する場合は、別の**フル ネーム**または**電子メール アドレス**を **[名前または電子メール アドレスで検索]** 検索ボックスに入力し、チェック ボックスをオンにして **[選択済み]** リストにこのユーザーを追加します。

13. ユーザーの選択が完了したら、**[選択]** ボタンをクリックして、アプリケーションに割り当てるユーザーとグループの一覧に追加します。

14. **省略可能:** **[割り当ての追加]** ブレードで **[ロールの選択]** セレクターをクリックし、選択したユーザーに割り当てるロールを選択します。

15. **[割り当て]** ボタンをクリックして、アプリケーションを選択したユーザーに割り当てます。

しばらくすると、選択したユーザーがアクセス パネルでこれらのアプリケーションを起動できるようになります。

## <a name="if-these-troubleshoot-steps-dont-resolve-the-issue"></a>これらのトラブルシューティング手順で問題が解決しない場合、 
使用可能な場合は、次の情報を含むサポート チケットを開きます。

-   関連エラー ID

-   UPN (ユーザーの電子メール アドレス)

-   TenantID

-   ブラウザーの種類

-   タイム ゾーンとエラーが発生した時刻/タイムフレーム

-   Fiddler のトレース

## <a name="next-steps"></a>次のステップ
[アプリケーション プロキシを使用してアプリにシングル サインオンを提供](active-directory-application-proxy-sso-using-kcd.md)

