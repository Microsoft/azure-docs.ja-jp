---
title: Azure Migrate Server Migration を使用した VMware 移行オプションを選択する | Microsoft Docs
description: Azure Migrate Server Migration を使用して VMware VM を Azure に移行するためのオプションの概要について説明します。
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: e62b9cea80f1ed7f672135b93e52ba606a717a6c
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950223"
---
# <a name="select-a-vmware-migration-option"></a>VMware 移行オプションを選択する

Azure Migrate Server Migration ツールを使用して VMware VM を Azure に移行できます。 このツールには、VMware VM の移行のためのいくつかのオプションが用意されています。

- エージェントレス レプリケーションを使用した移行。 VM に何もインストールする必要なく VM を移行します。
- レプリケーション用のエージェントを使用した移行。 レプリケーションのために VM 上にエージェントをインストールします。


## <a name="compare-migration-methods"></a>移行方法を比較する

これらの一部の比較を使用して、使用する方法を決定します。 [エージェントレス](migrate-support-matrix-vmware-migration.md#agentless-migration)および[エージェント ベース](migrate-support-matrix-vmware-migration.md#agent-based-migration)の移行についての完全なサポート要件を確認することもできます。

**設定** | **エージェントレス** | **エージェント ベース**
--- | --- | ---
**Azure のアクセス許可** | Azure Migrate プロジェクトを作成するためのアクセス許可、および Azure Migrate アプライアンスをデプロイするときに作成される Azure AD アプリを登録するためのアクセス許可が必要です。 | Azure サブスクリプションに対する共同作成者のアクセス許可が必要です。 
**レプリケーション** | vCenter Server から最大 300 台の VM を同時にレプリケートできます。<br/> 移行する VM が 50 を超える場合は、VM の複数のバッチを作成します。<br/> それを超える数を一度にレプリケートすると、パフォーマンスに影響を与えます。<br/><br/> ポータルでは、レプリケーションのために一度に最大 10 台のマシンを選択できます。 レプリケートするマシンの台数がそれを超える場合は、10 台のバッチ単位で追加してください。| レプリケーション アプライアンスをスケーリングすることで、レプリケーション容量が増加します。
**アプライアンスのデプロイ** | [Azure Migrate アプライアンス](migrate-appliance.md)がオンプレミスにデプロイされます。 | [Azure Migrate レプリケーション アプライアンス](migrate-replication-appliance.md)がオンプレミスにデプロイされます。
**Site Recovery の互換性** | 互換性あり。 | Site Recovery を使用してコンピューターのレプリケーションを設定している場合は、Azure Migrate Server Migration を使用してレプリケートすることはできません。
**ターゲット ディスク** | マネージド ディスク | マネージド ディスク
**ディスクの制限** | OS ディスク:2 TB<br/><br/> データ ディスク:8 TB<br/><br/> 最大ディスク数:60 | OS ディスク:2 TB<br/><br/> データ ディスク:8 TB<br/><br/> 最大ディスク数:63
**パススルー ディスク** | サポートされていません | サポートされています
**UEFI ブート** | サポートされていません | Azure 内の移行された VM は、自動的に BIOS ブート VM に変換されます。<br/><br/> OS ディスクには最大 4 つのパーティションが必要で、ボリュームは NTFS でフォーマットされている必要があります。

## <a name="compare-deployment-steps"></a>デプロイ手順を比較する

制限を確認した後、各ソリューションのデプロイに関連する手順を理解することが、どのオプションを選択するかを決定するために役立つ場合があります。

**タスク** | **詳細** |**エージェントレス** | **エージェント ベース**
--- | --- | --- | ---
**Azure Migrate アプライアンスをデプロイする** | VMware VM 上で実行される軽量のアプライアンス。<br/><br/> アプライアンスは、マシンを検出して評価し、エージェントレスの移行を使用してマシンを移行するために使用されます。 | 必須。<br/><br/> 既にアプライアンスを評価用に設定してある場合は、同じアプライアンスをエージェントレスの移行に使用できます。 | 不要。<br/><br/> アプライアンスを評価用に設定してある場合は、そのままにしておくことも、評価が済んだら削除することもできます。
**Server Assessment ツールを使用する** | Azure Migrate:Server Assessment ツールを使用してマシンを評価します。 | 移行前にマシンを評価できますが、必須ではありません。 | 評価は省略可能です | 評価はオプションです。
**Server Migration ツールを使用する** | Azure Migrate プロジェクトで Azure Migrate Server Migration ツールを追加します。 | 必須 | 必須
**VMware の移行を準備する** | VMware サーバーと VM で設定を構成します。 | 必須 | 必須
**VM 上にモビリティ サービスをインストールする** | Mobility Service は、レプリケートする各 VM 上で実行されます | 必要なし | 必須
**レプリケーション アプライアンスをデプロイする** | [レプリケーション アプライアンス](migrate-replication-appliance.md)は、エージェントベースの移行に使用されます。 VM 上で実行されている Mobility Service と Server Migration の間を接続します。 | 必要なし | 必須
**VM をレプリケートする**。 VM レプリケーションを有効にする。 | レプリケーション設定を構成し、レプリケートする VM を選択します。 | 必須 | 必須
**テスト移行を実行する** | すべてが想定どおりに動作していることを確認するためにテスト移行を実行します。 | 必須 | 必須
**完全な移行を実行する** | VM を移行します。 | 必須 | 必須



## <a name="next-steps"></a>次のステップ

エージェントレス移行で [VMware VM を移行する](tutorial-migrate-vmware.md)。



