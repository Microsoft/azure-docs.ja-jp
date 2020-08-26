---
title: チュートリアル:Postgres を使用した Python Django アプリをデプロイする
description: PostgreSQL データベースを使用する Python Web アプリを作成し、Azure にデプロイします。 このチュートリアルは Django フレームワークを使用しており、アプリは Azure App Service on Linux でホストされています。
ms.devlang: python
ms.topic: tutorial
ms.date: 07/22/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: d9d8694c8ac81352ab36f3d610f02f3751090b27
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120594"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>チュートリアル:PostgreSQL を使用した Django Web アプリを Azure App Service にデプロイする

このチュートリアルでは、データ ドリブンの Python [Django](https://www.djangoproject.com/) Web アプリを [Azure App Service](overview.md) にデプロイし、それを Azure Database for Postgres データベースに接続する方法について説明します。 App Service は、高いスケーラビリティを備えた、パッチを自己適用する Web ホスティング サービスです。

このチュートリアルでは、Azure CLI を使用して以下のタスクを実了します。

> [!div class="checklist"]
> * Python と Azure CLI を使用して初期環境を設定する
> * Azure Database for PostgreSQL データベースを作成する
> * Azure App Service にコードをデプロイして Postgres に接続する
> * コードを更新して再デプロイする
> * 診断ログを表示する
> * Azure portal で Web アプリを管理する

[このチュートリアルの Azure portal バージョン](/azure/developer/python/tutorial-python-postgresql-app-portal)も使用できます。


## <a name="set-up-your-initial-environment"></a>初期環境を設定する

1. アクティブなサブスクリプションが含まれる Azure アカウントを用意します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
1. <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 以降</a>をインストールします。
1. <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 以降をインストールします。それを任意のシェルから使用してコマンドを実行することで、Azure リソースのプロビジョニングと構成を行います。

ターミナル ウィンドウを開き、Python のバージョンが 3.6 以降であることを確認します。

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Azure CLI のバージョンが 2.0.80 以降であることを確認します。

```azurecli
az --version
```

次に CLI から Azure にサインインします。

```azurecli
az login
```

このコマンドを実行すると、お客様の資格情報を収集するためにブラウザーが開かれます。 コマンドが終了すると、ご利用のサブスクリプションに関する情報を含んだ JSON 出力が表示されます。

サインイン後は、Azure CLI を使用して Azure コマンドを実行して、サブスクリプション内のリソースを操作することができます。

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="clone-or-download-the-sample-app"></a>サンプル アプリをクローンまたはダウンロードする

# <a name="git-clone"></a>[git clone](#tab/clone)

サンプル リポジトリをクローンします。

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

次に、そのフォルダーに移動します。

```terminal
cd djangoapp
```

# <a name="download"></a>[ダウンロード](#tab/download)

[https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) にアクセスして **[Clone]\(クローン\)** を選択し、 **[Download ZIP]\(ZIP のダウンロード\)** を選択します。 

その ZIP ファイルを、*djangoapp* という名前のフォルダーに展開します。 

次に、その *djangoapp* フォルダー内でターミナル ウィンドウを開きます。

---

djangoapp サンプルには、データ ドリブンの Django 投票アプリが含まれます。これを、Django ドキュメントの「[はじめての Django アプリ作成](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)」に従って取得します。 参考までに、完成したアプリをここに記載しています。

このサンプルは、App Service のような運用環境で実行するために変更もされています。

- 運用環境の設定は、*azuresite/production.py* ファイルにあります。 開発の詳細は *azuresite/settings.py* にあります。
- `DJANGO_ENV` 環境変数を "production" に設定した場合に、アプリで運用環境の設定が使用されます。 この環境変数は、PostgreSQL データベース構成に使用する他のものと共に、チュートリアルの後半で作成します。

これらの変更は、任意の運用環境で実行するために Django を構成する場合に固有であり、App Service に固有ではありません。 詳細については、[Django デプロイ チェックリスト](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)に関するページを参照してください。

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="create-postgres-database-in-azure"></a>Azure で Postgres データベースを作成する

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. -->

Azure CLI 用 `db-up` 拡張機能をインストールします。

```azurecli
az extension add --name db-up
```

`az` コマンドが認識されない場合は、「[初期環境を設定する](#set-up-your-initial-environment)」の説明に従って Azure CLI がインストールされていることを確認してください。

次に、[`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) コマンドを使用して Azure に Postgres データベースを作成します。

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgre-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- *\<postgres-server-name>* は、すべての Azure で一意の名前に置き換えます (サーバー エンドポイントは `https://\<postgres-server-name>.postgres.database.azure.com`)。 会社名と別の一意の値を組み合わせて使用すると、適切なパターンになります。
- *\<admin-username>* と *\<admin-password>* には、この Postgres サーバーの管理者ユーザーを作成するための資格情報を指定します。
- ここで使用している B_Gen5_1 (Basic、Gen5、1 コア) の[価格レベル](../postgresql/concepts-pricing-tiers.md)は、コストが最も低いものです。 運用データベースの場合は、`--sku-name` 引数を省略して、代わりに GP_Gen5_2 (General Purpose、Gen 5、2 コア) レベルを使用します。

このコマンドによって次の操作が実行されます。これには数分かかる場合があります。

- `DjangoPostgres-tutorial-rg` という[リソース グループ](../azure-resource-manager/management/overview.md#terminology)がまだない場合は、作成します。
- Postgres サーバーを作成します。
- 一意のユーザー名とパスワードを使用して既定の管理者アカウントを作成します (ご自分の資格情報を指定するには、`--admin-user` および `--admin-password` 引数を `az postgres up` コマンドで使用します)。
- `pollsdb` データベースを作成します。
- ローカル IP アドレスからのアクセスを有効にします。
- Azure サービスからのアクセスを有効にします。
- `pollsdb` データベースへのアクセス権を持ったデータベース ユーザーを作成します。

すべての手順は、他の `az postgres` および `psql` コマンドを使用して個別に実行することもできますが、`az postgres up` を使用すれば、そのすべての手順をまとめて実行できます。

コマンドが完了すると、サーバーの URL、生成されたユーザー名 ("joyfulKoala@msdocs-djangodb-12345" など)、GUID パスワードと共に、データベースのさまざまな接続文字列を含む JSON オブジェクトが出力されます。 このチュートリアルの後半で必要になるため、ユーザー名とパスワードを一時的なテキスト ファイルにコピーします。

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>` は、いずれかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)に設定することができます。 ご利用のサブスクリプションから使用できるリージョンは、[`az account list-locations`](/cli/azure/account#az-account-list-locations) コマンドを使用して取得できます。 運用アプリの場合は、データベースとアプリを同じ場所に配置してください。

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="deploy-the-code-to-azure-app-service"></a>Azure App Service にコードをデプロイする

このセクションでは App Service アプリでアプリ ホストを作成し、このアプリを Postgres データベースに接続して、そのホストにコードをデプロイします。

### <a name="create-the-app-service-app"></a>App Service アプリを作成する

ターミナルで、現在の場所がアプリ コードを含むリポジトリのルート (`djangoapp`) であることを確認します。

[`az webapp up`](/cli/azure/webapp#az-webapp-up) コマンドを使用して App Service アプリ (ホスト プロセス) を作成します。

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- `--location` 引数には、前のセクションでデータベースに使用したのと同じ場所を使用します。
- *\<app-name>* は、すべての Azure で一意の名前に置き換えます (サーバー エンドポイントは `https://\<app-name>.azurewebsites.net`)。 *\<app-name>* に使用できる文字は `A`-`Z`、`0`-`9`、`-` です。 会社名とアプリ識別子を組み合わせて使用すると、適切なパターンになります。

このコマンドによって次の操作が実行されます。これには数分かかる場合があります。

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- [リソース グループ](../azure-resource-manager/management/overview.md#terminology)がまだ存在していない場合は作成します (このコマンドでは、先ほどデータベースを作成したのと同じリソース グループを使用します)。
- Basic 価格レベル (B1) で [App Service プラン](overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* を作成します (存在しない場合)。 `--plan` と `--sku` は省略可能ですが、
- App Service アプリが存在しない場合は作成します。
- アプリの既定のログがまだ有効になっていない場合は、有効にします。
- ビルド オートメーションを有効にし、ZIP デプロイを使用してリポジトリをアップロードします。

デプロイが成功すると、コマンドによって次の例のような JSON 出力が生成されます。

![az webapp up コマンド出力の例](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

> [!TIP]
> 多くの Azure CLI コマンドでは、リソース グループの名前や App Service プランなどの一般的なパラメーターが、ファイル *.azure/config* にキャッシュされます。このため、後のコマンドで同じパラメーターをすべて指定する必要はありません。 たとえば、変更を加えた後でアプリを再デプロイするには、パラメーターを指定せずに `az webapp up` を再実行するだけです。 ただし、CLI 拡張機能に含まれるコマンド (`az postgres up` など) では、現時点でキャッシュが使用されません。そのため、ここでは、リソース グループと場所を `az webapp up` で指定する必要がありました。

> [!NOTE]
> この時点でアプリの URL にアクセスしようとすると、"DisallowedHost at /" というエラーが発生します。 このエラーは、前に説明した運用環境の設定を使用するようにアプリがまだ構成されていないために発生します。これについては、次のセクションで説明します。

### <a name="configure-environment-variables-to-connect-the-database"></a>データベースに接続するための環境変数を構成する

コードを App Service にデプロイしたので、次の手順として、アプリを Azure の Postgres データベースに接続します。

アプリ コードでは、さまざまな環境変数でデータベース情報を検索することを想定しています。 App Service で環境変数を設定するには、[az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) コマンドを使用して "アプリ設定" を作成します。

```azurecli
az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- *\<postgres-server-name>* は、先ほど `az postgres up` コマンドで使用した名前に置き換えます。
- *\<username>* と *\<password>* も、そのコマンドで生成された資格情報に置き換えます。
- リソース グループとアプリ名は、 *.azure/config* ファイル内のキャッシュされた値から取得されます。
- このコマンドにより、アプリのコードで想定されている `DJANGO_ENV`、`DBHOST`、`DBNAME`、`DBUSER`、`DBPASS` という名前の設定が作成されます。
- Python コードでは、`os.environ.get('DJANGO_ENV')` のようなステートメントを使用して、環境変数としてこれらの設定にアクセスします。 詳細については、「[環境変数へのアクセス](configure-language-python.md#access-environment-variables)」を参照してください。

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="run-django-database-migrations"></a>Django データベースの移行を実行する

Django データベースの移行によって、Azure データベース上の PostgreSQL のスキーマが、コードに記述されているスキーマと一致していることが確認されます。

1. ブラウザーで *https://\<app-name>.scm.azurewebsites.net/webssh/host* に移動して SSH セッションを開き、Azure アカウントの資格情報 (データベース サーバーの資格情報ではなく) を使用してサインインします。

1. SSH セッションで次のコマンドを実行します (**Ctrl**+**Shift**+**V** キーを使用してコマンドを貼り付けることができます)。

    ```bash
    cd site/wwwroot
    
    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```
    
1. `createsuperuser` コマンドを使用すると、スーパーユーザーの資格情報の入力を求められます。 このチュートリアルの目的では、既定のユーザー名である `root` を使用し、**Enter** キーを押してメール アドレスを空白のままにして、パスワードを「`Pollsdb1`」と入力します。

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)
    
### <a name="create-a-poll-question-in-the-app"></a>アプリで投票の質問を作成する

1. ブラウザーで、URL *http:\//\<app-name>.azurewebsites.net* を開きます。 データベース内に特定の投票がまだないため、アプリには "No polls are available" (投票は利用できません) というメッセージが表示されます。

1. *http:\//\<app-name>.azurewebsites.net/admin* に移動します。前のセクションのスーパーユーザー資格情報 (`root` と `Pollsdb1`) を使用してサインインします。 **[Polls]\(投票\)** で、 **[Questions]\(質問\)** の横の **[Add]\(追加\)** を選択し、いくつかの選択肢がある投票の質問を作成します。

1. もう一度 *http:\//\<app-name>.azurewebsites.net/* に移動し、質問がユーザーに表示されるようになったことを確認します。 質問に好きなように回答してデータベースにデータを生成します。

**お疲れさまでした。** アクティブな Postgres データベースを使用して、Azure App Service for Linux で Python Django Web アプリが実行されています。

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> App Service では、各サブフォルダー内で *wsgi.py* ファイル (`manage.py startproject` によって既定で作成されます) を探すことにより、Django プロジェクトが検出されます。 App Service でそのファイルが見つかると、Django Web アプリが読み込まれます。 詳細については、[組み込みの Python イメージの構成](configure-language-python.md)に関するページを参照してください。

## <a name="make-code-changes-and-redeploy"></a>コードの変更を加えて再デプロイする

このセクションでは、アプリに対してローカルでの変更を行い、App Service にコードを再デプロイします。 このプロセスで、進行中の作業をサポートする Python 仮想環境を設定します。

### <a name="run-the-app-locally"></a>アプリをローカルで実行する

ターミナル ウィンドウで次のコマンドを実行します。 スーパーユーザーを作成するときは、必ずプロンプトに従ってください。

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```cmd
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Web アプリが完全に読み込まれると、Django 開発サーバーによって、次のメッセージにローカル アプリの URL が表示されます: "Starting development server at http://127.0.0.1:8000/. Quit the server with CTRL-BREAK" (http://127.0.0.1:8000/ で開発サーバーを起動しています。CTRL-BREAK キーを押してサーバーを終了します)。

![Django 開発サーバーの出力例](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

次の手順に従って、アプリをローカルでテストします。

1. ブラウザーで *http:\//localhost:8000* にアクセスすると、"No polls are available" (投票は利用できません) というメッセージが再び表示されます。 

1. *http:\//localhost:8000/admin* に移動し、前の手順で作成した管理者ユーザーを使用してサインインします。 **[Polls]\(投票\)** で、 **[Questions]\(質問\)** の横の **[Add]\(追加\)** をもう一度選択し、いくつかの選択肢がある投票の質問を作成します。 

1. *http:\//localhost:8000* に再び移動し、アプリをテストするために質問に回答します。 

1. **Ctrl**+**C** キーを押して Django サーバーを停止します。

ローカルで実行している場合、アプリではローカルの Sqlite3 データベースを使用しており、運用データベースに干渉することはありません。 必要に応じて、ローカルの PostgreSQL データベースを使用して、運用環境をより正確にシミュレートすることもできます。

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="update-the-app"></a>アプリの更新

`polls/models.py` で、`choice_text` で始まる行を見つけ、`max_length` パラメーターを 100 に変更します。

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

データ モデルを変更したので、新しい Django の移行を作成し、データベースを移行します。

```
python manage.py makemigrations
python manage.py migrate
```

`python manage.py runserver` を使用して開発サーバーを再度実行し、*http:\//localhost:8000/admin* でアプリをテストします。

**Ctrl**+**C** キーを使用して Django Web サーバーを再度停止します。

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="redeploy-the-code-to-azure"></a>Azure にコードを再デプロイする

リポジトリのルートで次のコマンドを実行します。

```azurecli
az webapp up
```

このコマンドでは、 *.azure/config* ファイルにキャッシュされたパラメーターを使用します。 アプリが既に存在することが App Service によって検出されるので、コードの再デプロイだけが行われます。

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="rerun-migrations-in-azure"></a>Azure で移行を再実行する

データ モデルに変更を加えたので、App Service へのデータベースの移行を再実行する必要があります。

ブラウザーで *https://\<app-name>.scm.azurewebsites.net/webssh/host* にアクセスして SSH セッションを再度開きます。 次のコマンドを実行します。

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="review-app-in-production"></a>運用環境でアプリを確認する

*http:\//\<app-name>.azurewebsites.net* に移動し、運用環境でアプリを再度テストします (データベース フィールドの長さを変更しただけなので、質問の作成時に長い回答を入力しようとした場合にのみ、この変更が明らかになります)。

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

Azure 上でアプリをホストするコンテナー内から生成されたコンソール ログに、アクセスすることができます。

ログ ストリームを確認するには、次の Azure CLI コマンドを実行します。 このコマンドでは、 *.azure/config* ファイルにキャッシュされたパラメーターを使用します。

```azurecli
az webapp log tail
```

コンソール ログがすぐに表示されない場合は、30 秒以内にもう一度確認します。

任意のタイミングでログのストリーミングを停止するには、**Ctrl**+**C** キーを押します。

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> `https://<app-name>.scm.azurewebsites.net/api/logs/docker` で、ブラウザーからログ ファイルを検査することもできます。
>
> `az webapp up` を実行すると、既定のログが自動的にオンになります。 パフォーマンス上の理由から、このログはしばらくすると自動的にオフになりますが、また `az webapp up` を実行すると、その都度オンに戻ります。 これを手動でオンにするには、次のコマンドを実行します。
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="manage-your-app-in-the-azure-portal"></a>Azure portal でアプリを管理する

[Azure portal](https://portal.azure.com) でアプリ名を探し、結果内のアプリを選択します。

![Azure portal で Python Django アプリに移動する](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

既定では、アプリの **[Overview]\(概要\)** ページがポータルに表示されます。これにより、全般的なパフォーマンス ビューが提供されます。 ここでは、参照、停止、再開、削除といった基本的な管理タスクを実行することもできます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure portal の [概要] ページで Python Django アプリを管理する](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリを残しておく場合、または次のチュートリアルに進む場合は、「[次のステップ](#next-steps)」に進んでください。 それ以外の場合は、継続して料金が発生しないように、このチュートリアルで作成したリソース グループを削除できます。

```azurecli
az group delete
```

このコマンドでは、 *.azure/config* ファイルにキャッシュされたリソース グループ名が使用されます。 リソース グループを削除することによって、その中に含まれるすべてのリソースの割り当て解除と削除も行われます。

[問題がある場合は、お知らせください。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="next-steps"></a>次のステップ

カスタム DNS 名をアプリにマップする方法を確認する:

> [!div class="nextstepaction"]
> [チュートリアル:カスタム DNS 名をアプリにマップする](app-service-web-tutorial-custom-domain.md)

App Service で Python アプリが実行される方法を確認する:

> [!div class="nextstepaction"]
> [Python アプリの構成](configure-language-python.md)
