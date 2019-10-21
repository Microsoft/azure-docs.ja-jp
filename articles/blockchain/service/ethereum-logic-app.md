---
title: Azure Logic Apps で Ethereum ブロックチェーン コネクタを使用する方法
description: Azure Logic Apps で Ethereum ブロックチェーン コネクタを使用して、スマート コントラクト関数をトリガーし、スマート コントラクト イベントに応答する方法。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/26/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 48f862bea6df50480ce4f9d21993693da34cfbc9
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720675"
---
# <a name="how-to-use-ethereum-blockchain-connector-with-azure-logic-apps"></a>Azure Logic Apps で Ethereum ブロックチェーン コネクタを使用する方法

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) で [Ethereum ブロックチェーン コネクタ](https://docs.microsoft.com/connectors/blockchainethereum/)を使用することにより、スマート コントラクト アクションを実行し、スマート コントラクト イベントに応答できます。 たとえば、ブロックチェーン台帳から情報を返す REST ベースのマイクロサービスを作成するとします。 ロジック アプリを使用すると、ブロックチェーン台帳に格納されている情報を照会する HTTP 要求を受け入れることができます。

## <a name="prerequisites"></a>前提条件

* オプションの前提条件である「クイックスタート: Visual Studio Code を使用して Azure Blockchain Service コンソーシアム ネットワークに接続する」を済ませておいてください。 このクイックスタート ガイドでは、[Azure Blockchain Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) のインストール方法と、ご自分のブロックチェーン開発環境の設定方法について説明しています。

## <a name="create-a-logic-app"></a>ロジック アプリを作成します

Azure Logic Apps は、システムとサービスを統合する必要があるときに、ビジネス プロセスとワークフローをスケジュール設定および自動化するのに役立ちます。 最初に、Ethereum ブロックチェーン コネクタを使用するロジックを作成します。

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成] > [統合] > [Logic App]** の順に選択します。
1. **[ロジック アプリの作成]** で、自分のロジック アプリの作成場所の詳細を入力します。 完了したら、 **[作成]** を選択します。

    Azure ロジック アプリの作成の詳細については、[Azure ロジック アプリの作成](../../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

1. Azure によってアプリがデプロイされたら、自分のロジック アプリのリソースを選択します。
1. [Logic Apps デザイナー] の **[テンプレート]** で、 **[空のロジック アプリ]** を選択します。

すべてのロジック アプリは必ずトリガーから起動され、トリガーは、特定のイベントが発生するか特定の条件が満たされたときに起動されます。 トリガーが起動するたびに、Logic Apps エンジンによって、ワークフローを開始および実行するロジック アプリ インスタンスが作成されます。

Ethereum ブロックチェーン コネクタには、1 つのトリガーといくつかのアクションが備わっています。 使用するトリガーまたはアクションは、シナリオによって異なります。

実際のワークフローに応じて、次のいずれかのセクションを選択します。

* ブロックチェーンでイベントが発生したときにトリガーする場合は、[イベント トリガーを使用します](#use-the-event-trigger)。
* スマート コントラクトを照会するか、デプロイする場合は、[アクションを使用します](#use-actions)。
* 一般的なシナリオに従う場合は、[開発者キットを使用してワークフローを生成します](#generate-a-workflow)。

## <a name="use-the-event-trigger"></a>イベント トリガーを使用する

スマート コントラクト イベントの発生後にロジック アプリを実行する場合は、Ethereum ブロックチェーン イベント トリガーを使用します。 たとえば、スマート コントラクト関数が呼び出されたときにメールを送信するとします。

1. ロジック アプリ デザイナーで、Ethereum ブロックチェーン コネクタを選択します。
1. **[トリガー]** タブで、 **[When a smart contract event occurs]\(スマート コントラクト イベントの発生時\)** を選択します。
1. Azure Blockchain Service に対する API 接続を変更するか[作成](#create-an-api-connection)します。
1. イベントを確認するスマート コントラクトの詳細を入力します。

    ![イベント トリガーのプロパティ](./media/ethereum-logic-app/event-properties.png)

    | プロパティ | 説明 |
    |----------|-------------|
    | **[Contract ABI]\(コントラクト ABI\)** | コントラクトのアプリケーション バイナリ インターフェイス (ABI) は、スマート コントラクトのインターフェイスを定義するものです。 [コントラクト ABI を取得](#get-contract-abi)する方法をご確認ください。 |
    | **[Smart contract address]\(スマート コントラクト アドレス\)** | コントラクト アドレスは、Ethereum ブロックチェーン上のスマート コントラクトの宛先アドレスです。 [コントラクト アドレスを取得](#get-contract-address)する方法をご確認ください。 |
    | **[イベント名]** | 確認するスマート コントラクト イベントを選択します。 イベントによって、ロジック アプリがトリガーされます。 |
    | **[間隔]** と **[頻度]** | イベントを確認する頻度を選択します。 |

1. **[保存]** を選択します。

ロジック アプリを完成させるために、Ethereum ブロックチェーン イベント トリガーに基づいてアクションを実行する新しいステップを追加します。 たとえば、メールの送信などです。

## <a name="use-actions"></a>アクションを使用する

ロジック アプリがブロックチェーン台帳に対してアクションを実行するようにしたい場合は、Ethereum ブロックチェーン アクションを使用します。 たとえば、ロジック アプリに対して HTTP 要求が行われたときにスマート コントラクト関数を呼び出す、REST ベースのマイクロサービスを作成するとします。

コネクタ アクションにはトリガーが必要です。 Ethereum ブロックチェーン コネクタ アクションは、トリガーの次のステップとして使用できます。 たとえば、マイクロサービスの HTTP 要求トリガーなどです。

1. ロジック アプリ デザイナーで、トリガーに続く **[新しいステップ]** を選択します。
1. Ethereum ブロックチェーン コネクタを選択します。
1. **[アクション]** タブから、使用可能なアクションのいずれかを選択します。

    ![アクションのプロパティ](./media/ethereum-logic-app/action-properties.png)

1. Azure Blockchain Service に対する API 接続を変更するか[作成](#create-an-api-connection)します。
1. 選択したアクションに応じて、使用するスマート コントラクト関数に関する次の詳細情報を入力します。

    | プロパティ | 説明 |
    |----------|-------------|
    | **[Contract ABI]\(コントラクト ABI\)** | コントラクトのアプリケーション バイナリ インターフェイス (ABI) は、スマート コントラクトのインターフェイスを定義するものです。 [コントラクト ABI を取得](#get-contract-abi)する方法をご確認ください。 |
    | **[Contract bytecode]\(コントラクトのバイトコード\)** | スマート コントラクトのコンパイルされたバイトコード。 [コントラクトのバイトコードを取得](#get-contract-bytecode)する方法をご確認ください。 |
    | **[Smart contract address]\(スマート コントラクト アドレス\)** | コントラクト アドレスは、Ethereum ブロックチェーン上のスマート コントラクトの宛先アドレスです。 [コントラクト アドレスを取得](#get-contract-address)する方法をご確認ください。 |
    | **[Smart contract function name]\(スマート コントラクト関数名\)** | アクションで使用するスマート コントラクト関数名を選択します。 この一覧は、コントラクト ABI の詳細情報から設定されます。 |

    スマート コントラクト関数名を選択すると、関数パラメーター用の必須フィールドが表示される場合があります。 自分のシナリオに必要な値または動的コンテンツを入力します。

これで、ロジック アプリを使用できるようになりました。 ロジック アプリのイベントがトリガーされると、Ethereum ブロックチェーン アクションが実行されます。 たとえば、HTTP 要求トリガーによって、スマート コントラクトの状態値を照会する Ethereum ブロックチェーン アクションが実行され、その結果、HTTP 応答を通じて値が返されます。

## <a name="generate-a-workflow"></a>ワークフローを生成する

Visual Studio Code 拡張機能である Azure Blockchain Development Kit for Ethereum では、一般的なシナリオ向けのロジック アプリ ワークフローを生成できます。 使用できるシナリオは次の 4 つです。

* Azure SQL Database へのデータの発行
* Azure Event Grid または Azure Service Bus へのイベント発行
* レポートの発行
* REST ベースのマイクロサービス

 Azure Blockchain Development Kit では、ブロックチェーンの開発を簡素化するために Truffle を使用します。 スマート コントラクトに基づいてロジック アプリを生成するには、スマート コントラクト用の Truffle ソリューションが必要です。 また、自分の Azure Blockchain Service コンソーシアム ネットワークに対する接続も必要です。 詳細については、[Visual Studio Code を使用した Azure Blockchain Service コンソーシアム ネットワークへの接続に関するクイックスタート](connect-vscode.md)を参照してください。

たとえば、次の手順では、クイックスタートの **HelloBlockchain** スマート コントラクトに基づいて、REST ベースのマイクロサービス ロジック アプリを生成します。

1. VS Code エクスプローラーのサイドバーで、自分のソリューションの **[コントラクト]** フォルダーを展開します。
1. **[HelloBlockchain.sol]** を右クリックし、メニューの **[Generate microservices for smart contracts]\(スマート コントラクト用のマイクロサービスの生成\)** を選択します。

    ![ロジック アプリを生成する](./media/ethereum-logic-app/generate-logic-app.png)

1. コマンド パレットで、 **[Logic App]** を選択します。
1. **コントラクト アドレス**を入力します。 詳細については、[コントラクト アドレスの取得方法](#get-contract-address)に関するセクションを参照してください。
1. そのロジック アプリ用の Azure サブスクリプションとリソース グループを選択します。

    ロジック アプリの構成とコード ファイルは、**generatedLogicApp** ディレクトリに生成されます。

1. **generatedLogicApp/HelloBlockchain** ディレクトリを表示します。 スマート コントラクト関数、イベント、プロパティごとに、ロジック アプリの JSON ファイルがあります。
1. **generatedLogicApp/HelloBlockchain/Service/property.RequestMessage.logicapp.json** ファイルを開いて内容をコピーします。

    ![RequestMessage プロパティの JSON](./media/ethereum-logic-app/requestmessage.png)

1. 自分のロジック アプリ内で、 **[ロジック アプリ コード ビュー]** を選択します。 既存の JSON を、生成されたロジック アプリの JSON に置き換えます。

    ![コード ビュー内でロジック アプリの構成を置き換える](./media/ethereum-logic-app/code-view.png)

1. **[デザイナー]** を選択して、デザイナー ビューに切り替えます。
1. ロジック アプリには、シナリオ用の基本的なステップが含まれています。 ただし、Ethereum ブロックチェーン コネクタの構成の詳細を更新する必要があります。
1. **[接続]** のステップを選択してから、Azure Blockchain Service に対する API 接続を変更するか[作成](#create-an-api-connection)します。

    ![マイクロサービス ロジック アプリ](./media/ethereum-logic-app/microservice-logic-app.png)

1. これで、ロジック アプリを使用できるようになりました。 REST ベースのマイクロサービスをテストするには、ロジック アプリの要求 URL に HTTP POST 要求を発行します。 **[HTTP 要求の受信時]** ステップから **HTTP POST URL** をコピーします。

    ![HTTP POST URL](./media/ethereum-logic-app/post-url.png)

1. cURL を使用して HTTP POST 要求を作成します。 プレースホルダーのテキストである **\<HTTP POST URL\>** を、前の手順の URL に置き換えます。

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    cURL コマンドは、ロジック アプリからの応答を返します。 この場合は、**RequestMessage** スマート コントラクト関数からの出力です。

    ![RequestMessage プロパティの出力](./media/ethereum-logic-app/curl.png)

開発キットの使用方法の詳細については、[Azure Blockchain Development Kit for Ethereum に関する Wiki](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) ページを参照してください。

## <a name="create-an-api-connection"></a>API 接続を作成する

Ethereum ブロックチェーン コネクタでは、ブロックチェーンへの API 接続が必要です。 API コネクタは、複数のロジック アプリに使用できます。 一部のプロパティは必須であり、他のプロパティについてはシナリオによって異なります。

> [!IMPORTANT]
> ブロックチェーン上のトランザクションを作成するには、秘密キーか、アカウント アドレスとパスワードが必要です。 必要な認証の形式は 1 つだけです。 秘密キーとアカウントの詳細の両方を指定する必要はありません。 コントラクトに対してクエリを実行する場合、トランザクションは必要ありません。 コントラクトの状態を照会するアクションを使用している場合は、秘密キーも、アカウント アドレスとパスワードも必要ありません。

Azure Blockchain Service メンバーに対する接続を設定するには、シナリオによっては次の一覧のプロパティが必要になる場合があります。

| プロパティ | 説明 |
|----------|-------------|
|**接続名** | API 接続の名前。 必須。 |
|**Ethereum RPC エンドポイント** | Azure Blockchain Service のトランザクション ノードの HTTP アドレス。 必須。 [RPC エンドポイントを取得](#get-rpc-endpoint)する方法をご確認ください。 |
|**秘密キー** | Ethereum アカウントの秘密キー。 トランザクションには、秘密キーか、アカウント アドレスとパスワードが必要です。 [秘密キーを取得](#get-private-key)する方法をご確認ください。 |
|**Account address** | Azure Blockchain Service メンバー アカウントのアドレス。 トランザクションには、秘密キーか、アカウント アドレスとパスワードが必要です。 [アカウント アドレスを取得](#get-account-address)する方法をご確認ください。 |
|**アカウントのパスワード** | アカウントのパスワードは、メンバーを作成するときに設定されます。 パスワードのリセット方法の詳細については、「[Ethereum アカウント](consortium.md#ethereum-account)」を参照してください。|

## <a name="get-rpc-endpoint"></a>RPC エンドポイントを取得する

ブロックチェーン ネットワークに接続するには、Azure Blockchain Service RPC エンドポイント アドレスが必要です。 このエンドポイント アドレスは、Azure Blockchain Development Kit for Ethereum または Azure portal を使用して取得できます。

**開発キットを使用する:**

1. Visual Studio Code の **[Azure Blockchain Service]** で、コンソーシアムを右クリックします。
1. [Copy RPC endpoint]\(RPC エンドポイントをコピーする\) を選択します。

    ![RPC エンドポイントをコピーする](./media/ethereum-logic-app/devkit-rpc.png)

    RPC エンドポイントがクリップボードにコピーされます。

**Azure portal を使用する:**

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. Azure Blockchain Service メンバーに移動します。 **[Transaction nodes]\(トランザクション ノード\)** と既定のトランザクション ノード リンクを選択します。

    ![既定のトランザクション ノードを選択する](./media/ethereum-logic-app/transaction-nodes.png)

1. **[接続文字列] > [アクセス キー]** の順に選択します。
1. **[HTTPS (Access key 1)]\(HTTPS (アクセス キー 1)\)** またはアクセス キー 2 からエンドポイント アドレスをコピーします。

    ![接続文字列](./media/ethereum-logic-app/connection-string.png)

    RPC エンドポイントは、ご自分の Azure Blockchain Service メンバー トランザクション ノードのアドレスとアクセス キーを含む HTTPS URL です。

## <a name="get-private-key"></a>秘密キーを取得する

ブロックチェーンにトランザクションを送信する際に、Ethereum アカウントの秘密キーを使用して認証することができます。 ご自分の Ethereum アカウントの公開キーと秘密キーは、12 ワードのニーモニックから生成されます。 Azure Blockchain Service のコンソーシアム メンバーに接続すると、Azure Blockchain Development Kit for Ethereum によってニーモニックが生成されます。 開発キット拡張機能を使用して、エンドポイントのアドレスを取得できます。

1. Visual Studio Code で、コマンド パレットを開きます (F1 キー)。
1. **[Azure Blockchain: Retrieve private key]\(Azure Blockchain: 秘密キーの取得\)** を選択します。
1. コンソーシアム メンバーへの接続時に保存したニーモニックを選択します。

    ![ニーモニックを選択する](./media/ethereum-logic-app/private-key.png)

    秘密キーがクリップボードにコピーされます。

## <a name="get-account-address"></a>アカウントのアドレスを取得する

ブロックチェーンにトランザクションを送信する際に、メンバー アカウントとパスワードを使用して認証することができます。 パスワードは、メンバーを作成するときに設定されます。

1. Azure portal で、自分の Azure Blockchain Service の概要ページに移動します。
1. **[メンバー アカウント]** のアドレスをコピーします。

    ![メンバー アカウントをコピーする](./media/ethereum-logic-app/member-account.png)

アカウントのアドレスとパスワードの詳細については、「[Ethereum アカウント](consortium.md#ethereum-account)」を参照してください。

## <a name="get-contract-abi"></a>コントラクト ABI を取得する

コントラクトのアプリケーション バイナリ インターフェイス (ABI) は、スマート コントラクトのインターフェイスを定義するものです。 これは、スマート コントラクトを操作する方法を示しています。 コントラクト ABI は、Azure Blockchain Development Kit for Ethereum を使用して、または Solidity のコンパイラ コントラクト メタデータ ファイルから取得できます。

**開発キットを使用する:**

開発キットまたは Truffle を使用してスマート コントラクトをビルドした場合は、拡張機能を使用して、コントラクト ABI をクリップボードにコピーできます。

1. Visual Studio Code のエクスプローラー ウィンドウで、自分の Solidity プロジェクトの **build/contracts** フォルダーを展開します。
1. コントラクト メタデータの JSON ファイルを右クリックします。 ファイル名は、スマート コントラクト名に **.json** 拡張子を付けたものです。
1. **[Copy contract ABI]\(コントラクト ABI をコピーする\)** を選択します。

    ![DevKit を使用してコントラクト ABI をコピーする](./media/ethereum-logic-app/abi-devkit.png)

    コントラクト ABI がクリップボードにコピーされます。

**コントラクト メタデータ ファイルを使用する:**

1. 自分の Solidity プロジェクトの **build/contracts** フォルダー内に含まれているコントラクト メタデータ ファイルを開きます。 ファイル名は、スマート コントラクト名に **.json** 拡張子を付けたものです。
1. JSON ファイルで **abi** セクションを見つけます。
1. **abi** JSON 配列をコピーします。

    ![メタデータ内のコントラクト ABI セクション](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-contract-bytecode"></a>コントラクトのバイトコードを取得する

コントラクトのバイトコードは、Ethereum 仮想マシンによって実行される、コンパイル済みのスマート コントラクトです。 コントラクトのバイトコードは、Azure Blockchain Development Kit for Ethereum を使用して、または Solidity のコンパイラから取得できます。

**開発キットを使用する:**

開発キットまたは Truffle を使用してスマート コントラクトをビルドした場合は、拡張機能を使用して、コントラクトのバイトコードをクリップボードにコピーできます。

1. Visual Studio Code のエクスプローラー ウィンドウで、自分の Solidity プロジェクトの **build/contracts** フォルダーを展開します。
1. コントラクト メタデータの JSON ファイルを右クリックします。 ファイル名は、スマート コントラクト名に **.json** 拡張子を付けたものです。
1. **[Copy contract bytecode]\(コントラクトのバイトコードをコピーする\)** を選択します。

    ![DevKit を使用してコントラクトのバイトコードをコピーする](./media/ethereum-logic-app/bytecode-devkit.png)

    コントラクトのバイトコードがクリップボードにコピーされます。

**コントラクト メタデータ ファイルを使用する:**

1. 自分の Solidity プロジェクトの **build/contracts** フォルダー内に含まれているコントラクト メタデータ ファイルを開きます。 ファイル名は、スマート コントラクト名に **.json** 拡張子を付けたものです。
1. JSON ファイル内の **bytecode** 要素を見つけます。
1. **bytecode** の値をコピーします。

    ![メタデータを使用してバイトコードをコピーする](./media/ethereum-logic-app/bytecode-metadata.png)

**Solidity コンパイラを使用する:**

`solc --bin <smart contract>.sol` コマンドを使用して、コントラクトのバイトコードを生成します。

## <a name="get-contract-address"></a>コントラクト アドレスを取得する

コントラクト アドレスは、Ethereum ブロックチェーン上のスマート コントラクトの宛先アドレスです。 このアドレスを使用して、トランザクションを送信したり、スマート コントラクトの状態を照会します。 コントラクト アドレスは、Truffle の移行の出力またはコントラクト メタデータ ファイルから取得できます。

**Truffle の移行の出力を使用する:**

Truffle により、スマート コントラクトのデプロイ後に、コントラクト アドレスが表示されます。 出力から**コントラクト アドレス**をコピーします。

![Truffle の出力からのコントラクト アドレス](./media/ethereum-logic-app/contract-address-truffle.png)

**コントラクト メタデータ ファイルを使用する:**

1. 自分の Solidity プロジェクトの **build/contracts** フォルダー内に含まれているコントラクト メタデータ ファイルを開きます。 ファイル名は、スマート コントラクト名に **.json** 拡張子を付けたものです。
1. JSON ファイルで **networks** セクションを見つけます。
1. プライベート ネットワークは、整数のネットワーク ID で識別されます。 network セクション内のアドレス値を見つけます。
1. **address** の値をコピーします。

![メタデータからのコントラクト アドレス](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>次の手順

[Azure Logic Apps を使用したブロックチェーン接続の一般的なシナリオ](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true)をご覧ください。
