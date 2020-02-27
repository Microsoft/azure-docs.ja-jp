---
title: チュートリアル - Azure AD Domain Services でフォレストの信頼を作成する | Microsoft Docs
description: Azure AD Domain Services 用の Azure portal 内で、オンプレミスの AD DS ドメインへの一方向の送信フォレストを作成する方法について学習します。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 3637a11724c1f0bab049077c5abbd817e168bd44
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931231"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>チュートリアル:Azure Active Directory Domain Services (プレビュー) で、オンプレミスのドメインへの送信フォレストの信頼を作成する

パスワード ハッシュを同期できない環境、またはスマート カードを使用して排他的にサインインするために自分でパスワードがわからないユーザーが含まれる環境では、Azure Active Directory Domain Services (AD DS) でリソース フォレストを使用できます。 リソース フォレストでは、Azure AD DS から 1 つまたは複数のオンプレミスの AD DS 環境への一方向の送信の信頼が使用されます。 この信頼関係により、ユーザー、アプリケーション、およびコンピューターは、Azure AD DS マネージド ドメインから、オンプレミスのドメインに対して認証を行うことができます。 Azure AD DS リソース フォレストは現在、プレビューの段階です。

![Azure AD DS からオンプレミスの AD DS へのフォレストの信頼の図](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure AD DS 接続をサポートするようにオンプレミスの AD DS 環境で DNS を構成する
> * オンプレミスの AD DS 環境で一方向の受信フォレストの信頼を作成する
> * Azure AD DS で一方向の送信フォレスト信頼を作成する
> * 認証とリソース アクセスのために信頼関係をテストおよび検証する

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* リソース フォレストを使用して作成され、ご利用の Azure AD テナント内で構成された Azure Active Directory Domain Services マネージド ドメイン。
    * 必要であれば、[Azure Active Directory Domain Services インスタンスを作成して構成][create-azure-ad-ds-instance-advanced]してください。
    
    > [!IMPORTANT]
    > 必ず*リソース* フォレストを使用して Azure AD DS マネージド ドメインを作成してください。 既定のオプションでは、*ユーザー* フォレストが作成されます。 オンプレミスの AD DS 環境への信頼を作成できるのは、リソース フォレストだけです。 また、マネージド ドメインに対して *Enterprise* SKU を少なくとも使用する必要があります。 必要に応じて、[Azure AD DS マネージド ドメインの SKU を変更][howto-change-sku]します。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

このチュートリアルでは、Azure portal を使用して Azure AD DS からの送信フォレストの信頼を作成して構成します。 最初に、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="networking-considerations"></a>ネットワークに関する考慮事項

Azure AD DS リソース フォレストがホストされる仮想ネットワークには、ご利用のオンプレミスの Active Directory へのネットワーク接続が必要です。 アプリケーションとサービスには、Azure AD DS リソース フォレストをホストする仮想ネットワークへのネットワーク接続も必要です。 Azure AD DS リソース フォレストへのネットワーク接続は常にオンの状態で安定している必要があります。そうでないと、ユーザーによる認証またはリソースへのアクセスは失敗する可能性があります。

Azure AD DS でフォレストの信頼を構成する前に、Azure とオンプレミスの環境との間でご利用のネットワークが次の要件を満たしていることを確認してください。

* プライベート IP アドレスを使用します。 動的 IP アドレスの割り当てを使用した DHCP に依存しないでください。
* 仮想ネットワークのピアリングとルーティングが Azure とオンプレミスの間で正常に通信できるように、IP アドレス空間の重複を回避してください。
* Azure 仮想ネットワークには、サイト間 (S2S) VPN 接続または ExpressRoute 接続を構成するためのゲートウェイ サブネットが必要です。
* シナリオをサポートするのに十分な IP アドレスを使用してサブネットを作成します。
* Azure AD DS に独自のサブネットがあることを確認し、この仮想ネットワーク サブネットはアプリケーション VM およびサービスと共有しないでください。
* ピアリングされた仮想ネットワークは推移的ではありません。
    * オンプレミスの AD DS 環境に対して Azure AD DS リソース フォレストの信頼を使用するすべての仮想ネットワークの間に、Azure 仮想ネットワーク ピアリングを作成する必要があります。
* ご利用のオンプレミスの Active Directory フォレストに継続的なネットワーク接続を提供します。 オンデマンド接続は使用しないでください。
* ご利用の Azure AD DS リソース フォレスト名とご利用のオンプレミスの Active Directory フォレスト名との間に、継続的名前解決 (DNS) があることを確認します。

## <a name="configure-dns-in-the-on-premises-domain"></a>オンプレミスのドメイン内で DNS を構成する

オンプレミスの環境から Azure AD DS マネージド ドメインを正しく解決するには、既存の DNS サーバーにフォワーダーを追加することが必要になる場合があります。 Azure AD DS マネージド ドメインと通信するようにオンプレミスの環境が構成されていない場合は、オンプレミスの AD DS ドメインに対して管理ワークステーションから次の手順を行います。

1. **[スタート] | [管理ツール] | [DNS]** の順に選択します。
1. *myAD01* などの DNS サーバーを右クリックし、 **[プロパティ]** を選択します。
1. **[フォワーダー]** 、 **[編集]** の順に選択して、他のフォワーダーを追加します。
1. *10.0.1.4* や *10.0.1.5* などの、Azure AD DS マネージド ドメインの IP アドレスを追加します。

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>オンプレミス ドメインに受信フォレストの信頼を作成する

オンプレミスの AD DS ドメインには、Azure AD DS マネージド ドメインに対する受信フォレストの信頼が必要です。 この信頼は、オンプレミスの AD DS ドメインで手動で作成する必要があり、Azure portal から作成することはできません。

オンプレミスの AD DS ドメイン上で受信の信頼を構成するには、オンプレミスの AD DS ドメインに対して管理ワークステーションから次の手順を行います。

1. **[スタート] | [管理ツール] | [Active Directory Active Directory ドメインと信頼関係]** の順に選択します。
1. *onprem.contoso.com* などのドメインを右クリックし、 **[プロパティ]** を選択します。
1. **[信頼]** タブ、 **[新しい信頼]** の順に選択します。

   > [!NOTE]
   > **[信頼]** メニュー オプションが表示されない場合は、 **[プロパティ]** で *フォレストの種類* を確認してください。 信頼を作成できるのは、*リソース* フォレストだけです。 フォレストの種類が*ユーザー*場合、信頼を作成することはできません。 現在、Azure AD DS マネージド ドメインのフォレストの種類を変更する方法はありません。 マネージド ドメインを削除し、リソース フォレストとして作成し直す必要があります。

1. Azure AD DS ドメイン名に対して名前 (*aadds.contoso.com* など) を入力してから、 **[次へ]** を選択します。
1. **フォレストの信頼**を作成するオプションを選択して、**一方向: 受信**の信頼を作成します。
1. **[This domain only]\(このドメインのみ\)** に信頼を作成することを選択します。 次の手順では、Azure portal で Azure AD DS マネージド ドメインに対する信頼を作成します。
1. **フォレスト全体の認証**を使用することを選択してから、信頼パスワードを入力して確認します。 これと同じパスワードを、次のセクションの Azure portal にも入力します。
1. 既定のオプションを使用して次のいくつかのウィンドウをステップ実行し、オプションの **[確認しない]** を選択します。
1. **[完了]** を選択します。

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Azure AD DS で送信フォレストの信頼を作成する

Azure AD DS マネージド ドメインを解決するようにオンプレミスの AD DS ドメインが構成され、受信フォレストの信頼が作成された状態で、現在、送信フォレストの信頼が作成されています。 この送信フォレストの信頼により、オンプレミスの AD DS ドメインと Azure AD DS マネージド ドメインとの間の信頼関係が完成します。

Azure portal で Azure AD DS マネージド ドメインに対する送信の信頼を作成するには、次の手順を行います。

1. Azure portal で **Azure AD Domain Services** を検索して選択し、次にご利用のマネージド ドメイン (*aadds.contoso.com* など) を選択します。
1. Azure AD DS マネージド ドメインの左側にあるメニューで、 **[信頼]** を選択してから、 **[+ 追加]** を選択して信頼を追加します。
1. ご利用の信頼を識別する表示名を入力し、次にオンプレミスの信頼されたフォレストの DNS 名 (*onprem.contoso.com* など) を入力します。
1. 前のセクションでオンプレミスの AD DS ドメインに対して受信フォレストの信頼を構成したときに使用したのと同じ信頼パスワードを指定します。
1. オンプレミスの AD DS ドメインに少なくとも 2 つの DNS サーバー (*10.0.2.4* や *10.0.2.5* など) を指定します。
1. 準備ができたら、送信フォレストの信頼を**保存**します

    [Azure portal で送信フォレストの信頼を作成する](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>リソース認証を検証する

次の一般的なシナリオにより、ユーザーとリソースへのアクセスとがフォレストの信頼によって正しく認証されることを検証できます。

* [Azure AD DS リソース フォレストからのオンプレミスのユーザー認証](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [オンプレミスのユーザーを使用して Azure AD DS リソース フォレスト内のリソースにアクセスする](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [ファイルとプリンターの共有を有効にする](#enable-file-and-printer-sharing)
    * [セキュリティ グループを作成してメンバーを追加する](#create-a-security-group-and-add-members)
    * [フォレスト間のアクセス用にファイル共有を作成する](#create-a-file-share-for-cross-forest-access)
    * [リソースに対するフォレスト間認証を検証する](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Azure AD DS リソース フォレストからのオンプレミスのユーザー認証

Windows Server 仮想マシンを Azure AD DS リソース ドメインに参加させる必要があります。 この仮想マシンを使用して、オンプレミスのユーザーが仮想マシン上で認証されることをテストします。

1. リモート デスクトップと Azure AD DS 管理者の資格情報を使用して、Azure AD DS リソース フォレストに参加している Windows Server VM に接続します。 ネットワーク レベル認証 (NLA) エラーが発生した場合は、使用したユーザー アカウントがドメイン ユーザー アカウントではないことを確認します。

    > [!NOTE]
    > Azure AD Domain Services に参加しているご利用の VM に安全に接続するには、サポートされている Azure リージョンで [Azure Bastion ホスト サービス](https://docs.microsoft.com/azure/bastion/bastion-overview)を使用できます。

1. コマンド プロンプトを開き、`whoami` コマンドを使用して、現在認証されているユーザーの識別名を表示します。

    ```console
    whoami /fqdn
    ```

1. `runas` コマンドを使用して、オンプレミスのドメインからのユーザーとして認証を受けます。 次のコマンドで、`userUpn@trusteddomain.com` を、信頼されているオンプレミス ドメインからのユーザーの UPN に置き換えます。 コマンドを実行すると、ユーザーのパスワードの入力を求められます。

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. 認証が成功した場合は、新しいコマンド プロンプトが開きます。 新しいコマンド プロンプトのタイトルには、`running as userUpn@trusteddomain.com` が含まれています。
1. 新しいコマンド プロンプトで `whoami /fqdn` を使用して、オンプレミスの Active Directory から認証されたユーザーの識別名を表示します。

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>オンプレミスのユーザーを使用して Azure AD DS リソース フォレスト内のリソースにアクセスする

Azure AD DS リソース フォレストに参加している Windows Server VM を使用すると、ユーザーは、オンプレミス ドメインのコンピューターからオンプレミス ドメインのユーザーを認証するときに、リソース フォレストでホストされているリソースにアクセスできる、というシナリオをテストすることができます。 次の例では、さまざまな一般的なシナリオを作成およびテストする方法を示しています。

#### <a name="enable-file-and-printer-sharing"></a>ファイルとプリンターの共有を有効にする

1. リモート デスクトップと Azure AD DS 管理者の資格情報を使用して、Azure AD DS リソース フォレストに参加している Windows Server VM に接続します。 ネットワーク レベル認証 (NLA) エラーが発生した場合は、使用したユーザー アカウントがドメイン ユーザー アカウントではないことを確認します。

    > [!NOTE]
    > Azure AD Domain Services に参加しているご利用の VM に安全に接続するには、サポートされている Azure リージョンで [Azure Bastion ホスト サービス](https://docs.microsoft.com/azure/bastion/bastion-overview)を使用できます。

1. **[Windows の設定]** を開き、 **[ネットワークと共有センター]** を検索して選択します。
1. **[共有の詳細設定の変更]** のオプションを選択します。
1. **[ドメイン プロファイル]** で、 **[ファイルとプリンターの共有を有効にする]** 、 **[変更の保存]** の順に選択します。
1. **[ネットワークと共有センター]** を閉じます。

#### <a name="create-a-security-group-and-add-members"></a>セキュリティ グループを作成してメンバーを追加する

1. **[Active Directory ユーザーとコンピューター]** を開きます。
1. ドメイン名を右クリックし、 **[新規]** を選択してから、 **[組織単位]** を選択します。
1. [名前] ボックスで、「*LocalObjects*」と入力して、 **[OK]** を選択します。
1. ナビゲーション ウィンドウで右クリックして、 **[LocalObjects]** を選択します。 **[新規]** を選択してから、 **[グループ]** を選択します。
1. **[グループ名]** ボックスに「*FileServerAccess*」と入力します。 **[グループのスコープ]** の場合は、 **[ドメイン ローカル]** を選択してから、 **[OK]** を選択します。
1. コンテンツ ウィンドウで、 **[FileServerAccess]** をダブルクリックします。 **[メンバー]** 、 **[追加]** 、 **[場所]** の順に選択します。
1. **[場所]** ビューからご利用のオンプレミスの Active Directory を選択して、 **[OK]** を選択します。
1. **[選択するオブジェクト名を入力してください]** ボックスに「*Domain Users*」と入力します。 **[名前の確認]** を選択してから、オンプレミスの Active Directory の資格情報を入力して、 **[OK]** を選択します。

    > [!NOTE]
    > 信頼関係は一方向のみであるため、資格情報を指定する必要があります。 つまり、Azure AD DS からのユーザーは、リソースにアクセスすることも、信頼された (オンプレミスの) ドメイン内のユーザーまたはグループを検索することもできません。

1. ご利用のオンプレミスの Active Directory からの **[ドメイン ユーザー]** グループは、 **[FileServerAccess]** グループのメンバーである必要があります。 **[OK]** を選択してグループを保存し、ウィンドウを閉じます。

#### <a name="create-a-file-share-for-cross-forest-access"></a>フォレスト間のアクセス用にファイル共有を作成する

1. Azure AD DS リソース フォレストに参加している Windows Server VM 上に、フォルダーを作成し、「*CrossForestShare*」などの名前を入力します。
1. フォルダーを右クリックし、 **[プロパティ]** を選択します。
1. **[セキュリティ]** タブを選択してから、 **[編集]** を選択します。
1. *[CrossForestShare のアクセス許可]* ダイアログ ボックスで **[追加]** を選択します。
1. **[選択するオブジェクト名を入力してください]** に、「*FileServerAccess*」と入力し、 **[OK]** を選択します。
1. **[グループまたはユーザー名]** 一覧から *[FileServerAccess]* を選択します。 **[FileServerAccess のアクセス許可]** 一覧で、 **[変更]** アクセス許可と **[書き込み]** アクセス許可に対して *[許可]* を選択し、 **[OK]** を選択します。
1. **[共有]** タブを選択してから、 **[詳細な共有]** を選択します。
1. **[このフォルダーを共有する]** を選択し、次に *CrossForestShare* など、ファイル共有の覚えやすい名前を **[共有名]** に入力します。
1. **[アクセス許可]** を選択します。 **[Permissions for Everyone]\(全員のアクセス許可\)** 一覧で、 **[変更]** アクセス許可に対して **[許可]** を選択します。
1. **[OK]** を 2 回選択して、 **[閉じる]** を選択します。

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>リソースに対するフォレスト間認証を検証する

1. ご利用のオンプレミスの Active Directory からのユーザー アカウントを使用して、ご利用のオンプレミスの Active Directory に参加している Windows コンピューターにサインインします。
1. **Windows Explorer** 使用して、完全修飾ホスト名と共有 (`\\fs1.aadds.contoso.com\CrossforestShare` など) を使用して作成した共有に接続します。
1. 書き込みアクセス許可を検証するには、フォルダー内で右クリックし、 **[新規]** を選択してから、 **[テキスト ドキュメント]** を選択します。 既定の名前 **[新しいテキスト ドキュメント]** を使用します。

    書き込みアクセス許可が適切に設定されている場合は、新しいテキスト ドキュメントが作成されます。 次の手順では、必要に応じてファイルを開き、編集したり削除したりします。
1. 読み取りアクセス許可を検証するには、 **[新しいテキスト ドキュメント]** を開きます。
1. 変更アクセス許可を検証するには、ファイルにテキストを追加し、 **[メモ帳]** を閉じます。 変更を保存するように促すメッセージが表示されたら、 **[保存]** を選択します。
1. 削除アクセス許可を検証するには、 **[新しいテキスト ドキュメント]** を右クリックし、 **[削除]** を選択します。 ファイルの削除を確定するには **[はい]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure AD DS 接続をサポートするようにオンプレミスの AD DS 環境で DNS を構成する
> * オンプレミスの AD DS 環境で一方向の受信フォレストの信頼を作成する
> * Azure AD DS で一方向の送信フォレスト信頼を作成する
> * 認証とリソース アクセスのために信頼関係をテストおよび検証する

Azure AD DS 内のフォレストの種類に関する概念的な詳細については、[リソース フォレストとは何か][concepts-forest]および[フォレストの信頼が Azure AD DS で機能する方法][concepts-trust]に関するページを参照してください。

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
