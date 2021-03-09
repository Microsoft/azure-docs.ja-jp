---
title: チュートリアル - Azure Communication Services 用の Web アプリを準備する (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Azure Communication Services をサポートするベースライン Web アプリケーションを作成する方法について説明します
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 01/03/2012
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: cd535227b421f4fb56dac3afb37033e3d77f75f7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691537"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>チュートリアル:Azure Communication Services 用の Web アプリを準備する (Node.js)

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure Communication Services を使用すると、自分のアプリケーションにリアルタイム コミュニケーションを追加することができます。 このチュートリアルでは、Azure Communication Services をサポートする Web アプリケーションの設定方法について説明します。 リアルタイム コミュニケーションの実装を始めたいと考えている新規開発者向けの入門チュートリアルとなっています。

リアルタイム コミュニケーション ソリューションの構築を始めるにあたって、Azure Communication Services クライアント ライブラリを使用できます。このチュートリアルの最後には、このクライアント ライブラリを使用して構成された基本的な Web アプリケーションが完成します。

お気軽に [Azure Communication Services の GitHub](https://github.com/Azure/communication) ページにアクセスしてフィードバックをお寄せください。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * 開発環境を構成する
> * ローカル Web サーバーを設定する
> * Azure Communication Services パッケージを Web サイトに追加する
> * Azure の静的 Web サイトに Web サイトを発行する

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。 無料アカウントでは 200 ドル分の Azure クレジットが提供されますので、さまざまな組み合わせのサービスをお試しいただけます。
- [Visual Studio Code](https://code.visualstudio.com/):ローカル開発環境でコードを編集する際に使用します。
- [webpack](https://webpack.js.org/): コードをバンドルしてローカルでホストする際に使用します。
- [Node.js](https://nodejs.org/en/):Azure Communication Services クライアント ライブラリや webpack などの依存関係をインストールして管理する際に使用します。
- [nvm および npm](/windows/nodejs/setup-on-windows): バージョン コントロールの運用に使用します。
- Visual Studio Code 用の [Azure Storage 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)。 アプリケーションを Azure Storage に発行する際に、この拡張機能が必要となります。 [詳細については、Azure Storage での静的 Web サイトのホスティングに関する記事を参照してください](../../storage/blobs/storage-blob-static-website.md)。
- [Azure App Service 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。 この拡張機能を使用すると、(従来と同様の) Web サイトをデプロイすることに加え、フル マネージドの CI/CD (継続的インテグレーションと継続的デリバリー) を構成することもできます。
- 独自のサーバーレス アプリケーションを構築するための [Azure Function 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。 たとえば、Azure Functions で認証アプリケーションをホストすることができます。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../quickstarts/create-communication-resource.md)。
- ユーザー アクセス トークン。 手順については、[アクセス トークンに関するクイックスタート](../quickstarts/access-tokens.md?pivots=programming-language-javascript)または[信頼されたサービスに関するチュートリアル](./trusted-service-tutorial.md)を参照してください。


## <a name="configure-your-development-environment"></a>開発環境を構成する

ローカルの開発環境は、次のように構成されます。

:::image type="content" source="./media/step-one-pic-one.png" alt-text="開発環境のアーキテクチャ":::


### <a name="install-nodejs-nvm-and-npm"></a>Node.js、nvm、npm をインストールする

クライアント側アプリケーションに必要な各種依存関係は、Node.js を使用してダウンロード、インストールします。 それを使用して静的ファイルを生成し、それらのファイルを Azure でホストすることになるため、自分のサーバー上での構成を気にする必要はありません。

Windows 開発者の方は、[こちらの NodeJS チュートリアル](/windows/nodejs/setup-on-windows)に従って、Node、nvm、npm を構成してください。 

このチュートリアルは、LTS 12.20.0 バージョンを使用してテストしました。 nvm のインストール後、次の PowerShell コマンドを使用して目的のバージョンをデプロイします。

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="nvm を使用して Node.js をデプロイする":::

### <a name="configure-visual-studio-code"></a>Visual Studio Code を構成する

いずれかの[サポート対象プラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)に [Visual Studio Code](https://code.visualstudio.com/) をダウンロードしてください。

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Azure Communication Services プロジェクト用のワークスペースを作成する

プロジェクト ファイルの格納先となる新しいフォルダーを作成します (例: `C:\Users\Documents\ACS\CallingApp`)。 Visual Studio Code の [ファイル]、[ワークスペースにフォルダーを追加] を順にクリックし、ワークスペースにフォルダーを追加します。

:::image type="content" source="./media/step-one-pic-three.png" alt-text="新しいワークプレースを作成する":::

Visual Studio Code の左ペインにある [エクスプローラー] に移動すると、無題のワークスペースに "CallingApp" フォルダーが表示されます。

:::image type="content" source="./media/step-one-pic-four.png" alt-text="エクスプローラー":::

ワークスペースの名前は自由に更新してください。 Node.js のバージョンは、"CallingApp" フォルダーを右クリックし、[Open in Integrated Terminal]\(統合ターミナルで開く\) を選択すると確認できます。

:::image type="content" source="./media/step-one-pic-five.png" alt-text="ターミナルを開く":::

ターミナルで次のコマンドを入力して、前の手順でインストールした Node.js のバージョンを確認します。

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Node.js のバージョンを確認する":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Visual Studio Code 用の Azure 拡張機能をインストールする

Visual Studio Marketplace から、または Visual Studio Code ([表示] > [拡張機能] > [Azure Storage]) で [Azure Storage 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)をインストールします。

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Azure Storage 拡張機能 1 をインストールする":::

[Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) や [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) の拡張機能と同じ手順に従います。


## <a name="set-up-a-local-webserver"></a>ローカル Web サーバーを設定する

### <a name="create-a-new-npm-package"></a>新しい npm パッケージを作成する

ターミナルで、ワークスペース フォルダーのパスから次のコマンドを入力します。

``` console
npm init -y
```

このコマンドによって新しい npm パッケージが初期化され、プロジェクトのルート フォルダーに `package.json` が追加されます。

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="パッケージ JSON":::

npm init コマンドについては、[こちら](https://docs.npmjs.com/cli/v6/commands/npm-init)で詳しいドキュメントをご覧いただけます。

### <a name="install-webpack"></a>webpack をインストールする

[webpack](https://webpack.js.org/) を使用すると、Azure にデプロイできる静的ファイルとしてコードをバンドルすることができます。 また、これには開発サーバーも含まれています。後で、このサーバーに対し、通話サンプルを使用するための構成を行います。

ターミナルで、次のように入力して webpack をインストールします。

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

このチュートリアルのテストには、上記の指定バージョンが使用されています。 この依存関係は開発用であり、Azure にデプロイするコードに含める必要はないということを、`-dev` を指定することでパッケージ マネージャーに伝えています。

`package.json` ファイルに、"devDependencies" として 2 つの新しいパッケージが追加されていることがわかります。 これらのパッケージは `./CallingApp/node_modules/` ディレクトリにインストールされます。

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="webpack の構成":::

### <a name="configure-the-development-server"></a>開発サーバーを構成する

ブラウザーから静的アプリケーション (`index.html` ファイルなど) を実行する際は `file://` プロトコルが使用されます。 ローカルの開発サーバーとして webpack を使用して、npm モジュールを適切に動作させるためには HTTP プロトコルが必要となります。

ここでは、開発環境用と運用環境用に 2 つの構成を作成します。 運用環境用に準備されるファイルは縮小されます。つまり、使用されない空白や文字は削除されます。 これは待ち時間を最小限に抑えたり、コードを難読化したりする必要がある運用環境のシナリオに適した処理です。

[webpack のさまざまな構成ファイル](https://webpack.js.org/guides/production/)の操作には、`webpack-merge` ツールを使用します。

それでは、開発環境の作業を始めましょう。 まず、`webpack merge` をインストールする必要があります。 ターミナルで次のコマンドを実行します。

```Console
npm install --save-dev webpack-merge
```

`package.json` ファイルを見ると、"devDependencies" にもう 1 つの依存関係が追加されていることがわかります。

次の手順では、`webpack.common.js` という新しいファイルを作成し、次のコードを追加する必要があります。

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }     
}
```

さらに、各構成に対応する 2 つのファイルを追加します。

* webpack.dev.js
* webpack.prod.js

次の手順では、`webpack.dev.js` ファイルに変更を加える必要があります。 このファイルに次のコードを追加します。

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
この構成では、`webpack.common.js` から共通パラメーターをインポートし、2 つのファイルをマージします。さらに、モードを "development" に設定し、SourceMap を "inline-source-map" として構成します。

開発モードを指定した場合、webpack によってファイルは縮小されず、運用環境向けの最適化されたファイルは生成されません。 webpack のモードについては、[こちら](https://webpack.js.org/configuration/mode/)で詳しいドキュメントをご覧いただけます。

ソース マップのオプションは、[こちら](https://webpack.js.org/configuration/devtool/#root)に掲載されています。 ソース マップを設定することで、ブラウザーからのデバッグがしやすくなります。

:::image type="content" source="./media/step-one-pic-11.png" alt-text="webpack を構成する":::

開発サーバーを実行するには、`package.json` に移動し、scripts に次のコードを追加します。

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

ファイルは次のようになります。

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

npm から使用できるコマンドを追加しました。 

:::image type="content" source="./media/step-one-pic-12.png" alt-text="package.json に変更を加える":::

### <a name="testing-the-development-server"></a>開発サーバーをテストする

 Visual Studio Code で、次の 3 つのファイルをプロジェクトに作成します。

* `index.html`
* `app.js`
* `app.css` (省略可。アプリにスタイルを適用できます)

次のコードを `index.html` に貼り付けます。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="HTML ファイル":::

`app.js` に次のコードを追加します。

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
`app.css` に次のコードを追加します。

```CSS
html {
    font-family: sans-serif;
  }
```
忘れずに保存してください。 エクスプローラーでは、保存されていないファイルの名前の横に白色のドットが表示されます。

 :::image type="content" source="./media/step-one-pic-14.png" alt-text="JS コードを含む App.js ファイル":::

このページを開くと、ブラウザのコンソール内に、メッセージがアラートと共に表示されるはずです。

:::image type="content" source="./media/step-one-pic-15.png" alt-text="App.css ファイル":::

次のターミナル コマンドを使用して開発の構成をテストします。

```Console
npm run build:dev
```

サーバーの実行場所がコンソールに表示されます。 既定では、これは `http://localhost:8080` です。 build:dev コマンドは、先ほど `package.json` に追加したコマンドです。

 :::image type="content" source="./media/step-one-pic-16.png" alt-text="開発サーバーを起動する":::
 
 ブラウザーで目的のアドレスに移動すると、前の手順で構成したページとアラートが表示されるはずです。
 
  :::image type="content" source="./media/step-one-pic-17.png" alt-text="HTML ページ":::
  
 
サーバーの実行中にコードに変更を加えることができます。その場合、サーバーと HTML ページが自動的にリロードされます。 

次に、Visual Studio Code で `app.js` ファイルに移動し、`alert('Hello world alert!');` を削除します。 ファイルを保存し、ブラウザーからアラートが消えたことを確認します。

サーバーを停止するには、ターミナルで `Ctrl+C` を実行します。 サーバーを起動したければ、いつでも「`npm run build:dev`」を入力してください。

## <a name="add-the-azure-communication-services-packages"></a>Azure Communication Services パッケージを追加する

`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services 通話クライアント ライブラリをインストールします。

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

このアクションによって、Azure Communication Services の common パッケージと calling パッケージがパッケージの依存関係として追加されます。 `package.json` ファイルに、2 つの新しいパッケージが追加されていることがわかります。 `npm install` コマンドの詳細については、[こちら](https://docs.npmjs.com/cli/v6/commands/npm-install)を参照してください。

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Azure Communication Services のパッケージをインストールする":::

これらのパッケージは Azure Communication Services チームにより提供され、認証ライブラリと通話ライブラリを含んでいます。 "--save" コマンドによって、これらのパッケージが運用アプリケーションの依存関係であることが伝えられて `package.json` ファイルの `dependencies` に追加されます。 運用環境向けにアプリケーションをビルドすると、これらのパッケージが運用環境のコードに追加されます。


## <a name="publish-your-website-to-azure-static-websites"></a>Azure の静的 Web サイトに Web サイトを発行する

### <a name="create-a-configuration-for-production-deployment"></a>運用環境デプロイ用の構成を作成する

`webpack.prod.js` に次のコードを追加します。

```JavaScript
const { merge } = require('webpack-merge');
 const common = require('./webpack.common.js');

 module.exports = merge(common, {
   mode: 'production',
 });
 ```

この構成は webpack.common.js (入力ファイルと結果の保存先を指定したファイル) にマージされることに注意してください。また、この構成によってモードが "production" に設定されます。
 
`package.json` に次のコードを追加します。

```JavaScript
"build:prod": "webpack --config webpack.prod.js" 
```

ファイルは次のようになります。

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js" 
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.3",
    "@azure/communication-common": "^1.0.0-beta.3"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

 :::image type="content" source="./media/step-one-pic-20.png" alt-text="構成済みのファイル":::


ターミナルから、次のコマンドを実行します。

```Console
npm run build:prod
```

このコマンドによって `dist` フォルダーが作成され、運用環境用の `app.js` 静的ファイルがそこに作成されます。 

 :::image type="content" source="./media/step-one-pic-21.png" alt-text="運用環境向けのビルド":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Azure Storage にアプリをデプロイする
 
`dist` フォルダーに `index.html` と `app.css` をコピーします。

`dist` フォルダーに、新しいファイルを作成し、`404.html` という名前を付けます。 そのファイルに次のマークアップをコピーします。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

ファイルを保存します (Ctrl キーを押しながら S キーを押します)。

右クリックして [Deploy to Static Website via Azure Storage]\(Azure Storage を介して静的 Web サイトにデプロイ\) を選択します。

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Azure へのデプロイを開始する":::
 
`Select subscription` フィールドで、[Azure にサインイン] (過去にサブスクリプションを作成したことがない場合は [無料の Azure アカウントの作成]) を選択します。
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Azure へのサインイン":::
 
`Create new Storage Account`[ > ]`Advanced` を選択します。

 :::image type="content" source="./media/step-one-pic-24.png" alt-text="ストレージ アカウント グループを作成する":::
 
 ストレージ グループの名前を入力します。
 
 :::image type="content" source="./media/step-one-pic-25.png" alt-text="アカウントの名前を追加する":::
 
必要に応じて新しいリソース グループを作成します。
 
  :::image type="content" source="./media/step-one-pic-26.png" alt-text="新しいグループを作成する":::
  
  "Would you like to enable static website hosting?" (静的 Web サイトのホスティングを有効にしますか?) で [はい] を選択します。
  
  :::image type="content" source="./media/step-one-pic-27.png" alt-text="静的 Web サイトのホスティングを有効にするためのオプションを選択する":::
  
[Enter the index document name]\(インデックス ドキュメントの名前を入力してください\) には、既定のファイル名 (先ほど作成した `index.html` ファイル) をそのまま使用します。

[Enter the 404 error document path]\(404 エラー ドキュメントのパスを入力してください\) には「`404.html`」と入力します。  
  
アプリケーションの場所を選択します。 選択した場所によって、今後通話アプリケーションのグループ通話で使用されるメディア プロセッサが定義されます。 

メディア プロセッサは、アプリケーションの場所に基づいて Azure Communication Services が選択します。

:::image type="content" source="./media/step-one-pic-28.png" alt-text="場所を選択する":::
  
リソースと Web サイトが作成されるまで待ちます。 
 
[Browse to website]\(Web サイトを参照\) をクリックします。

:::image type="content" source="./media/step-one-pic-29.png" alt-text="デプロイメントが完了":::
 
ブラウザーの開発ツールからソースをチェックし、運用に必要なファイルが準備されていることを確認します。
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Web サイト":::

[Azure portal](https://portal.azure.com/#home) に移動し、リソース グループを選択します。作成したアプリケーションを選択し、`Settings` > `Static website` に移動します。 静的 Web サイトが有効になっていることが確認できます。プライマリ エンドポイント、インデックス ドキュメント、エラー ドキュメント ファイルのパスに注目してください。

:::image type="content" source="./media/step-one-pic-31.png" alt-text="静的 Web サイトの選択":::

[Blob service] の [コンテナー] を選択すると、2 つのコンテナーが作成されていることがわかります。1 つはログ用 ($logs) で、もう 1 つは Web サイトのコンテンツ用 ($web) です。

:::image type="content" source="./media/step-one-pic-32.png" alt-text="コンテナーの構成":::

`$web` に移動すると、Visual Studio で作成して Azure にデプロイしたファイルが表示されます。 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="デプロイ":::

アプリケーションはいつでも Visual Studio Code から再デプロイすることができます。

初めての Azure Communication Services Web アプリケーションをビルドする準備はこれで完了です。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [音声通話をアプリに追加する](../quickstarts/voice-video-calling/getting-started-with-calling.md)

次のことも実行できます。

- [チャットをアプリに追加する](../quickstarts/chat/get-started.md)
- [ユーザー アクセス トークンを作成する](../quickstarts/access-tokens.md)
- [クライアントとサーバーのアーキテクチャについて学習する](../concepts/client-and-server-architecture.md)
- [認証について学習する](../concepts/authentication.md)