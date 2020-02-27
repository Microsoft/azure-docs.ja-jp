---
title: Azure Active Directory のパスワード ライトバックを有効にする
description: このチュートリアルでは、Azure AD Connect を使用して Azure AD セルフサービス パスワード リセットのライトバックを有効にし、変更をオンプレミスの Active Directory Domain Services 環境に同期する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d383acae83f0f42f9c16fcb5d4ea7efbdf2b5f8
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486480"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>チュートリアル:オンプレミス環境への Azure Active Directory のセルフサービス パスワード リセットのライトバックを有効にする

Azure Active Directory (Azure AD) のセルフサービス パスワード リセット (SSPR) を使用すると、ユーザーは Web ブラウザーを使用して自分のパスワードを更新したり、自分のアカウントのロックを解除したりできます。 Azure AD がオンプレミスの Active Directory Domain Services (AD DS) 環境に接続されているハイブリッド環境では、このシナリオにより、パスワードが 2 つのディレクトリ間で異なる場合があります。

パスワード ライトバックを使用すると、Azure AD でのパスワード変更をオンプレミスの AD DS 環境に同期することができます。 Azure AD Connect には、これらのパスワード変更を Azure AD から既存のオンプレミス ディレクトリに送信するための安全なメカニズムが用意されています。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * パスワード ライトバックに必要なアクセス許可を構成する
> * Azure AD Connect でパスワード ライトバック オプションを有効にする
> * Azure AD SSPR でパスワード ライトバックを有効にする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 少なくとも試用版ライセンスが有効になっている、動作している Azure AD テナント。
    * 必要に応じて、[無料で作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
    * 詳細については、[Azure AD SSPR のライセンス要件](concept-sspr-licensing.md)に関するページを参照してください。
* "*グローバル管理者*" 特権を持つアカウント。
* セルフサービス パスワード リセット用に構成された Azure AD。
    * 必要に応じて、[前のチュートリアルを完了して Azure AD SSPR を有効にしてください](tutorial-enable-sspr.md)。
* Azure AD Connect の現在のバージョンを使用して構成された既存のオンプレミスの AD DS 環境。
    * 必要に応じて、[簡易](../hybrid/how-to-connect-install-express.md)または[カスタム](../hybrid/how-to-connect-install-custom.md)設定を使用して Azure AD Connect を構成してください。

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Azure AD Connect に対するアカウントのアクセス許可を構成する

Azure AD Connect を使用すると、オンプレミスの AD DS 環境と Azure AD の間でユーザー、グループ、資格情報を同期できます。 通常は、オンプレミスの AD DS ドメインに参加している Windows Server 2012 以降のコンピューターに Azure AD Connect をインストールします。

SSPR のライトバックを正しく操作するには、Azure AD Connect で指定されたアカウントに適切なアクセス許可とオプションが設定されている必要があります。 現在どのアカウントが使用されているかわからない場合は、Azure AD Connect を開き、 **[現在の構成を表示]** オプションを選択します。 アクセス許可を追加する必要があるアカウントが、 **[同期されたディレクトリ]** の下に表示されます。 このアカウントには、次のアクセス許可とオプションを設定する必要があります。

* **パスワードのリセット**
* **[パスワードの変更]**
* `lockoutTime` に対する**書き込みアクセス許可**
* `pwdLastSet` に対する**書き込みアクセス許可**
* 次のいずれかに対する**拡張権限**:
   * そのフォレスト内の*各ドメイン*のルート オブジェクト
   * SSPR の範囲内にしたいユーザー組織単位 (OU)

これらのアクセス許可を割り当てないと、ライトバックが正しく構成されているように見えても、ユーザーがクラウドからオンプレミスのパスワードを管理するときにエラーが発生します。

パスワード ライトバックを行うための適切なアクセス許可を設定するには、以下の手順を完了します。

1. オンプレミスの AD DS 環境で、適切な*ドメイン管理者*のアクセス許可を持つアカウントを使用して **[Active Directory ユーザーとコンピューター]** を開きます。
1. **[表示]** メニューで、 **[高度な機能]** がオンになっていることを確認します。
1. 左側のパネルで、ドメインのルートを表すオブジェクトを右クリックし、 **[プロパティ]**  >  **[セキュリティ]**  >  **[Advanced]\(詳細設定\)** の順に選択します。
1. **[アクセス許可]** タブで **[追加]** を選びます。
1. **[プリンシパル]** で、アクセス許可を適用するアカウント (Azure AD Connect で使用されているアカウント) を選択します。
1. **[適用対象]** ドロップダウン ボックスの一覧で、 **[ユーザーの子孫オブジェクト]** オブジェクトを選びます。
1. *[アクセス許可]* で次のオプションのボックスを選択します。
    * **[パスワードの変更]**
    * **パスワードのリセット**
1. *[プロパティ]* で次のオプションのボックスを選択します。 これらのオプションを見つけるには、リストをスクロールする必要があります。これらは、既定で設定されている場合があります。
    * **Write lockoutTime**
    * **Write pwdLastSet**

    [![](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png "Set the appropriate permissions in Active Users and Computers for the account that is used by Azure AD Connect")](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. 準備ができたら、 **[適用] または [OK]** を選択して変更を適用し、開いているすべてのダイアログ ボックスを終了します。

アクセス許可を更新すると、ディレクトリ内のすべてのオブジェクトにこれらのアクセス許可がレプリケートされるまで最大で 1 時間以上かかる場合があります。

オンプレミスの AD DS 環境のパスワード ポリシーによって、パスワードのリセットが正しく処理されない場合があります。 パスワード ライトバックを正常に機能させるには、"*パスワードの変更禁止期間*" のグループ ポリシーを 0 に設定する必要があります。 この設定は、 **[コンピューターの構成] > [ポリシー] > [Windows の設定] > [セキュリティの設定] > [アカウント ポリシー]** の下の `gpedit.msc` にあります。

グループ ポリシーを更新する場合は、更新されたポリシーがレプリケートされるまで待つか、`gpupdate /force` コマンドを使用します。

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Azure AD Connect でパスワード ライトバックを有効にする

Azure AD Connect の構成オプションの 1 つはパスワード ライトバック用です。 このオプションが有効になっていると、パスワード変更イベントにより、Azure AD Connect は更新された資格情報をオンプレミスの AD DS 環境に同期します。

セルフサービス パスワード リセットのライトバックを有効にするには、まず、Azure AD Connect でライトバック オプションを有効にします。 Azure AD Connect サーバーから、次の手順を実行します。

1. Azure AD Connect サーバーにサインインし、**Azure AD Connect** 構成ウィザードを開始します。
1. **[ようこそ]** ページで **[構成]** を選びます。
1. **[追加のタスク]** ページで **[同期オプションのカスタマイズ]** を選んで、 **[次へ]** を選びます。
1. **[Azure AD に接続]** ページで、お使いの Azure テナントの全体管理者の資格情報を入力し、 **[次へ]** を選択します。
1. **[Connect ディレクトリ]** ページおよび **[ドメイン/OU のフィルタリング]** ページで、 **[次へ]** を選びます。
1. **[オプション機能]** ページで **[パスワード ライトバック]** の横にあるチェック ボックスをオンにし、 **[次へ]** を選びます。

    ![パスワード ライトバック用に Azure AD Connect を構成する](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. **[構成の準備完了]** ページで **[構成]** を選び、処理が完了するまで待ちます。
1. 構成の完了が表示されたら、 **[終了]** を選びます。

## <a name="enable-password-writeback-for-sspr"></a>SSPR のパスワード ライトバックを有効にする

Azure AD Connect でパスワード ライトバックが有効になったところで、ライトバックのために Azure AD SSPR を構成します。 SSPR でパスワード ライトバックを使用できるようにすると、自分のパスワードを変更またはリセットするユーザーは、その更新したパスワードがオンプレミスの AD DS 環境にも同期されるようになります。

SSPR でパスワード ライトバックを有効にするには、次の手順を実行します。

1. グローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]** を検索して選択し、 **[パスワード リセット]** を選択してから、 **[オンプレミスの統合]** を選択します。
1. **[オンプレミスのディレクトリにパスワードをライトバックしますか?]** オプションを *[はい]* に設定します。
1. **[パスワードをリセットせずにアカウントのロックを解除することをユーザーに許可しますか?]** オプションを *[はい]* に設定します。

    ![パスワード ライトバックのためにセルフサービス パスワード リセット (SSPR) を有効にする](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. 準備ができたら、 **[保存]** を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルの一環として構成した SSPR のライトバック機能をもう使用しない場合は、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]** を検索して選択し、 **[パスワード リセット]** を選択してから、 **[オンプレミスの統合]** を選択します。
1. **[オンプレミスのディレクトリにパスワードをライトバックしますか?]** オプションを *[いいえ]* に設定します。
1. **[パスワードをリセットせずにアカウントのロックを解除することをユーザーに許可しますか?]** オプションを *[いいえ]* に設定します。

パスワード機能を使用する必要がなくなった場合は、Azure AD Connect サーバーから次の手順を実行します。

1. Azure AD Connect サーバーにサインインし、**Azure AD Connect** 構成ウィザードを開始します。
1. **[ようこそ]** ページで **[構成]** を選びます。
1. **[追加のタスク]** ページで **[同期オプションのカスタマイズ]** を選んで、 **[次へ]** を選びます。
1. **[Azure AD に接続]** ページで、お使いの Azure テナントの全体管理者の資格情報を入力し、 **[次へ]** を選択します。
1. **[Connect ディレクトリ]** ページおよび **[ドメイン/OU のフィルタリング]** ページで、 **[次へ]** を選びます。
1. **[オプション機能]** ページで **[パスワード ライトバック]** の横にあるチェック ボックスをオフにし、 **[次へ]** を選択します。
1. **[構成の準備完了]** ページで **[構成]** を選び、処理が完了するまで待ちます。
1. 構成の完了が表示されたら、 **[終了]** を選びます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、オンプレミスの AD DS 環境に対する Azure AD SSPR のライトバックを有効にしました。 以下の方法を学習しました。

> [!div class="checklist"]
> * パスワード ライトバックに必要なアクセス許可を構成する
> * Azure AD Connect でパスワード ライトバック オプションを有効にする
> * Azure AD SSPR でパスワード ライトバックを有効にする

> [!div class="nextstepaction"]
> [サインイン時にリスクを評価する](tutorial-risk-based-sspr-mfa.md)
