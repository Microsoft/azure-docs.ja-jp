---
title: バックアップ センターを使用して洞察を得る
description: バックアップ センターを使用して、履歴の傾向を分析し、バックアップに関する詳細な洞察を得る方法について説明します。
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c5f707ce3bc8d1172805ac14958a50bf59d1412d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893695"
---
# <a name="obtain-insights-using-backup-center-preview"></a>バックアップ センター (プレビュー) を使用して分析情報を得る

履歴の傾向を分析し、バックアップに関する詳細な分析情報を得るために、バックアップ センターには、[Azure Monitor ログ](../azure-monitor/platform/data-platform-logs.md)と [Azure ブック](../azure-monitor/platform/workbooks-overview.md)を使用する[バックアップ レポート (プレビュー)](configure-reports.md)へのインターフェイスが用意されています。 バックアップ レポートには次の機能が用意されています。

- 使用されるクラウド ストレージの割り当てと予測。

- バックアップおよび復元の監査。

- さまざまな細分性レベルで主要な傾向を特定する。

- バックアップのコスト最適化の機会を把握し、洞察を得る。

## <a name="supported-scenarios"></a>サポートされるシナリオ

- 現在、バックアップ レポート (プレビュー) は Azure Database for PostgreSQL サーバーのバックアップには使用できません。

- サポートされるシナリオとサポートされないシナリオの詳細な一覧については、[サポート マトリックス](backup-center-support-matrix.md)を参照してください。

## <a name="get-started"></a>作業開始

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>コンテナーを構成して Log Analytics ワークスペースにデータを送信する

[コンテナーに対して大規模な診断設定を構成する方法についてはこちらをご覧ください](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>バックアップ センター ポータルでバックアップ レポートを表示する

バックアップ センターの **[バックアップ レポート]** メニュー項目を選択すると、レポートが開きます。 バックアップの重要な情報を表示して分析するには、1 つまたは複数の Log Analytics ワークスペースを選択します。

![バックアップ センターのバックアップ レポート](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

使用できるビューは次のとおりです。

1. **概要** - このタブを使用して、バックアップ資産の概要を大まかに把握します。 [詳細情報](./configure-reports.md#summary)

1. **バックアップ項目** - このタブを使用して、バックアップ項目レベルで使用されたクラウド ストレージの情報および傾向を確認します。 [詳細情報](./configure-reports.md#backup-items)

1. **使用** - このタブを使用して、バックアップの主要な請求先パラメーターを表示します。 [詳細情報](./configure-reports.md#usage)

1. **ジョブ** - このタブを使用して、1 日あたりの失敗したジョブの数やジョブの失敗の主な原因など、ジョブの長期傾向を表示します。 [詳細情報](./configure-reports.md#jobs)

1. **ポリシー** - このタブを使用して、関連する項目の数や、特定のポリシーでバックアップされた項目によって使用されたクラウド ストレージの合計など、アクティブなすべてのポリシーに関する情報を表示します。 [詳細情報](./configure-reports.md#policies)

1. **最適化** - このタブを使用すると、バックアップに関するコストを最適化できる潜在的な機会を把握できます。 [詳細情報](./configure-reports.md#optimize)

## <a name="next-steps"></a>次のステップ

- [バックアップを監視および操作する](backup-center-monitor-operate.md)
- [バックアップ資産を管理する](backup-center-govern-environment.md)
- [バックアップ センターを使用してアクションを実行する](backup-center-actions.md)