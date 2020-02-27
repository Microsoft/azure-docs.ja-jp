---
title: パターンと精度 - LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) アプリケーションで、パターン テンプレートを追加して予測精度を改善します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311718"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>パターンを追加して予測精度を改善する方法
LUIS アプリでエンドポイント発話を受信したら、単語の順序と選択を示す[パターン](luis-concept-patterns.md)を使用して、発話の予測精度を改善します。 パターンでは、特定の[構文](luis-concept-patterns.md#pattern-syntax)を使用して、[エンティティ](luis-concept-entity-types.md)、エンティティの[ロール](luis-concept-roles.md)、およびオプションのテキストの場所を示します。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> パターンに含まれるのは、サブコンポーネントではなく、機械学習エンティティの親のみです。

## <a name="adding-example-utterances-as-pattern"></a>発話の例をパターンとして追加する

エンティティのパターンを追加する場合、[Intent details] (意図の詳細) ページからパターンを作成するのが _最も簡単な_ 方法です。 これにより、構文が発話の例と一致することが保証されます。

1. [プレビュー LUIS ポータル](https://preview.luis.ai)で、 **[マイ アプリ]** ページからアプリを選択します。
1. **[Intents]\(意図\)** 一覧ページで、テンプレート発話の作成元となる発話の例の意図名を選択します。
1. [Intent details] (意図の詳細) ページで、テンプレート発話として使用する発話の例の行を選択し、コンテキスト ツール バーから **[+ Add as pattern]\(+ パターンとして追加\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![意図の詳細ページで発話の例をテンプレート パターンとして選択するスクリーンショット。](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. ポップアップ ボックスで、 **[Confirm patterns]\(パターンの確認\)** ページの **[完了]** を選択します。 エンティティのサブコンポーネント、制約、または記述子を定義する必要はありません。 機械学習エンティティのみを一覧にする必要があります。

    > [!div class="mx-imgBorder"]
    > ![意図の詳細ページで発話の例をテンプレート パターンとして確認するスクリーンショット。](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. テキストを省略可能として選択するなど、`[]` (角かっこ) を使用してテンプレートを編集する必要がある場合、この編集は **[パターン]** ページから行う必要があります。

1. ナビゲーション バーで **[トレーニング]** を選択して、新しいパターンを使用してアプリをトレーニングします。

## <a name="add-template-utterance-using-correct-syntax"></a>正しい構文を使用してテンプレート発話を追加する

1. **[マイ アプリ]** ページでアプリの名前を選択してアプリを開き、左側のパネルの **[アプリのパフォーマンス向上]** の下で **[パターン]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![パターン一覧のスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. パターンの適切な意図を選択します。

1. テンプレート テキスト ボックスに、テンプレートの発話を入力し、Enter キーを押します。 エンティティ名を入力する場合は、正しいパターン エンティティ構文を使用します。 エンティティ構文は `{` で開始します。 エンティティの一覧が表示されます。 正しいエンティティを選択します。

    > [!div class="mx-imgBorder"]
    > ![パターンのエンティティのスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    ご自身のエンティティに[ロール](luis-concept-roles.md)が含まれる場合、エンティティ名の後ろにコロンを 1 つ付けて`:`ロールを指定します (`{Location:Origin}` など)。 エンティティのロールの一覧が表示されます。 ロールを選択し、Enter キーを押します。

    > [!div class="mx-imgBorder"]
    > ![ロールを含むエンティティのスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    正しいエンティティを選択したら、パターンを入力して、Enter キーを押します。 パターンの入力が完了したら、ご自身のアプリを[トレーニング](luis-how-to-train.md)します。

    > [!div class="mx-imgBorder"]
    > ![両方の型のエンティティを含む入力済みパターンのスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>パターンを含むモデルの変更後にアプリをトレーニング
パターンを追加、編集、削除、または再割り当てしたら、その変更について、エンドポイントのクエリに影響を与えるようにアプリを[トレーニング](luis-how-to-train.md)して[公開](luis-how-to-publish-app.md)します。

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>コンテキスト ツールバーの使用

パターンの一覧の上部にあるコンテキスト ツールバーを使用して、以下を実行できます。

* パターンの検索
* パターンの編集
* 別の意図への 1 つのパターンの再割り当て
* 別の意図への複数のパターンの再割り当て
* 単一のパターンの削除
* 複数のパターンの削除
* エンティティでのパターンの一覧のフィルター処理
* 意図でのパターンの一覧のフィルター処理
* エンティティまたは意図フィルターの削除
* 意図またはエンティティ ページでの既存の発話からのパターンの追加

## <a name="next-steps"></a>次のステップ

* チュートリアルに従って、pattern.any とロールを使用する[パターンをビルド](luis-tutorial-pattern.md)する方法を確認します。
* お使いのアプリを[トレーニング](luis-how-to-train.md)する方法を確認します。
