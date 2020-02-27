---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4af3c202d4f00c4ac3041921175c92226f0db7c
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964131"
---
## <a name="run-the-function-locally"></a>関数をローカルで実行する

Visual Studio Code を [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) と統合することで、このプロジェクトをローカルの開発用コンピューター上で実行してから、Azure に発行することができます。

1. 関数を呼び出すには、F5 キーを押して関数アプリ プロジェクトを起動します。 Core Tools からの出力が**ターミナル** パネルに表示されます。

1. Azure Functions Core Tools をまだインストールしていない場合は、プロンプトで **[インストール]** を選択します。 Core Tools がインストールされると、アプリが**ターミナル** パネルで起動します。 HTTP によってトリガーされる関数の URL エンドポイントがローカルで実行されていることを確認できます。 

    ![Azure のローカル出力](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Core Tools が実行されている状態で、次の URL に移動して、`?name=Functions` クエリ文字列を含む GET 要求を実行します。

    <http://localhost:7071/api/HttpExample?name=Functions>

1. 応答が返され、ブラウザーに次のように表示されます。

    ![ブラウザーでの関数 localhost の応答](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. 要求に関する情報が **[ターミナル]** パネルに表示されます。

    ![[ターミナル] パネルでの関数の実行](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Ctrl + C キーを押して Core Tools を停止し、デバッガーの接続を解除します。
