---
title: マッピング データ フローのウィンドウ変換
description: Azure Data Factory マッピング データ フローのウィンドウ変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 1f440a5877dcefeca8c1baa82fad4299b5ce7bce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606222"
---
# <a name="azure-data-factory-window-transformation"></a>Azure Data Factory のウィンドウ変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ウィンドウ変換では、データ ストリームに含まれる列のウィンドウ ベースの集計を定義します。 式ビルダーで、データまたは時間ウィンドウに基づくさまざまな種類の集計 (SQL OVER 句の LEAD、LAG、NTILE、CUMEDIST、RANK など) を定義できます。 これらの集計を含む新しいフィールドが出力に生成されます。 省略可能な group-by フィールドを含めることもできます。

![ウィンドウ オプション](media/data-flow/windows1.png "ウィンドウ 1")

## <a name="over"></a>Over
ウィンドウ変換のために列データのパーティション分割を設定します。 SQL では Over 句の ```Partition By``` がこれに相当します。 パーティション分割に使用する計算または式を作成する場合は、列名にポインターを合わせて、[計算列] を選択します。

![ウィンドウ オプション](media/data-flow/windows4.png "ウィンドウ 4")

## <a name="sort"></a>並べ替え
Over 句の別の部分では、```Order By``` を設定します。 これにより、データの並べ替え順序が設定されます。 この列フィールドの計算値を並べ替えるための式を作成することもできます。

![ウィンドウ オプション](media/data-flow/windows5.png "ウィンドウ 5")

## <a name="range-by"></a>Range By
次に、ウィンドウ枠を無制限または制限ありに設定します。 無制限のウィンドウ枠を設定するには、スライダーを両端の [Unbounded]\(無制限\) に設定します。 [Unbounded]\(無制限\) と [現在の行] の間の設定にする場合は、オフセットの開始値と終了値を設定する必要があります。 どちらの値も正の整数になります。 相対値か、自分のデータの値を使用できます。

ウィンドウのスライダーでは 2 つの値を設定します。現在の行よりも前の値と、現在の行より後の値です。 開始と終了のオフセットは、スライダーの 2 つのセレクターと一致します。

![ウィンドウ オプション](media/data-flow/windows6.png "ウィンドウ 6")

## <a name="window-columns"></a>ウィンドウの列
最後に、式ビルダーを使って、RANK、COUNT、MIN、MAX、DENSE RANK、LEAD、LAG など、データ ウィンドウで使用する集計を定義します。

![ウィンドウ オプション](media/data-flow/windows7.png "ウィンドウ 7")

式ビルダーを通じて ADF Data Flow 記述言語で使用できる集計関数と分析関数の全一覧については、 https://aka.ms/dataflowexpressions をご覧ください。

## <a name="next-steps"></a>次のステップ

単純なグループ別集計が必要な場合は、[集計変換](data-flow-aggregate.md)を使用してください。
