---
title: "よく寄せられる質問 (FAQ) - Azure AD B2C | Microsoft Docs"
description: "Azure Active Directory B2C についてよく寄せられる質問"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: bryanla
ms.assetid: ed33c2ca-76d0-442a-abb1-8b7b7bb92d6a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.translationtype: HT
ms.sourcegitcommit: 368589509b163cacf495fd0be893a8953fe2066e
ms.openlocfilehash: e8b28bc9ccc12b280b1746272519bd4c9ea9e4a4
ms.contentlocale: ja-jp
ms.lasthandoff: 08/17/2017

---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: よく寄せられる質問 (FAQ) 
このページには、Azure Active Directory (Azure AD) B2C に関してよく寄せられる質問への回答が記載されています。 常に最新情報をチェックしてください。

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>既存の従業員ベースの Azure AD テナントで Azure AD B2C 機能を使用できますか。
Azure AD と Azure AD B2C は、別個の製品であるため、同じテナントで共存させることはできません。  Azure AD テナントは、組織を表します。  Azure AD B2C テナントは、証明書利用者アプリケーションで使用される ID のコレクションを表します。  Azure AD B2C では、カスタム ポリシー (パブリック プレビュー中) を使用して、Azure AD にフェデレーションし、組織の従業員の認証を許可することができます。

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Azure AD B2C を使用してソーシャル ログイン (Facebook および Google+) を Office 365 に提供することはできますか。
Azure AD B2C は、Microsoft Office 365 のユーザーの認証に使用できません。  Azure AD は、SaaS アプリへの従業員のアクセスを管理するための Microsoft のソリューションであり、その目的で設計された機能 (ライセンス付与や条件付きアクセスなど) を備えています。  Azure AD B2C には、Web およびモバイル アプリケーションを構築するための ID およびアクセスの管理プラットフォームが用意されています。  Azure AD テナントにフェデレーションするように Azure AD B2C を構成すると、Azure AD テナントでは、Azure AD B2C に依存するアプリケーションへの従業員のアクセスが管理されます。

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Azure AD B2C のローカル アカウントとは何ですか。 それらは、Azure AD の職場または学校アカウントとはどのような点が異なるのですか。
Azure AD テナントでは、テナントに属するユーザーは、`<xyz>@<tenant domain>` の形式の電子メール アドレスを使用してサインインします。  `<tenant domain>` は、テナントの確認済みドメインの 1 つ、または初期の `<...>.onmicrosoft.com` ドメインです。 この種類のアカウントは、職場または学校アカウントです。

Azure AD B2C テナントでは、ユーザーは大部分のアプリに任意のメール アドレス (joe@comcast.net、bob@gmail.com、sarah@contoso.com、jim@live.com など) を使用してサインインします。 この種類のアカウントはローカル アカウントです。  ローカル アカウントとして任意のユーザー名もサポートしています (joe、bob、sarah、jim など)。 Azure Portal で Azure AD B2C サービスを構成することで、これらの 2 種類のローカル アカウントのいずれかを選択できます。

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>現在サポートされているソーシャル ID プロバイダーはどれですか。 将来サポートする予定のプロバイダーはどれですか。
現在、Facebook、Google+、LinkedIn、Amazon、Twitter (プレビュー)、WeChat (プレビュー)、Weibo (プレビュー)、および QQ (プレビュー) をサポートしています。 お客様のご要望に基づいて、他の人気のあるソーシャル ID プロバイダーのサポートを追加する予定です。

Azure AD B2C でも[カスタム ポリシー](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview-custom)のサポートがを追加されています。  開発者は、これらの[カスタム ポリシー](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview-custom)を使用して、[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) または SAML をサポートする任意の ID プロバイダーで独自のポリシーを作成できます。 

[カスタム ポリシー スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)を使ってカスタム ポリシーの作成をお試しください。

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>さまざまなソーシャル ID プロバイダーからお客様に関する情報をさらに収集するためにスコープを構成できますか。
いいえ。ただし、この機能は検討中です。 サポートされている一連のソーシャル ID プロバイダーに使用されている、既定のスコープは次のとおりです。

* Facebook: 電子メール
* Google +: 電子メール
* Microsoft アカウント: openid 電子メール プロファイル
* Amazon: プロファイル
* LinkedIn: r_emailaddress、r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>使用しているアプリケーションを Azure AD B2C で機能させるには、Azure で実行する必要がありますか。
いいえ。アプリケーションは任意の場所でホストできます (クラウドまたはオンプレミス)。 Azure AD B2C とやり取りするために必要なのは、パブリックにアクセス可能なエンドポイントで HTTP 要求を送受信する機能のみです。

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>複数の Azure AD B2C テナントがあります。 Azure Portal でこれらのテナントを管理するにはどうすればよいですか。
Azure ポータルの左側にあるメニューの [Azure AD B2C] を開く前に、管理するディレクトリに切り替える必要があります。  Azure ポータルの右上にある自分の ID をクリックしてディレクトリを切り替えた後、表示されるドロップダウンからディレクトリを選択します。  図を使用した詳細な手順については、「[Azure AD B2C の設定に移動する](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)」を参照してください。

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Azure AD B2C によって送信される検証電子メールをカスタマイズするにはどうすればいいですか (コンテンツおよび "From:" フィールド)。
検証電子メールの内容をカスタマイズするには、 [会社のブランド化機能](../active-directory/active-directory-add-company-branding.md) を使用します。 具体的には、電子メールの次の 2 つの要素をカスタマイズできます。

* **バナー ロゴ**: 右下に表示されます。
* **背景色**: 上部に表示されます。

    ![カスタマイズされた検証電子メールのスクリーンショット](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

電子メールの署名には、最初に B2C テナントを作成したときに指定した B2C テナントの名前が含まれます。 名前は次の手順を使用して変更できます。

1. サブスクリプション管理者として [Azure クラシック ポータル](https://manage.windowsazure.com/) にサインインします。
1. B2C テナントに移動します。
1. [ **構成** ] タブをクリックします。
1. **[ディレクトリのプロパティ]** セクションの **[名前]** フィールドを変更します。
1. ページの下部にある **[保存]** をクリックします。

現在、電子メールの送信元フィールドを変更する方法はありません。 [feedback.azure.com](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails) で、検証電子メールの本文のカスタマイズについてのご要望を投稿してください。

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>既存のユーザー名、パスワード、およびプロファイルを自分のデータベースから Azure AD B2C に移行するにはどのようにすればいいですか。
Azure AD Graph API を使用して、移行ツールを作成できます。 詳細については、 [Graph API サンプル](active-directory-b2c-devquickstarts-graph-dotnet.md) に関するページを参照してください。

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Azure AD B2C のローカル アカウントに使用されるパスワード ポリシーはどのようなものですか。
Azure AD B2C のローカル アカウントのパスワード ポリシーは Azure AD のポリシーに基づいています。 Azure AD B2C のサインアップ、サインアップまたはサインイン、パスワード リセットの各ポリシーでは、"強力な" パスワード強度を使用しており、いずれのパスワードにも有効期限がありません。 詳細については、 [Azure AD のパスワード ポリシー](https://msdn.microsoft.com/library/azure/jj943764.aspx) に関するページを参照してください。

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Azure AD Connect を使用して、自分のオンプレミス Active Directory に保存されているお客様の ID を Azure AD B2C に移行できますか。
いいえ。Azure AD Connect は Azure AD B2C と連携するようには設計されていません。 ユーザーの移行には、[Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) を使用することを検討してください。

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>アプリで Azure AD B2C ページを iFrame 内で開くことはできますか。
いいえ。セキュリティ上の理由から、Azure AD B2C ページを iFrame 内で開くことはできません。  このサービスは、iFrame を禁止するためにブラウザーと通信します。  一般のセキュリティ コミュニティと OAUTH2 仕様では、ID エクスペリエンスに iFrame を使用しないことを推奨しています。これは、クリックジャッキングの危険があるためです。

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C は Microsoft Dynamics のような CRM システムと連携しますか。
Microsoft Dynamics 365 ポータルとの統合を使用できます。  [Azure AD B2C を使用して認証するための Dynamics 365 ポータルの構成](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/portals/azure-ad-b2c)に関する記事を参照してください。

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C はオンプレミスの SharePoint 2016 以前と連携しますか。
Azure AD B2C は、SharePoint 外部パートナー共有のシナリオには適していません。この記事ではなく、[Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) に関する記事を参照してください。

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>外部 ID を管理するために Azure AD B2C または B2B を使用する必要がありますか。
外部 ID のシナリオに適した機能を使用する方法の詳細については、 [外部 ID](../active-directory/active-directory-b2b-compare-external-identities.md) に関するこの記事を参照してください。

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Azure AD B2C ではどのようなレポート機能と監査機能が提供されますか。 それは Azure AD Premium の機能と同じですか。
いいえ。Azure AD B2C では Azure AD Premium と同じレポート セットはサポートされていません。 ただし、多くの共通点があります。

* サインイン レポートは、各サインインの記録を、詳細は除外して提供します。
* 監査レポートは、Azure Portal で利用できます。[Azure Active Directory]、[アクティビティ]、[監査ログ]、[B2C] の順に選択し、必要に応じてフィルターを適用します。 管理アクティビティとアプリケーション アクティビティの両方が対象となります。 
* ユーザー数、ログイン数、および MFA の数を対象とする使用状況レポートは、[Usage Reporting API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-usage-reporting-api) で利用できます。

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Azure AD B2C で提供されているページの UI をローカライズできますか。 どの言語がサポートされていますか。
はい。  [言語のカスタマイズ](active-directory-b2c-reference-language-customization.md) (パブリック プレビュー中) に関する記事を確認してください。  Microsoft では、36 言語の翻訳を提供しおり、お客様は、ニーズに合わせて任意の文字列を上書きすることができます。

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Azure AD B2C によって提供されているサインアップおよびサインイン ページで独自の URL を使用できますか。 たとえば、URL を login.microsoftonline.com から login.contoso.com に変更できますか。
現時点では連携しません。 この機能は検討中です。 Azure クラシック ポータルの **[ドメイン]** タブでドメインを確認しても、この目的は達成できません。

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを削除する方法はありますか。
Azure AD B2C テナントを削除するには、次の手順に従います。

1. Azure ポータルで、[Azure AD B2C の設定に移動する](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)ための手順に従います。
1. **[アプリケーション]**、**[ID プロバイダー]**、**[すべてのポリシー]** の順に移動しますが、それぞれですべてのエントリを削除します。
1. 次に、サブスクリプション管理者として [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします  (Azure へのサインアップに使用したものと同じ職場/学校アカウント、または同じ Microsoft アカウントを使用します)。
1. 左側の Active Directory 拡張機能に移動し、B2C テナントをクリックします。
1. **[Users]** タブをクリックします。
1. 各ユーザーを順に選択します (ただし、現在サインインに使用しているサブスクリプション管理者ユーザーは除きます)。 ページ下部の **[削除]** をクリックし、確認プロンプトに **[はい]** をクリックします。
1. **[アプリケーション]** タブをクリックします。
1. **[表示]** ドロップダウン フィールドで **[自分の会社が所有するアプリケーション]** を選択し、チェック マークをクリックします。
1. **b2c-extensions-app** というアプリケーションが表示されます。 ページ下部の **[削除]** をクリックし、確認プロンプトに **[はい]** をクリックします。
1. Active Directory 拡張機能にもう一度移動し、B2C テナントをクリックします。
1. ページの下部にある **[削除]** をクリックします。 プロセスを完了するには、画面に表示される指示に従ってください。

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Enterprise Mobility Suite の一部として Azure AD B2C を取得できますか。
いいえ。Azure AD B2C は従量課金の Azure サービスであり、Enterprise Mobility Suite には含まれていません。

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Azure AD B2C に関する問題を報告するにはどのようにすればいいですか。
[Azure Active Directory B2C のサポート要求の提出](active-directory-b2c-support.md)に関するページを参照してください。

