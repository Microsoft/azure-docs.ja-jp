---
title: クイック スタート:Azure REST API と Java を使って画像から顔を検出する
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Azure Face REST API と Java を使用して、画像から顔を検出します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 8aaf0b25a20f24739bb556583cd020d8f11eaf2c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549526"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>クイック スタート:REST API と Java を使用して画像内の顔を検出する

このクイック スタートでは、Azure Face REST API と Java を使用して、画像から人の顔を検出します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Face リソースを作成"  target="_blank">Face リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Face API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* 任意の Java IDE。

## <a name="create-the-java-project"></a>Java プロジェクトを作成する

1. IDE で新しいコマンド ライン Java アプリを作成し、**main** メソッドを含んだ **Main** クラスを追加します。
1. 次のライブラリを Java プロジェクトにインポートします。 Maven を使用している場合、Maven 座標はライブラリごとに提供されます。
   - [Apache HTTP client](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.6)
   - [Apache HTTP core](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.10)
   - [JSON library](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Apache Commons Logging](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (commons-logging:commons-logging:1.1.2)

## <a name="add-face-detection-code"></a>顔検出コードを追加する

プロジェクトのメイン クラスを開きます。 画像を読み込んで顔を検出するために必要なコードをここに追加します。

### <a name="import-packages"></a>パッケージをインポートする

次の `import` ステートメントをファイルの先頭に追加します。

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;
```

### <a name="add-essential-fields"></a>必須フィールドを追加する

**Main** クラスを次のコードに置き換えます。 このデータによって、Face サービスへの接続方法と入力データの取得場所が指定されます。 `subscriptionKey` フィールドは、実際のサブスクリプション キーの値で更新する必要があります。また `uriBase` 文字列も、適切なエンドポイント文字列を含むように変更する必要があります。 また、`imageWithFaces` 値は、別の画像ファイルのパスに設定することもできます。

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

`faceAttributes` フィールドは、簡単に言えば、特定の種類の属性を列挙したものです。 検出された顔について、どの情報を取得するかを指定します。

```Java
public class Main {
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    private static final String uriBase =
        "https://<My Endpoint String>.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>顔検出 REST API を呼び出す

次のコードを含む **main** メソッドを追加します。 これは、リモートの画像から顔情報を検出する Face API の REST 呼び出しを構築するものです (取得する顔属性は `faceAttributes` 文字列で指定します)。 出力データは、JSON 文字列に書き込まれます。

```Java
    public static void main(String[] args) {
        HttpClient httpclient = HttpClientBuilder.create().build();

        try
        {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("returnFaceId", "true");
            builder.setParameter("returnFaceLandmarks", "false");
            builder.setParameter("returnFaceAttributes", faceAttributes);

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity reqEntity = new StringEntity(imageWithFaces);
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();
```

### <a name="parse-the-json-response"></a>JSON 応答を解析します

前のコードのすぐ下に、次のブロックを追加します。返された JSON データは、コンソールに出力される前に、このコードによって、より読みやすい形式に変換されます。 最後に、try-catch ブロック、**main** メソッド、および **Main** クラスを閉じます。

```Java
            if (entity != null)
            {
                // Format and display the JSON response.
                System.out.println("REST Response:\n");

                String jsonString = EntityUtils.toString(entity).trim();
                if (jsonString.charAt(0) == '[') {
                    JSONArray jsonArray = new JSONArray(jsonString);
                    System.out.println(jsonArray.toString(2));
                }
                else if (jsonString.charAt(0) == '{') {
                    JSONObject jsonObject = new JSONObject(jsonString);
                    System.out.println(jsonObject.toString(2));
                } else {
                    System.out.println(jsonString);
                }
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="run-the-app"></a>アプリを実行する

コードをコンパイルして実行します。 実行に成功した場合、応答として顔データが、読みやすい JSON 形式でコンソール ウィンドウに表示されます。 次に例を示します。

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Face API への REST 呼び出しを使って画像内の顔を検出し、その属性を返す単純な Java コンソール アプリケーションを作成しました。 この後は、サポートされているシナリオについて、Face API のリファレンス ドキュメントでさらに理解を深めましょう。

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)