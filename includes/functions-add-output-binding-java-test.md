---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: 179ae760f146a5ac3041a54065ae12147f3f9bf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97739818"
---
アーキタイプはテストのセットも作成するため、`run` メソッド シグネチャ内の新しい `msg` パラメーターを処理するためにこれらのテストを更新する必要があります。  

_src/main/java_ の下の対象のテスト コードの場所を参照し、*Function.java* プロジェクト ファイルを開きます。`//Invoke` の下のコード行を次のコードに置き換えます。

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
