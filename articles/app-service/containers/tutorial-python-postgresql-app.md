---
title: Linux 上の PostgreSQL を使用して Python Web アプリを構築する - Azure App Service | Microsoft Docs
description: PostgreSQL データベースに接続するデータ駆動型の Python アプリを Azure 上で実行する方法について説明します。
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: beverst;cephalin
ms.custom: seodec18
ms.openlocfilehash: 8846ec386ad1776172ae1949b5e0f26e03ddf1df
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337991"
---
# <a name="build-a-python-and-postgresql-web-app-in-azure-app-service"></a>Azure App Service で Python と PostgreSQL Web アプリを構築する

[App Service on Linux](app-service-linux-intro.md) では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このチュートリアルでは、PostgreSQL をデータベース バックエンドとして使用する、データ駆動型の Python Web アプリを作成する方法について説明します。 完了すると、App Service on Linux 上で実行される Django アプリケーションが完成します。

![App Service on Linux での Python Django アプリ](./media/tutorial-python-postgresql-app/django-admin-azure.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure で PostgreSQL データベースを作成する
> * Python アプリを PostgreSQL に接続する
> * Azure にアプリケーションをデプロイする
> * 診断ログを表示する
> * Azure Portal でアプリを管理する

この記事の手順は macOS で実行できます。 Linux および Windows でも手順はほとんど同じですが、このチュートリアルでは相違点について詳しく説明していません。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

1. [Git をインストールする](https://git-scm.com/)
2. [Python をインストールする](https://www.python.org/downloads/)
3. [PostgreSQL をインストールして実行する](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>PostgreSQL のローカル インストールをテストし、データベースを作成する

ローカルのターミナル ウィンドウで、`psql` を実行してローカルの PostgreSQL サーバーに接続します。

```bash
sudo -u postgres psql postgres
```

`unknown user: postgres` のようなメッセージが表示された場合、PostgreSQL インストールがログイン ユーザー名で構成されている可能性があります。 代わりに次のコマンドを試してください。

```bash
psql postgres
```

接続に成功すれば、PostgreSQL データベースは実行されています。 接続されない場合は、「[Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/)」 (ダウンロード - PostgreSQL コア ディストリビューション) にあるお使いの OS の指示に従って、ローカル PostgreSQL データベースが開始されていることを確認します。

*pollsdb* という名前のデータベースを作成し、名前が *manager* でパスワードが *supersecretpass* である別のデータベース ユーザーを設定します。

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

「`\q`」を入力して PostgreSQL クライアントを終了します。

<a name="step2"></a>

## <a name="create-local-python-app"></a>ローカルの Python アプリを作成する

この手順では、ローカルの Python Django プロジェクトを設定します。

### <a name="clone-the-sample-app"></a>サンプル アプリの複製

ターミナル ウィンドウを開き、`CD` コマンドで作業ディレクトリに移動します。

次のコマンドを実行して、サンプル リポジトリを複製します。

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

このサンプル リポジトリに、[Django](https://www.djangoproject.com/) アプリケーションが含まれています。 これは、[Django ドキュメントの使用開始チュートリアル](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)に従って取得できるデータ駆動型アプリと同じです。 このチュートリアルでは、Django について詳しく説明しません。App Service に Django アプリ (または別のデータ駆動型の Python アプリ) をデプロイして実行する方法だけを示します。

### <a name="configure-environment"></a>環境の構成

Python 仮想環境を作成し、スクリプトを使用してデータベース接続の設定を行います。

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

*env.sh* と *env.ps1* に定義されている環境変数が、_azuresite/settings.py_ で使用され、データベース設定が定義されます。

### <a name="run-app-locally"></a>アプリをローカルで実行する

必要なパッケージをインストールし、[Django の移行を実行](https://docs.djangoproject.com/en/2.1/topics/migrations/)し、[管理者ユーザーを作成](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user)します。

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

管理者ユーザーが作成されたら、Django サーバーを実行します。

```bash
python manage.py runserver
```

アプリが完全に読み込まれると、次のようなメッセージが表示されます。

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

ブラウザーで `http://localhost:8000` にアクセスします。 `No polls are available.` というメッセージが表示されます。 

`http://localhost:8000/admin` に移動し、前の手順で作成した管理者ユーザーを使用してサインインします。 **[質問]** の横の **[追加]** をクリックし、いくつかの選択肢があるポーリングの質問を作成します。

![ローカルで実行される Python Django アプリケーション](./media/tutorial-python-postgresql-app/django-admin-local.png)

`http://localhost:8000` にもう一度移動し、ポーリングの質問が表示されていることを確認します。

Django サンプル アプリケーションでは、ユーザー データがデータベースに格納されます。 ポーリングの質問の追加に成功していれば、アプリによってローカル PostgreSQL データベースにデータが書き込まれます。

Django サーバーを任意のタイミングで停止するには、ターミナルで Ctrl + C キーを押します。

## <a name="create-a-production-postgresql-database"></a>運用 PostgreSQL データベースを作成する

この手順では、Azure に PostgreSQL データベースを作成します。 アプリを Azure にデプロイすると、このクラウド データベースがアプリで使用されます。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成

Cloud Shell 内で [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) コマンドを使用して、PostgreSQL サーバーを作成します。

次のコマンドの例では、*\<postgresql_name>* を一意のサーバー名、*\<admin_username>* と *\<admin_password>* を目的のユーザー資格情報に置き換えます。 このユーザー資格情報は、データベース管理者のアカウントのものです。 このサーバー名は、PostgreSQL エンドポイント (`https://<postgresql_name>.postgres.database.azure.com`) の一部として使用されるため、Azure のすべてのサーバーで一意である必要があります。

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
```

Azure Database for PostgreSQL サーバーが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{
  "administratorLogin": "<admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> 後のために \<admin_username> と \<admin_password> を覚えておきます。 Postgre サーバーとそのデータベースにサインインするために必要です。


### <a name="create-firewall-rules-for-the-postgresql-server"></a>PostgreSQL サーバー向けのファイアウォール規則を作成する

Cloud Shell 内で次の Azure CLI コマンドを実行し、Azure リソースからデータベースにアクセスできるようにします。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> この設定により、Azure ネットワーク内のすべての IP からネットワーク接続できます。 運用環境で使用する場合、[アプリで使用されている送信 IP アドレスのみを使用する](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)ことで、最も制限の厳しいファイアウォール規則を構成してみてください。

Cloud Shell 内で *\<you_ip_address>* を [ローカル IPv4 IP アドレス](https://www.whatsmyip.org/)に置き換えてコマンドを再び実行し、ローカル コンピューターからアクセスできるようにします。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Python アプリを運用データベースに接続する

この手順では、作成した Azure Database for PostgreSQL サーバーに、Django サンプル アプリを接続します。

### <a name="create-empty-database-and-user-access"></a>空のデータベースおよびユーザー アクセスを作成する

Cloud Shell で次のコマンドを実行してデータベースに接続します。 管理者パスワードの入力を求められたら、「[PostgreSQL サーバー向けの Azure データベースの作成](#create-an-azure-database-for-postgresql-server)」で指定したのと同じパスワードを使用します。

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

ローカル Postgres サーバーと同様に、Azure Postgres サーバーでデータベースとユーザーを作成します。

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

「`\q`」を入力して PostgreSQL クライアントを終了します。

> [!NOTE]
> ベスト プラクティスとしては、管理者ユーザーを使用する代わりに、特定のアプリケーションに対して作成するデータベース ユーザーのアクセス許可を制限します。 この例では、`manager` ユーザーには `pollsdb` データベースに _のみ_ 完全な権限が与えられています。

### <a name="test-app-connectivity-to-production-database"></a>アプリから運用データベースへの接続をテストする

ローカル ターミナル ウィンドウで、データベース環境変数 (*env.sh* または *env.ps1* を実行して先ほど構成したもの) を変更します。

```bash
# Bash
export DBHOST="<postgresql_name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql_name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql_name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql_name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Azure データベースへの Django の移行を実行し、管理者ユーザーを作成します。

```bash
python manage.py migrate
python manage.py createsuperuser
```

管理者ユーザーが作成されたら、Django サーバーを実行します。

```bash
python manage.py runserver
```

もう一度 `http://localhost:8000` に移動します。 `No polls are available.` というメッセージが再び表示されます。 

`http://localhost:8000/admin` に移動し、作成した管理者ユーザーを使用してサインインし、前と同じようにポーリングの質問を作成します。

![ローカルで実行される Python Django アプリケーション](./media/tutorial-python-postgresql-app/django-admin-local.png)

`http://localhost:8000` にもう一度移動し、ポーリングの質問が表示されていることを確認します。 これで、ご自分のアプリによって、Azure 上のデータベースにデータが書き込まれます。

## <a name="deploy-to-azure"></a>[Deploy to Azure (Azure へのデプロイ)]

この手順では、Postgre に接続される Python アプリケーションを Azure App Service にデプロイします。

### <a name="configure-repository"></a>リポジトリの構成

Django では、受信要求の `HTTP_HOST` ヘッダーが検証されます。 App Service 上で Django アプリを動作させるには、許可されるホストにアプリの完全修飾ドメイン名を追加する必要があります。 _azuresite/settings.py_ を開き、`ALLOWED_HOSTS` 設定を見つけます。 その行を次のように変更します。

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

次に、Django では[運用環境での静的ファイルの使用](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/)はサポートされないため、これを手動で有効にする必要があります。 このチュートリアルでは、[WhiteNoise](https://whitenoise.evans.io/en/stable/) を使用します。 WhiteNoise パッケージは _requirements.txt_ に既に含まれています。 必要なのは、それを使用するように Django を構成することだけです。 

_azuresite/settings.py_ で、`MIDDLEWARE` 設定を見つけ、一覧の `django.middleware.security.SecurityMiddleware` ミドルウェアのすぐ下に `whitenoise.middleware.WhiteNoiseMiddleware` ミドルウェアを追加します。 `MIDDLEWARE` 設定は次のようになります。

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

_azuresite/settings.py_ の末尾に、次の行を追加します。

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

WhiteNoise の構成の詳細については、[WhiteNoise のドキュメント](https://whitenoise.evans.io/en/stable/)を参照してください。

> [!IMPORTANT]
> データベースの設定セクションは、環境変数の使用に関するセキュリティのベスト プラクティスに既に従っています。 完全なデプロイの推奨事項については、[Django ドキュメントのデプロイ チェックリスト](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)を参照してください。


リポジトリに変更をコミットします。

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service プランを作成する 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Web アプリを作成する 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>環境変数を構成する

チュートリアルの前半で、PostgreSQL データベースに接続する環境変数を定義しました。

App Service では、Cloud Shell で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) コマンドを使用し、環境変数を_アプリ設定_として設定します。

次の例では、データベース接続の詳細をアプリ設定として指定します。 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git 
   06b6df4..6520eea  master -> master
```  

App Service デプロイ サーバーによってリポジトリ ルート内の _requirements.txt_ が確認され、`git push` 後に Python パッケージ管理が自動的に実行されます。

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリの参照

デプロイした Web アプリを参照します。 これには少し時間がかかります。これは、アプリが最初に要求されたときは、コンテナーをダウンロードして実行する必要があるためです。 ページがタイムアウトしたか、エラー メッセージが表示された場合、数分待ってからページを更新してください。

```bash
http://<app_name>.azurewebsites.net
```

先ほど作成したポーリングの質問が表示されます。 

各サブディレクトリ内で _wsgi.py_ (`manage.py startproject` によって既定で作成されます) を探すことで、App Service によってリポジトリ内の Django プロジェクトが検出されます。 ファイルが見つかると、Django アプリが読み込まれます。 App Service による Python アプリのロード方法の詳細については[組み込みの Python イメージの構成](how-to-configure-python.md)に関する記事を参照してください。

`<app_name>.azurewebsites.net` に移動し、作成済みの管理者ユーザーを使用してサインインします。 必要に応じて、いくつかのポーリングの質問を追加で作成します。

![ローカルで実行される Python Django アプリケーション](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**お疲れさまでした。** App Service for Linux で Python アプリが実行されています。

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

App Service on Linux では、アプリは、既定の Docker イメージのコンテナー内で実行されます。 コンテナー内から生成されたコンソール ログにアクセスできます。 ログを取得するには、まず Cloud Shell で次のコマンドを実行して、コンテナーのログ記録をオンにします。

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --docker-container-logging filesystem
```

コンテナーのログ記録がオンになったら、次のコマンドを実行して、ログのストリームを確認します。

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

## <a name="manage-your-web-app-in-the-azure-portal"></a>Azure Portal で Web アプリを管理する

[Azure Portal](https://portal.azure.com) に移動し、作成した Web アプリを表示します。

左側のメニューで **[App Services (App Services)]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/tutorial-python-postgresql-app/app-resource.png)

既定では、ポータルは Web アプリの **[概要]** ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の [App Service] ページ](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure で PostgreSQL データベースを作成する
> * Python アプリを PostgreSQL に接続する
> * Azure にアプリケーションをデプロイする
> * 診断ログを表示する
> * Azure Portal でアプリを管理する

次のチュートリアルに進み、カスタム DNS 名を Web アプリにマップする方法を学習してください。

> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure Web Apps にマップする](../app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [組み込みの Python イメージの構成とエラーのトラブルシューティングを行う](how-to-configure-python.md)

