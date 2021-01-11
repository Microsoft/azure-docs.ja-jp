---
title: SSH で SFTP サーバーに接続する
description: SSH と Azure Logic Apps を使用して、SFTP サーバーのファイルの監視、作成、管理、および受信を行うタスクを自動化します
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/20/2020
tags: connectors
ms.openlocfilehash: f3de582ff69dbd57aa4692fd5c3901602569cf9e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286616"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>SSH と Azure Logic Apps を使用して SFTP ファイルの監視、作成、および管理を行う

[Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) プロトコルを使用して[セキュア ファイル転送プロトコル (SFTP)](https://www.ssh.com/ssh/sftp/) サーバー上のファイルを監視、作成、送信、および受信するタスクを自動化するために、Azure Logic Apps と SFTP-SSH コネクタを使用して、統合ワークフローの構築と自動化を行えます。 SFTP は、任意の信頼性の高いデータ ストリームを通して、ファイル アクセス、ファイル転送、およびファイル管理を提供するネットワーク プロトコルです。

> [!NOTE]
> SFTP-SSH コネクタでは現在、次の SFTP サーバーはサポートされていません。
> 
> * IBM DataPower
> * MessageWay
> * OpenText Secure MFT
> * OpenText GXS

次に、自動化できるタスクの例をいくつか示します。

* ファイルの追加または変更を監視します。
* ファイルの取得、作成、コピー、名前変更、更新、一覧、および削除を行います。
* フォルダーを作成します。
* ファイルの内容とメタデータを取得します。
* アーカイブをフォルダーに抽出します。

SFTP サーバー上のイベントを監視し、出力を他のアクションで使用できるようにするトリガーを使用できます。 SFTP サーバーで各種のタスクを実行するアクションを使用できます。 ロジック アプリ内の他のアクションでも、SFTP アクションからの出力を使用するようにできます。 たとえば、SFTP サーバーからファイルを定期的に取得する場合は、Office 365 Outlook コネクタまたは Outlook.com コネクタを使用して、これらのファイルやその内容に関するメールのアラートを送信できます。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

SFTP-SSH コネクタと SFTP コネクタの違いについては、このトピックで後述する「[SFTP-SSH と SFTP を比較する](#comparison)」で確認してください。

## <a name="limits"></a>制限

* [チャンク](../logic-apps/logic-apps-handle-large-messages.md)をサポートする SFTP-SSH アクションでは 1 GB までのファイルを処理できますが、チャンクをサポートしない SFTP-SSH アクションでは 50 MB までのファイルを処理できます。 既定のチャンク サイズは 15 MB ですが、このサイズは、ネットワーク待機時間、サーバーの応答時間などの要因に応じて、5 MB から段階的に増やして最大 50 MB まで動的に変更できます。

  > [!NOTE]
  > [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) のロジック アプリの場合、このコネクタの ISE のラベルが付いたバージョンでは、代わりに [ISE メッセージ制限](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)を使用するためにチャンクが必要です。

  代わりに使用する[一定のチャンク サイズを指定する](#change-chunk-size)と、このアダプティブ動作をオーバーライドできます。 このサイズの範囲は 5 MB から 50 MB です。 たとえば、45 MB のファイルと、そのファイル サイズを待機時間なしでサポートするネットワークがあるとします。 アダプティブ チャンクは、1 回の呼び出しではなく複数回の呼び出しになります。 呼び出しの回数を減らすために、50 MB のチャンク サイズを設定してみることができます。 別のシナリオで、たとえば 15 MB のチャンクを使用しているときに、ロジック アプリがタイムアウトする場合は、サイズを 5 MB に減らしてみることができます。

  チャンク サイズは接続に関連付けられます。つまり、チャンクをサポートするアクションと、チャンクをサポートしないアクションに対して同じ接続を使用できます。 この場合、チャンクをサポートしないアクションのチャンク サイズは、5 MB から 50 MB の範囲です。 次の表に、チャンクをサポートする SFTP-SSH アクションを示します。

  | アクション | チャンクのサポート | チャンク サイズのオーバーライドのサポート |
  |--------|------------------|-----------------------------|
  | **ファイルのコピー** | いいえ | 利用不可 |
  | **ファイルを作成する** | はい | はい |
  | **フォルダーの作成** | 適用なし | 適用なし |
  | **ファイルの削除** | 適用なし | 適用なし |
  | **アーカイブをフォルダーに抽出する** | 適用なし | 適用なし |
  | **ファイルの内容を取得する** | はい | はい |
  | **パスを使用してファイルの内容を取得する** | はい | はい |
  | **ファイルのメタデータを取得する** | 適用なし | 適用なし |
  | **パスを使用してファイルのメタデータを取得する** | 適用なし | 適用なし |
  | **フォルダー内のファイルを一覧表示する** | 適用なし | 適用なし |
  | **ファイル名の変更** | 適用なし | 適用なし |
  | **ファイルを更新する** | いいえ | 適用なし |
  ||||

* SFTP-SSH トリガーではメッセージ チャンクはサポートされていません。 ファイルのコンテンツを要求する場合、トリガーは 15 MB 以下のファイルのみを選択します。 15 MB より大きいファイルを取得するには、代わりに次のパターンに従います。

  1. **ファイルが追加または変更されたとき (プロパティのみ)** といった、ファイルのプロパティのみを返す SFTP-SSH トリガーを使用します。

  1. SFTP-SSH トリガーの **ファイルのコンテンツの取得**アクションに従います。これにより、暗黙的にメッセージ チャンクを使用してファイル全体が読み取られます。

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH と SFTP を比較する

以下に、SFTP-SSH コネクタと SFTP-SSH コネクタのその他の主な違いを、SFTP コネクタは以下の機能を備えているという形で示します。

* .NET をサポートするオープン ソース Secure Shell (SSH) ライブラリである [SSH.NET ライブラリ](https://github.com/sshnet/SSH.NET)を使用します。

* SFTP サーバーの指定されたパスにフォルダーを作成する**フォルダーの作成**アクションを提供します。

* SFTP サーバーのファイルの名前を変更する**ファイル名の変更**アクションを提供します。

* SFTP サーバーへの接続を*最大 1 時間*キャッシュします。これによりパフォーマンスが向上し、サーバーへの接続試行数が減少します。 このキャッシュ動作の期間を設定するには、SFTP サーバーの SSH 構成で、[**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) プロパティを編集します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* SFTP サーバーのアドレスとアカウントの資格情報。これにより、ロジック アプリが SFTP アカウントにアクセスできます。 SSH 秘密キーと SSH 秘密キーのパスワードにもアクセスできる必要があります。 大きなファイルをアップロードするときにチャンクを使用するには、SFTP サーバー上のルート フォルダーに対する読み取りと書き込みの両方のアクセス許可が必要です。 そうでない場合は、"401 権限がありません" エラーが表示されます。

  > [!IMPORTANT]
  >
  > SFTP-SSH コネクタは、以下に示す秘密キー形式、アルゴリズム、およびフィンガープリント*のみ*をサポートしています。
  >
  > * **秘密キーの形式**: OpenSSH 形式と ssh.com 形式の両方の RSA (Rivest Shamir Adleman) キーと DSA (デジタル署名アルゴリズム) キー。 秘密キーが PuTTY (.ppk) ファイル形式の場合は、まず、[キーを OpenSSH (.pem) ファイル形式に変換します](#convert-to-openssh)。
  >
  > * **暗号化アルゴリズム**: DES-EDE3-CBC、DES-EDE3-CFB、DES-CBC、AES-128-CBC、AES-192-CBC、AES-256-CBC
  >
  > * **フィンガープリント**: MD5
  >
  > ご利用のロジック アプリに必要な SFTP-SSH トリガーまたはアクションを追加した後、SFTP サーバーの接続情報を指定する必要があります。 この接続に SSH 秘密キーを指定する場合、***キーを手動で入力したり編集したりしないでください***。これにより、接続が失敗する可能性があります。 代わりに、必ず、ご利用の SSH 秘密キー ファイルから***キーをコピー***し、そのキーを接続の詳細に***貼り付け***てください。 
  > 詳細については、この記事の後の「[SSH で SFTP に接続する](#connect)」セクションを参照してください。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* SFTP アカウントにアクセスするロジック アプリ。 SFTP-SSH トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 SFTP-SSH アクションを使用するには、**繰り返し**トリガーなど、別のトリガーでロジック アプリを開始します。

## <a name="how-sftp-ssh-triggers-work"></a>SFTP-SSH トリガーの動作

SFTP-SSH は、SFTP ファイル システムをポーリングし、前回のポーリング以降に変更されたファイルを検索することで動作をトリガーします。 一部のツールでは、ファイルが変更されたときにタイムスタンプを保持できます。 これらの場合は、トリガーが動作できるように、この機能を無効にする必要があります。 一般的な設定をいくつか次に示します。

| SFTP クライアント | アクション |
|-------------|--------|
| Winscp | **[オプション]**  >  **[ユーザー設定]**  >  **[転送]**  >  **[編集]**  >  **[Preserve timestamp] (タイムスタンプを保持する)**  >  **[無効]** に移動する |
| FileZilla | **[転送]**  >  **[Preserve timestamps of transferred files] (転送されたファイルのタイムスタンプを保持する)**  >  **[無効]** に移動する |
|||

トリガーによって新しいファイルが検出されると、トリガーはその新しいファイルが完了していて、すべて書き込まれていることを確認します。 たとえば、トリガーがファイル サーバーを確認している際に、ファイルの進行状態が変化する場合があります。 部分的に書き込まれたファイルが返されることなないように、トリガーは最新の変更を含むファイルのタイムスタンプをメモしますが、すぐにそのファイルを返すことはありません。 トリガーは、もう一度サーバーをポーリングしてファイルを返します。 場合によっては、この動作によって、最大でトリガーのポーリング間隔が 2 倍となる遅延が生じる場合があります。

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>PuTTY ベースのキーを OpenSSH に変換する

ご利用の秘密キーが .ppk (PuTTY 秘密キー) ファイル名拡張子する PuTTY 形式の場合、まずキーを .pem (Privacy Enhanced Mail) ファイル名拡張子を使用する OpenSSH 形式に変換します。

### <a name="unix-based-os"></a>Unix ベースの OS

1. PuTTY ツールがご利用のシステムにまだインストールされていない場合は、今すぐ次のような操作を行います。

   `sudo apt-get install -y putty`

1. このコマンドを実行します。これにより、SFTP-SSH コネクタで使用できるファイルが作成されます。

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   次に例を示します。

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows OS

1. まだ行っていない場合は、[最新の PuTTY Generator (puttygen.exe) ツールをダウンロードし](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)、ツールを起動します。

1. この画面で **[Load]/[読み込む/]** を選択します。

   ![[Load]\[読み込む\] を選択](./media/connectors-sftp-ssh/puttygen-load.png)

1. PuTTY 形式で秘密キー ファイルを参照し、 **[Open]\(開く\)** を選択します。

1. **[Conversions]\(変換\)** メニューで、 **[Export OpenSSH key]\(OpenSSH キーのエクスポート\)** を選択します。

   ![[Export OpenSSH key]\(OpenSSH キーのエクスポート\) を選択](./media/connectors-sftp-ssh/export-openssh-key.png)

1. `.pem` のファイル名拡張子で秘密キー ファイルを保存します。

## <a name="considerations"></a>考慮事項

このセクションでは、このコネクタのトリガーとアクションについて確認すべき考慮事項について説明します。

<a name="create-file"></a>

### <a name="create-file"></a>[ファイルの作成]

SFTP サーバーにファイルを作成するには、SFTP-SSH の **[ファイルの作成]** アクションを使用できます。 このアクションによってファイルが作成されると、Logic Apps サービスはファイルのメタデータを取得するために、SFTP サーバーも自動的に呼び出します。 ただし、メタデータを取得するために Logic Apps サービスが呼び出しを行うことができるようになる前に、新しく作成されたファイルを移動すると、`404` エラー メッセージ `'A reference was made to a file or folder which does not exist'` が表示されます。 ファイルの作成後にファイルのメタデータの読み取りをスキップするには、[ **[Get all file metadata]\(すべてのファイル メタデータの取得\)** プロパティを追加して **[いいえ]** に設定する](#file-does-not-exist)手順に従ってください。

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>SSH で SFTP に接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 空のロジック アプリの場合は、検索ボックスに、フィルターとして「`sftp ssh`」と入力します。 トリガーの一覧で、目的のトリガーを選択します。

   または

   既存のロジック アプリの場合、アクションを追加する最後のステップの下で、 **[新しいステップ]** を選択します。 検索ボックスに、フィルターとして「`sftp ssh`」と入力します。 アクションの一覧で、目的のアクションを選択します。

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

1. 接続に必要な詳細を指定します。

   > [!IMPORTANT]
   >
   > **SSH 秘密キー** プロパティに SSH 秘密キーを入力する場合は、次の追加の手順に従ってください。これは、このプロパティの完全で正しい値を確実に指定する助けになります。 無効なキーを指定すると、接続が失敗します。

   任意のテキスト エディターを使用できますが、次に例として、Notepad.exe を使用してキーを正しくコピーして貼り付ける方法を示す手順の例を示します。

   1. テキスト エディターで SSH 秘密キーのファイルを開きます。 これらの手順では、例としてメモ帳を使用します。

   1. メモ帳の **[編集]** メニューで、 **[すべて選択]** を選択します。

   1. **[編集]**  >  **[コピー]** を選択します。

   1. 追加した SFTP-SSH トリガーまたはアクションで、コピーした*完全な*キーを **SSH 秘密キー** プロパティ (これは、複数行をサポートします) に貼り付けます。  キーは***貼り付けるようにしてください***。 ***キーを手動で入力または編集しないでください***。

1. 接続の詳細の入力を完了したら、 **[作成]** を選択します。

1. ここから、選択したトリガーまたはアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>チャンク サイズをオーバーライドする

チャンクで使用される既定のアダプティブ動作をオーバーライドするために、5 MB から 50 MB までの一定のチャンク サイズを指定することができます。

1. アクションの右上隅の省略記号ボタン ( **...** ) を選択し、 **[設定]** を選択します。

   ![SFTP-SSH 設定を開く](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. **[Content Transfer]\(コンテンツ転送\)** の **[Chunk size]\(チャンク サイズ\)** プロパティで、`5` から `50` までの整数値を入力します。たとえば次のようにします。 

   ![代わりに使用するチャンク サイズを指定する](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. 完了したら、 **[完了]** をクリックします。

## <a name="examples"></a>例

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - SSH トリガー: When a file is added or modified (ファイルの追加または変更時)

このトリガーは、SFTP サーバーでファイルが追加または変更されたときにロジック アプリ ワークフローを開始します。 たとえば、ファイルの内容をチェックし、内容が指定された条件を満たすかどうかに基づいてその内容を取得する条件を追加できます。 その後、ファイルの内容を取得し、その内容を SFTP サーバーのフォルダーに格納するアクションを追加できます。

**企業での使用例**: このトリガーを使用して、SFTP フォルダーに顧客からの注文を表す新しいファイルがあるかどうかを監視できます。 その後、さらに処理するために注文の内容を取得し、その注文を注文データベース内に格納できるように、**ファイルの内容を取得する**などの SFTP アクションを使用できます。

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP - SSH アクション: パスを使用してファイルの内容を取得する

このアクションは、ファイル パスを指定することによって SFTP サーバー上のファイルの内容を取得します。 そのため、たとえば前の例のトリガーと、ファイルの内容が満たす必要がある条件を追加できます。 条件が true であれば、内容を取得するアクションを実行できます。

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-errors"></a>エラーをトラブルシューティングする

このセクションでは、一般的なエラーまたは問題に対する考えられる解決策について説明します。

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404 エラー:"存在しないファイルまたはフォルダーが参照されました"

このエラーが発生する可能性があるのは、Logic Apps が SFTP-SSH の **[ファイルの作成]** アクションを通じて SFTP サーバー上に新しいファイルを作成したが、新しく作成されたファイルのメタデータを Logic Apps サービスが取得できるようになる前に、そのファイルがすぐに移動された場合です。 Logic Apps が **[ファイルの作成]** アクションを実行すると、Logic Apps サービスはファイルのメタデータを取得するために、SFTP サーバーも自動的に呼び出します。 ただし、ファイルが移動されると Logic Apps サービスはファイルを見つけられなくなるため、ユーザーは `404` エラー メッセージを受け取ります。

ファイルの移動を回避も遅延もできない場合は、次の手順に従って、ファイルの作成後にファイルのメタデータを読み取ることをスキップできます。

1. **[ファイルの作成]** アクションで、 **[新しいパラメーターの追加]** リストを開き、 **[Get all file metadata]\(すべてのファイル メタデータの取得\)** プロパティを選択して、値を **[いいえ]** に設定します。

1. 後でこのファイル メタデータが必要になった場合は、 **[ファイルのメタデータの取得]** アクションを使用できます。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Swagger ファイルに記述される、トリガー、アクション、制限などのこのコネクタの技術的詳細については、[コネクタの参照ページ](/connectors/sftpwithssh/)を参照してください。

> [!NOTE]
> [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) のロジック アプリの場合、このコネクタの ISE のラベルが付いたバージョンでは、代わりに [ISE メッセージ制限](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)を使用するためにチャンクが必要です。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。

