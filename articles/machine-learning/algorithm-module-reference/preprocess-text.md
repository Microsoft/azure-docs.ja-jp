---
title: Preprocess Text (テキストの前処理):モジュール リファレンス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 内で Preprocess Text (テキストの前処理) モジュールを使用して、テキストをクリーンアップして簡素化する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: a8938eba0f7af995086ab1e2baba41aee7dc6330
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153810"
---
# <a name="preprocess-text"></a>Preprocess Text (テキストの前処理)

この記事では Azure Machine Learning デザイナー (プレビュー) 内のモジュールについて説明します。

**Preprocess Text (テキストの前処理)** モジュールを使用して、テキストをクリーンアップして簡素化します。 次の一般的なテキスト処理操作がサポートされています。

* ストップワードの削除
* 正規表現を使用して特定の対象文字列を検索して置換する
* レンマ化 (複数の関連する単語を 1 つの正規形式に変換する)
* 大文字と小文字の正規化
* 数字、特殊文字、および繰り返し文字のシーケンス (たとえば、"aaaa") など、特定のクラスの文字の削除
* 電子メールと URL の識別と削除

現在、**Preprocess Text (テキストの前処理)** モジュールでは英語のみがサポートされます。

## <a name="configure-text-preprocessing"></a>テキストの前処理を構成する  

1.  Azure Machine Learning 内で **Preprocess Text (テキストの前処理)** モジュールをパイプラインに追加します。 このモジュールは、**Text Analytics** にあります。

1. テキストを含む列が少なくとも 1 列あるデータセットを接続します。

1. **[言語]** ドロップダウン リストから言語を選択します。

1. **Text column to clean (クリーンアップするテキスト列)** :前処理する列を選択します。

1. **Remove stop words (ストップワードの削除)** :定義済みのストップワード リストをテキスト列に適用する場合は、このオプションを選択します。 

    ストップワード リストは言語に依存し、カスタマイズ可能です。

1. **Lemmatization (レンマ化)** :正規形式で単語を表示する場合は、このオプションを選択します。 このオプションは、類似するテキスト トークンの一意の出現回数を減らすのに役立ちます。

    レンマ化処理は、言語に大きく依存しています。

1. **Detect sentences (文の検出)** :分析の実行時にモジュールによって文の境界マークを挿入する場合は、このオプションを選択します。

    このモジュールは、一連の 3個のパイプ文字 `|||` を使用して、文の終端記号を表します。

1. 正規表現を使用して、オプションの検索と置換の操作を実行します。

    * **Custom regular expression (カスタム正規表現)** :検索するテキストを定義します。
    * **Custom replacement string (カスタム置換文字列)** :1 つの置換値を定義します。

1. **Normalize case to lowercase (大文字を小文字に正規化)** :ASCII の大文字を小文字の形式に変換する場合は、このオプションを選択します。

    文字が正規化されていない場合は、大文字と小文字の同一の単語が 2 つの異なる単語と見なされます。

1. また、処理された出力テキストから、次の種類の文字または文字シーケンスを削除することもできます。

    * **Remove numbers (数字の削除)** :このオプションを選択すると、指定した言語のすべての数字が削除されます。 ID 番号はドメインに依存し、言語に依存します。 数字が既知の単語の不可欠の部分である場合、その数字は削除されない可能性があります。
    
    * **Remove special characters (特殊文字の削除)** :英数字以外の特殊文字をすべて削除するには、このオプションを使用します。
    
    * **Remove duplicate characters (重複する文字の削除)** :このオプションを選択すると、3 回以上繰り返されているシーケンス内の余分な文字が削除されます。 たとえば、"aaaaa" のようなシーケンスは "aa" に短縮されます。
    
    * **Remove email addresses (メール アドレスの削除)** :`<string>@<string>` の形式のシーケンスをすべて削除するには、このオプションを選択します。  
    * **Remove URLs (URL の削除)** :次の URL プレフィックスを含むシーケンスをすべて削除するには、このオプションを選択します: `http`、`https`、`ftp`、`www`
    
1. **Expand verb contractions (動詞の短縮形を展開)** :このオプションは、動詞の短縮形を使用している言語 (現在、英語のみ) に適用されます。 

    たとえば、このオプションを選択することにより、 *"wouldn't stay there"* という句を *"would not stay there"* に置き換えることができます。

1. **Normalize backslashes to slashes (バックスラッシュをスラッシュに正規化)** :`\\` のすべてのインスタンスを `/` にマップするには、このオプションを選択します。

1. **Split tokens on special characters (特殊文字でトークンを分割)** :`&` や `-` などの文字で単語を分割する場合は、このオプションを選択します。 このオプションはまた、特殊文字が 3 回以上繰り返される場合にその文字を減らすことができます。 

    たとえば、文字列 `MS---WORD` は、3 つのトークン `MS`、`-`、`WORD` に分割されます。

1. パイプラインを実行します。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 