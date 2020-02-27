---
title: インクルード ファイル
description: インクルード ファイル
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 5be6e7937a6e1f710b8e2576a9058963413fb6c2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984589"
---
1. [Azure portal](https://ms.portal.azure.com/) で、 **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** を選択します。

   [![Azure Active Directory での新しいアプリケーションの登録](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    アプリを登録すると、ここにそのアプリが表示されます。

1. アプリケーションの名前を指定し、 **[この組織のディレクトリ内のアカウントのみ]** を選択して、この API にアクセスできる **[サポートされているアカウントの種類]** を指定します。 認証されたユーザーのリダイレクト先となる有効な URI を選択し、 **[登録]** を選択します。

   [![Azure Active Directory にアプリケーションを作成する](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. 表示されているアプリの **[概要]** ブレードに、Azure Active Directory アプリの重要な情報が表示されます。 **[所有しているアプリ]** からアプリを選択し、 **[概要]** を選択します。

   [![アプリケーション ID をコピーする](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   使用する **[アプリケーション (クライアント) ID]** を、クライアント アプリケーション内にコピーします。

1. **[認証]** ブレードで、重要な認証構成設定を指定します。 

    1. **[+ プラットフォームを追加]** を選択して、 **[リダイレクト URI]** を追加し、 **[アクセス トークン]** を構成します。

    1. **[はい]** または **[いいえ]** を選択して、アプリが**パブリック クライアント**であるかどうかを指定します。

    1. サポートされているアカウントとテナントを確認します。

    [![暗黙的な許可を構成する](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. 適切なプラットフォームを選択したら、ユーザー インターフェイスの右側のサイド パネルで **[リダイレクト URI]** と **[アクセス トークン]** を構成します。

    1. **[リダイレクト URI]** は、認証要求で指定されたアドレスと一致する必要があります。

        * ローカル開発環境でホストされているアプリでは、 **[パブリック クライアント (モバイルとデスクトップ)]** を選択します。 **[パブリック クライアント]** は必ず **[はい]** に設定してください。
        * Azure App Service でホストされているシングルページ アプリでは、 **[Web]** を選択します。

    1. **[ログアウト URL]** が適切かどうかを確認します。

    1. **[アクセス トークン]** または **[ID トークン]** をオンにすることによって、暗黙的な許可のフローを有効にします。

    [![リダイレクト URI を作成する](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    **[構成]** 、 **[保存]** の順にクリックします。

1. **[証明書とシークレット]** を選択し、 **[新しいクライアント シークレット]** を選択して、クライアント アプリがその ID を証明する際に使用できるアプリケーション パスワードを作成します。

   [![新しいクライアント シークレットを生成する](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   クライアント シークレット パスワードが表示されます。 普段使用しているテキスト エディターにキーをコピーします。

   > [!NOTE]
   > 代わりに証明書をインポートすることもできます。 セキュリティを強化するには、証明書をお勧めします。 証明書を使用する場合は、 **[証明書のアップロード]** を選択してください。

1. お使いの Azure Active Directory アプリを Azure Time Series Insights に関連付けます。 **[API のアクセス許可]**  >  **[アクセス許可の追加]**  >  **[所属する組織で使用している API]** を選択します。 

    [![API をお使いの Azure Active Directory アプリに関連付ける](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   検索バーに「`Azure Time Series Insights`」と入力した後、`Azure Time Series Insights` を選択します。

1. 次に、お使いのアプリで必要な API のアクセス許可の種類を指定します。 既定では、 **[委任されたアクセス許可]** が強調表示されます。 アクセス許可の種類を選択した後、 **[アクセス許可の追加]** を選択します。

    [![お使いのアプリで必要な API のアクセス許可の種類を指定する](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
