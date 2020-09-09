---
title: クイック スタート:Ruby を使用して接続する - Azure Database for MySQL
description: このクイックスタートでは、Azure Database for MySQL に接続してデータを照会するために使用できる、Ruby コード サンプルをいくつか紹介します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 79f3efad9ba5f6c0378f8b093a2f375275767659
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185844"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-mysql"></a>クイック スタート:Ruby を使用して Azure Database for MySQL に接続してデータを照会する

このクイックスタートでは、Windows、Ubuntu Linux、Mac の各プラットフォームから [Ruby](https://www.ruby-lang.org) アプリケーションと [mysql2](https://rubygems.org/gems/mysql2) gem を使用して Azure Database for MySQL に接続する方法を紹介します。 ここでは、SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、削除する方法を説明します。 このトピックでは、Ruby を使用した開発には慣れているものの、Azure Database for MySQL の使用は初めてであるユーザーを想定しています。

## <a name="prerequisites"></a>前提条件

このクイックスタートでは、次のいずれかのガイドで作成されたリソースを出発点として使用します。
- [Azure Portal を使用した Azure Database for MySQL サーバーの作成](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI を使用した Azure Database for MySQL サーバーの作成](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> [Azure portal](./howto-manage-firewall-using-portal.md) または [Azure CLI](./howto-manage-firewall-using-cli.md) を使用して、接続元の IP アドレスにサーバーのファイアウォール規則が追加されていることを確認します。

## <a name="install-ruby"></a>Ruby のインストール
ご使用のコンピューターに Ruby、Gem、MySQL2 ライブラリをインストールします。 

### <a name="windows"></a>Windows
1. バージョン 2.3 の [Ruby](https://rubyinstaller.org/downloads/) をダウンロードしてインストールします。
2. スタート メニューから新しいコマンド プロンプト (cmd) を起動します。
3. バージョン 2.3 の Ruby ディレクトリに移動します。 `cd c:\Ruby23-x64\bin`
4. Ruby のインストールをテストします。`ruby -v` コマンドを実行して、インストールされているバージョンを確認してください。
5. Gem のインストールをテストします。`gem -v` コマンドを実行して、インストールされているバージョンを確認してください。
6. コマンド `gem install mysql2` を実行し、Gem を使用して Ruby 用の Mysql2 モジュールをビルドします。

### <a name="macos"></a>MacOS
1. コマンド `brew install ruby` を実行して、Homebrew で Ruby をインストールします。 その他のインストール オプションについては、Ruby の [インストール ドキュメント](https://www.ruby-lang.org/en/documentation/installation/#homebrew)を参照してください。
2. Ruby のインストールをテストします。`ruby -v` コマンドを実行して、インストールされているバージョンを確認してください。
3. Gem のインストールをテストします。`gem -v` コマンドを実行して、インストールされているバージョンを確認してください。
4. コマンド `gem install mysql2` を実行し、Gem を使用して Ruby 用の Mysql2 モジュールをビルドします。

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. コマンド `sudo apt-get install ruby-full` を実行して Ruby をインストールします。 その他のインストール オプションについては、Ruby の [インストール ドキュメント](https://www.ruby-lang.org/en/documentation/installation/)を参照してください。
2. Ruby のインストールをテストします。`ruby -v` コマンドを実行して、インストールされているバージョンを確認してください。
3. コマンド `sudo gem update --system` を実行して、Gem の最新の更新プログラムをインストールします。
4. Gem のインストールをテストします。`gem -v` コマンドを実行して、インストールされているバージョンを確認してください。
5. `sudo apt-get install build-essential` コマンドを実行して、gcc や make などのビルド ツールをインストールします。
6. コマンド `sudo apt-get install libmysqlclient-dev` を実行して、MySQL client developer ライブラリをインストールします。
7. コマンド `sudo gem install mysql2` を実行し、Gem を使用して Ruby 用の mysql2 モジュールをビルドします。

## <a name="get-connection-information"></a>接続情報の取得
Azure Database for MySQL に接続するために必要な接続情報を取得します。 完全修飾サーバー名とログイン資格情報が必要です。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、作成したサーバー (例: **mydemoserver**) を検索します。
3. サーバー名をクリックします。
4. サーバーの **[概要]** パネルから、 **[サーバー名]** と **[サーバー管理者ログイン名]** を書き留めます。 パスワードを忘れた場合も、このパネルからパスワードをリセットすることができます。
 ![Azure Database for MySQL サーバー名](./media/connect-ruby/1_server-overview-name-login.png)

## <a name="run-ruby-code"></a>Ruby コードの実行 
1. 以下のセクションからテキスト ファイルに Ruby コードを貼り付け、.rb というファイル拡張子でプロジェクト フォルダーに保存します (例: `C:\rubymysql\createtable.rb`、`/home/username/rubymysql/createtable.rb`)。
2. このコードを実行するには、コマンド プロンプトまたは Bash シェルを起動します。 プロジェクト フォルダーに移動します (`cd rubymysql`)。
3. そのうえで、Ruby コマンドに続けてファイル名を入力し、アプリケーションを実行します (例: `ruby createtable.rb`)。
4. Windows OS で環境変数 PATH に Ruby アプリケーションが追加されていない場合、ruby アプリケーションを起動するには完全なパスを使用する必要があります (例: `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`)。

## <a name="connect-and-create-a-table"></a>接続とテーブルの作成
接続し、**CREATE TABLE** SQL ステートメントでテーブルを作成してから、**INSERT INTO** SQL ステートメントでそのテーブルに行を追加するには、次のコードを使用します。

このコードは、[mysql2::client](https://www.rubydoc.info/gems/mysql2/0.4.8) クラスの .new() メソッドを使用して Azure Database for MySQL に接続しています。 次に、[query()](https://www.rubydoc.info/gems/mysql2/0.4.8#Usage) メソッドを何度か呼び出して、DROP、CREATE TABLE、INSERT INTO の各コマンドを実行します。 その後、[close()](https://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) メソッドを呼び出して、終了する前に接続を閉じます。

`host`、`database`、`username`、`password` の各文字列は、実際の値に置き換えてください。 
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>データの読み取り
接続し、**SELECT** SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。 

このコードは、[mysql2::client](https://www.rubydoc.info/gems/mysql2/0.4.8) クラスの .new() メソッドを使用して Azure Database for MySQL に接続しています。 次に、[query()](https://www.rubydoc.info/gems/mysql2/0.4.8#Usage) メソッドを呼び出して SELECT コマンドを実行します。 その後、[close()](https://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) メソッドを呼び出して、終了する前に接続を閉じます。

`host`、`database`、`username`、`password` の各文字列は、実際の値に置き換えてください。 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>データの更新
接続し、**UPDATE** SQL ステートメントを使用してデータを更新するには、次のコードを使用します。

このコードは、[mysql2::client](https://www.rubydoc.info/gems/mysql2/0.4.8) クラスの .new() メソッドを使用して Azure Database for MySQL に接続しています。 次に、[query()](https://www.rubydoc.info/gems/mysql2/0.4.8#Usage) メソッドを呼び出して UPDATE コマンドを実行します。 その後、[close()](https://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) メソッドを呼び出して、終了する前に接続を閉じます。

`host`、`database`、`username`、`password` の各文字列は、実際の値に置き換えてください。 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>データの削除
接続し、**DELETE** SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。 

このコードは、[mysql2::client](https://www.rubydoc.info/gems/mysql2/0.4.8) クラスの .new() メソッドを使用して Azure Database for MySQL に接続しています。 次に、[query()](https://www.rubydoc.info/gems/mysql2/0.4.8#Usage) メソッドを呼び出して DELETE コマンドを実行します。 その後、[close()](https://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) メソッドを呼び出して、終了する前に接続を閉じます。

`host`、`database`、`username`、`password` の各文字列は、実際の値に置き換えてください。 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [エクスポートとインポートを使用したデータベースの移行](./concepts-migrate-import-export.md)
