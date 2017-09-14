---
title: "C と C++ を使用して SQL Database に接続する | Microsoft Docs"
description: "このクイック スタートのコード サンプルを使用して、C++ で最新のアプリケーションを構築し、Azure SQL Database を使用してクラウドの強力なリレーショナル データベースでバックアップします。"
services: sql-database
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: 
ms.assetid: 07d9e0b1-3234-4f17-a252-a7559160a9db
ms.service: sql-database
ms.custom: develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 03/06/2017
ms.author: edmacauley
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 713e4a29550f37b03af3547bead42146a84a417f
ms.contentlocale: ja-jp
ms.lasthandoff: 04/15/2017

---
# <a name="connect-to-sql-database-using-c-and-c"></a>C と C++ を使用して SQL Database に接続する
これは Azure SQL DB に接続を試みる C および C++ の開発者向けの投稿です。 セクションに分かれているため、興味のあるセクションから読み進めてください。 

## <a name="prerequisites-for-the-cc-tutorial"></a>C/C++ チュートリアルの前提条件
以下のものがそろっていることを確認してください。

* アクティブな Azure アカウント。 これがない場合は、 [Azure の無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
* [Visual Studio](https://www.visualstudio.com/downloads/)。 このサンプルをビルドして実行するには、C++ 言語コンポーネントをインストールする必要があります。
* [Visual Studio Linux Development](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e)。 Linux で開発する場合は、Visual Studio Linux 拡張機能もインストールする必要があります。 

## <a id="AzureSQL"></a>Azure SQL Database と仮想マシン上の SQL Server
Azure SQL は Microsoft SQL Server 上に構築されており、高可用性、パフォーマンス、拡張性の高いサービスを提供できます。 オンプレミスで実行されている専用のデータベースでの SQL Azure の使用は、多くのメリットがあります。 SQL Azure があれば、データベースのインストール、設定、メンテナンス、管理は必要なく、必要なのはコンテンツとデータベースの構造のみになります。 通常、データベースについて懸案されるフォールト トレランスや冗長性といった機能は、すべて組み込み済みです。 

現在、Azure には、SQL サーバー ワークロードをホストする機能として、サービスとしてのデータベースである Azure SQL Database と、仮想マシン (VM) 上の SQL サーバーの2つのオプションがあります。 これら 2 つの違いについて細かくは見ていきませんが、クラウド サービスで提供するコスト削減とパフォーマンスの最適化を利用できる、新しいクラウド ベースのアプリケーションには Azure SQL Database が最適です。 オンプレミス アプリケーションのクラウドへの移行または拡張を検討している場合は、Azure 仮想マシン上の SQL サーバーがお勧めです。 記事の内容をわかりやすくするために、Azure SQL Database を作成しましょう。 

## <a id="ODBC"></a>データ アクセス テクノロジ: ODBC および OLE DB
Azure SQL DB に接続することに違いはありませんが、現在、データベースへの接続方法として、ODBC (Open Database connectivity) と OLE DB (オブジェクトのリンクと埋め込みデータベース) の 2 つがあります。 近年、Microsoft は、[ネイティブのリレーショナル データベースにおいて ODBC](https://blogs.msdn.microsoft.com/sqlnativeclient/2011/08/29/microsoft-is-aligning-with-odbc-for-native-relational-data-access/)と連携してきました。 ODBC は比較的シンプルであり、OLE DB よりも動作が速くなります。 ただし、ODBC は以前の C スタイルの API を使用しているという点で注意が必要です。 

## <a id="Create"></a>手順 1: Azure SQL Database を作成する
「 [作業の開始](sql-database-get-started-portal.md) 」ページで、サンプル データベースを作成する方法についてご確認ください。  または、こちらの[ 2 分ほどの短い動画](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/)に従えば、Azure Portal を使用して Azure SQL Database を作成できます。

## <a id="ConnectionString"></a>手順 2: 接続文字列を取得する
Azure SQL Database がプロビジョニングされたら、次の手順に従って接続情報を指定し、ファイアウォール アクセスのクライアント IP アドレスを追加する必要があります。 

[Azure Portal](https://portal.azure.com/)で、**[データベース接続文字列の表示]** を使用し、データベースの概要セクションの一部として表示されている Azure SQL Database ODBC 接続文字列を取得します。 

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

**ODBC (Node.js を含む) [SQL 認証]** 文字列の内容をコピーします。 この文字列は後で C++ ODBC コマンド ライン インタープリターからの接続に使用します。 この文字列は、ドライバー、サーバー、その他のデータベース接続パラメーターといった詳細情報を提供します。 

## <a id="Firewall"></a>手順 3: ファイアウォールに IP アドレスを追加する
データベース サーバーのファイアウォールに関するセクションに移動し、[次の手順を使用してクライアントの IP アドレスをファイアウォール](sql-database-configure-firewall-settings.md)に追加して、正しく接続できることを確認します。 

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

この時点で、Azure SQL DB が構成され、C++ コードから接続する準備が整いました。 

## <a id="Windows"></a>手順 4: Windows C/C++ アプリケーションから接続する
Visual Studio でビルドする[こちらのサンプルを使用すれば、Windows の ODBC を使用して Azure SQL DB](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) に簡単に接続できます。 このサンプルでは、ODBC コマンド ライン インタープリターを使用して Azure SQL DB に接続します。 このサンプルでは、コマンドライン引数としてデータベース ソース名ファイル (DSN ファイル) か、先に Azure Portal からコピーした詳細な接続文字列を使用します。 このプロジェクトのプロパティ ページを表示し、次に示すように、コマンド引数に接続文字列を貼り付けます。 

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

データベースの接続文字列の一部として、データベースの適切な認証情報が指定されていることを確認します。 

アプリケーションを起動してビルドします。 正常な接続を検証する次のウィンドウが表示されます。 **create table**のような基本的な SQL コマンドを実行して、データベースの接続を検証することもできます。

![SQL コマンド](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

また、コマンド引数が指定されていないときに開くウィザードを使用して、DSN ファイルを作成することもできます。 このオプションも試してみることをお勧めします。 この DSN ファイルを使用すれば、認証設定を自動化して保護できます。 

![DSN ファイルを作成する](./media/sql-database-develop-cplusplus-simple/datasource.png)

ご利用ありがとうございます。 これで、C++ と Windows の ODBC を使用して Azure SQL に正しく接続できました。 引き続き Linux プラットフォーム向けの同様の記事もご覧ください。 

## <a id="Linux"></a>手順 5: Linux C/C++ アプリケーションから接続する
まだご存じないかもしれませんが、Visual Studio では C++ Linux アプリケーションを開発できるようになりました。 これについての詳細は、ブログ記事「[Visual C++ for Linux Development (Visual C++ で Linux 向けの開発)](https://blogs.msdn.microsoft.com/vcblog/2016/03/30/visual-c-for-linux-development/) をご覧ください。 Linux 向けにビルドするには、Linux ディストリビューションを実行しているリモート コンピューターが必要です。 お持ちでない場合は、[Linux Azure 仮想マシン](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使用して簡単に設定できます。 

このチュートリアルでは、Ubuntu 16.04 Linux ディストリビューションが設定されていることを前提としています。 ここに示す手順は、Ubuntu 15.10、Red Hat 6、Red Hat 7 でも使用できます。 

次の手順では、ディストリビューションの SQL と ODBC に必要なライブラリをインストールします。

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Visual Studio を起動します。 [ツール]、[オプション]、[クロス プラットフォーム]、[接続マネージャー] と移動し、Linux ボックスに接続を追加します。 

![ツール オプション](./media/sql-database-develop-cplusplus-simple/tools.png)

SSH 経由で接続が確立されたら、空のプロジェクト (Linux) テンプレートを作成します。 

![新しいプロジェクト テンプレート](./media/sql-database-develop-cplusplus-simple/template.png)

次に、[新しい C ソース ファイルを追加し、次の内容で置き換えます](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c)。 ODBC APIs SQLAllocHandle、SQLSetConnectAttr、SQLDriverConnect を使用すると、データベースへの接続を初期化し、確立することができます。 Windows ODBC のサンプルと同様に、SQLDriverConnect 呼び出しを、先に Azure Portal からコピーしたデータベース接続文字列パラメーターの詳細に置き換える必要があります。 

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

最後に、コンパイルする前に **odbc** をライブラリ依存関係として追加します。 

![入力ライブラリとして ODBC を追加する](./media/sql-database-develop-cplusplus-simple/lib.png)

アプリケーションを起動するには、**[デバッグ]** メニューから Linux コンソールを表示します。 

![Linux コンソール](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

接続に成功すると、現在のデータベース名が Linux コンソールに表示されます。 

![Linux コンソール ウィンドウの出力](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

ご利用ありがとうございます。 これでチュートリアルは終わりです。これで、Windows および Linux プラットフォームの C++ から Azure SQL DB に接続できるようになりました。

## <a id="GetSolution"></a>詳細な C++ チュートリアル ソリューションを確認する
github では、この記事のすべてのサンプルを含む GetStarted ソリューションをご利用いただけます。

* [ODBC C++ Windows サンプル](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29): Azure SQL に接続するための、Windows C++ ODBC サンプルをダウンロードします
* [ODBC C++ Linux サンプル](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29): Azure SQL に接続するための、Linux C++ ODBC サンプルをダウンロードします

## <a name="next-steps"></a>次のステップ
* 「 [SQL Database の開発: 概要](sql-database-develop-overview.md)
* [ODBC API リファレンス](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>その他のリソース
* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)


