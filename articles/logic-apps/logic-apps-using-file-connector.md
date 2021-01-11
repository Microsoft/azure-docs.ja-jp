---
title: オンプレミスのファイル システムに接続する
description: Azure Logic Apps でファイル システム コネクタを使用し、オンプレミス データ ゲートウェイを介してオンプレミスのファイル システムに接続するタスクとワークフローを自動化します。
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: 1e6abeff8998e55eb7cbf450d1c3cc32f233e382
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065962"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Azure Logic Apps でオンプレミスのファイル システムに接続する

Azure Logic Apps とファイル システム コネクタを使用すると、オンプレミスのファイル共有上のファイルを作成および管理する、自動化されたタスクとワークフローを作成できます。

- ファイルを作成、取得、追加、更新、削除する。
- フォルダーまたはルート フォルダー内のファイルをリストする。
- ファイルの内容とメタデータを取得します。

この記事では、Dropbox にアップロードされているファイルをファイル共有にコピーして電子メールを送信するシナリオを例に、オンプレミスのファイル システムに接続する方法を紹介します。 オンプレミスのシステムに安全に接続してアクセスするために、ロジック アプリでは、[オンプレミス データ ゲートウェイ](../logic-apps/logic-apps-gateway-connection.md)が使用されます。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。 コネクタ固有の技術情報については、[ファイル システム コネクタ リファレンス](/connectors/filesystem/)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* ファイル システム サーバーなどのオンプレミス システムにロジック アプリを接続するには、あらかじめ[オンプレミス データ ゲートウェイをインストールしてセットアップ](../logic-apps/logic-apps-gateway-install.md)しておく必要があります。 そうすることで、ロジック アプリからファイル システムへの接続を作成するときに、ゲートウェイのインストール済み環境を使用するように指定できます。

* 無料でサインアップできる [Dropbox アカウント](https://www.dropbox.com/)。 ご自分のロジック アプリと Drobox アカウント間の接続を作成するには、アカウントの資格情報が必要です。

* 使用するファイル システムが存在するコンピューターへのアクセス権。 たとえば、ご使用のファイル システムと同じコンピューターにデータ ゲートウェイをインストールする場合は、そのコンピューター用のアカウントの資格情報が必要です。

* Logic Apps によってサポートされているプロバイダー (Office 365 Outlook、Outlook.com、Gmail など) の電子メール アカウント。 その他のプロバイダーについては、[こちらのコネクタ一覧を参照](/connectors/)してください。 このロジック アプリでは、Office 365 Outlook アカウントを使います。 別のメール アカウントをお使いの場合でも、全体的な手順は同じですが、UI がやや異なる場合があります。

  > [!IMPORTANT]
  > Gmail コネクタの使用を希望する場合、ロジック アプリで制限なしにこのコネクタを使用できるのは、G-Suite ビジネス アカウントだけです。 Gmail コンシューマー アカウントを持っている場合は、Google によって承認された特定のサービスのみでこのコネクタを使用できるほか、[認証に使用する Google クライアント アプリを Gmail コネクタで作成する](/connectors/gmail/#authentication-and-bring-your-own-application)ことができます。 詳細については、「[Azure Logic Apps での Google コネクタのデータ セキュリティとプライバシー ポリシー](../connectors/connectors-google-data-security-privacy-policy.md)」を参照してください。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識。 この例では、空のロジック アプリが必要となります。

## <a name="add-trigger"></a>トリガーの追加

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 検索ボックスに、フィルターとして「dropbox」と入力します。 トリガーの一覧から、**ファイルの作成時**

   ![Dropbox トリガーを選択](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. お使いの Dropbox アカウントの資格情報でサインインし、Dropbox データにアクセスすることについての承認を Azure Logic Apps に与えます。

1. トリガーに必要な情報を入力します。

   ![Dropbox トリガー](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>アクションの追加

1. トリガーで、 **[次のステップ]** を選択します。 検索ボックスに、フィルターとして「file system」と入力します。 アクションの一覧から、次のアクションを選択します。**ファイルを作成する**

   ![ファイル システム コネクタの検索](media/logic-apps-using-file-connector/find-file-system-action.png)

1. ファイル システムへの接続がまだない場合は、接続を作成するように求められます。

   ![接続を作成する](media/logic-apps-using-file-connector/file-system-connection.png)

   | プロパティ | 必須 | 値 | 説明 |
   | -------- | -------- | ----- | ----------- |
   | **Connection Name** | はい | <*connection-name*> | 接続に付ける名前 |
   | **ルート フォルダー** | はい | <*root-folder-name*> | ファイル システムのルート フォルダー。たとえば、オンプレミスのデータ ゲートウェイをインストールしている場合は、オンプレミスのデータ ゲートウェイがインストールされているコンピューターのローカル フォルダーや、コンピューターがアクセス可能なネットワーク共有のフォルダー。 <p>例: `\\PublicShare\\DropboxFiles` <p>ルート フォルダーはメインの親フォルダーで、すべてのファイル関連のアクションの相対パスに使用されます。 |
   | **認証の種類** | いいえ | <*auth-type*> | ファイル システムで使用される認証の種類:**Windows** |
   | **ユーザー名** | はい | <*domain*>\\<*username*> <p>または <p><*local-computer*>\\<*username*> | ファイル システム フォルダーが存在するコンピューターでのユーザー名。 <p>ファイル システム フォルダーがオンプレミス データ ゲートウェイと同じコンピューター上にある場合は、<*local-computer*>\\<*username*> を使用できます。 |
   | **パスワード** | はい | <*your-password*> | ファイル システムが存在するコンピューターでのパスワード |
   | **gateway** | はい | <*installed-gateway-name*> | あらかじめインストールしておいたゲートウェイの名前 |
   |||||

1. 操作が完了したら、 **[作成]** を選択します。

   Logic Apps により、接続の構成とテストが行われ、適切に機能していることが確認されます。 接続が正しくセットアップされると、事前に選択したアクションのオプションが表示されます。

1. **[ファイルの作成]** アクションで、Dropbox からオンプレミスのファイル共有のルート フォルダーにファイルをコピーするための詳しい情報を入力します。 前のステップからの出力を追加するには、ボックス内をクリックし、動的コンテンツ リストが表示されたら、フィールドの候補の中からいずれかを選択します。

   ![ファイル アクションの作成](media/logic-apps-using-file-connector/create-file-filled.png)

1. 今度は、適切なユーザーに新しいファイルについて知らせるための電子メールを送信する Outlook アクションを追加します。 受信者、タイトル、および電子メールの本文を入力します。 テスト目的で自分のメール アドレスを使用できます。

   ![電子メール送信アクション](media/logic-apps-using-file-connector/send-email.png)

1. ロジック アプリを保存します。 Dropbox にファイルをアップロードして、アプリをテストします。

   オンプレミスのファイル共有にファイルをコピーし、そのコピーされたファイルについて知らせるメールを特定の受信者に送信するロジック アプリが完成しました。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Swagger ファイルに記述される、トリガー、アクション、制限などのこのコネクタの技術的詳細については、[コネクタの参照ページ](/connectors/fileconnector/)を参照してください。

> [!NOTE]
> [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) のロジック アプリの場合、このコネクタの ISE のラベルが付いたバージョンでは、代わりに [ISE メッセージ制限](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)が使用されます。

## <a name="next-steps"></a>次のステップ

* [オンプレミス データへの接続方法](../logic-apps/logic-apps-gateway-connection.md)を確認します。 
* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
