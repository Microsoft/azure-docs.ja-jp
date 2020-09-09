---
title: Jupyter ノートブックの例
titleSuffix: Azure Machine Learning
description: サンプルの Jupyter Notebook を検索して使用し、Azure Machine Learning Python SDK を探索します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 10cf06a829b991bfe15d7b24c4fc61d0fe22eaaf
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653717"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Jupyter ノートブックを使用して Azure Machine Learning を探索する

[サンプルの Azure Machine Learning Notebooks のリポジトリ](https://github.com/azure/machinelearningnotebooks)には、最新の Azure Machine Learning Python SDK のサンプルが含まれています。 これらの Juypter Notebook は、SDK の探索を支援し、独自の機械学習プロジェクトのモデルとして機能するように設計されています。

この記事では、次の環境からこのリポジトリにアクセスする方法を示します。

- [Azure Machine Learning コンピューティング インスタンス](#notebookvm)
- [独自の Notebook サーバーを使用する](#byo)
- [Data Science Virtual Machine](#dsvm)

> [!NOTE]
> リポジトリを複製すると、**tutorials** フォルダー内にチュートリアル ノートブック、**how-to-use-azureml** フォルダー内に機能固有のノートブックがあることがわかります。

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Azure Machine Learning コンピューティング インスタンスでサンプルを取得する

サンプルを開始する最も簡単な方法は、「[チュートリアル:環境とワークスペースを設定する](tutorial-1st-experiment-sdk-setup.md)」を完了することです。 完了すると、SDK とサンプル リポジトリが事前に読み込まれた専用の Notebook サーバーが用意されます。 ダウンロードやインストールは必要ありません。

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>ノートブック サーバー上でサンプルを入手する

ローカル開発に独自の Notebook サーバーを使用するには、次の手順に従ってください。

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

これらの手順では、クイック スタートとチュートリアルのノートブックに必要な基本 SDK パッケージがインストールされます。 その他のサンプル ノートブックでは、追加のコンポーネントのインストールが必要になる場合があります。 詳細については、「[Install the Azure Machine Learning SDK for Python (Azure Machine Learning SDK for Python をインストールする)](https://docs.microsoft.com/python/api/overview/azure/ml/install)」を参照してください。

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>DSVM 上でサンプルを入手する

データ サイエンス仮想マシン (DSVM) とは、データ サイエンスの実行専用に構築されたカスタマイズ済み VM イメージです。 [DSVM を作成する](how-to-configure-environment.md#dsvm)と、SDK と Notebook サーバーがユーザーのためにインストールされて構成されます。 ただし、その場合も、ワークスペースの作成とサンプル リポジトリの複製は必要になります。

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>次のステップ

[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks)を探索して、Azure Machine Learning で実行できることを把握します。

他の GitHub サンプル プロジェクトと例については、次のサンプル リポジトリを参照してください。
+ [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
+ [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

以下のチュートリアルを試します:

- [MNIST を使用した画像分類モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)

- [NYC タクシー データ セットを使用して回帰モデルをトレーニングするためのデータの準備と自動化された機械学習](tutorial-auto-train-models.md)
