---
title: デザイナーのログ メトリック (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure ML デザイナーの実験を監視します。 Execute Python Script モジュールを使用してログ記録を有効にし、ログに記録された結果をスタジオに表示します。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 20845a6f1238095b40c9b05b5f5d8d85217b6db5
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950410"
---
# <a name="enable-logging-in-azure-machine-learning-designer-preview-pipelines"></a>Azure Machine Learning デザイナー (プレビュー) パイプラインでログ記録を有効にする
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、デザイナー パイプラインにログ コードを追加する方法について説明します。 また、Azure Machine Learning Studio の Web ポータルを使用してこれらのログを表示する方法についても説明します。

SDK 作成エクスペリエンスを使用したメトリックのログ記録の詳細については、[Azure ML の実験の実行とメトリックの監視](how-to-track-experiments.md)に関する記事を参照してください。

## <a name="enable-logging-with-execute-python-script"></a>Execute Python Script を使用してログ記録を有効にする

__Execute Python Script__ モジュールを使用して、デザイナー パイプラインでログ記録を有効にします。 このワークフローを使用して任意の値をログに記録することができますが、__Evaluate Model__ モジュールからメトリックをログに記録し、複数の実行にわたってモデルのパフォーマンスを追跡する場合に特に便利です。

次の例では、Evaluate Model モジュールと Execute Python Script モジュールを使用して、トレーニングされた 2 つのモデルの平均二乗誤差をログに記録する方法を示します。

1. __Execute Python Script__ モジュールを __Evaluate Model__ モジュールの出力に接続します。

    ![Python スクリプトの実行モジュールをモデルの評価モジュールに接続する](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. __Execute Python Script__ のコード エディターに次のコードを貼り付けて、トレーニングされたモデルの平均絶対誤差をログに記録します。 同様のパターンを使用して、デザイナーで他の値をログに記録できます。

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.

        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        run.parent.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])

        # Log right output port result of Evaluate Model.
        run.parent.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])
    
        return dataframe1,
    ```
    
このコードでは、Azure Machine Learning Python SDK を使用して値をログに記録します。 これは Run.get_context() を使用して、現在の実行のコンテキストを取得します。 次に、run.parent.log() メソッドを使用して、そのコンテキストに値を記録します。 `parent` を使用して、モジュールの実行ではなく、親パイプラインの実行に値を記録します。

Python SDK を使用して値をログに記録する方法の詳細については、「[Azure ML のトレーニングの実行でログ記録を有効にする](how-to-track-experiments.md)」を参照してください。

## <a name="view-logs"></a>ログを表示する

パイプラインの実行が完了すると、実験ページに *Mean_Absolute_Error* が表示されます。

1. **[実験]** セクションに移動します。
1. 実験を選択します。
1. 表示する実験で実行を選択します。
1. **[メトリック]** を選びます。

    ![Studio で実行メトリックを表示する](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>次のステップ

この記事では、デザイナーでログを使用する方法について説明しました。 次の手順については、次の関連記事を参照してください。

* デザイナー パイプラインのトラブルシューティング方法については、[ML パイプラインのデバッグとトラブルシューティング](how-to-debug-pipelines.md#logging-in-azure-machine-learning-designer-preview)に関する記事を参照してください。
* Python SDK を使用して SDK 作成エクスペリエンスのメトリックをログに記録する方法については、「[Azure ML のトレーニングの実行でログ記録を有効にする](how-to-track-experiments.md)」を参照してください。
