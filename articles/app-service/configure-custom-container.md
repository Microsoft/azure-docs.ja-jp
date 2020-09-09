---
title: カスタム Linux コンテナーの構成
description: Azure App Service のカスタム Linux コンテナーを構成する方法を学びます この記事では、最も一般的な構成タスクを紹介しています。
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 2f26f1b041b2d369b68aeb11755c8e8053862b16
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88082810"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Azure App Service のカスタム Linux コンテナーを構成する

この記事では、Azure App Service を実行するようにカスタム Linux コンテナーを構成する方法を示します。

このガイドでは、App Service の Linux アプリのコンテナー化の主要概念および手順について説明します。 Azure App Service を使用したことがない場合は、最初に[カスタム コンテナー クイック スタート](quickstart-custom-container.md?pivots=container-linux)と[チュートリアル](tutorial-custom-container.md?pivots=container-linux)に従ってください。 [複数コンテナー アプリのクイック スタート](quickstart-multi-container.md)と[チュートリアル](tutorial-multi-container-app.md)もあります。

## <a name="configure-port-number"></a>ポート番号を構成する

既定では、App Service はカスタム コンテナーがポート 80 でリッスンしていることを前提としています。 カスタム イメージの Web サーバーは、80 以外のポートを使用できます。 カスタム コンテナーによって利用されるポートを Azure に指示するには、`WEBSITES_PORT` アプリ設定を使用します。 [このチュートリアルに含まれる Python サンプル](https://github.com/Azure-Samples/docker-django-webapp-linux)の GitHub ページは、`WEBSITES_PORT` を _8000_ に設定する必要があることを示しています。 Cloud Shell で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを実行することにより設定できます。 次に例を示します。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>環境変数を構成する

カスタム コンテナーには、外部で指定する必要がある環境変数を使用できます。 Cloud Shell で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを実行することによりこれらを渡すことができます。 次に例を示します。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

この方法は単一のコンテナー アプリまたは複数のコンテナー アプリの両方で利用できます。ただし、環境変数は *docker compose.yml* ファイルで指定されます。

## <a name="use-persistent-shared-storage"></a>永続的な共有ストレージを使用する

アプリのファイル システムの */home* ディレクトリを使用して、再起動間でファイルを永続化し、インスタンス間でそれらを共有することができます。 アプリの `/home` は、コンテナー アプリが永続的ストレージにアクセスできるようにするために指定されます。

永続的ストレージが無効になると、`/home` ディレクトリへの書き込みは、アプリの再起動後、または複数のインスタンス間で保持されません。 唯一の例外は `/home/LogFiles` ディレクトリであり、これは Docker およびコンテナーのログを格納するために使用されます。 永続的ストレージが有効になると、`/home` ディレクトリへのすべての書き込みは存続し、スケールアウトされたアプリのすべてのインスタンスからアクセスできます。

既定では、永続的ストレージが*有効*になり、設定はアプリケーション設定では公開されません。 これを無効にするには、Cloud Shell で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを実行することにより `WEBSITES_ENABLE_APP_SERVICE_STORAGE` アプリ設定を指定します。 次に例を示します。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> [独自の永続的ストレージを構成](configure-connect-to-azure-storage.md?pivots=platform-linux)することもできます。

## <a name="enable-ssh"></a>SSH を有効にする

SSH では、コンテナーとクライアント間の通信をセキュリティで保護できます。 カスタム コンテナーで SSH をサポートするために、Dockerfile 自体に追加する必要があります。

> [!TIP]
> すべての組み込み Linux コンテナーは、そのイメージ リポジトリ内に SSH 命令を追加しました。 [Node.js 10.14 リポジトリ](https://github.com/Azure-App-Service/node/blob/master/10.14)で次のとおり実行し、それをそこで有効にする方法を表示できます。

- [RUN](https://docs.docker.com/engine/reference/builder/#run) 命令を使用して、SSH サーバーをインストールし、ルート アカウントのパスワードを `"Docker!"` に設定します。 たとえば、[Alpine Linux](https://hub.docker.com/_/alpine) に基づくイメージの場合、次のコマンドが必要です。

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    この構成は、コンテナーへの外部接続を許可しません。 SSH は `https://<app-name>.scm.azurewebsites.net` を通じてのみ利用でき、公開用の資格情報で認証されます。

- [この sshd_config ファイル](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config)をイメージ リポジトリに追加し、[COPY](https://docs.docker.com/engine/reference/builder/#copy) 命令を使用してファイルを */etc/ssh/* ディレクトリにコピーします。 *sshd_config* ファイルの詳細については、[OpenBSD ドキュメント](https://man.openbsd.org/sshd_config)を参照してください。

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > *sshd_config* ファイルには次の項目を指定する必要があります。
    > - `Ciphers` には、`aes128-cbc,3des-cbc,aes256-cbc` の項目を少なくとも 1 つ含める必要があります。
    > - `MACs` には、`hmac-sha1,hmac-sha1-96` の項目を少なくとも 1 つ含める必要があります。

- [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) 命令を使用して、コンテナーでポート 2222 を開きます。 ルート パスワードはわかっていますが、ポート 2222 にはインターネットからアクセスできません。 それには、プライベート仮想ネットワークのブリッジ ネットワーク内でコンテナーのみがアクセスできます。

    ```Dockerfile
    EXPOSE 80 2222
    ```

- コンテナーのスタートアップ スクリプトで、SSH サーバーを起動します。

    ```bash
    /usr/sbin/sshd
    ```

    例については、既定の [Node.js 10.14 コンテナー](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh)が SSH サーバーを起動する方法を参照してください。

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="change-the-docker-image-of-a-custom-container"></a>カスタム コンテナーの Docker イメージを変更する

既存のカスタム コンテナー アプリの現在の Docker イメージを新しいイメージに変更するには、次のコマンドを使用します。

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>プライベート レジストリからイメージを使用する

Azure Container Registry などのプライベート レジストリから イメージを使用するには、次のコマンドを実行します。

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

*\<username>* および *\<password>* には、プライベート レジストリ アカウントのログイン資格情報を指定します。

## <a name="configure-multi-container-apps"></a>複数コンテナー アプリを構成する

- [Docker Compose で永続的ストレージを使用する](#use-persistent-storage-in-docker-compose)
- [プレビューの制限事項](#preview-limitations)
- [Docker Compose のオプション](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Docker Compose で永続的なストレージを使用する

WordPress のような複数コンテナー アプリでは、永続的ストレージが適切に機能する必要があります。 有効にするには、Docker Compose 構成が、コンテナー*外*の保存場所を指す必要があります。 コンテナー内部の保存場所では、アプリの再起動後に変更内容が保持されません。

`WEBSITES_ENABLE_APP_SERVICE_STORAGE` アプリ設定を指定して、Cloud Shell で [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用し、永続的ストレージを有効にします。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

*docker-compose.yml* ファイルで、`volumes` オプションを `${WEBAPP_STORAGE_HOME}` にマップします。 

`WEBAPP_STORAGE_HOME` は、アプリの永続的なストレージにマップされる App Service の環境変数です。 次に例を示します。

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>プレビューの制限事項

複数コンテナーは現在プレビュー段階です。 次の App Service プラットフォーム機能はサポートされません。

- 認証/認可
- マネージド ID

### <a name="docker-compose-options"></a>Docker Compose のオプション

以下の一覧は、Docker Compose 構成オプションでサポートされているものとサポートされていないものを示しています。

#### <a name="supported-options"></a>サポートされているオプション

- command
- entrypoint
- 環境
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>サポートされていないオプション

- build (禁止)
- depends_on (無視)
- networks (無視)
- secrets (無視)
- ports (80 および 8080 以外) (無視)

> [!NOTE]
> パブリック プレビューでは、ここで明示的に示されていないその他のオプションが無視されます。

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:プライベート コンテナー リポジトリからデプロイする](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [チュートリアル:マルチコンテナーの WordPress アプリ](tutorial-multi-container-app.md)
