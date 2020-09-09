---
title: Azure メンテナンス イベントの計画
description: Azure SQL データベースおよび Azure SQL Managed Instance で計画メンテナンス イベントを準備する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 08/25/2020
ms.openlocfilehash: 85459f357032a7f9944d50e3e4f3929015c6dcfd
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869119"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database および Azure SQL Managed Instance での Azure メンテナンス イベントの計画
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL データベースおよび Azure SQL Managed Instance 内のデータベースに計画メンテナンス イベントを準備する方法について説明します。

## <a name="what-is-a-planned-maintenance-event"></a>計画メンテナンス イベントとは

データベースごとに、Azure SQL Database および Azure SQL Managed Instance で、1 つのレプリカがプライマリであるデータベース レプリカのクォーラムが維持されます。 常にプライマリ レプリカがオンラインでサービスを提供する必要があり、1 つ以上のセカンダリ レプリカが正常な状態である必要があります。 計画メンテナンス中は、データベース クォーラムのメンバーは一度に 1 つずつオフラインになります。これは、1 つの対応するプライマリ レプリカと 1 つ以上のセカンダリ レプリカをオンラインの状態に保ち、クライアントのダウンタイムが発生しないようにするためです。 プライマリ レプリカをオフラインにする必要がある場合、再構成/フェールオーバー プロセスが発生し、1 つのセカンダリ レプリカが新しいプライマリになります。  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>計画メンテナンス イベント時に予期されること

メンテナンス イベントでは、メンテナンス イベントの開始時に集められたプライマリ レプリカとセカンダリ レプリカに応じて、1 つまたは複数のフェールオーバーが生成される場合があります。 平均すると、1 回の計画メンテナンス イベントあたり 1.7 回のフェールオーバーが発生します。 再構成/フェールオーバーは、通常 30 秒以内に完了します。 平均は 8 秒です。 アプリケーションが既に接続されている場合は、データベースの新しいプライマリ レプリカに再接続する必要があります。 新しいプライマリ レプリカがオンラインになる前にデータベースで再構成を行っている間に新しい接続が試行された場合、エラー 40613 (データベースは使用できません):" *"サーバー '{servername}' 上のデータベース '{databasename}' は現在使用できません。後で接続を再試行してください。"* データベースに実行時間の長いクエリがある場合、このクエリは再構成中に中断され、再開する必要があります。

## <a name="how-to-simulate-a-planned-maintenance-event"></a>計画メンテナンス イベントをシミュレートする方法

運用環境にデプロイする前に、クライアント アプリケーションがメンテナンス イベントに対する回復性を備えていることを確認することは、アプリケーションの障害のリスクを軽減するのに役立ち、エンド ユーザーにとっては、アプリケーションの可用性に寄与します。 PowerShell、CLI、または REST API を使用して[手動フェールオーバーを開始](https://aka.ms/mifailover-techblog)することによって、計画メンテナンス イベント中にクライアント アプリケーションの動作をテストできます。 プライマリ レプリカをオフラインにするメンテナンス イベントと同じ動作が生成されます。

## <a name="retry-logic"></a>再試行ロジック

クラウド データベース サービスに接続するクライアント運用アプリケーションでは、堅牢な接続[再試行ロジック](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)を実装する必要があります。 これにより、フェールオーバーは、エンド ユーザーに対して透過的になります。または、少なくとも、悪影響を最小限に抑えることができます。

## <a name="resource-health"></a>リソース ヘルス

データベースでログオン エラーが発生している場合は、[Azure portal](https://portal.azure.com) の [[リソース正常性]](../../service-health/resource-health-overview.md#get-started) ウィンドウで、現在の状態を確認します。 [正常性の履歴] セクションには、(該当する場合は) イベントごとのダウンタイムの理由が含まれています。

## <a name="next-steps"></a>次のステップ

- Azure SQL Database と Azure SQL Managed Instance の[リソース正常性](resource-health-to-troubleshoot-connectivity.md)について説明します。
- 再試行ロジックの詳細については、「[一時エラーの再試行ロジック](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)」を参照してください。
