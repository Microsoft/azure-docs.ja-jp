---
title: Azure IaaS VM 用のディスクの種類の選択 - マネージド ディスク
description: Ultra Disks、Premium SSD、Standard SSD、Standard HDD など、仮想マシンで使用できる Azure ディスクの種類について説明します。
author: roygara
ms.author: rogarana
ms.date: 06/03/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 53089fa42c536cbdc59865f80f63a77c76720e2c
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752016"
---
# <a name="what-disk-types-are-available-in-azure"></a>Azure で利用できるディスクの種類

現在、Azure マネージド ディスクには 4 種類のディスクがあり、それぞれの種類は特定の顧客シナリオを対象としています。

## <a name="disk-comparison"></a>ディスクの比較

次の表では、使用するものを決定するときの参考に、マネージド ディスクの Ultra Disk、Premium ソリッド ステート ドライブ (SSD)、Standard SSD、Standard HDD (ハード ディスク ドライブ) を比較します。

| Detail | Ultra ディスク | Premium SSD | Standard SSD | Standard HDD |
| ------ | ---------- | ----------- | ------------ | ------------ |
|ディスクの種類   |SSD   |SSD   |SSD   |HDD   |
|シナリオ   |[SAP HANA](workloads/sap/hana-vm-operations-storage.md) やトップ レベルのデータベース (たとえば SQL や Oracle) などの I/O 集約型のワークロードと、その他のトランザクションが多いワークロード。   |運用環境のワークロードやパフォーマンスに影響されやすいワークロード   |Web サーバー、あまり使用されていないエンタープライズ アプリケーション、および開発/テスト   |バックアップ、重要ではない、不定期に起こるアクセス   |
|最大ディスク サイズ   |65,536 ギビバイト (GiB)    |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|最大スループット   |2,000 MB/秒    |900 MB/秒   |750 MB/秒   |500 MB/秒   |
|最大 IOPS   |160,000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>Ultra Disk

Azure Ultra Disk では、Azure IaaS VM 用に高スループット、高 IOPS、一貫性のある低待機時間のディスク ストレージが提供されます。 Ultra Disk のその他のメリットとして、仮想マシン (VM) を再起動する必要なしに、ワークロードに合わせてディスクのパフォーマンスを動的に変更できます。 Ultra ディスクは、SAP HANA、最上位層のデータベース、トランザクションの多いワークロードなどのデータ集中型のワークロードに適しています。 Ultra Disk は、データ ディスクとしてのみ使用できます。 OS ディスクとしては Premium SSD を使用することをお勧めします。

### <a name="performance"></a>パフォーマンス

Ultra ディスクをプロビジョニングするときは、ディスクの容量とパフォーマンスを個別に構成することができます。 Ultra Disk は、4 GiB から最大 64 TiB までの複数の固定されたサイズで提供されます。IOPS とスループットを個別に構成できる柔軟なパフォーマンス構成モデルであることが特徴です。

Ultra Disk の主な機能は次のとおりです。

- ディスク容量:Ultra Disk の容量の範囲は 4 GiB から最大 64 TiB です。
- ディスク IOPS: Ultra Disk は、300 IOPS/GiB の IOPS 制限をサポートし、ディスクあたり最大 160 K の IOPS をサポートします。 プロビジョニングした IOPS を達成するには、選択したディスクの IOPS が VM の IOPS 制限未満であることを確認してください。 ディスクあたりに保証されている最小 IOPS は 2 IOPS/GiB で、全体のベースラインの最小値は 100 IOPS です。 たとえば、4 GiB の Ultra Disk が 1 つある場合、最小値は 8 IOPS ではなく 100 IOPS になります。
- ディスク スループット: Ultra Disk では、1 つのディスクのスループットは、プロビジョニングされた IOPS ごとに 256 KiB/秒に制限され、ディスクあたり最大 2000 MBps に制限されます (MBps = 秒あたり 10^6 バイト)。 各ディスクで保証されている最小スループットは、プロビジョニングされた IOPS ごとに 4 KiB/秒で、全体のベースラインの最小値は 1 MBps です。
- Ultra Disk では、実行時に、ディスクをデタッチすることなく、仮想マシンから、ディスク パフォーマンス属性 (IOPS とスループット) を調整できます。 ディスクでディスク パフォーマンス サイズ変更操作を実行した場合、変更が実際に有効になるまでに最大で 1 時間かかることがあります。 パフォーマンス サイズ変更操作は、24 時間で 4 回に制限されています。 パフォーマンス帯域幅の容量が不足しているために、パフォーマンス サイズ変更操作が失敗する可能性があります。

### <a name="disk-size"></a>ディスク サイズ

|ディスク サイズ (GiB)  |IOPS の上限  |スループットの上限 (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1,024 - 65,536 (この範囲内のサイズは 1 TiB ずつ増えます)     |160,000         |2,000         |

### <a name="ga-scope-and-limitations"></a>GA の範囲と制限事項

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]


Ultra ディスクの使用を開始したい場合は、この件に関する記事「[Azure Ultra ディスクの使用](disks-enable-ultra-ssd.md)」を参照してください。

## <a name="premium-ssd"></a>Premium SSD

Azure Premium SSD では、IO (入出力) を集中的に行うワークロードが存在する仮想マシン (VM) 向けに、高パフォーマンスで待ち時間の少ないディスク サポートが提供されます。 Premium Storage ディスクの速度とパフォーマンスを活用するために、既存の VM ディスクを Premium SSD に移行することができます。 Premium SSD は、ミッション クリティカルな運用アプリケーションに適しています。 Premium SSD は、Premium Storage に互換性のある VM シリーズでのみ使用できます。

Premium Storage に互換性のあるサイズなど、Windows または Linux 用の Azure 内の個別の VM の種類とサイズについて詳しくは、「[Azure の仮想マシンのサイズ](sizes.md)」を参照してください。 Premium Storage に互換性のあるサイズなど、Linux 用の Azure 内の個別の VM の種類とサイズについて詳しくは、「[Azure の仮想マシンのサイズ](sizes.md)」を参照してください。 これらのいずれかの記事から、個々の VM サイズの記事を確認し、Premium ストレージと互換性があるかどうかを判断する必要があります。

### <a name="disk-size"></a>ディスク サイズ
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Premium Storage ディスクをプロビジョニングすると、Standard Storage とは異なり、対象のディスクの容量、IOPS、スループットが保証されます。 たとえば、P50 ディスクを作成した場合、対象のディスクに 4,095 GB のストレージ容量、7,500 IOPS、および 250 MB/秒のスループットがプロビジョニングされます。 アプリケーションでは、容量とパフォーマンスのすべてまたは一部を使用できます。 Premium SSD ディスクは、1 桁のミリ秒の低遅延と、前出の表に示した目標 IOPS とスループットを 99.9% の時間で提供するように設計されています。

## <a name="bursting"></a>バースト

P30 より小さいサイズの Premium SSD では、ディスクのバーストを提供しており、ディスクあたり IOPS を最高 3500 まで、帯域幅を最高 170 Mbps までバーストできます。 バーストは自動化され、クレジット システムに基づいて動作します。 ディスク トラフィックがプロビジョニングされたパフォーマンス ターゲットを下回る場合、クレジットはバースト バケットに自動的に蓄積されます。また、トラフィック バーストがターゲットを超えた場合、最大バースト制限まで自動的にクレジットが消費されます。 最大バースト制限は、消費されるバースト クレジットがある場合でも、ディスク IOPS および帯域幅の上限を定義します。 ディスク バースティングは、IO パターンの予測できない変更に対して優れた耐性を示します。 これは、OS ディスクの起動や激しいトラフィックを含むアプリケーションにおいて、最大限に活用できます。    

既定では、ディスク バースティング サポートは適用可能なディスク サイズを新しくデプロイする際に有効になります。ユーザーの操作は必要ありません。 既存の適用可能なサイズのディスクの場合、ディスクのデタッチと再アタッチまたは接続済み VM の停止と再起動という、2 つのオプションのいずれかを使用してバースティングを有効にすることができます。 バーストが適用可能なすべてのディスク サイズは、ディスクが 30 分のピーク バースト制限での最大期間をサポートする仮想マシンに接続されている場合、最大のバースト クレジット バケットを使用して起動されます。 Azure ディスクでのバースティングの動作の詳細については、[Premium SSD バースティング](linux/disk-bursting.md)に関する記事を参照してください。 

### <a name="transactions"></a>トランザクション

Premium SSD では、スループットが 256 KiB 以下の I/O 操作は、それぞれ単一の I/O 操作とみなされます。 スループットが 256 KiB を超える I/O 操作は、サイズが 256 KiB の複数の I/O とみなされます。

## <a name="standard-ssd"></a>Standard SSD

Azure Standard SSD は、比較的低い IOPS レベルで一貫したパフォーマンスを必要とするワークロード向けに最適化された、コスト効果に優れたストレージ オプションです。 Standard SSD は、クラウドへの移行を希望している方、特にオンプレミスの HDD ソリューションで実行されているワークロードのばらつきによって問題が発生している場合に、エントリ レベルの優れたエクスペリエンスを提供します。 Standard HDD に比べて、Standard SSD は、可用性、一貫性、信頼性、遅延が優れています。 Standard SSD は、Web サーバー、低い IOPS のアプリケーション サーバー、使用の少ないエンタープライズ アプリケーション、開発/テストのワークロードに適しています。 Standard HDD のように、Standard SSD はすべての Azure VM で使用できます。

### <a name="disk-size"></a>ディスク サイズ
[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

Standard SSD は、1 桁のミリ秒の遅延と、前出の表に示されている上限までの IOPS とスループットを 99% の時間で提供するように設計されています。 実際の IOPS とスループットは、トラフィック パターンによって異なる場合があります。 Standard SSD は、HDD ディスクよりも一貫したパフォーマンスを提供し、遅延が少なくなります。

### <a name="transactions"></a>トランザクション

Standard SSD では、スループットが 256 KiB 以下の I/O 操作は、それぞれ単一の I/O 操作とみなされます。 スループットが 256 KiB を超える I/O 操作は、サイズが 256 KiB の複数の I/O とみなされます。 これらのトランザクションは、課金への影響があります。

## <a name="standard-hdd"></a>Standard HDD

Azure Standard HDD では、遅延の影響を受けないワークロードを実行する VM 向けの信頼性の高い低コストのディスク サポートが提供されます。 Standard Storage では、データはハード ディスク ドライブ (HDD) に格納されます。 Standard HDD ディスクの遅延、IOPS、およびスループットは、SSD ベースのディスクと比較してより大きく異なる可能性があります。 Standard HDD ディスクは、ほとんどの IO 操作で 10 ミリ秒未満の書き込み待機時間と 20 ミリ秒未満の読み取り待機時間を実現するように設計されていますが、実際のパフォーマンスは IO サイズやワークロードのパターンによって異なる可能性があります。 VM を使用するとき、開発/テスト シナリオや重要度の低いワークロードには Standard HDD ディスクを使用できます。 Standard HDD はすべての Azure リージョンで使用可能であり、すべての Azure VM で使用できます。

### <a name="disk-size"></a>ディスク サイズ
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>トランザクション

Standard HDD では、各 IO 操作は、I/O サイズに関係なく、単一のトランザクションとみなされます。 これらのトランザクションは、課金への影響があります。

## <a name="billing"></a>課金

マネージド ディスクを使用するときには、課金に関する次の考慮事項が適用されます。

- ディスクの種類
- マネージド ディスクのサイズ
- スナップショット
- 送信データ転送
- トランザクション数

**マネージド ディスクのサイズ**: マネージド ディスクは、プロビジョニング済みサイズに対して課金されます。 Azure では、プロビジョニング済みサイズ (切り上げたもの) は、提供される最も近いディスク サイズにマップされます。 提供されるディスク サイズの詳細については、前の表を参照してください。 各ディスクは、サポートされているプロビジョニング済みディスク サイズのオファリングにマップされ、それに応じて課金されます。 たとえば、200 GiB の Standard SSD をプロビジョニングした場合、E15 (256 GiB) のディスク サイズのプランにマップされます。 プロビジョニングされたディスクには、ストレージ サービスの月額料金を使用して、時間割りで計算して課金されます。 たとえば、E10 ディスクをプロビジョニングし、20 時間後にそのディスクを削除した場合は、E10 製品の 20 時間分に対して課金されます。 ディスクに書き込まれた実際のデータ量には関係ありません。

**スナップショット**: スナップショットは、使用されているサイズに基づいて請求されます。 たとえば、64 GiB のプロビジョニング済み容量でマネージド ディスクのスナップショットを作成し、実際に使用されたデータ サイズが 10 GiB である場合、スナップショットは使用されたデータ サイズである 10 GiB 分のみ課金されます。

スナップショットの詳細については、[マネージド ディスクの概要](managed-disks-overview.md)に関するページのスナップショットのセクションを参照してください。

**送信データ転送**: [送信データ転送](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure データ センターから送信されるデータ) では、帯域幅の使用量に対して課金されます。

**トランザクション**:Standard マネージド ディスクで実行されるトランザクションの数に対して課金されます。 Standard SSD では、スループットが 256 KiB 以下の I/O 操作は、それぞれ単一の I/O 操作とみなされます。 スループットが 256 KiB を超える I/O 操作は、サイズが 256 KiB の複数の I/O とみなされます。 Standard HDD では、各 IO 操作は、I/O サイズに関係なく、単一のトランザクションとみなされます。

トランザクション コストを含め、Managed Disks の価格の詳細については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks)」をご覧ください。

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra Disk VM 予約料金

Azure VM には、Ultra Disk と互換性があるかどうかを示す機能があります。 Ultra ディスク互換 VM では、コンピューティング VM インスタンスとブロック ストレージ スケール ユニット間に専用の帯域幅キャパシティが割り当てられてパフォーマンスが最適化され、待機時間が減少します。 このキャパシティを VM に追加すると、Ultra ディスクがアタッチされていない VM 上で Ultra ディスク キャパシティを有効にした場合にのみ、予約料金がかかります。 Ultra ディスク互換の VM に Ultra ディスクがアタッチされている場合、この料金は適用されません。 この料金は、VM 上にプロビジョニングされている vCPU ごとにかかります。 

> [!Note]
> [制約付きコア VM サイズ](constrained-vcpu.md)の場合、予約料金は、制限付きコアではなく実際の vCPU 数に基づいています。 Standard_E32-8s_v3 の場合、予約料金は 32 コアに基づきます。 

Ultra Disk の価格について詳しくは、[Azure ディスクの価格](https://azure.microsoft.com/pricing/details/managed-disks/)に関するページをご覧ください。

### <a name="azure-disk-reservation"></a>Azure ディスク予約

ディスク予約は、1 年分のディスク ストレージを割引価格で事前に購入して、総コストを削減するオプションです。 ディスク予約を購入する場合は、ターゲット リージョンの特定のディスク SKU を選択します。たとえば、1 年間の米国東部 2 リージョンの 10 P30 (1TiB) プレミアム SSD を選択します。 予約エクスペリエンスは、予約済みの仮想マシン (VM) インスタンスに似ています。 VM 予約とディスク予約をバンドルすると、最大限に節約できます。 現時点で、Azure ディスク予約には、すべての運用リージョンで P30 (1 TiB) から P80 (32 TiB) までの Premium SSD SKU の 1 年間のコミットメント プランが用意されています。 予約済みディスクの価格の詳細については、[Azure ディスクの価格のページ](https://azure.microsoft.com/pricing/details/managed-disks/)を参照してください。

## <a name="next-steps"></a>次のステップ

作業を開始するには、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」を参照してください。
