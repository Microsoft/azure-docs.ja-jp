---
title: 意図の取得、Python
titleSuffix: Language Understanding - Azure Cognitive Services
description: このクイック スタートでは、LUIS エンドポイントに発話を渡して、意図とエンティティを取得します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: e4601b5b6156ace897df65cd42159a1193f8194a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100125"
---
# <a name="quickstart-get-intent-using-python"></a>クイック スタート: Python を使用した意図の取得
このクイック スタートでは、LUIS エンドポイントに発話を渡して、意図とエンティティを取得します。

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>前提条件

* [Python 3.6](https://www.python.org/downloads/) 以降。
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS キーを取得する

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>ブラウザーで意図を取得する

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>プログラムで意図を取得する

Python を使用して、前の手順でブラウザー ウィンドウに表示されたものと同じ結果にアクセスできます。

1. 次のコード スニペットのいずれかを、`quickstart-call-endpoint.py` というファイルにコピーします。

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. `Ocp-Apim-Subscription-Key` フィールドの値を実際の LUIS エンドポイント キーに置き換えます。

3. `pip install requests` で依存関係をインストールします。

4. `python ./quickstart-call-endpoint.py` でスクリプトを実行します。 前の手順でブラウザー ウィンドウに表示されたものと同じ JSON が表示されます。

## <a name="luis-keys"></a>LUIS キー

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ
python ファイルを削除します。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [発話の追加](luis-get-started-python-add-utterance.md)
