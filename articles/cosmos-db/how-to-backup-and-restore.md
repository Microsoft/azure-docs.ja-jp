---
title: バックアップから Azure Cosmos DB データを復元する方法
description: この記事では、バックアップから Azure Cosmos DB データを復元する方法、Azure サポートに連絡してデータを復元する方法、およびデータが復元された後に実行する手順について説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: e21a3ae23ca6a856c9524e52e80f33362f53cae1
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040848"
---
# <a name="how-to-restore-azure-cosmos-db-data-from-a-backup"></a>バックアップから Azure Cosmos DB データを復元する方法

データベースまたはコンテナーを誤って削除した場合は、[サポート チケットを申請するか]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)、[Azure サポートに連絡して]( https://azure.microsoft.com/support/options/)、自動オンライン バックアップからデータを復元できます。 Azure サポートは、**Standard**、**Developer、またはそれよりも高度な特定のプランのみで利用可能です。 Azure サポートは、**Basic** プランでは利用できません。 さまざまなサポート プランについては、「[Azure のサポート プラン](https://azure.microsoft.com/support/plans/)」ページを参照してください。 

バックアップの特定のスナップショットを復元するには、該当のスナップショットのバックアップ サイクル期間中にデータが利用可能であることが、Azure Cosmos DB から求められます。

## <a name="request-a-restore"></a>復元の要請

復元を要請する前に、次の詳細を確認する必要があります。

* サブスクリプション ID を準備ししておきます。

* データが誤って削除または変更された際の方法に基づいて、詳細情報を準備しておく必要があります。 情報を探す時間を最小限に抑えるために、事前に情報を入手しておくことをお勧めします。時間に制約がある一部のケースでは、探す時間が悪影響になる場合があります。

* Azure Cosmos DB アカウント全体が削除された場合は、削除されたアカウントの名前を提示する必要があります。 削除されたアカウントと同じ名前の別のアカウントを作成する場合は、選択する適切なアカウントを判断できるように、サポート チームに報告してください。 削除されたアカウントごとに別のサポート チケットを申請することをお勧めします。そうすることで、復元の状態についての混乱を最小限に抑えられます。

* 1 つ以上のデータベースが削除された場合は、Azure Cosmos データベース名だけでなく Azure Cosmos アカウントも提示して、同じ名前の新しいデータベースが存在するかどうかを明確にする必要があります。

* 1 つ以上のコンテナーが削除された場合は、Azure Cosmos アカウント名、データベース名、およびコンテナー名を提示する必要があります。 そうすることで、同じ名前のコンテナーが存在するかどうかを明確にします。

データの破損が発生したときに、コンテナー内のドキュメントが変更または削除された場合は、**できるだけ迅速にコンテナーを削除**してください。 コンテナーを削除することで、Azure Cosmos DB でのバックアップの上書きを防止できます。 何らかの理由により削除ができない場合は、できるだけ迅速にチケットを申請する必要があります。 Azure Cosmos アカウント名、データベース名、コレクション名に加えて、データが復元可能な特定の時点を指定する必要があります。 その時点で利用可能な最善のバックアップを特定できるように、できるだけ厳密に示すことが重要です。 また、UTC で時間を指定することも重要です。 

次のスクリーンショットは、Azure portal を使ってデータを復元するコンテナー (コレクション/グラフ/テーブル) に関するサポート リクエストを作成する方法を示しています。 要請の優先度付けに役立つように、データの種類、復元の目的、データが削除された時刻などの追加の詳細を提示します。

![Azure portal を使用してバックアップのサポート リクエストを作成する](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>復元後の操作

データを復元した後は、新しいアカウント名 (通常は `<original-name>-restored1` 形式) とアカウントが復元された時刻に関する通知を受け取ります。 復元されたアカウントは、同じプロビジョニング済みのスループットとインデックス作成ポリシーを保持し、元のアカウントと同じリージョン内にあります。 サブスクリプションの管理者または共同管理者になっているユーザーには、復元されたアカウントが表示されます。

データが復元された後は、復元されたアカウント内のデータを検査および検証して、ご自身の希望するバージョンが含まれていることを確認する必要があります。 すべてが順調なようであれば、[Azure Cosmos DB 変更フィード](change-feed.md)または [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) を使用して、データを元のアカウントに移行して戻す必要があります。

データの移行後は、コンテナーやデータベースをすぐに削除することをお勧めします。 復元されたデータベースまたはコンテナーを削除しなかった場合は、要求ユニット、ストレージ、およびエグレスに対するコストが発生します。

## <a name="next-steps"></a>次の手順

次に、以下の記事を利用して、元のアカウントにデータを移行して戻す方法について学習できます。

* 復元の要請を行うために、Azure サポートに連絡して [Azure portal からチケットを申請](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)する。
* [Cosmos DB 変更フィードを使用](change-feed.md)してデータを Azure Cosmos DB に移動する。

* [Azure Data Factory を使用](../data-factory/connector-azure-cosmos-db.md)してデータを Azure Cosmos DB に移動する。
