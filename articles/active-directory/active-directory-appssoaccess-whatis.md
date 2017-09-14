---
title: "Azure Active Directory のアプリケーション アクセスとシングル サインオンとは  | Microsoft Docs"
description: "Azure Active Directory を使用して、業務上必要なすべての SaaS アプリケーションと Web アプリケーションへのシングル サインオンを有効にします。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 75d1a3fd-b3c5-4495-a5c8-c4c24145ff00
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: curtand
ms.reviewer: asmalser
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: cb41fbd7ca931df52ba8e5304459bb04298a108c
ms.contentlocale: ja-jp
ms.lasthandoff: 02/16/2017

---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>Azure Active Directory のアプリケーション アクセスとシングル サインオンとは
シングル サインオンとは、1 つのユーザー アカウントを使って 1 回サインインするだけで作業に必要なすべてのアプリケーションとリソースにアクセスできる機能です。 いったんサインインすると、もう一度認証 (パスワードの入力など) を求められることなく、必要なすべてのアプリケーションにアクセスできます。

多くの組織では、エンド ユーザーの生産性向上のため、Office 365、Box、Salesforce などのサービスとしてのソフトウェア (SaaS) アプリケーションに依存しています。 従来は、IT スタッフが各 SaaS アプリケーションのユーザー アカウントを個別に作成し、更新する必要がありました。さらに、ユーザーは、各 SaaS アプリケーションのパスワードを覚える必要がありました。

Azure Active Directory は、オンプレミスの Active Directory をクラウドに拡張して、ユーザーがプライマリ組織アカウントを使用してドメイン参加デバイスおよび会社のリソースにサインインするだけでなく、それぞれの業務に必要なすべての Web アプリケーションおよび SaaS アプリケーションにもサインインできるようにします。

これにより、ユーザーが複数のユーザー名とパスワードのセットを管理する必要がなくなるだけでなく、組織のグループ メンバーや従業員としての地位に基づいてアプリケーションのアクセスを自動的にプロビジョニングまたはプロビジョニング解除することが可能になります。 Azure Active Directory には、SaaS アプリケーション間でユーザー アクセスを一元的に管理するためのセキュリティおよびアクセス管理コントロールが導入されています。

Azure AD を使用すると、今日一般的に利用されているさまざまな SaaS アプリケーションに簡単に統合できます。ID およびアクセスの管理が提供されるため、ユーザーは、アプリケーションに直接 "シングル サインオン" することができ、Office 365 などのポータルや Azure AD アクセス パネルからアプリケーションを検出して開始することができます。

統合のアーキテクチャは、次の 4 つの主な構成要素で構成されます。

* シングル サインオンにより、ユーザーは、Azure AD 内の組織アカウントに基づいて SaaS アプリケーションにアクセスできます。 シングル サインオンは、ユーザーが 1 つの組織アカウントを使用してアプリケーションに対する認証を行うことを可能にします。
* ユーザー プロビジョニングは、Windows Server Active Directory や Azure AD で行われた変更に基づいてターゲット SaaS に対するユーザー プロビジョニングおよびプロビジョニング解除を行うことを可能にします。 プロビジョニングされたアカウントとは、ユーザーがシングル サインオンを介して承認された後にアプリケーションの使用が許可されるアカウントです。
* Azure 管理ポータルでの一元化されたアプリケーション アクセス管理により、1 か所での SaaS アプリケーションへのアクセスと管理が可能になるほか、アプリケーション アクセスに関する意思決定と承認を組織内のユーザーに委任できます。
* Azure AD でのユーザー アクティビティの統合されたレポートおよび監視

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Azure Active Directory によるシングル サインオンのしくみ
ユーザーがアプリケーションに "サインイン" すると、認証プロセスで自分の身元を証明する必要があります。 シングル サインオンを使用しない場合、通常は、アプリケーションに保存されているパスワードを入力してこの認証を行います。この場合、ユーザーはこのパスワードを知っている必要があります。

Azure AD では、アプリケーションにサインインする方法として次の 3 つをサポートしています。

* **フェデレーション シングル サインオン** は、アプリケーションが独自のパスワードの入力を求める代わりに、ユーザー認証のために Azure AD にリダイレクトできるようにします。 これは、SAML 2.0、WS-Federation、OpenID Connect などのプロトコルをサポートするアプリケーションでサポートされていて、シングル サインオンのモードのうちで最も多機能です。
* **パスワードベースのシングル サインオン** は、セキュリティで保護されたアプリケーションのパスワードの保存と、Web ブラウザーの拡張機能またはモバイル アプリを使用した再生を可能にします。 この場合、アプリケーションによって提供される既存のサインイン プロセスが利用されますが、管理者がパスワードを管理できる一方で、ユーザーがパスワードを知っている必要はありません。
* **既存のシングル サインオン** では、Azure AD がアプリケーションに対して設定されているすべての既存のシングル サインオンを利用できる一方、これらのアプリケーションを Office 365 または Azure AD アクセス パネル ポータルにリンクしたり、アプリケーションが Azure AD から起動されたときに追加のレポートを生成したりできます。

アプリケーションで認証されたユーザーに対しては、アプリケーションでアカウント レコードをプロビジョニングして、アプリケーションにおける権限とアクセス レベルをアプリケーションに示す必要があります。 このアカウント レコードのプロビジョニングは、自動的に実行することも、ユーザーにシングル サインオン アクセスが提供される前に管理者が手動で実行することもできます。

 以降では、これらのシングル サインオン モードとプロビジョニングについて詳しく説明します。

### <a name="federated-single-sign-on"></a>フェデレーション シングル サインオン
フェデレーション シングル サインオンは、Azure AD からのユーザー アカウント情報を使用して、組織のユーザーが Azure AD によってサードパーティの SaaS アプリケーションに自動的にサインインできるようにします。

このシナリオでは、Azure AD に既にログインしていて、サードパーティの SaaS アプリケーションによって制御されるリソースにアクセスする場合に、フェデレーションによりユーザーを再認証する必要がなくなります。

Azure AD では、SAML 2.0、WS-Federation、または OpenID Connect プロトコルをサポートするアプリケーションでフェデレーション シングル サインオンをサポートできます。

参照: [フェデレーション シングル サインオンのための証明書の管理](active-directory-sso-certs.md)

### <a name="password-based-single-sign-on"></a>パスワードベースのシングル サインオン
パスワードベースのシングル サインオンを構成すると、サードパーティの SaaS アプリケーションからのユーザー アカウント情報を使用して、組織のユーザーが Azure AD によってサードパーティの SaaS アプリケーションに自動的にサインインするように設定できます。 この機能を有効にした場合、ユーザー アカウント情報および関連するパスワードが Azure AD によって収集され、安全に格納されます。

Azure AD は、HTML ベースのサインイン ページを持つあらゆるクラウド ベース アプリに対して、パスワードベースのシングル サインオンをサポートできます。 AAD は、カスタム ブラウザー プラグインを使用して、ユーザーのサインイン プロセスを自動化します。つまり、ディレクトリからアプリケーション資格情報 (ユーザー名、パスワードなど) を安全に取得し、アプリケーションのサインイン ページにこれらの資格情報をユーザーに代わって入力します。 次の 2 つのユース ケースがあります。

1. **管理者が資格情報を管理する** – アプリケーションの資格情報を管理者が作成および管理し、アプリケーションにアクセスする必要のあるユーザーまたはグループにこれらの資格情報を割り当てます。 この場合、エンド ユーザーが資格情報を知る必要がない一方で、アクセス パネルまたは指定されたリンクを使用してクリックするだけで、アプリケーションへのシングル サインオン アクセスを実現できます。 これにより、管理者による資格情報のライフ サイクル管理と、エンド ユーザーがアプリケーション固有のパスワードを覚えたり管理したりする必要がないという利便性の両方が実現されます。 自動化されたサインイン プロセスの実行中、資格情報はエンド ユーザーに対して難読化されますが、技術的にはユーザーが Web デバッグ ツールを使用して識別することができます。ユーザーおよび管理者は、資格情報がユーザーによって直接指定された場合と同じく、同じセキュリティ ポリシーに従う必要があります。 管理者が提供する資格情報は、ソーシャル メディアやドキュメント共有アプリケーションなど、多くのユーザー間で共有されるアカウントのアクセスを提供するときに非常に便利です。
2. **ユーザーが資格情報を管理する** – 管理者は、エンド ユーザーまたはグループにアプリケーションを割り当てることができます。これにより、エンド ユーザーは、アクセス パネルで最初にアプリケーションにアクセスするときに自分の資格情報を直接入力できます。 この場合、アプリケーションにアクセスするたびにアプリケーション固有のパスワードを毎回入力する必要がないため、エンド ユーザーにとっての利便性が得られます。 このユース ケースは、資格情報の管理を実現する足がかりとしても使用できます。管理者は、エンド ユーザーのアプリケーション アクセス エクスペリエンスを変えることなく、将来アプリケーションの新しい資格情報を設定できます。

どちらの場合も、資格情報はディレクトリ内に暗号化された状態で格納され、自動化されたサインイン プロセス中にのみ HTTPS 経由で渡されます。 Azure AD は、パスワードベースのシングル サインオンを使用して、フェデレーション プロトコルをサポートしないアプリケーションに対して、便利な ID アクセス管理ソリューションを提供します。

パスワードベースの SSO は、ブラウザーの拡張機能に依存して、アプリケーションおよびユーザーに固有の情報を Azure AD から安全に取得してサービスに適用します。 Azure AD でサポートされているほとんどのサードパーティ SaaS アプリケーションは、この機能をサポートします。

パスワードベースの SSO の場合、エンド ユーザーのブラウザーには次のいずれかを使用できます。

* Internet Explorer 8、9、10、11 -- Windows 7 以降 (「 [グループ ポリシーを使用して Internet Explorer 用アクセス パネル拡張機能をデプロイする方法](active-directory-saas-ie-group-policy.md)」も参照)
* Chrome - Windows 7 以降、MacOS X 以降
* Firefox 26.0 以降 - Windows XP SP2 以降、Mac os X 10.6 以降

**注:** パスワードベースの SSO の拡張機能は、ブラウザーの拡張機能が Windows 10 の Edge でサポートされるようになると、Edge で使用できるようになります。

### <a name="existing-single-sign-on"></a>既存のシングル サインオン
Azure 管理ポータルでアプリケーションのシングル サインオンを構成する場合、第 3 のオプションとして "既存のシングル サインオン" が用意されています。 このオプションを選択した場合、管理者は、アプリケーションへのリンクを作成し、これを選択したユーザーのアクセス パネルに配置できます。

たとえば、Active Directory Federation Services 2.0 を使用してユーザーを認証するように構成されたアプリケーションがある場合、管理者は、[既存のシングル サインオン] オプションを使用して、アクセス パネルにそのアプリケーションへのリンクを作成することができます。 ユーザーがこのリンクにアクセスすると、ユーザーは Active Directory Federation Services 2.0 またはアプリケーションによって提供された任意の既存のシングル サインオン ソリューションを使用して認証されます。

### <a name="user-provisioning"></a>ユーザーのプロビジョニング
Azure AD では、特定のアプリケーションに対して、Windows Server Active Directory または Azure AD の ID 情報を使用して、Azure 管理ポータル内からサードパーティ製 SaaS アプリケーションのアカウントの自動ユーザー プロビジョニングおよびプロビジョニング解除を行うことができます。 Azure AD 内でこれらのアプリケーションの 1 つに対するアクセス許可をユーザーに与えると、ターゲット SaaS アプリケーションにアカウントを自動的に作成 (プロビジョニング) できます。

Azure AD 内でユーザーを削除するかまたはユーザー情報を変更すると、これらの変更は SaaS アプリケーションにも反映されます。 つまり、自動 ID ライフサイクル管理を構成すると、管理者は、自動化されたプロビジョニングおよびプロビジョニング解除を SaaS アプリケーションから制御および提供できます。 Azure AD において、この ID ライフサイクル管理の自動化は、ユーザー プロビジョニングによって有効になります。

詳細については、 [SaaS アプリケーションへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーの概要
使い始める準備はできていますか。 Azure AD と組織で使用する SaaS アプリケーションの間にシングル サインオンをデプロイする場合は、次のガイドラインに従ってください。

### <a name="using-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーの使用
[Azure Active Directory アプリケーション ギャラリー](https://azure.microsoft.com/marketplace/active-directory/all/) には、Azure Active Directory によるシングル サインオンの形式をサポートすることがわかっているアプリケーションの一覧が表示されます。

![][1]

サポートされている機能によってアプリを検索するためのヒントを次に示します。

* Azure AD は、 [Azure Active Directory アプリケーション ギャラリー](https://azure.microsoft.com/marketplace/active-directory/all/)のすべての "おすすめ" アプリについて、自動プロビジョニングおよびプロビジョニング解除をサポートしています。
* SAML、WS-Federation、OpenID Connect などのプロトコルを使用してフェデレーション シングル サインオンをサポートするフェデレーション アプリケーションの一覧については、 [このページ](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx)を参照してください。

アプリケーションが見つかったら、アプリケーション ギャラリーおよび Azure 管理ポータルに示される指示に従ってシングル サインオンを有効にします。

### <a name="application-not-in-the-gallery"></a>アプリケーションがギャラリーに見つからない
アプリケーションが Azure AD アプリケーション ギャラリーに見つからない場合は、次の操作を試してください。

* **使用している、一覧にないアプリを追加する** - Azure 管理ポータル内のアプリケーション ギャラリーでカスタム カテゴリを使用して、組織が使用している、一覧にないアプリケーションを接続します。 SAML 2.0 をサポートする任意のアプリケーション、または HTML ベースのサインイン ページをパスワード SSO アプリとして持つ任意のアプリケーションをフェデレーション アプリケーションとして追加できます。 詳細については、 [独自のアプリケーションの追加](active-directory-saas-custom-apps.md)に関する記事を参照してください。
* **開発した独自のアプリケーションを追加する** - 自分でアプリケーションを開発した場合は、Azure AD 開発者向けドキュメントのガイドラインに従って、Azure AD Graph API を使用してフェデレーション シングル サインオンまたはプロビジョニングを実装します。 詳細については、次のリソースを参照してください。
  
  * [Azure AD の認証シナリオ](active-directory-authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **アプリケーションの統合を要求する** - [Azure AD のフィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/)で必要なアプリケーションのサポートを要請します。

### <a name="using-the-azure-management-portal"></a>Azure 管理ポータルの使用
Azure 管理ポータルで Active Directory 拡張機能を使用して、アプリケーションのシングル サインオンを構成できます。 最初の手順として、ポータルの [Active Directory] セクションからディレクトリを選択する必要があります。

![][2]

サードパーティの SaaS アプリケーションを管理するには、選択したディレクトリの [アプリケーション] タブに切り替えます。 管理者は、このビューで次の操作を行うことができます。

* 開発しているアプリのほか、Azure AD ギャラリーから新しいアプリケーションを追加する。
* 統合アプリケーションを削除する。
* 統合済みのアプリケーションを管理する。

サードパーティの SaaS アプリケーションの一般的な管理タスクには、次の項目が含まれます。

* パスワード SSO を使用して、またはターゲット SaaS で使用可能な場合はフェデレーション SSO を使用して、Azure AD によるシングル サインオンを有効にします。
* 必要に応じて、ユーザー プロビジョニングを有効にして、ユーザー プロビジョニングおよびプロビジョニング解除 (ID ライフサイクル管理) を行います。
* ユーザー プロビジョニングが有効なアプリケーションに対し、そのアプリケーションへのアクセスが許可されるユーザーを選択します。

フェデレーション シングル サインオンをサポートするギャラリー アプリの場合、一般に、構成時に証明書やメタデータなどの追加の構成設定を指定して、サードパーティのアプリと Azure AD との間にフェデレーション信頼を作成する必要があります。 構成ウィザードに従うと、詳細な構成を設定でき、SaaS アプリケーションに固有のデータや手順に簡単にアクセスできます。

自動ユーザー プロビジョニングをサポートするギャラリー アプリの場合、SaaS アプリケーションでのアカウントを管理するための Azure AD のアクセス許可を与える必要があります。 少なくとも、ターゲット アプリケーションへの認証時に Azure AD が使用する必要がある資格情報を指定する必要があります。 追加の構成設定を指定する必要があるかどうかは、アプリケーションの要件によって異なります。

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>Azure AD 統合アプリケーションをユーザーにデプロイする
Azure AD では、カスタマイズ可能ないくつかの方法で組織内のエンド ユーザーにアプリケーションをデプロイできます。

* Azure AD アクセス パネル
* Office 365 アプリケーション起動プログラム
* フェデレーション アプリへの直接サインオン
* フェデレーション アプリ、パスワードベースのアプリ、または既存のアプリへのディープ リンク

管理者は、自分の判断で、組織内でのデプロイにどの方法を使用するかを決定することができます。

### <a name="azure-ad-access-panel"></a>Azure AD アクセス パネル
アクセス パネル (https://myapps.microsoft.com) は Web ベースのポータルで、Azure Active Directory ディレクトリの組織アカウントを持つエンド ユーザーが、Azure AD 管理者によってアクセスを許可されたクラウドベースのアプリケーションを表示および起動することができます。 [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/)のエンド ユーザーの場合、アクセス パネルを介してセルフサービスのグループ管理機能を利用することもできます。

![][3]

アクセス パネルは Azure 管理ポータルから独立しているため、ユーザーが Azure サブスクリプションまたは Office 365 サブスクリプションを持っている必要はありません。

Azure AD アクセス パネルの詳細については、「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を参照してください。

### <a name="office-365-application-launcher"></a>Office 365 アプリケーション起動プログラム
Office 365 をデプロイしている組織では、Azure AD を介してユーザーに割り当てられているアプリケーションは Office 365 ポータル (https://portal.office.com/myapps) にも表示されます。 これにより、組織のユーザーは、2 つ目のポータルを使用することなく簡単かつ手軽にアプリを起動できます。これは、Office 365 を使用する組織に対して推奨されるアプリ起動ソリューションです。

![][4]

Office 365 アプリケーション起動プログラムの詳細については、 [Office 365 アプリ起動プログラムにアプリを表示する方法](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)に関するページを参照してください。

### <a name="direct-sign-on-to-federated-apps"></a>フェデレーション アプリへの直接サインオン
SAML 2.0、WS-Federation、または OpenID Connect をサポートするほとんどのフェデレーション アプリでは、ユーザーがアプリケーションから開始し、自動リダイレクトかサインイン用リンクのクリックによって Azure AD 経由でサインインする機能もサポートしています。 これはサービス プロバイダーによって開始されるサインオンと呼ばれ、Azure AD アプリケーション ギャラリーのほとんどのフェデレーション アプリケーションでこの機能がサポートされています (詳細については、Azure 管理ポータルのアプリのシングル サインオンの構成ウィザードからリンクされているドキュメントを参照してください)。

![][5]

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>フェデレーション アプリ、パスワードベースのアプリ、または既存のアプリの直接サインオン リンク
Azure AD では、パスワードベースのシングル サインオン、既存のシングル サインオン、任意の形式のフェデレーション シングル サインオンをサポートする個々のアプリケーションへの直接シングル サインオン リンクもサポートされます。

これらのリンクは、特定のアプリケーションについてユーザーを Azure AD サインイン プロセスに送るための特別に生成された URL です。ユーザーは、Office 365 または Azure AD アクセス パネルからアプリケーションを起動する必要はありません。 これらのシングル サインオン URL は、次のスクリーン ショットに示すように、Azure 管理ポータルの [Active Directory] セクションの事前に統合されたアプリケーションの [ダッシュボード] タブに表示されます。

![][6]

これらのリンクをコピーして任意の場所に貼り付けることにより、選択したアプリケーションへのサインイン リンクを提供できます。 これらのリンクは、電子メールや、ユーザー アプリケーション アクセス用に設定した任意のカスタムの Web ベースのポータルに貼り付けることができます。 Twitter 用の Azure AD 直接シングル サインオン URL の例を次に示します。

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

アクセス パネルの組織に固有の URL と同様、myapps.microsoft.com ドメインの後にディレクトリのアクティブ ドメインまたは検証済みのドメインの 1 つを追加して、この URL をカスタマイズすることもできます。 これにより、サインイン ページで組織のブランド設定が即座に読み込まれ、ユーザーが最初にユーザー ID を入力する必要がなくなります。

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

許可されたユーザーがこれらのアプリケーションに固有のリンクのいずれかをクリックすると、最初に組織のサインイン ページ (まだ署名されていない状態を想定) が表示され、サインイン後は最初にアクセス パネルで停止することなくアプリにリダイレクトされます。 パスワードベースのシングル サインオン用ブラウザー拡張機能など、ユーザーがアプリケーションにアクセスするための前提条件を満たしていない場合、リンクをクリックすると、ユーザーは不足している拡張機能をインストールするよう要求されます。 アプリケーションのシングル サインオン構成が変更された場合でもリンク URL は変わりません。

これらのリンクにはアクセス パネルや Office 365 と同じアクセス制御メカニズムが使用され、Azure 管理ポータルでアプリケーションに割り当てられているユーザーまたはグループのみが正常に認証されます。 ただし、許可されていないユーザーには、アクセスが与えられていないことを示すメッセージと、アクセスが許可されている使用可能なアプリケーションを表示するアクセス パネルを呼び出すためのリンクが示されます。

## <a name="related-articles"></a>関連記事
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [承認されていないクラウド アプリケーションを Cloud App Discovery で検出する](active-directory-cloudappdiscovery-whatis.md)
* [アプリへのアクセスの管理](active-directory-managing-access-to-apps.md)
* [Azure Active Directory を使用して外部 ID を管理するための機能の比較](active-directory-b2b-compare-external-identities.md)

<!--Image references-->
[1]: ./media/active-directory-appssoaccess-whatis/onlineappgallery.png
[2]: ./media/active-directory-appssoaccess-whatis/azuremgmtportal.png
[3]: ./media/active-directory-appssoaccess-whatis/accesspanel.png
[4]: ./media/active-directory-appssoaccess-whatis/officeapphub.png
[5]: ./media/active-directory-appssoaccess-whatis/workdaymobile.png
[6]: ./media/active-directory-appssoaccess-whatis/deeplink.png

