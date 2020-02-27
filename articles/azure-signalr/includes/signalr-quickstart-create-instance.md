---
title: インクルード ファイル
description: インクルード ファイル
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 0a228f6d8b4f72acd3783f27bf192fe6bd13f988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392136"
---
## <a name="create-an-azure-signalr-service-instance"></a>Azure SignalR Service のインスタンスを作成する

アプリケーションは Azure 内の SignalR Service インスタンスに接続します。

1. Azure portal の左上にある [新規] ボタンを選択します。 [新規] 画面で、検索ボックスに「*SignalR Service*」と入力して Enter キーを押します。

    ![SignalR Service の検索](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. 検索結果から **[SignalR Service]** を選択し、 **[Create]** を選択します。

1. 次の設定を入力します。

    | 設定      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **リソース名** | グローバルに一意の名前 | 新しい SignalR Service インスタンスを識別する名前。 有効な文字は、`a-z`、`0-9`、および `-` です。  | 
    | **サブスクリプション** | 該当するサブスクリプション | この新しい SignalR Service インスタンスが作成されるサブスクリプション。 | 
    | **[リソース グループ](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | SignalR Service インスタンスの作成先となる新しいリソース グループの名前。 | 
    | **Location** | 米国西部 | 近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
    | **[価格レベル]** | 無料 | Azure SignalR Service を無料試用します。 |
    | **[ユニット数]** |  適用なし | ユニット数は、SignalR Service インスタンスで受け付けることができる接続の数を指定します。 Standard レベルでのみ構成可能です。 |
    | **サービス モード** |  サーバーレス | Azure Functions または REST API で使用するため。 |

    ![SignalR Service の作成](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. **[作成]** を選択して SignalR Service インスタンスのデプロイを開始します。

1. インスタンスがデプロイされたら、ポータルで開き、その [設定] ページを見つけます。 Azure Functions バインディングまたは REST API を通じて Azure SignalR サービスを使用している場合にのみ、サービス モード設定を *[サーバーレス]* に変更します。 それ以外の場合は *[クラシック]* または *[既定]* のままにします。