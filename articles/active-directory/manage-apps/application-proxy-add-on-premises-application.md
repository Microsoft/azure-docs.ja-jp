---
title: チュートリアル - オンプレミス アプリの追加 - Azure AD でのアプリケーション プロキシ
description: Azure Active Directory (Azure AD) のアプリケーション プロキシ サービスを使用すると、ユーザーは Azure AD アカウントでサインインして、オンプレミスのアプリケーションにアクセスできます。 このチュートリアルでは、アプリケーション プロキシで使用できるように環境を準備する方法について説明します。 次に、Azure portal を使用して、自分の Azure AD テナントにオンプレミス アプリケーションを追加します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: e44de0058af0210ecb42eaa4be8b55d543d66103
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212813"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>チュートリアル:Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する

Azure Active Directory (Azure AD) のアプリケーション プロキシ サービスを使用すると、ユーザーは Azure AD アカウントでサインインして、オンプレミスのアプリケーションにアクセスできます。 このチュートリアルでは、アプリケーション プロキシで使用できるように環境を準備します。 環境の準備ができたら、Azure portal を使用して Azure AD テナントにオンプレミス アプリケーションを追加します。

このチュートリアルの内容:

> [!div class="checklist"]
> * アウトバウンド トラフィック用のポートを開き、特定の URL へのアクセスを許可します
> * Windows サーバーにコネクタをインストールし、アプリケーション プロキシに登録します
> * コネクタが正しくインストールおよび登録されていることを確認します
> * Azure AD テナントにオンプレミスのアプリケーションを追加します
> * テスト ユーザーが Azure AD アカウントを使用してアプリケーションにサインオンできることを確認します

## <a name="prerequisites"></a>前提条件

オンプレミスのアプリケーションを Azure AD に追加するには、次が必要です。

* [Microsoft Azure AD の Premium サブスクリプション](https://azure.microsoft.com/pricing/details/active-directory)
* アプリケーション管理者アカウント
* ユーザー ID をオンプレミス ディレクトリから同期するか、Azure AD テナント内に直接作成する必要があります。 ID 同期によって、Azure AD が、アプリケーション プロキシが発行したアプリケーションへのアクセス権をユーザーに付与する前にユーザーを事前に認証でき、シングル サインオン (SSO) を実行するために必要なユーザー ID 情報を得ることができます。

### <a name="windows-server"></a>Windows サーバー

アプリケーション プロキシを使用するには、Windows Server 2012 R2 以降を実行している Windows サーバーが必要です。 アプリケーション プロキシ コネクタをサーバーにインストールしてください。 このコネクタ サーバーは、Azure 内のアプリケーション プロキシ サービスと、公開予定のオンプレミス アプリケーションに接続する必要があります。

運用環境を高可用性にするため、複数の Windows サーバーを使用することをお勧めします。 このチュートリアルでは、1 つの Windows サーバーで十分です。

> [!IMPORTANT]
> Windows Server 2019 にコネクタをインストールする場合は、Kerberos の制約付き委任が正しく動作するようにするために、WinHttp コンポーネントで HTTP2 プロトコルのサポートを無効にする必要があります。 それよりも前のバージョンのサポート対象オペレーティング システムでは、これが既定で無効になっています。 Windows Server 2019 では、次のレジストリ キーを追加してサーバーを再起動すれば無効になります。 これはマシン レベルのレジストリ キーであることに注意してください。
>
> ```
> Windows Registry Editor Version 5.00
> 
> [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp] "EnableDefaultHttp2"=dword:00000000
> ```
>

#### <a name="recommendations-for-the-connector-server"></a>コネクタ サーバーの推奨事項

1. コネクタとアプリケーション間のパフォーマンスを最適化するため、アプリケーション サーバーと物理的に近い場所にコネクタ サーバーを配置します。 詳しくは、「[ネットワーク トポロジに関する考慮事項](application-proxy-network-topology.md)」をご覧ください。
1. コネクタ サーバーと Web アプリケーション サーバーは、同じ Active Directory ドメインに属しているか、または信頼する側のドメインの範囲である必要があります。 統合 Windows 認証 (IWA) および Kerberos 制約付き委任 (KCD) でのシングル サインオン (SSO) を使用するには、サーバーを同じドメインまたは信頼する側のドメインに配置する必要があります。 コネクタ サーバーと Web アプリケーション サーバーが別の Active Directory ドメイン内にある場合は、シングル サインオン用にリソースベースの委任を使用する必要があります。 詳しくは、「[KCD for single sign-on with Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)」 (アプリケーション プロキシを使用したシングル サインオンのための KCD) をご覧ください。

> [!WARNING]
> Azure AD パスワード保護プロキシをデプロイした場合は、Azure AD アプリケーション プロキシと Azure AD パスワード保護プロキシを同じマシンに一緒にインストールすることは避けてください。 Azure AD アプリケーション プロキシと Azure AD パスワード保護プロキシでは、インストールされる Azure AD Connect Agent Updater サービスのバージョンが異なります。 両者を同じマシンに一緒にインストールした場合、バージョン間の不整合が生じます。

#### <a name="tls-requirements"></a>TLS の要件

アプリケーション プロキシ コネクタをインストールするには、Windows コネクタ サーバーで TLS 1.2 が有効になっている必要があります。

TLS 1.2 を有効にするには、次の手順に従います。

1. 次のレジストリ キーを設定します。
    
   ```
   Windows Registry Editor Version 5.00

   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   "SchUseStrongCrypto"=dword:00000001
   ```

1. サーバーを再起動します。

## <a name="prepare-your-on-premises-environment"></a>オンプレミスの環境を準備する

Azure AD アプリケーション プロキシの環境を準備するには、まず Azure データ センターへの通信を有効にします。 パスにファイアウォールがある場合、それが開かれていることを確認します。 ファイアウォールが開かれていることで、コネクタによるアプリケーション プロキシへの HTTPS (TCP) 要求が可能になります。

> [!IMPORTANT]
> Azure Government クラウドのコネクタをインストールする場合は、[前提条件](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#allow-access-to-urls) と [インストール手順](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#install-the-agent-for-the-azure-government-cloud)に従います。 これには、別の URL のセットへのアクセスを有効にし、インストールを実行するための追加のパラメーターが必要です。

### <a name="open-ports"></a>ポートを開く

以下の各ポートを**アウトバウンド** トラフィックに対して開きます。

   | ポート番号 | 用途 |
   | --- | --- |
   | 80 | TLS または SSL 証明書の検証時に証明書失効リスト (CRL) をダウンロードする |
   | 443 | アプリケーション プロキシ サービスとのすべての送信通信 |

ファイアウォールが送信元ユーザーに応じてトラフィックを処理している場合は、ネットワーク サービスとして実行されている Windows サービスからのトラフィック用にポート 80 と 443 も開きます。

### <a name="allow-access-to-urls"></a>URL へのアクセスを許可する

次の URL へのアクセスを許可します。

| URL | 用途 |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | コネクタとアプリケーション プロキシ クラウド サービスの間の通信 |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | コネクタでは、証明書の検証にこれらの URL が使用されます。 |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>\*.microsoftonline.com<br>\*.microsoftonline-p.com<br>\*.msauth.net<br>\*.msauthimages.net<br>\*.msecnd.net<br>\*.msftauth.net<br>\*.msftauthimages.net<br>\*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com:80 | コネクタでは、登録プロセスの間にこれらの URL が使用されます。 |

ファイアウォールまたはプロキシで DNS 許可リストを構成できる場合は、\*.msappproxy.net と \*.servicebus.windows.net への接続を許可できます。 そうでない場合は、[Azure IP ranges and Service Tags - Public Cloud (Azure IP 範囲とサービス タグ - パブリック クラウド)](https://www.microsoft.com/download/details.aspx?id=56519) へのアクセスを許可する必要があります。 これらの IP 範囲は毎週更新されます。

## <a name="install-and-register-a-connector"></a>コネクタのインストールと登録

アプリケーション プロキシを使用するには、アプリケーション プロキシ サービスで使用している各 Windows サーバーにコネクタをインストールします。 コネクタは、オンプレミスのアプリケーション サーバーから Azure AD のアプリケーション プロキシへのアウトバウンド接続を管理するエージェントです。 コネクタをインストールするサーバーには、Azure AD Connect などの他の認証エージェントがインストールされていてもかまいません。


コネクタをインストールするには:

1. アプリケーション プロキシを使用するディレクトリのアプリケーション管理者として、[Azure portal](https://portal.azure.com/) にサインインします。 たとえば、テナントのドメインが contoso.com の場合、管理者は admin@contoso.com またはそのドメイン上の他の管理者エイリアスであることが必要です。
1. 右上隅で自分のユーザー名を選択します。 アプリケーション プロキシを使用するディレクトリにサインインしていることを確認します。 ディレクトリを変更する必要がある場合は、 **[ディレクトリの切り替え]** を選択し、アプリケーション プロキシを使用するディレクトリを選択します。
1. 左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。
1. **[管理]** で、 **[アプリケーション プロキシ]** を選択します。
1. **[コネクタ サービスのダウンロード]** を選択します。

    ![コネクタ サービスをダウンロードしてサービス使用条件を表示](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. サービス利用規約を読みます。 準備ができたら、 **[規約に同意してダウンロード]** を選択します。
1. ウィンドウの下部で、 **[実行]** を選択してコネクタをインストールします。 インストール ウィザードが開きます。
1. ウィザードの指示に従ってサービスをインストールします。 Azure AD テナントのアプリケーション プロキシにコネクタを登録するよう求められたら、アプリケーション管理者の資格情報を提供します。
    - Internet Explorer (IE) では、 **[IE セキュリティ強化の構成]** が **[オン]** に設定されていると、登録画面が表示されないことがあります。 アクセスするには、エラー メッセージに示された指示に従ってください。 **[Internet Explorer セキュリティ強化の構成]** が **[オフ]** に設定されていることを確認します。

### <a name="general-remarks"></a>一般的な注釈

以前にコネクタをインストールした場合は、再インストールして最新バージョンにします。 過去にリリースされたバージョンと変更履歴については、次を参照してください: [アプリケーション プロキシのバージョン リリース履歴](application-proxy-release-version-history.md)。

オンプレミス アプリケーション用に複数の Windows サーバーを選択する場合は、サーバーごとにコネクタをインストールして登録する必要があります。 コネクタはコネクタ グループに整理できます。 詳しくは、[コネクタ グループ](application-proxy-connector-groups.md)に関するページをご覧ください。

お客様の組織がプロキシ サーバーを使用してインターネットに接続する場合は、それらをアプリケーション プロキシ用に構成する必要があります。  詳しくは、「[既存のオンプレミス プロキシ サーバーと連携する](application-proxy-configure-connectors-with-proxy-servers.md)」をご覧ください。 

コネクタ、能力計画、コネクタを最新の状態に維持する方法については、「[Azure AD アプリケーション プロキシ コネクタを理解する](application-proxy-connectors.md)」をご覧ください。

## <a name="verify-the-connector-installed-and-registered-correctly"></a>コネクタが正しくインストールおよび登録されていることを確認する

Azure portal または Windows サーバーを使用して、新しいコネクタが正しくインストールされていることを確認できます。

### <a name="verify-the-installation-through-azure-portal"></a>Azure portal を介してインストールを確認する

コネクタが正しくインストールおよび登録されていることを確認するには:

1. [Azure portal](https://portal.azure.com) でお使いのテナント ディレクトリにサインインします。
1. 左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択し、 **[管理]** セクションの **[アプリケーション プロキシ]** を選択します。 コネクタとコネクタ グループはすべてこのページに表示されます。
1. 詳細を確認するコネクタを表示します。 コネクタは既定で展開されているはずです。 表示したいコネクタが展開されていない場合、そのコネクタを展開して詳細を表示します。 アクティブな緑色のラベルは、コネクタがサービスに接続できることを示します。 ただし、ラベルが緑色であっても、ネットワークの問題のためにコネクタがメッセージを受信できない可能性があります。

    ![Azure AD アプリケーション プロキシ コネクタ](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

コネクタのインストールの詳細については、[アプリケーション プロキシ コネクタのインストール時の問題](application-proxy-connector-installation-problem.md)に関するページを参照してください。

### <a name="verify-the-installation-through-your-windows-server"></a>Windows サーバーを介してインストールを確認する

コネクタが正しくインストールおよび登録されていることを確認するには:

1. **Windows** キーをクリックして「*services.msc*」と入力し、Windows サービス マネージャーを開きます。
1. 次の 2 つのサービスの状態が **[実行中]** であることを確認します。
   - **Microsoft AAD アプリケーション プロキシ コネクタ**により、接続が有効になります。
   - **Microsoft AAD アプリケーション プロキシ コネクタ アップデーター**は自動更新サービスです。 アップデーターはコネクタの新しいバージョンをチェックし、必要に応じてコネクタを更新します。

     ![App Proxy Connector services - screenshot](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. サービスの状態が **[実行中]** でない場合は、各サービスを右クリックして選択し、 **[開始]** を選択します。

## <a name="add-an-on-premises-app-to-azure-ad"></a>オンプレミス アプリを Azure AD に追加する

環境を準備してコネクタをインストールしたので、Azure AD にオンプレミス アプリケーションを追加する準備ができました。  

1. [Azure Portal](https://portal.azure.com/) に管理者としてサインインします。
2. 左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。
3. **[エンタープライズ アプリケーション]** を選択し、 **[新しいアプリケーション]** を選択します。
4. **[オンプレミスのアプリケーション]** セクションで、 **[オンプレミスのアプリケーションの追加]** を選択します。
5. **[独自のオンプレミスのアプリケーションの追加]** セクションで、自分のアプリケーションについて次の情報を指定します。

    | フィールド | 説明 |
    | :---- | :---------- |
    | **名前** | アクセス パネルと Azure portal に表示されるアプリケーションの名前。 |
    | **内部 URL** | プライベート ネットワークの内部からアプリケーションにアクセスするための URL。 バックエンド サーバー上の特定のパスを指定して発行できます。この場合、サーバーのそれ以外のパスは発行されません。 この方法では、同じサーバー上の複数のサイトを別々のアプリとして発行し、それぞれに独自の名前とアクセス規則を付与することができます。<br><br>パスを発行する場合は、アプリケーションに必要な画像、スクリプト、スタイル シートが、すべてそのパスに含まれていることを確認してください。 たとえば、アプリケーションが https:\//yourapp/app にあり、https:\//yourapp/media にある画像を使用する場合は、パスとして https:\//yourapp/ を発行する必要があります。 この内部 URL は、ユーザーに表示されるランディング ページである必要はありません。 詳細については、「[発行されたアプリのカスタム ホーム ページを設定する](application-proxy-configure-custom-home-page.md)」を参照してください。 |
    | **外部 URL** | ユーザーがネットワークの外部からアプリにアクセスするためのアドレス。 既定のアプリケーション プロキシ ドメインを使用しない場合は、[Azure AD アプリケーション プロキシのカスタム ドメイン](application-proxy-configure-custom-domain.md)に関する記事を参照してください。|
    | **事前認証** | ユーザーにアプリケーションへのアクセス権を付与する前にアプリケーション プロキシがユーザーを認証する方法。<br><br>**Azure Active Directory** - アプリケーション プロキシによってユーザーが Azure AD のサインイン ページにリダイレクトされます。これにより、ディレクトリとアプリケーションに対するユーザーのアクセス許可が認証されます。 このオプションは、条件付きアクセスや Multi-Factor Authentication など、Azure AD のセキュリティ機能を活用できるように、既定のままにしておくことをお勧めします。 Microsoft Cloud Application Security を使用してアプリケーションを監視するには、**Azure Active Directory** が必要です。<br><br>**パススルー** - アプリケーションにアクセスするための Azure AD に対するユーザーの認証は必要ありません。 ただし、バックエンドで認証要件を設定できます。 |
    | **コネクタ グループ** | コネクタは、アプリケーションへのリモート アクセスを処理します。コネクタ グループを使用して、コネクタとアプリをリージョン、ネットワーク、または目的別に整理できます。 まだコネクタ グループを作成していない場合、アプリは **[既定]** グループに割り当てられます。<br><br>アプリケーションで接続に Websocket を使用する場合は、グループ内のすべてのコネクタがバージョン 1.5.612.0 以降である必要があります。|

6. 必要に応じて、**追加設定**を構成します。 ほとんどのアプリケーションでは、これらの設定は既定の状態のままにしてください。 

    | フィールド | 説明 |
    | :---- | :---------- |
    | **バックエンド アプリケーションのタイムアウト** | アプリケーションの認証と接続に時間がかかる場合のみ、この値を **[遅い]** に設定します。 既定では、バックエンド アプリケーションのタイムアウトは 85 秒です。 [Long]\(長\) に設定すると、バックエンドのタイムアウトは 180 秒に増加します。 |
    | **HTTP 専用 Cookie を使用する** | アプリケーション プロキシ Cookie に HTTP 応答ヘッダーの HTTPOnly フラグを含めるには、この値を **[はい]** に設定します。 リモート デスクトップ サービスを使用している場合は、この値を **[いいえ]** に設定します。|
    | **セキュリティで保護された Cookie を使用します**| 暗号化された HTTPS 要求などのセキュリティ保護されたチャネル経由で Cookie を送信するために、この値を **[はい]** に設定します。
    | **永続 Cookie を使用**| この値は、 **[いいえ]** のままにしておきます。 この設定は、プロセス間で Cookie を共有できないアプリケーションにのみ使用してください。 Cookie の設定の詳細については、「[Azure Active Directory でオンプレミスのアプリケーションにアクセスするための Cookie 設定](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)」を参照してください。
    | **ヘッダーの URL を変換する** | 認証要求でアプリケーションの元のホスト ヘッダーが必要でない場合を除き、この値は **[はい]** のままにします。 |
    | **Translate URLs in Application Body (アプリケーションの本文内の URL を変換する)** | 他のオンプレミス アプリケーションへのハードコーディングされた HTML リンクがあり、カスタム ドメインを使用しない場合を除き、この値は **[いいえ]** のままにします。 詳細については、[Azure AD アプリケーション プロキシを使用したリンクの変換](application-proxy-configure-hard-coded-link-translation.md)に関する記事を参照してください。<br><br>このアプリケーションを Microsoft Cloud App Security (MCAS) を使用して監視する予定の場合は、この値を **[はい]** に設定します。 詳細については、「[Microsoft Cloud App Security と Azure Active Directory を使用してリアルタイムでのアプリケーション アクセスの監視を構成する](application-proxy-integrate-with-microsoft-cloud-application-security.md)」を参照してください。 |

7. **[追加]** を選択します。

## <a name="test-the-application"></a>アプリケーションをテストする

アプリケーションが正しく追加されることをテストする準備ができました。 次の手順で、アプリケーションにユーザー アカウントを追加し、サインインしてみます。

### <a name="add-a-user-for-testing"></a>テスト用のユーザーを追加する

アプリケーションにユーザーを追加する前に、企業ネットワーク内からアプリケーションにアクセスするためのアクセス許可がユーザー アカウントに既にあることを確認します。

テスト ユーザーを追加するには:

1. **[エンタープライズ アプリケーション]** を選択し、テストしたいアプリケーションを選択します。
2. **[はじめに]** を選択し、次に **[テスト用のユーザーを割り当てる]** を選択します。
3. **[ユーザーとグループ]** で、 **[ユーザーの追加]** を選択します。
4. **[割り当ての追加]** で、 **[ユーザーとグループ]** を選択します。 **[ユーザーとグループ]** セクションが表示されます。
5. 追加したいアカウントを選択します。
6. **[選択]** を選択し、 **[割り当て]** を選択します。

### <a name="test-the-sign-on"></a>サインオンをテストする

アプリケーションへのサインオンをテストするには:

1. テストするアプリケーションから **[アプリケーション プロキシ]** を選択します。
2. ページの上部にある **[アプリケーションをテストする]** を選択してアプリケーションのテストを実行し、構成の問題がないか確認します。
3. 最初にアプリケーションを起動して、アプリケーションへのサインインをテストし、その後、診断レポートをダウンロードして、問題が検出されれば、その解決のガイダンスを確認します。

トラブルシューティングについては、「[アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](application-proxy-troubleshoot.md)」をご覧ください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースは、不要になったら削除してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、オンプレミス環境をアプリケーション プロキシで動作するように準備した後、アプリケーション プロキシ コネクタをインストールして登録しました。 次に、Azure AD テナントにアプリケーションを追加しました。 ユーザーが Azure AD アカウントを使用してアプリケーションにサインオンできることを確認しました。

以下のことを行いました。
> [!div class="checklist"]
> * アウトバウンド トラフィック用のポートを開き、特定の URL へのアクセスを許可しした
> * Windows サーバーにコネクタをインストールし、アプリケーション プロキシに登録しました
> * コネクタが正しくインストールおよび登録されていることを確認しました
> * Azure AD テナントにオンプレミスのアプリケーションを追加しました
> * テスト ユーザーが Azure AD アカウントを使用してアプリケーションにサインオンできることを確認しました

シングル サインオン用にアプリケーションを構成する準備が整いました。 次のリンクを使用してシングル サインオンの方法を選択し、シングル サインオンのチュートリアルを見つけてください。

> [!div class="nextstepaction"]
> [シングル サインオンの構成](sso-options.md#choosing-a-single-sign-on-method)
