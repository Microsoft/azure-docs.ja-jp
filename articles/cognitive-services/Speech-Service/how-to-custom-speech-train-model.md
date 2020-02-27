---
title: Custom Speech 用のモデルをトレーニングする - 音声サービス
titleSuffix: Azure Cognitive Services
description: 音声テキスト変換モデルをトレーニングすると、Microsoft のベースライン モデルまたはカスタム モデルの認識精度を向上できます。 モデルは、"人間" とラベルが付いた文字起こしと関連するテキストを使用してトレーニングされます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: a2bc39a35299f56ba52a0143ce123560bd4d88fa
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137768"
---
# <a name="train-a-model-for-custom-speech"></a>Custom Speech 用のモデルをトレーニングする

音声テキスト変換モデルをトレーニングすると、Microsoft のベースライン モデルの認識精度を向上できます。 モデルは、"人間" とラベルが付いた文字起こしと関連するテキストを使用してトレーニングされます。 これらのデータセットと以前にアップロードされたオーディオ データは、音声変換モデルを改良してトレーニングし、単語、語句、略語、名前、およびその他の製品固有の用語を認識するために使用されます。 提供するドメイン内データセット (ユーザーの発言内容と認識すると想定する内容に関連するデータ) が多いほど、モデルの精度が高くなり、結果として認識が改善されます。 無関係なデータをトレーニングに使用すると、モデルの正確性が低下したり、損なわれたり可能性があることに注意してください。

## <a name="use-training-to-resolve-accuracy-issues"></a>トレーニングを使用して正確性の問題を解決する

モデルで認識の問題が発生している場合は、追加のトレーニングに "人間" とラベルが付いた文字起こしと関連データを使用すると、正確性が向上します。 この表を使用して、問題の対処に使用するデータセットを決定します。

| 使用事例 | データ型 |
| -------- | --------- |
| 医療用語や IT 用語などの業界固有の語彙や文法に対する認識精度を向上させる。 | 関連テキスト (文/発話) |
| 製品名や頭字語など、発音が標準ではない単語または用語の表音および表示形式を定義する。 | 関連テキスト (発音) |
| 読み上げのスタイル、アクセント、または特定の背景ノイズの認識精度を向上させる。 | "オーディオ + 人間" とラベルが付いたトランスクリプト |

> [!IMPORTANT]
> データ セットをアップロードしていない場合は、[データの準備とテスト](how-to-custom-speech-test-data.md)に関する記事を参照してください。 このドキュメントでは、データをアップロードするための手順と、高品質のデータセットを作成するためのガイドラインについて説明します。

## <a name="train-and-evaluate-a-model"></a>モデルのトレーニングと評価

モデルをトレーニングする最初の手順は、トレーニング データのアップロードです。 "人間" とラベルが付いた文字起こしと関連テキスト (発話と発音) を準備する手順については、[データの準備とテスト](how-to-custom-speech-test-data.md)に関する記事を参照してください。 トレーニング データをアップロードしたら、次の手順に従ってモデルのトレーニングを開始します。

1. [Custom Speech ポータル](https://speech.microsoft.com/customspeech)にサインインします。
2. **[音声変換]、[Custom Speech]、[トレーニング]** の順に移動します。
3. **[Train model]\(モデルのトレーニング\)** をクリックします。
4. 次に、トレーニングの **[名前]** と **[説明]** を指定します。
5. **[Scenario and Baseline model]\(シナリオとベースライン モデル\)** ドロップダウン メニューから、ドメインに最適なシナリオを選択します。 どのシナリオを選択すればよいかわからない場合は、 **[一般]** を選択します。 このベースライン モデルがトレーニングの開始点です。 好みがない場合は、最新のものを使用できます。
6. **[Select training data]\(トレーニング データの選択\)** ページから、トレーニングに使用する 1 つまたは複数の "オーディオ + 人間" とラベルが付いた文字起こしデータセットを選択します。
7. トレーニングが完了したら、新しくトレーニングしたモデルに対して正確性テストを実行することを選択できます。 この手順は省略可能です。
8. **[作成]** を選択してカスタム モデルを作成します。

[トレーニング] の表に、この新しく作成されたモデルに対応する新しいエントリが表示されます。 この表には、次の状態も表示されます。処理中、成功、失敗。

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>トレーニング済みモデルの正確性を評価する

これらのドキュメントを使用して、データを検査し、モデルの正確性を評価できます。

- [データを検査する](how-to-custom-speech-inspect-data.md)
- [データを評価する](how-to-custom-speech-evaluate-data.md)

正確性のテストを選択する場合、現実的なモデルのパフォーマンスを把握するために、モデルで使用したものとは異なる音響データセットを選択することが重要です。

## <a name="next-steps"></a>次のステップ

- [モデルをデプロイする](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>その他のリソース

- [データを準備してテストする](how-to-custom-speech-test-data.md)
- [データを検査する](how-to-custom-speech-inspect-data.md)
- [データを評価する](how-to-custom-speech-evaluate-data.md)
- [モデルをトレーニングする](how-to-custom-speech-train-model.md)
