---
title: Recovery Services コンテナーの概要
description: Recovery Services コンテナーの概要と Azure Backup コンテナーとの比較。
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 0e1d061c6baf31fad2e937a604098f0baff6086d
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88041903"
---
# <a name="recovery-services-vaults-overview"></a>Recovery Services コンテナーの概要

この記事では、Recovery Services コンテナーの機能について説明します。 Recovery Services コンテナーは、データを格納する Azure のストレージ エンティティです。 データは通常、データのコピーであるか、仮想マシン (VM)、ワークロード、サーバー、ワークステーションのいずれかの構成情報です。 Recovery Services コンテナーを使用すると、IaaS VM (Linux または Windows) や Azure SQL データベースなどのさまざまな Azure サービスのバックアップ データを保持できます。 Recovery Services コンテナーは、System Center DPM、Windows Server、Azure Backup Server などをサポートします。 Recovery Services コンテナーでは、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。 Recovery Services コンテナーは、Azure の Azure Resource Manager モデルに基づいていて、以下のような機能を提供します。

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Recovery Services コンテナーと Backup コンテナーの比較

- **強化されたバックアップ データの保護機能**:Recovery Services コンテナーの場合、Azure Backup によってクラウド バックアップを保護するセキュリティ機能が提供されます。 このセキュリティ機能により、バックアップをセキュリティで保護することができ、運用サーバーとバックアップ サーバーが侵害された場合でもデータを安全に回復できます。 [詳細情報](backup-azure-security-feature.md)

- **ハイブリッド IT 環境の一元監視**:Recovery Services コンテナーの場合、[Azure IaaS VM](backup-azure-manage-vms.md) だけでなく、[オンプレミス資産](backup-azure-manage-windows-server.md#manage-backup-items)も中央ポータルで監視することができます。 [詳細情報](backup-azure-monitoring-built-in-monitor.md)

- **ロールベースのアクセス制御 (RBAC)** :RBAC を使用して、Azure のアクセス権を詳細に管理できます。 [Azure にはさまざまな組み込みのロールがあります](../role-based-access-control/built-in-roles.md)。また、Azure Backup には、[復旧ポイントを管理するための 3 つの組み込みのロールがあります](backup-rbac-rs-vault.md)。 Recovery Services コンテナーは、定義されたユーザー ロールのセットに対するバックアップと復元アクセスを制限する RBAC と互換性があります。 [詳細情報](backup-rbac-rs-vault.md)

- **論理的な削除**:論理的な削除を使用すると、悪意のあるアクターによってバックアップが削除 (またはバックアップ データが誤って削除) された場合でも、バックアップ データは追加で 14 日間保持されるので、データを失うことなくバックアップ項目を回復できます。 バックアップ データが "論理的な削除" 状態にあるこの追加の 14 日間のリテンション期間中は、お客様にコストは発生しません。 [詳細については、こちらを参照してください](backup-azure-security-feature-cloud.md)。

- **リージョンをまたがる復元**:リージョンをまたがる復元 (CRR) を使用すると、Azure VM をセカンダリ リージョン (Azure のペアになっているリージョン) に復元できます。 プライマリ リージョンで障害が発生したと Azure で宣言された場合、当該環境のプライマリ リージョンにおける実際のダウンタイム障害を軽減するため、セカンダリ リージョンでレプリケートされたデータを、セカンダリ リージョンでの復元のために使用できます。 [詳細については、こちらを参照してください](backup-azure-arm-restore-vms.md#cross-region-restore)。

## <a name="storage-settings-in-the-recovery-services-vault"></a>Recovery Services コンテナーのストレージ設定

Recovery Services コンテナーは、経時的に作成されたバックアップと復旧ポイントを格納するエンティティです。 Recovery Services コンテナーには、保護される仮想マシンに関連付けられたバックアップ ポリシーも含まれます。

- Azure Backup では、コンテナーのストレージが自動的に処理されます。 [ストレージ設定の変更](./backup-create-rs-vault.md#set-storage-redundancy)方法を確認してください。

- ストレージの冗長性の詳細については、[geo](../storage/common/storage-redundancy.md) と[ローカル](../storage/common/storage-redundancy.md)の冗長性に関する記事を参照してください。

### <a name="additional-resources"></a>その他のリソース

- [コンテナーでサポートされているシナリオとサポートされていないシナリオ](backup-support-matrix.md#vault-support)
- [コンテナーについてよく寄せられる質問](backup-azure-backup-faq.md)

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) は、Azure の使用を最適化するのに役立つ、カスタマイズされたクラウド コンサルタントです。 Azure の使用状況を分析し、デプロイを最適化してセキュリティで保護するためのタイムリーな推奨事項を提供します。 提供される推奨事項には、高可用性、セキュリティ、パフォーマンス、コストの 4 つのカテゴリがあります。

Azure Advisor では、バックアップされていない VM についての[推奨事項](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion)が 1 時間ごとに提供されるため、もう重要な VM のバックアップを忘れることはありません。 また、再通知を使用して推奨事項を制御することができます。  推奨事項を選択して、コンテナー (バックアップが格納される場所) とバックアップ ポリシー (バックアップのスケジュールとバックアップ コピーの保持期間) を指定することによって、インラインで VM のバックアップを有効にできます。

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>次のステップ

次の記事を参照してください。</br>
[IaaS VM のバックアップ](backup-azure-arm-vms-prepare.md)</br>
[Azure Backup Server のバックアップ](backup-azure-microsoft-azure-backup.md)</br>
[Windows Server のバックアップ](backup-windows-with-mars-agent.md)
