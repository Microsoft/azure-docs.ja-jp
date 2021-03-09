---
title: Azure Stream Analytics の入力について
description: この記事では、Azure Stream Analytics ジョブでの入力の概念について説明し、参照データ入力へのストリーミングの入力と比較します。
ms.service: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 442c5a1174c4a91ea9401315bb3e518e4fe6cc4e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183915"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Azure Stream Analytics の入力について

Azure Stream Analytics ジョブは、1 つまたは複数のデータ入力に接続します。 各入力は、既存のデータ ソースへの接続を定義しています。 Stream Analytics が受け取るデータは、Event Hubs、IoT Hub、Blob ストレージなど、各種のイベント ソースから着信します。 各ジョブのために記述するストリーミング SQL クエリ内では、入力は名前によって参照されます。 クエリでは、複数の入力を結合してデータをブレンドするか、参照データへの参照を使用してストリーミング データを比較し、結果を出力に渡すことができます。 

Stream Analytics には、入力として次の 4 種類のリソースを使用する優れた統合機能が組み込まれています。
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure BLOB Storage](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 

これらの入力ソースは、Stream Analytics ジョブと同じ Azure サブスクリプションに存在していても、異なるサブスクリプションに存在していてもかまいません。

Stream Analytics ジョブへの入力の作成、編集、テストは、[Azure portal](stream-analytics-quick-create-portal.md#configure-job-input)、[Azure PowerShell](/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput)、[.NET API](/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions)、[REST API](/rest/api/streamanalytics/2016-03-01/inputs)、[Visual Studio](stream-analytics-tools-for-visual-studio-install.md) を使用して行えます。

## <a name="stream-and-reference-inputs"></a>ストリームと参照入力
データがそのデータ ソースにプッシュされると、Stream Analytics ジョブによって使用され、リアルタイムで処理されます。 入力は 2 つのタイプであるデータ ストリーム入力と参照データ入力に分けられます。

### <a name="data-stream-input"></a>データ ストリーム入力
データ ストリームは、時間をかけて受け取る、制約のない一連のイベントです。 Stream Analytics のジョブには、少なくとも 1 つのデータ ストリーム入力を含める必要があります。 Event Hubs、IoT Hub、Azure Data Lake Storage Gen2、BLOB ストレージがデータ ストリームの入力ソースとしてサポートされています。 Event Hubs は、複数のデバイスとサービスからイベント ストリームを収集するために使用されます。 これらのストリームには、ソーシャル メディア アクティビティ フィード、株式取引情報、またはセンサーからのデータが含まれている場合があります。 IoT Hub はモノのインターネット化 (IoT) シナリオ内で、接続されているデバイスからデータを収集するように最適化されています。  ストリームとしてバルク データ (ログ ファイルなど) を取り込むための入力ソースとして、Blob Storage を使用できます。  

ストリーミングによるデータ入力の詳細については、「[Stream Analytics に入力としてデータをストリーム配信する](stream-analytics-define-inputs.md)」を参照してください

### <a name="reference-data-input"></a>参照データ入力
Stream Analytics は、"*参照データ*" と呼ばれる入力もサポートします。 参照データは、完全に静的であるか、ゆっくりと変化しているかのいずれかです。 これは通常、相関関係の関連付けと参照を実行するために使用されます。 たとえば、SQL の結合を実行して静的な値を参照する場合と同様に、データ ストリーム入力のデータを参照データのデータに結合できます。 Azure Blob Storage、Azure Data Lake Storage Gen2、Azure SQL Database が現在、参照データの入力ソースとしてサポートされています。 クエリの複雑さと割り当てられているストリーミング ユニットに応じて、参照データのソース BLOB には最大 300 MB というサイズ制限があります (詳細については、参照データのドキュメントの「[サイズ制限](stream-analytics-use-reference-data.md#size-limitation)」セクションをご覧ください)。

参照データ入力に関する詳細については、「[Stream Analytics での参照に参照データを使用する](stream-analytics-use-reference-data.md)」を参照してください

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
