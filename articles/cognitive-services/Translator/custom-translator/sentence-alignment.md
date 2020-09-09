---
title: 文のペアリングとアライン - Custom Translator
titleSuffix: Azure Cognitive Services
description: トレーニングの実行時に、並列ドキュメントに存在する文はペアリングまたはアラインされます。 Custom Translator では、文とその文の翻訳を読み取ることで、一度に 1 文ずつ翻訳が学習されます。 次に、2 つの文に含まれる単語とフレーズが相互にアラインされます。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 97327517b40e715294393a97e8a26c078173e762
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510846"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>並列ドキュメントの文のペアリングとアライン

トレーニング時に、並列ドキュメントに存在する文はペアリングまたはアラインされます。 Custom Translator では、各データ セットの [Aligned Sentences]\(アライン済みの文\) として、ペアリングできる文の数がレポートされます。

## <a name="pairing-and-alignment-process"></a>ペアリングとアラインのプロセス

Custom Translator では、一度に 1 文ずつ、文の翻訳が学習されます。 ソースから文が読み取られ、次にターゲットからその文の翻訳が読み取られます。 次に、2 つの文に含まれる単語とフレーズが相互にアラインされます。 このプロセスによって、ある文の単語とフレーズから、その文の翻訳に含まれる同義の単語とフレーズへのマップを作成できるようになります。 アラインでは、相互の翻訳である文に対してシステムが確実にトレーニングされるように試行します。

## <a name="pre-aligned-documents"></a>事前にアラインされたドキュメント

並列ドキュメントがあることがわかっている場合は、事前にアラインされたテキスト ファイルを提供して、文のアラインを上書きすることができます。 両方のドキュメントのすべての文をテキスト ファイルに抽出し、1 行に 1 文を構成し、`.align` の拡張子でアップロードすることができます。 `.align` の拡張子で、文のアラインをスキップする必要があることを Custom Translator に指示します。

最適な結果を得るために、ファイルの 1 行に 1 文ずつを含めます。 不適切なアライン結果になるため、文中には改行文字を入れないでください。

## <a name="suggested-minimum-number-of-sentences"></a>推奨される文の最小数

トレーニングを成功させるために、以下の表に各ドキュメントの種類で必要な文の最小数を示します。 この制限は、翻訳モデルのトレーニングを成功させるために、並列文に十分な一意のボキャブラリが確実に含まれるようにするための安全策です。 一般的なガイドラインとして、人間による翻訳品質のドメイン内並列文が多くなると、より高品質のモデルが生成されます。

| ドキュメントの種類   | 推奨される文の最小数 | 文の最大数 |
|------------|--------------------------------------------|--------------------------------|
| トレーニング   | 10,000                                     | 上限なし                 |
| チューニング     | 500                                      | 2,500       |
| テスト    | 500                                      | 2,500  |
| Dictionary | 0                                          | 上限なし                 |

> [!NOTE]
> - トレーニングの 10,000 の文の最小数が満たされていない場合、トレーニングは開始されず、失敗します。 
> - チューニングとテストは省略可能です。 これらを指定しないと、確認とテストに使用するためのトレーニングからの適切な比率がシステムで削除されます。 
> - モデルは、辞書データのみを使用してトレーニングすることができます。 「[辞書とは](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Custom Translator で[辞書](what-is-dictionary.md)を使用する方法について説明します。
