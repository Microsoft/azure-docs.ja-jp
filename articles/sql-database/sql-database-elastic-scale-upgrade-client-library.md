---
title: "最新の Elastic Database クライアント ライブラリへのアップグレード | Microsoft Docs"
description: "Nuget を使用してアプリとライブラリをアップグレードする"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 0a546510-76e7-465e-9271-f15ff0cfa959
ms.service: sql-database
ms.custom: multiple databases
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ddove
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 0bbbc4f61c37fe4d296221c372bfdf6dd429a32b
ms.lasthandoff: 03/07/2017


---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>最新のエラスティック データベース クライアント ライブラリを使用するためのアプリのアップグレード
[Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library.md)の新しいバージョンが NuGet と Visual Studio の NuGetPackage マネージャーのインターフェイスから利用できるようになりました。 アップグレードにはバグ修正が含まれており、クライアント ライブラリの新機能をサポートします。

**最新バージョン:**[Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)に移動します。

新しいライブラリを使用してアプリケーションを再構築し、新しい機能をサポートするために、Azure SQL Database に格納されている既存のシャード マップ マネージャーのメタデータを変更します。

これらの手順を実行することで、メタデータ オブジェクトの更新時に古いバージョンのクライアント ライブラリが環境内に存在しないようにします。つまり、古いバージョンのメタデータ オブジェクトはアップグレード後は作成されなくなります。   

## <a name="upgrade-steps"></a>アップグレードの手順
**1.アプリケーションをアップグレードします。** Visual Studio で、最新のクライアント ライブラリのバージョンをダウンロードして、ライブラリを使用する開発プロジェクトのすべてに参照させます。その後、再構築してデプロイします。 

* Visual Studio ソリューションで **[ツール]** --> **[NuGet Package Manager]** -->  **[Manage NuGet Packages for Solution (ソリューションの NuGet パッケージを管理)]** と選択します。 
* (Visual Studio 2013) 左側のパネルで **[更新]** を選択し、ウィンドウに表示される **Azure SQL Database Elastic Scale Client Library** パッケージの **[更新]** ボタンを選択します。
* (Visual Studio 2015) [フィルター] ボックスで **[アップグレード可能]**を選択します。 アップグレードするパッケージを選択し、 **[アップグレード]** ボタンをクリックします。
* (Visual Studio 2017) ダイアログの上部にある **[更新プログラム]** を選択します。 アップグレードするパッケージを選択し、 **[アップグレード]** ボタンをクリックします。
* 構築してデプロイします。 

**2.スクリプトをアップグレードします。** シャードの管理に **PowerShell** スクリプトを使用している場合は、[新しいライブラリ バージョンをダウンロード](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)して、スクリプトを実行するディレクトリにコピーします。 

**3.Split-Merge サービスをアップグレードします。** Elastic Database 分割/マージ ツールを使用してシャード化されたデータを再編成する場合は、[最新バージョンのツールをダウンロードしてデプロイ](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)します。 サービスの詳しいアップグレード手順については、[こちら](sql-database-elastic-scale-overview-split-and-merge.md)をご覧ください。 

**4.シャード マップ マネージャーのデータベースをアップグレードします**。 Azure SQL Database でシャード マップをサポートするメタデータをアップグレードします。  これは、PowerShell か C# を使用して実行できます。 次に両方の方法について説明します。

***オプション 1: PowerShell を使用してメタデータをアップグレードする***

1. [こちら](http://nuget.org/nuget.exe) から NuGet の最新のコマンド ライン ユーティリティをダウンロードして、フォルダーに保存します。 
2. コマンド プロンプトを開き、同じフォルダーに移動して、次のコマンドを発行します。`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. ダウンロードした新しいクライアントの DLL バージョンを含むサブフォルダーに移動します。例:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. [スクリプト センター](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)から エラスティック データベース クライアントのアップグレード スクリプトレットをダウンロードして、DLL が含まれる同じフォルダーに保存します。
5. そのフォルダーのコマンド プロンプトから"PowerShell.\upgrade.ps1"を実行し、画面の指示に従います。

***オプション 2: C# を使用してメタデータをアップグレードする***

または、次の例のように、メソッド [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) と [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) を呼び出して、ShardMapManager を開き、すべてのシャードを反復し、メタデータのアップグレードを実行する Visual Studio アプリケーションを作成します。 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

このメタデータのアップグレードの手法は問題なく何度も適用できます。 たとえば古いクライアント バージョンで、シャードをすでに更新した後に不注意にシャードを作成してしまった場合、すべてのシャードで再びアップグレードを実行し、最新のメタデータ バージョンがインフラストラクチャ全体に存在するようにできます。 

**注:** 今まで公開されたクライアント ライブラリの新しいバージョンは、Azure SQL DB の古いバージョンのシャード マップ マネージャー メタデータで引き続き操作できます (逆の場合も同様)。   ただし、最新のクライアントで一部の新機能を活用するには、メタデータをアップグレードする必要があります。   メタデータのアップグレードはユーザー データやアプリケーション専用データに影響することはありません。シャード マップ マネージャーで作成し、使用したオブジェクトのみに影響します。  アプリケーションは以下に説明するアップグレード シーケンスを通じて引き続き操作できます。 

## <a name="elastic-database-client-version-history"></a>エラスティック データベース クライアントのバージョン履歴
バージョン履歴については、 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png


