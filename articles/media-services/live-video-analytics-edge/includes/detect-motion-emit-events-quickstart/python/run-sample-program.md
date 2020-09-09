---
ms.openlocfilehash: ed7be82146f38cc7ae57fd863bb0c1b8e6910fd2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691174"
---
サンプル コードを実行するには、これらの手順に従います。

1. Visual Studio Code で、*src/cloud-to-device-console-app/operations.json* に移動します。
1. **GraphTopologySet** ノードで、次の値が表示されていることを確認します。

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. **GraphInstanceSet** および **GraphTopologyDelete** ノードで、`topologyName` の値が、グラフ トポロジの `name` プロパティの値と一致していることを確認します。

    `"topologyName" : "MotionDetection"`
    
1. F5 キーを押して、デバッグ セッションを開始します。 **[ターミナル]** ウィンドウに、いくつかのメッセージが表示されます。
1. *operations.json* ファイルは、`GraphTopologyList` および `GraphInstanceList` の呼び出しから始まります。 前回のクイックスタートを終了した後にリソースをクリーンアップしている場合は、このプロセスにより空のリストが返されてから、一時停止します。 続行するには、Enter キーを押します。

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
        "@apiVersion": "1.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
        "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    
    **[ターミナル]** ウィンドウに、次の一連のダイレクト メソッド呼び出しが表示されます。
     * 前の `topologyUrl` を使用する `GraphTopologySet` の呼び出し
     * 次の本文を使用する `GraphInstanceSet` の呼び出し。
         
    ```
    {
      "@apiVersion": "1.0",
      "name": "Sample-Graph",
      "properties": {
        "topologyName": "MotionDetection",
        "description": "Sample graph description",
        "parameters": [
          {
            "name": "rtspUrl",
            "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
          },
          {
            "name": "rtspUserName",
            "value": "testuser"
          },
          {
            "name": "rtspPassword",
            "value": "testpassword"
          }
        ]
      }
    }
    ```
     
    * グラフ インスタンスとビデオのフローを開始する `GraphInstanceActivate` の呼び出し。
    * グラフ インスタンスが実行状態であることを示す `GraphInstanceList` の 2 回目の呼び出し。
1. **[ターミナル]** ウィンドウの出力は `Press Enter to continue` で一時停止します。 Enter キーはまだ押さないでください。 上へスクロールして、呼び出したダイレクト メソッドの JSON 応答のペイロードを確認します。
1. Visual Studio Code の **[出力]** ウィンドウに切り替えます。 Live Video Analytics on IoT Edge モジュールから IoT ハブに送信されているメッセージが表示されます。 このクイックスタートの次のセクションでは、これらのメッセージについて説明します。
1. メディア グラフは引き続き実行され、結果が出力されます。 RTSP シミュレーターによって、ソース ビデオがループ処理され続けます。 メディア グラフを停止するには、 **[ターミナル]** ウィンドウに戻り、Enter キーを押します。 

    次の一連の呼び出しによって、リソースがクリーンアップされます。

    * `GraphInstanceDeactivate` の呼び出しによって、グラフ インスタンスが非アクティブ化されます。
    * `GraphInstanceDelete` の呼び出しによって、インスタンスが削除されます。
    * `GraphTopologyDelete` の呼び出しによって、トポロジが削除されます。
    * `GraphTopologyList` の最後の呼び出しによって、リストが空であることが示されます。
