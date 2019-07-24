---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: da796f8eeb6b24dfbbe8418cc728f09b424228cf
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528494"
---
### <a name="premium-performance-block-blob-storage"></a>Premium パフォーマンス ブロック BLOB ストレージ

Premium パフォーマンス ブロック BLOB ストレージ アカウントは、キロバイト単位の小さなオブジェクトを使用するアプリケーションのために最適化されています。 高いトランザクション率、または、一貫して待ち時間の短いストレージが必要とされるアプリケーションに最適です。 Premium パフォーマンス ブロック BLOB ストレージは、アプリケーションに合わせて規模を変えられるように設計されています。 秒単位で非常に大量の要求が必要とされる、あるいはペタバイト単位のストレージ容量が必要とされるアプリケーションをデプロイする場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) でサポート リクエストを送信し、Microsoft にお問い合わせください。

### <a name="premium-performance-filestorage-preview"></a>Premium パフォーマンス FileStorage (プレビュー)

Premium ファイルは、**FileStorage (プレビュー)** と呼ばれる一意のストレージ アカウントを使用します。 このアカウントの種類は、高 IOPS、高スループットで、一貫して待機時間の短い のワークロード向けに設計されています。 Premium ファイル ストレージは、プロビジョニングされた共有サイズに合わせて拡大縮小されます。

|領域  |ターゲット  |
|---------|---------|
|プロビジョニングされた最大サイズ     |5 TiB (パブリック プレビュー)、100 TiB (限定パブリック プレビュー)     |
|共有   |無制限  |
|IOPS     |100,000 (限定パブリック プレビュー)    |
|イングレス|4,136 MiB/秒     |
|エグレス|6,204 MiB/秒 |

 Premium ファイル共有のスケール ターゲットについては、[Premium ファイルのスケール ターゲット](../articles/storage/common/storage-scalability-targets.md#premium-files-scale-targets)に関するセクションを参照してください。

### <a name="premium-performance-page-blob-storage"></a>Premium パフォーマンス ページ BLOB ストレージ

Premium パフォーマンスの汎用 v1 ストレージ アカウントまたは v2 ストレージ アカウントには、次のスケーラビリティ ターゲットがあります。

| 合計アカウント容量                            | ローカル冗長ストレージ アカウントの合計帯域幅                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| ディスク容量:35 TB <br>スナップショット容量:10 TB | 受信<sup>1</sup> と送信<sup>2</sup> を合わせて最大 50 GB/秒 |

<sup>1</sup> ストレージ アカウントに送信されるすべてのデータ (要求)

<sup>2</sup> ストレージ アカウントから受信されるすべてのデータ (応答)

非管理対象ディスクに Premium パフォーマンス ストレージ アカウントを使用しており、アプリケーションが 1 つのストレージ アカウントのスケーラビリティ ターゲットを超えた場合、マネージド ディスクへの移行が必要になることがあります。 管理ディスクに移行しない場合は、複数のストレージ アカウントを使用するようにアプリケーションを作成します。 その後、それらのストレージ アカウント間でデータをパーティション分割します。 たとえば、複数の VM で合計 51 TB のディスクを接続する場合、ディスクを 2 つのストレージ アカウントに分散します。 Premium Storage アカウント 1 つあたりの上限は 35 TB です。 1 つの Premium パフォーマンス ストレージ アカウントでプロビジョニングするディスクの容量が 35 TB を超えることがないようにしてください。