---
title: マイ アプリ ポータルでアプリを検索して使用する - Azure AD
description: マイ アプリ ポータルを見つける方法と、組織のクラウドベース アプリへのアクセス方法について説明します。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: a5dae30c8b8f43602581b39005389211da83cb9e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022307"
---
# <a name="sign-in-and-start-apps-from-the-my-apps-portal"></a>マイ アプリ ポータルからアプリにサインインして開始する

職場または学校アカウントを Web ベースの**マイ アプリ** ポータルで使用して、次のことができます。

- 組織のクラウドベースのアプリの多くを表示して開始する
- プロファイルとアカウントの情報を更新する
- **グループ**の情報を表示する
- アプリとグループについての**アクセス レビュー**を実行する

**マイ アプリ** ポータルにアクセスできない場合は、ヘルプデスクに連絡してアクセス許可を得てください。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

> [!Important]
> このコンテンツは、マイ アプリのユーザーを対象としています。 管理者の方は、「[アプリケーション管理のドキュメント](https://docs.microsoft.com/azure/active-directory/manage-apps)」で、クラウドベースのアプリの設定と管理の方法を詳しくご覧いただけます。

## <a name="supported-browsers"></a>サポートされているブラウザー

次の Web ブラウザーのいずれかから、**マイ アプリ** ポータルにアクセスできます。

- Google Chrome

- Mozilla Firefox、バージョン 26.0 以降

- Microsoft Edge

- Internet Explorer、バージョン 11 (制限付きサポート)

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>マイ アプリによるセキュリティで保護されたサインイン拡張機能をダウンロードしてインストールする

求められた場合は、マイ アプリによるセキュリティで保護されたサインイン拡張機能をダウンロードしてインストールします。 この拡張機能は、シングル サインオン プロセスを使用する必要がある組織の任意のクラウド アプリを開始する場合に役立ちます。 既に組織によりシングル サインオンが設定されている場合は、拡張機能は自動的にインストールされ、このセクションをスキップできます。

この拡張機能は次の場合に役立ちます。

- サインイン ページから直接アプリにサインインする。

- **クイック検索**機能を使用して、アプリを開始する。

- **[最近の使用]** セクションで使用した最後のアプリを確認する。

- リモート時に[アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)を利用して社内 URL を使用する。

### <a name="to-download-and-install-the-extension"></a>拡張機能をダウンロードしてインストールするには

使用しているブラウザーに基づいて、拡張機能をダウンロードしてインストールします。

- **Google Chrome。** Chrome Web ストアから、[マイ アプリによるセキュリティで保護されたサインイン拡張](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl)機能に移動して、 **[Add to Chrome]** (Chrome に追加) を選択します。

- **Mozilla Firefox**。 **[Firefox Add-ons]** (Firefox アドオン) ページから、[マイ アプリによるセキュリティで保護されたサインイン拡張](https://addons.mozilla.org/firefox/addon/access-panel-extension/)機能に移動して、 **[Add to Firefox]** (Firefox に追加) を選択します。

- **Microsoft Edge**。Microsoft Store から、[マイ アプリによるセキュリティで保護されたサインイン拡張](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab)機能に移動して、 **[取得]** を選択します。

アイコンは**アドレス** バーの右側に追加され、サインインして拡張機能をカスタマイズできます。

## <a name="to-change-your-my-apps-portal-using-the-extension"></a>拡張機能を使用してマイ アプリ ポータルを変更するには

**[最近の使用]** セクションに表示するアプリの数を選択し、組織の内部 URL がリダイレクトできるようにするかどうかを決定できます。

1. **アドレス** バーの右側にある新しい**アプリによるセキュリティで保護されたサインイン拡張機能**アイコン![拡張機能アイコン](media/my-apps-portal/my-apps-portal-extension-icon.png)を選択し、続いて **[開始するにはサインインしてください]** を選択します。

2. **[設定]** アイコン![設定アイコン](media/my-apps-portal/my-apps-portal-extension-settings-icon.png) を右クリックし、 **[設定]** を選択します。

3. **[設定]** ボックスで、ポータルに表示する最近使用したアプリの数を選択し、リモートで使用できるように、組織の内部 URL のリダイレクトを許可するかどうかを選択します。

    ![拡張機能の [設定] ページ、使用可能なカスタマイズを表示](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>デバイスごとにマイ アプリ ポータルにアクセスして使用する

Intune で管理されるブラウザーから、または iOS または Android モバイル デバイスからコンピューター上のマイ アプリ ポータルにアクセスして使用できます。

![マイ アプリ ポータルの [アプリ] ページ](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>コンピューター上のマイ アプリ ポータルにアクセスして使用する

組織のクラウドベースのアプリにアクセスして使用するアクセス許可がある場合、**マイ アプリ** ポータルを通じてそれらに到達できます。

1. ご自分のコンピューター上でご自分の職場または学校アカウントにサインインします。

2. サポートされている Web ブラウザーで、 https://myapps.microsoft.com を開いて移動するか、組織から提供されている `https://myapps.microsoft.com/contoso.com` などのカスタマイズされたページへのリンクを使用します。

    **[アプリ]** ページが表示され、組織が所有していて、ユーザーが使用できるクラウドベースのアプリがすべて示されます。

3. **[アプリ]** ページから、使用開始するアプリを選択します。

    アプリの新しいページが開き、(必要に応じて) サインインしたり、アプリを使用開始できます。

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>Intune Managed Browser でマイ アプリ ポータルにアクセスして使用する

iOS および Android デバイスで Intune Managed Browser から組織のアプリを表示し使用します。

1. モバイル デバイス上では、[Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) および [Google Play ストア](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser)から Intune Managed Browser アプリをダウンロードしインストールします。

2. Intune Managed Browser アプリを開き、 https://myapps.microsoft.com に移動するか、組織から提供されている https://myapps.microsoft.com/contoso.com などのカスタマイズされたページへのリンクを使用します。

    **[アプリ]** ページが表示され、組織が所有し、お客様が利用できるクラウドベースのアプリがすべて示されます。

3. **[アプリ]** ページから、使用開始するアプリを選択します。

    アプリの新しいページが開き、(必要に応じて) サインインしたり、アプリを使用開始できます。

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>iOS デバイス上のマイ アプリ ポータルにアクセスして使用する

iOS バージョン 7 以降を実行している iPhone または iPad デバイスから **マイ アプリ** ポータルを表示および使用します。 [マイ アプリ モバイル アプリ](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)をインストールして、iOS デバイス上の組織のアプリにアクセスすることもできます。

1. モバイル デバイスでは、Safari などの Web ブラウザー アプリを起動します。

2. https://myapps.microsoft.com に移動するか、組織から提供されている https://myapps.microsoft.com/contoso.com などのカスタマイズされたページへのリンクを使用します。

    **[アプリ]** ページが表示され、組織が所有していて、ユーザーが使用できるクラウドベースのアプリがすべて示されます。

3. **[アプリ]** ページから、使用開始するアプリを選択します。

    アプリの新しいページが開き、(必要に応じて) サインインしたり、アプリを使用開始できます。

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>Android デバイス上のマイ アプリ ポータルにアクセスして使用する

Android デバイス上の**マイ アプリ** ポータルを表示して使用します。

1. モバイル デバイスでは、Google Chrome などの Web ブラウザー アプリを起動します。

2. https://myapps.microsoft.com に移動するか、組織から提供されている https://myapps.microsoft.com/contoso.com などのカスタマイズされたページへのリンクを使用します。

    **[アプリ]** ページが表示され、組織が所有し、お客様が利用できるクラウドベースのアプリがすべて示されます。

3. **[アプリ]** ページから、使用開始するアプリを選択します。

    アプリの新しいページが開き、(必要に応じて) サインインしたり、アプリを使用開始できます。

## <a name="add-a-new-app-to-the-my-apps-portal"></a>マイ アプリ ポータルに新しいアプリを追加する

管理者からアクセス許可が与えられている場合は、新しいアプリを **[アプリ]** ページに追加できます。

1. **[アプリ]** ページで、次のいずれかの操作を行います。
    - 元のマイ アプリのエクスペリエンスを使用している場合は、示されているように **[アプリの追加]** を選択します。

      ![[アプリの追加] ページ、マイ アプリ ポータル内](media/my-apps-portal/my-apps-portal-add-apps-page.png)

    - 更新されたマイ アプリのエクスペリエンスを使用している場合は、 **[Add self-service apps]\(セルフサービス アプリの追加\)** を選択します。

       ![myapplications.microsoft.com のマイ アプリ ポータルのアプリの追加ページ](media/my-apps-portal/my-apps-portal-add-app-link.png)

2. 指定されたリストから追加するアプリを選択し、 **[追加]** を選択します。

3. アプリは、**アプリ** ページ上のリストに追加されます。

    アプリの中には、追加される前に管理者の承認が必要なものがあります。 その場合、アプリは、管理者が承認するまで、 **[アプリ]** ページに追加されません。

## <a name="start-a-cloud-based-app"></a>クラウドベースのアプリを起動する

**マイ アプリ** ポータルからは、使用可能な任意のクラウドベースのアプリを起動することができます。 表示されるアプリは、使用するアクセス許可があるアプリのみです。

- **[アプリ]** ページから、使用開始するアプリを選択します。

    アプリの新しいページが開き、(必要に応じて) サインインしたり、アプリを使用開始できます。

## <a name="activities-in-the-my-apps-portal"></a>マイ アプリ ポータルでのアクティビティ

**マイ アプリ** ポータルを開いたら、次の操作を実行できます。

- 自分のアプリを、組織が作成して提供しているさまざまなカテゴリに整理できます。 詳細については、[マイ アプリ ポータル内のコレクションへのアクセスと使用](my-applications-portal-workspaces.md)に関する記事を参照してください。

- アプリケーションに付与されたアクセス許可を確認し、更新し、取り消します。 詳細については、「[マイ アプリ ポータルでのアプリケーションのアクセス許可の編集または取り消し](my-applications-portal-permissions-saved-accounts.md)」を参照してください。

>[!Note]
>使用可能なコレクションまたはカテゴリが表示されない場合は、管理者が設定していないか、共有されているものがないことを意味します。 共有コレクションを表示するための追加のサポートやアクセス許可については、組織のヘルプデスクに問い合わせてください。

## <a name="next-steps"></a>次のステップ

**アプリ** ページに到達した後、次の操作を実行できます。

- [プロファイルの情報を変更する](my-apps-portal-end-user-update-profile.md)

- [グループ関連の情報を表示し、更新する](my-apps-portal-end-user-groups.md)

- [自分のアクセス レビューを実行する](my-apps-portal-end-user-access-reviews.md)
