---
title: Azure Functions Core Tools の操作
description: Azure 関数を Azure Functions で実行する前に、ローカル コンピューターのコマンド プロンプトまたはターミナルでコーディングしてテストする方法について説明します。
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: dd36895a34b36bbdf8e796cf629ab031613663cd
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208884"
---
# <a name="work-with-azure-functions-core-tools"></a>Azure Functions Core Tools の操作

Azure Functions Core Tools を使用すると、ローカル コンピューター上のコマンド プロンプトまたはターミナルから関数を開発およびテストできます。 ローカル関数はライブ Azure サービスに接続できるため、完全な Functions ランタイムを使用してローカル コンピューター上で関数をデバッグすることができます。 Azure サブスクリプションに関数アプリをデプロイすることもできます。

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Core Tools を使用して、ローカル コンピューターで関数を開発し、Azure に発行するには、次の基本的な手順に従います。

> [!div class="checklist"]
> * [Core Tools と依存関係をインストールします。](#v2)
> * [言語固有のテンプレートから関数アプリ プロジェクトを作成します。](#create-a-local-functions-project)
> * [トリガーとバインド拡張を登録します。](#register-extensions)
> * [ストレージとその他の接続を定義します。](#local-settings-file)
> * [トリガーおよび言語固有のテンプレートから関数を作成します。](#create-func)
> * [関数をローカルで実行します。](#start)
> * [Azure にプロジェクトを発行します。](#publish)

## <a name="core-tools-versions"></a>Core Tools のバージョン

Azure Functions Core Tools には、3 つのバージョンがあります。 使用するバージョンは、ローカル開発環境、[選択した言語](supported-languages.md)、および必要なサポートのレベルによって異なります。

+ **バージョン 1.x**: Azure Functions ランタイムのバージョン 1.x をサポートします。 ツールのこのバージョンは Windows コンピューター上でのみサポートされ、[npm パッケージ](https://www.npmjs.com/package/azure-functions-core-tools)からインストールされます。

+ [**バージョン 2.x および 3.x**](#v2): [Azure Functions ランタイムのバージョン 2.x および 3.x](functions-versions.md) をサポートします。 これらのバージョンでは [Windows](/azure/azure-functions/functions-run-local?tabs=windows#v2)、[macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)、および [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2) がサポートされ、インストールにはプラットフォーム固有のパッケージ マネージャーまたは npm を使用します。

特に記載がない限り、この記事の例ではバージョン 3.x を対象にしています。

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions Core Tools のインストール

[Azure Functions Core Tools] は、Azure Functions ランタイムを実行するのと同じランタイムのバージョンを含み、ローカルの開発コンピューターで実行できます。 また、このツールには、関数を作成し、Azure に接続し、関数プロジェクトをデプロイするためのコマンドも用意されています。

### <a name="v2"></a>バージョン 2.x および 3.x

バージョン 2.x および 3.x のツールは、.NET Core 上に構築されている Azure Functions ランタイムを使用します。 このバージョンは、[Windows](/azure/azure-functions/functions-run-local?tabs=windows#v2)、[macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)、[Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2) など、.NET Core が対応しているすべてのプラットフォームでサポートされています。 

> [!IMPORTANT]
> [拡張バンドル]を使用すると、.NET Core SDK をインストールするための要件をバイパスできます。

# <a name="windows"></a>[Windows](#tab/windows)

次の手順では、npm を使用して Windows 上に Core Tools をインストールします。 また、[Chocolatey](https://chocolatey.org/) を使用することもできます。 詳細については、[Core Tools の readme ](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)に関するページを参照してください。

1. [Node.js]をインストールします。これには、npm が同梱されています。
    - 2x バージョンのツールの場合、Node.js 8.5 以降のバージョンのみがサポートされています。
    - 3\.x バージョンのツールの場合、Node.js 10 以降のバージョンのみがサポートされています。

1. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ##### <a name="v2x"></a>v2.x

    ```bash
    npm install -g azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```bash
    npm install -g azure-functions-core-tools@3
    ```

   npm をダウンロードして Core Tools パッケージをインストールするには、数分かかる場合があります。

1. [拡張バンドル]を使用しない場合、[.NET Core 2.x SDK for Windows](https://www.microsoft.com/net/download/windows) をインストールします。

# <a name="macos"></a>[MacOS](#tab/macos)

次の手順では、Homebrew を使用して macOS 上に Core Tools をインストールします。

1. まだインストールしていない場合は、[Homebrew](https://brew.sh/) をインストールします。

1. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ##### <a name="v2x"></a>v2.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```

# <a name="linux"></a>[Linux](#tab/linux)

次の手順では [APT ](https://wiki.debian.org/Apt)を使用して、Ubuntu/Debian Linux ディストリビューションに Core Tools をインストールします。 他の Linux ディストリビューションについては、[Core Tools の readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux) に関するページを参照してください。

1. パッケージの整合性を検証するには、Microsoft パッケージ リポジトリの GPG キーをインストールします。

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. APT 更新を行う前に、.NET 開発ソース リストを設定します。

   Ubuntu の APT ソース リストを設定するには、次のコマンドを実行します。

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   Debian の APT ソース リストを設定するには、次のコマンドを実行します。

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. `/etc/apt/sources.list.d/dotnetdev.list` ファイルを調べて、次に示す適切な Linux バージョン文字列のいずれかがあることを確認します。

    | Linux ディストリビューション | Version |
    | --------------- | ----------- |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. APT ソースの更新を開始します。

    ```bash
    sudo apt-get update
    ```

1. 次のコマンドを使って、Core Tools のパッケージをインストールします。

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. [拡張バンドル]を使用しない場合、[.NET Core 2.x SDK for Linux](https://www.microsoft.com/net/download/linux) をインストールします。

---

## <a name="create-a-local-functions-project"></a>ローカル関数プロジェクトを作成する

関数プロジェクト ディレクトリには、[host.json](functions-host-json.md) ファイル、[local.settings.json](#local-settings-file) ファイル、および個々の関数のコードを含むサブフォルダーが含まれています。 このディレクトリは、Azure の関数アプリに相当します。 Functions のフォルダー構造の詳細については、[Azure Functions の開発者向けガイド](functions-reference.md#folder-structure)を参照してください。

バージョン 2.x では、プロジェクトの初期化時に既定の言語を選択する必要があります。 バージョン 2.x では、追加されたすべての関数に既定の言語テンプレートが使用されます。 バージョン 1.x では、関数を作成するたびに言語を指定します。

ターミナル ウィンドウまたはコマンド プロンプトで、次のコマンドを実行してプロジェクトおよびローカルの Git リポジトリを作成します。

```bash
func init MyFunctionProj
```

プロジェクト名を指定すると、その名前の新しいフォルダーの作成と初期化が実行されます。 それ以外の場合は、現在のフォルダーが初期化されます。  
バージョン 2.x では、コマンドを実行するときにプロジェクトのランタイムを選択する必要があります。 

```output
Select a worker runtime:
dotnet
node
python 
powershell
```

上/下方向キーを使用して言語を選択し、Enter キーを押します。 JavaScript または TypeScript 関数の開発を計画している場合は、**ノード**を選択し、言語を選択します。 TypeScript には[いくつかの追加要件](functions-reference-node.md#typescript)があります。 

出力は、次の JavaScript プロジェクトの例のようになります。

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init` では、次のオプションがサポートされています。特に注意書きがない限り、バージョン 2.x だけが対象です。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | [C# クラス ライブラリ (.cs) プロジェクト](functions-dotnet-class-library.md)を初期化します。 |
| **`--csx`** | [C# スクリプト (.csx) プロジェクト](functions-reference-csharp.md)を初期化します。 後続のコマンドで、`--csx` を指定する必要があります。 |
| **`--docker`** | 選択した `--worker-runtime` に基づく基本イメージを使用して、コンテナーの Dockerfile を作成します。 カスタム Linux コンテナーに発行する場合は、このオプションを使用します。 |
| **`--docker-only`** |  既存のプロジェクトに Dockerfile を追加します。 local.settings.json で設定されていないか指定されていない場合、worker ランタイムのプロンプトを表示します。 既存のプロジェクトをカスタム Linux コンテナーに発行する場合は、このオプションを使用します。 |
| **`--force`** | プロジェクトに既存のファイルがある場合でも、プロジェクトを初期化します。 この設定は、同じ名前の既存のファイルを上書きします。 プロジェクト フォルダー内の他のファイルには影響ありません。 |
| **`--java`**  | [Java プロジェクト](functions-reference-java.md)を初期化します。 |
| **`--javascript`**<br/>**`--node`**  | [JavaScript プロジェクト](functions-reference-node.md)を初期化します。 |
| **`--no-source-control`**<br/>**`-n`** | バージョン 1.x での Git リポジトリの既定の作成を禁止します。 バージョン 2.x では、Git リポジトリは既定では作成されません。 |
| **`--powershell`**  | [PowerShell プロジェクト](functions-reference-powershell.md)を初期化します。 |
| **`--python`**  | [Python プロジェクト](functions-reference-python.md)を初期化します。 |
| **`--source-control`** | Git リポジトリを作成するかどうかを制御します。 既定では、リポジトリは作成されません。 `true` を指定すると、リポジトリが作成されます。 |
| **`--typescript`**  | [TypeScript プロジェクト](functions-reference-node.md#typescript)を初期化します。 |
| **`--worker-runtime`** | プロジェクトの言語ランタイムを設定します。 サポートされる値は、`csharp`、`dotnet`、`java`、`javascript`、`node` (JavaScript)、`powershell`、`python`、`typescript` です。 設定しないと、初期化中にランタイムの選択を求められます。 |

> [!IMPORTANT]
> 既定では、Core Tools のバージョン 2.x では、.NET ランタイムの関数アプリ プロジェクトは [C# クラス プロジェクト](functions-dotnet-class-library.md) (.csproj) として作成されます。 Visual Studio または Visual Studio Code で使用できるこれらの C# プロジェクトは、テスト中および Azure への発行時にコンパイルされます。 バージョン 1.x およびポータルで作成される同じ C# スクリプト (.csx) ファイルを代わりに作成および使用したい場合は、関数を作成して展開するときに、`--csx` パラメーターを指定する必要があります。

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

既定では、プロジェクトが Azure に発行されても、これらの設定は自動的に移行されません。 [発行する際](#publish)に `--publish-local-settings` スイッチを使用して、これらの設定が Azure 内の関数アプリに追加されていることを確認してください。 **ConnectionStrings** 内の値は発行されないことに注意してください。

関数アプリの設定値は、コードの中で環境変数として読み込むこともできます。 詳細については、以下の言語固有のリファレンス トピックの「環境変数」のセクションを参照してください。

* [C# プリコンパイル済み](functions-dotnet-class-library.md#environment-variables)
* [C# スクリプト (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

有効なストレージ接続文字列が [`AzureWebJobsStorage`] に設定されていなく、エミュレーターが使用されていない場合は、次のエラー メッセージが表示されます。

> local.settings.json に AzureWebJobsStorage の値がありません。 これは HTTP 以外のすべてのトリガーに必要です。 "func azure functionapp fetch-app-settings \<functionAppName\>" を実行するか、local.settings.json で接続文字列を指定することができます。

### <a name="get-your-storage-connection-strings"></a>ストレージ接続文字列の取得

開発のために Microsoft Azure ストレージ エミュレーターを使用している場合であっても、実際のストレージに接続してテストすることができます。 [ストレージ アカウントを作成済み](../storage/common/storage-create-storage-account.md)である場合は、次の方法のいずれかで、有効なストレージ接続文字列を取得できます。

- [Azure portal] から、 **[ストレージ アカウント]** を検索して選択します。 
  ![Azure portal で [ストレージ アカウント] を選択する](./media/functions-run-local/select-storage-accounts.png)
  
  使用するストレージ アカウントを選択し、 **[設定]** の **[アクセス キー]** を選択してから、 **[接続文字列]** の値のいずれかをコピーします。
  ![Azure portal から接続文字列をコピーする](./media/functions-run-local/copy-storage-connection-portal.png)

- [Azure Storage Explorer](https://storageexplorer.com/) を使用して、Azure アカウントに接続します。 **Explorer** でサブスクリプションを展開し、 **[ストレージ アカウント]** を展開し、ストレージ アカウントを選択して、プライマリまたはセカンダリの接続文字列をコピーします。

  ![Storage Explorer から接続文字列をコピーする](./media/functions-run-local/storage-explorer.png)

+ Core Tools を使用して、次のコマンドのいずれで Azure から接続文字列をダウンロードします。

  + 既存の関数アプリからすべての設定をダウンロードします。

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + 特定のストレージ アカウントの接続文字列を取得します。

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Azure にまだサインインしていない場合は、それを求めるメッセージが表示されます。

## <a name="create-func"></a>関数を作成する

関数を作成するには、次のコマンドを実行します。

```bash
func new
```

バージョン 2.x では、`func new` を実行したときに関数アプリの既定の言語のテンプレートを選択するように求められ、次に関数の名前を選択するように求められます。 バージョン 1.x では、さらに言語を選択するように求められます。

```output
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
```

次のキュー トリガーの出力に示すように、指定した関数名のサブフォルダーに関数のコードが生成されます。

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

次の引数を使用して、コマンドでこれらのオプションを指定することもできます。

| 引数     | 説明                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (バージョン 2.x) バージョン 1.x およびポータルで使用されるものと同じ C# スクリプト (.csx) テンプレートを生成します。 |
| **`--language`** 、 **`-l`**| テンプレート プログラミング言語。C#、F#、JavaScript など。 このオプションは、バージョン 1.x で必須です。 バージョン 2.x では、このオプションを使用しないか、または worker ランタイムと一致する言語を選択してください。 |
| **`--name`** 、 **`-n`** | 関数名。 |
| **`--template`** 、 **`-t`** | サポートされている各言語で使用可能なテンプレートの完全な一覧を表示するには、`func templates list` コマンドを使います。   |

たとえば、JavaScript HTTP トリガーを 1 つのコマンドで作成するには、次を実行します。

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

キューによってトリガーされる関数を 1 つのコマンドで作成するには、次を実行します。

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>関数をローカルで実行する

Functions プロジェクトを実行するには、Functions ホストを実行します。 ホストによって、プロジェクトのすべての関数に対するトリガーが有効になります。 

### <a name="version-2x"></a>バージョン 2.x

ランタイムのバージョン 2.x では、開始コマンドはプロジェクトの言語によって異なります。

#### <a name="c"></a>C\#

```command
func start --build
```

#### <a name="javascript"></a>JavaScript

```command
func start
```

#### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

### <a name="version-1x"></a>バージョン 1.x

Functions ランタイムのバージョン 1.x では、次の例のように `host` コマンドが必要です。

```command
func host start
```

`func start` では、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | 実行前に現在のプロジェクトをビルドしません。 dotnet プロジェクトの場合のみ。 既定値は false に設定されます。 Version 2.x のみ。 |
| **`--cert`** | 秘密キーが含まれる .pfx ファイルへのパス。 `--useHttps` でのみ使用されます。 Version 2.x のみ。 |
| **`--cors-credentials`** | クロスオリジン認証済み要求 (つまり、Cookie と Authentication ヘッダー) バージョン 2.x のみを許可します。 |
| **`--cors`** | CORS オリジンのコンマ区切りのリスト (スペースなし)。 |
| **`--language-worker`** | 言語ワーカーを構成するための引数。 たとえば、[デバッグ ポートとその他の必要な引数](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers)を指定して、言語ワーカーのデバッグを有効にすることができます。 Version 2.x のみ。 |
| **`--nodeDebugPort`** 、 **`-n`** | 使用する Node.js デバッガーのポート。 既定値はlaunch.json または 5858 の値。 バージョン 1.x のみ。 |
| **`--password`** | .pfx ファイルのパスワードまたはパスワードが格納されているファイルのいずれか。 `--cert` でのみ使用されます。 Version 2.x のみ。 |
| **`--port`** 、 **`-p`** | ローカル ポート。このポートでリッスンします。 既定値:7071。 |
| **`--pause-on-error`** | プロセスを終了する前に、追加入力を一時停止します。 統合開発環境 (IDE) から Core Tools を起動した場合にのみ使用されます。|
| **`--script-root`** 、 **`--prefix`** | 実行または展開される関数アプリのルートへのパスを指定するために使用されます。 これは、サブフォルダーにプロジェクト ファイルを生成するコンパイル済みプロジェクトに使用されます。 たとえば、C# クラス ライブラリ プロジェクトをビルドすると、host.json、local.settings.json、および function.json ファイルが、`MyProject/bin/Debug/netstandard2.0` のようなパスの "*ルート*" サブフォルダーに生成されます。 この場合は、プレフィックスを `--script-root MyProject/bin/Debug/netstandard2.0` と設定します。 これは、Azure で実行する場合の関数アプリのルートです。 |
| **`--timeout`** 、 **`-t`** | Functions ホスト開始のタイムアウト (秒単位)。 既定値は20 秒。|
| **`--useHttps`** | `http://localhost:{port}` ではなく `https://localhost:{port}` にバインドします。 既定では、このオプションにより、信頼された証明書がコンピューターに作成されます。|

Functions ホストの起動時、HTTP によってトリガーされる関数の URL が出力されます。

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>ローカルで実行される場合、HTTP エンドポイントに対して承認は適用されません。 つまり、ローカルの HTTP 要求はすべて、`authLevel = "anonymous"` として処理されます。 詳細については、[HTTP バインディング](functions-bindings-http-webhook-trigger.md#authorization-keys)に関する記事を参照してください。

### <a name="passing-test-data-to-a-function"></a>関数へのテスト データの受け渡し

関数をローカルでテストするには、[Functions ホストを起動](#start)し、HTTP 要求を使用してローカル サーバーでエンドポイントを呼び出します。 呼び出すエンドポイントは、関数の種類によって異なります。

>[!NOTE]
> このトピックの例では、cURL ツールを使用して端末またはコマンド プロンプトから HTTP 要求を送信します。 お好みのツールを使用して HTTP 要求をローカル サーバーに送信できます。 Linux ベースのシステムと Windows 10 ビルド 17063 以降では既定で cURL ツールを使用できます。 以前の Windows では、最初に [cURL ツール](https://curl.haxx.se/)をダウンロードし、インストールする必要があります。

関数のテストの全般的な情報については、「[Azure Functions のコードをテストするための戦略](functions-test-a-function.md)」を参照してください。

#### <a name="http-and-webhook-triggered-functions"></a>HTTP と webhook でトリガーされる関数

次のエンドポイントを呼び出して、HTTP と webhook でトリガーされる関数をローカルで実行できます。

    http://localhost:{port}/api/{function_name}

Functions ホストがリッスンしているのと同じサーバー名とポートを使用していることを確認してください。 これは、Functions ホストの起動時に生成される出力で確認できます。 トリガーでサポートされている任意の HTTP メソッドを使用して、この URL を呼び出すことができます。

次の cURL コマンドは、`MyHttpTrigger` クイックスタート関数を、クエリ文字列で渡された _name_ パラメーターを使用して、GET 要求からトリガーします。

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

次の例は、要求本文で _name_ を渡す POST 要求から呼び出される同じ関数です。

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

ブラウザーから GET 要求を行ってクエリ文字列でデータを渡すことが可能です。 その他すべての HTTP メソッドについては、cURL、Fiddler、Postman、または類似の HTTP テスト ツールを使用する必要があります。

#### <a name="non-http-triggered-functions"></a>HTTP でトリガーされない関数

HTTP トリガーと webhook を除く、あらゆる種類の関数の場合、管理エンドポイントを呼び出すことによって、関数をローカルでテストできます。 HTTP POST 要求を使ってローカル サーバーでこのエンドポイントを呼び出すと、関数がトリガーされます。 必要に応じて、POST 要求の本体でテスト データを実行に渡すことができます。 この機能は、Azure Portal の **[テスト]** タブに似ています。

次の管理者エンドポイントを呼び出して、非 HTTP 関数をトリガーします。

    http://localhost:{port}/admin/functions/{function_name}

テスト データを関数の管理者エンドポイントに渡すには、そのデータを POST 要求メッセージの本文で提供する必要があります。 メッセージ本文は、次の JSON 形式にする必要があります。

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>` 値には、関数が必要とする形式でデータが含まれています。 次の cURL の例は、`QueueTriggerJS` 関数に対する POST です。 この場合、入力は、キューにあることが期待されるメッセージに相当する文字列です。

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>バージョン 1.x での `func run` コマンドの使用

>[!IMPORTANT]
> `func run` コマンドは、ツールのバージョン 2.x ではサポートされていません。 詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](set-runtime-version.md)」を参照してください。

`func run <FunctionName>` を使用して関数を直接呼び出し、関数の入力データを渡すこともできます。 このコマンドは、Azure Portal の **[テスト]** タブを使用して関数を実行するのと似ています。

`func run` では、次のオプションがサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--content`** 、 **`-c`** | インライン コンテンツ。 |
| **`--debug`** 、 **`-d`** | 関数を実行する前に、デバッガーを、ホスト プロセスにアタッチします。|
| **`--timeout`** 、 **`-t`** | Functions ホストの準備が完了するまでの待機時間 (秒単位)。|
| **`--file`** 、 **`-f`** | コンテンツとして使用するファイル名。|
| **`--no-interactive`** | 入力を促しません。 自動シナリオで便利です。|

たとえば、HTTP によってトリガーされる関数を呼び出して、コンテンツ本文を渡すには、次のコマンドを実行します。

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Azure に発行する

Azure Functions Core Tools でサポートされている 2 種類のデプロイは、[Zip デプロイ](functions-deployment-technologies.md#zip-deploy)による関数アプリへの関数プロジェクト ファイルの直接的なデプロイと、[カスタム Docker コンテナーのデプロイ](functions-deployment-technologies.md#docker-container)です。 コードをデプロイする [Azure サブスクリプションで関数アプリを作成してある](functions-cli-samples.md#create)必要があります。 コンパイルを必要とするプロジェクトは、バイナリをデプロイできるように、ビルドする必要があります。

プロジェクト フォルダーには、発行するべきではない言語固有のファイルやディレクトリが含まれている場合があります。 除外された項目は、ルート プロジェクト フォルダーの .funcignore ファイルにリストされます。     

### <a name="project-file-deployment"></a>デプロイ (プロジェクト ファイル)

Azure で ローカル コードを関数アプリに発行するには、`publish` コマンドを使用します。

```bash
func azure functionapp publish <FunctionAppName>
```

このコマンドは、Azure で既存の関数アプリに公開されるコマンドです。 サブスクリプションに存在しない `<FunctionAppName>` に発行しようとすると、エラーが表示されます。 Azure CLI を使用してコマンド プロンプトまたはターミナル ウィンドウから関数アプリを作成する方法については、「[サーバーレス実行用の Function App を作成する](./scripts/functions-cli-create-serverless.md)」を参照してください。 既定では、このコマンドでは[リモート ビルド](functions-deployment-technologies.md#remote-build)が使用され、アプリがデプロイされて、[デプロイ パッケージから実行](run-functions-from-deployment-package.md)されます。 この推奨されるデプロイ モードを無効にするには、`--nozip` オプションを使用します。

>[!IMPORTANT]
> Azure portal で関数アプリを作成すると、既定でバージョン 2.x の Function ランタイムが使用されます。 関数アプリにバージョン 1.x のランタイムを使用させるには、[バージョン 1.x での実行](functions-versions.md#creating-1x-apps)に関するページの説明に従ってください。
> 既存の関数がある関数アプリのランタイム バージョンを変更することはできません。

次の発行オプションは、1.x と 2.x の両方のバージョンに適用されます。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  local.settings.json の設定を Azure に発行し、設定が既に存在する場合は上書きを促します。 Microsoft Azure ストレージ エミュレーターを使用している場合は、まずアプリ設定を[実際のストレージ接続](#get-your-storage-connection-strings)に変更します。 |
| **`--overwrite-settings -y`** | `--publish-local-settings -i` を使用するときに、アプリの設定を上書きするプロンプトを抑制します。|

次の発行オプションは、バージョン 2.x でのみサポートされています。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`** 、 **`-o`** |  設定のみを発行し、コンテンツをスキップします。 既定値は prompt です。 |
|**`--list-ignored-files`** | 発行時に無視されるファイルの一覧を表示します。これは、.funcignore ファイルに基づきます。 |
| **`--list-included-files`** | 発行されるファイルの一覧を表示します。これは、.funcignore ファイルに基づきます。 |
| **`--nozip`** | 既定の `Run-From-Package` モードをオフにします。 |
| **`--build-native-deps`** | Python 関数アプリを発行するときに、.wheels フォルダーの生成をスキップします。 |
| **`--build`** 、 **`-b`** | Linux 関数アプリにデプロイするときにビルド アクションを実行します。 `remote` および `local` を受け入れます。 |
| **`--additional-packages`** | ネイティブの依存関係を構築するときにインストールするパッケージの一覧。 (例: `python3-dev libevent-dev`)。 |
| **`--force`** | 特定のシナリオで発行前の検証を無視します。 |
| **`--csx`** | C# スクリプト (.csx) プロジェクトを発行します。 |
| **`--no-build`** | .NET クラス ライブラリ関数をビルドしません。 |
| **`--dotnet-cli-params`** | コンパイル済み C# (.csproj) 関数を発行するとき、Core Tools は "dotnet build --output bin/publish" を呼び出します。 これに渡されるすべてのパラメーターは、コマンド ラインに追加されます。 |

### <a name="deployment-custom-container"></a>デプロイ (カスタム コンテナー)

Azure Functions では、[カスタム Docker コンテナー](functions-deployment-technologies.md#docker-container)に関数プロジェクトをデプロイできます。 詳しくは、「[カスタム イメージを使用して Linux で関数を作成する](functions-create-function-linux-custom-image.md)」をご覧ください。 カスタム コンテナーには、Dockerfile が必要です。 Dockerfile でアプリを作成するには、`func init` で --dockerfile オプションを使用します。

```bash
func deploy
```

次のカスタム コンテナー デプロイ オプションを使用できます。

| オプション     | 説明                            |
| ------------ | -------------------------------------- |
| **`--registry`** | 現在のユーザーがサインインしている Docker レジストリの名前。 |
| **`--platform`** | 関数アプリのホスティング プラットフォーム。 有効なオプションは `kubernetes` です。 |
| **`--name`** | 関数アプリの名前。 |
| **`--max`**  | 必要に応じて、デプロイする関数アプリ インスタンスの最大数を設定します。 |
| **`--min`**  | 必要に応じて、デプロイする関数アプリ インスタンスの最小数を設定します。 |
| **`--config`** | オプションのデプロイ構成ファイルを設定します。 |

## <a name="monitoring-functions"></a>関数の監視

関数の実行を監視するための推奨される方法は、Azure Application Insights との統合です。 また、ローカル コンピューターに実行ログをストリーミングすることもできます。 詳細については、「[Azure Functions を監視する](functions-monitoring.md)」を参照してください。

### <a name="application-insights-integration"></a>Application Insights の統合

Azure で関数アプリを作成するときに Application Insights の統合を有効にする必要があります。 何らかの理由で関数アプリが Application Insights インスタンスに接続されていない場合は、Azure portal でこの統合を簡単に行うことができます。 

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>ストリーミング ログを有効にする

関数によって生成されているログ ファイルのストリームは、ローカル コンピューター上のコマンド ライン セッションで表示できます。 

#### <a name="native-streaming-logs"></a>ネイティブ ストリーミング ログ

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

この種類のストリーミング ログを使用するには、関数アプリの Application Insights との統合を有効にする必要があります。   


## <a name="next-steps"></a>次のステップ

Azure Functions Core Tools [Microsoft 学習モジュール](https://docs.microsoft.com/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) を使用して Azure Functions を開発、テスト、および発行する方法について説明します。Azure Functions Core Tools は[オープンソースであり、GitHub ](https://github.com/azure/azure-functions-cli)でホストされています。  
バグまたは機能要求を提出するには、[GitHub の問題をオープン](https://github.com/azure/azure-functions-cli/issues)してください。

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
[拡張バンドル]: functions-bindings-register.md#extension-bundles
