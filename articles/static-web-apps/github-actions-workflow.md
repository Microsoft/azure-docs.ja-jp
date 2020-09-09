---
title: Azure Static Web Apps の GitHub Actions ワークフロー
description: GitHub リポジトリを使用して Azure Static Web Apps への継続的デプロイを設定する方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 92d445991aa8b90a343ad7d015787cff35ddf183
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85340926"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>Azure Static Web Apps プレビューの GitHub Actions ワークフロー

新しい Azure Static Web App リソースを作成すると、アプリの継続的デプロイを制御する GitHub Actions ワークフローが生成されます。 このワークフローは、YAML ファイルによって起動されます。 この記事では、ワークフロー ファイルの構造とオプションについて詳しく説明します。

デプロイは[トリガー](#triggers)によって開始されます。このトリガーにより、個々の[手順](#steps)で定義された[ジョブ](#jobs)が実行されます。

## <a name="file-location"></a>ファイルの場所

GitHub リポジトリを Azure Static Web Apps にリンクすると、そのリポジトリにワークフロー ファイルが追加されます。

生成されたワークフロー ファイルを表示するには、次の手順に従います。

1. GitHub でアプリのリポジトリを開きます。
1. _[コード]_ タブで、`.github/workflows` フォルダーをクリックします。
1. `azure-static-web-apps-<RANDOM_NAME>.yml` のような名前のファイルをクリックします。

リポジトリ内の YAML ファイルは、次の例のようになります。

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
    - uses: actions/checkout@v2
      with:
        submodules: true
    - name: Build And Deploy
      id: builddeploy
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
        action: 'upload'
        ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
        app_location: '/' # App source code path
        api_location: 'api' # Api source code path - optional
        app_artifact_location: 'dist' # Built app content directory - optional
        ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
    - name: Close Pull Request
      id: closepullrequest
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        action: 'close'
```

## <a name="triggers"></a>トリガー

GitHub Actions の[トリガー](https://help.github.com/actions/reference/events-that-trigger-workflows)は、イベント トリガーに基づいてジョブを実行するように GitHub Actions ワークフローに通知します。 トリガーは、ワークフロー ファイルの `on` プロパティを使用して一覧表示されます。

```yml
on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master
```

`on` プロパティに関連付けられている設定を使用して、ジョブをトリガーするブランチを定義し、さまざまなプル要求の状態に対してトリガーを起動するように設定できます。

この例では、_master_ ブランチが変更されるとワークフローが開始されます。 ワークフローを開始する変更としては、選択したブランチに対するコミットのプッシュとプル要求のオープンがあります。

## <a name="jobs"></a>ジョブ

各イベント トリガーにはイベント ハンドラーが必要です。 [ジョブ](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs)では、イベントがトリガーされたときの動作が定義されます。

Static Web Apps ワークフロー ファイルには、2 つの使用可能なジョブがあります。

| 名前  | 説明 |
|---------|---------|
|`build_and_deploy_job` | `on` プロパティに一覧表示されているブランチに対してコミットをプッシュするかプル要求を開くと実行されます。 |
|`close_pull_request_job` | プル要求を閉じたときにのみ実行され、これにより、プル要求から作成されたステージング環境が削除されます。 |

## <a name="steps"></a>手順

手順は、ジョブのシーケンシャル タスクです。 手順では、依存関係のインストール、テストの実行、運用環境へのアプリケーションのデプロイなどのアクションが実行されます。

ワークフロー ファイルでは、次の手順が定義されます。

| ジョブ  | 手順  |
|---------|---------|
| `build_and_deploy_job` |<ol><li>Action の環境のリポジトリをチェックアウトします。<li>リポジトリをビルドして Azure Static Web Apps にデプロイします。</ol>|
| `close_pull_request_job` | <ol><li>プル要求が終了したことを Azure Static Web Apps に通知します。</ol>|

## <a name="build-and-deploy"></a>ビルドとデプロイ

`Build and Deploy` という名前の手順では、Azure Static Web Apps インスタンスへのビルドとデプロイが行われます。 `with` セクションで、デプロイ用に次の値をカスタマイズできます。

```yml
with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
    repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
    action: 'upload'
    ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
    app_location: '/' # App source code path
    api_location: 'api' # Api source code path - optional
    app_artifact_location: 'dist' # Built app content directory - optional
    ###### End of Repository/Build Configurations ######
```

| プロパティ | 説明 | 必須 |
|---|---|---|
| `app_location` | アプリケーション コードの場所です。<br><br>たとえば、アプリケーションのソース コードがリポジトリのルートにある場合は `/` を入力し、アプリケーション コードが `app` という名前のディレクトリにある場合は `/app` を入力します。 | はい |
| `api_location` | Azure Functions コードの場所です。<br><br>たとえば、アプリ コードが `api` という名前のフォルダーにある場合は、`/api` を入力します。 フォルダー内で Azure Functions アプリが検出されない場合、ビルドは失敗せず、API が必要とされていないことがワークフローで想定されます。 | いいえ |
| `app_artifact_location` | `app_location` を基準としたビルド出力ディレクトリの場所です。<br><br>たとえば、アプリケーションのソース コードが `/app` にあり、ビルド スクリプトによってファイルが `/app/build` フォルダーに出力される場合、`build` を値 `app_artifact_location` として設定します。 | いいえ |

`repo_token`、`action`、`azure_static_web_apps_api_token` の値は Azure Web Apps Static によって設定されます。手動で変更しないでください。

## <a name="custom-build-commands"></a>カスタム ビルド コマンド

デプロイ中に実行されるコマンドを詳細に制御できます。 次のコマンドは、ジョブの `with` セクションの下で定義できます。

デプロイでは常に、カスタム コマンドの前に `npm install` が呼び出されます。

| コマンド            | 説明 |
|---------------------|-------------|
| `app_build_command` | 静的コンテンツ アプリケーションのデプロイ時に実行するカスタム コマンドを定義します。<br><br>たとえば、Angular アプリケーションの運用ビルドを構成するには、`ng build --prod` と入力します。 空白のままにすると、ワークフローでは `npm run build` または `npm run build:Azure` コマンドの実行が試みられます。  |
| `api_build_command` | Azure Functions API アプリケーションのデプロイ時に実行するカスタム コマンドを定義します。 |

## <a name="route-file-location"></a>ルート ファイルの場所

ワークフローをカスタマイズして、リポジトリ内の任意のフォルダー内で [routes.json](routes.md) を検索できます。 次のプロパティは、ジョブの `with` セクションの下で定義できます。

| プロパティ            | 説明 |
|---------------------|-------------|
| `routes_location` | _routes.json_ ファイルが存在するディレクトリの場所を定義します。 この場所は、リポジトリのルートを基準としています。 |

 _routes.json_ ファイルの場所を明確にすることは、フロントエンド フレームワークのビルド手順で、既定でこのファイルが `app_artifact_location` に移動されない場合に特に重要です。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [運用前環境でプル要求を確認する](review-publish-pull-requests.md)
