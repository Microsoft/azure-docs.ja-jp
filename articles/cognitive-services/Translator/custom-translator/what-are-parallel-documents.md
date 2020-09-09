---
title: 並列ドキュメントとは - Custom Translator
titleSuffix: Azure Cognitive Services
description: 並列ドキュメントは、一方がもう一方の翻訳であるドキュメントのペアです。 ペアの一方のドキュメントにはソース言語の文が含まれ、もう一方のドキュメントにはターゲット言語に翻訳されたこれらの文が含まれています。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: ce9f7e922b9a168205ea1d193830dd654691998a
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510812"
---
# <a name="what-are-parallel-documents"></a>並列ドキュメントとは

並列ドキュメントは、一方がもう一方の翻訳であるドキュメントのペアです。 ペアの一方のドキュメントにはソース言語の文が含まれ、もう一方のドキュメントにはターゲット言語に翻訳されたこれらの文が含まれています。
"ソース" とマークされている言語と "ターゲット" とマークされている言語にかかわらず、並列ドキュメントを使用して、いずれの方向でも翻訳システムをトレーニングできます。

## <a name="requirements"></a>必要条件

システムをトレーニングするには、10,000 以上の一意のアラインされた並列文が必要です。 この制限は、翻訳モデルのトレーニングを成功させるために、並列文に十分な一意のボキャブラリが確実に含まれるようにするための安全策です。 翻訳システムの品質を向上させるために、並列コンテンツを継続的に追加して保持することをお勧めします。 [文のアライン](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment)に関するページを参照してください。

Microsoft では、Custom Translator にアップロードするドキュメントが、第三者の著作権または知的財産に違反していないことを必須にしています。 詳細については、[使用条件](https://azure.microsoft.com/support/legal/cognitive-services-terms/)に関するページを参照してください。
ポータルを使用してドキュメントをアップロードしても、ドキュメント自体の知的財産の所有権は変わりません。

## <a name="use-of-parallel-documents"></a>並列ドキュメントの使用

並列ドキュメントは次のようにシステムから使用されます。

1.  単語、フレーズ、文が 2 つの言語間で一般的にマップされる方法を学習するため。

2.  周囲のフレーズに応じて、適切なコンテキストを処理する方法を学習するため。 ある単語は、もう一方の言語で常にまったく同じ言語に翻訳されるとは限りません。

ソース言語とターゲット言語のバージョンのドキュメント間には、1 対 1 の文の対応を付けることをお勧めします。

プロジェクトがドメイン (カテゴリ) 固有である場合、ドキュメントはそのカテゴリ内の用語で一貫している必要があります。 結果の翻訳システムの品質は、ドキュメント セット内の文の数と文の品質によって変わります。 カテゴリに固有の単語のさまざまな用途共に、ドキュメントに含まれている例が多くなればなるほど、翻訳時にシステムで実行できるジョブが改善されます。

アップロードされたドキュメントは各ワークスペース専用であり、任意の数のプロジェクトやトレーニングで使用できます。 ドキュメントから抽出された文は、プレーンな Unicode テキスト ファイルとしてリポジトリ内で別に保存され、削除することもできます。 Custom Translator をドキュメント リポジトリとして使用しないでください。アップロードしたドキュメントをアップロードした形式でダウンロードできなくなります。



## <a name="next-steps"></a>次のステップ

- Custom Translator で[辞書](what-is-dictionary.md)を使用する方法について説明します。
