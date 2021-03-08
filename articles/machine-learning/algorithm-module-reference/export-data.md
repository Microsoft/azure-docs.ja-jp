---
title: 'データのエクスポート: モジュール リファレンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーのデータのエクスポート モジュールを使用して、Azure Machine Learning の外部に結果と中間データを保存します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/28/2020
ms.openlocfilehash: c6e3d56958168cd279c98a4ba4c021c2362c2694
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421313"
---
# <a name="export-data-module"></a>データのエクスポート モジュール

この記事では Azure Machine Learning デザイナーのモジュールについて説明します。

このモジュールを使用して、パイプラインからの結果、中間データ、および作業データをクラウド ストレージに保存します。 

このモジュールは、次のクラウド データ サービスにデータをエクスポートする作業を支援します。

- Azure BLOB コンテナー
- Azure ファイル共有
- Azure Data Lake Storage Gen1
- Azure Data Lake Storage Gen2
- Azure SQL データベース

データをエクスポートする前に、Azure Machine Learning ワークスペースにデータストアを登録する必要があります。 詳細については、「[Azure ストレージ サービスのデータにアクセスする](../how-to-access-data.md)」を参照してください。

## <a name="how-to-configure-export-data"></a>データのエクスポートを構成する方法

1. デザイナーで、**データのエクスポート** モジュールをパイプラインに追加します。 このモジュールは、 **[Input and Output]\(入力と出力\)** カテゴリにあります。

1. エクスポートしたいデータがあるモジュールに **[Export Data]\(データのエクスポート\)** を接続します。

1. **[データのエクスポート]** を選択して **[プロパティ]** ウィンドウを開きます。

1. **[データストア]** では、ドロップダウン リストから既存のデータストアを選択します。 新しいデータストアを作成することもできます。 方法については、「[Azure ストレージ サービスのデータにアクセスする](../how-to-access-data.md)」を参照してください。

    > [!NOTE]
    > 別のデータ型として指定された SQL データベース列に特定のデータ型のデータをエクスポートすることはできません。 ターゲット テーブルが最初から存在している必要はありません。

1. **[出力の再生成]** チェックボックスを使用して、実行時に出力を再生成するようにモジュールを実行するかどうかを決定します。 

    それは既定ではオフになっています。つまり、モジュールが前と同じパラメーターを使用して実行された場合は、実行時間を短縮するために、最後の実行からの出力が再利用されます。 

    オンにした場合は、モジュールが再び実行されて出力が再生成されます。

1. データが書き込まれるデータストア内のパスを定義します。 このパスは相対パスです。 空のパスまたは URL パスは許可されません。


1. **[ファイル形式]** で、データの保存形式を選択します。
 
1. パイプラインを送信します。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
