---
title: クイック スタート:音声を複数の言語に翻訳する、Java (Windows、Linux) - Speech サービス
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: 1ebd40fdc63512e0cc60e0e1d473075e3a8ae53a
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445845"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [使用する開発環境を設定する](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="add-sample-code"></a>サンプル コードを追加する

1. 新しい空のクラスを Java プロジェクトに追加するために、 **[File]**  >  **[New]**  >  **[Class]** の順に選択します。

1. **[New Java Class]** ウィンドウで、 **[Package]** フィールドに **speechsdk.quickstart** と入力し、 **[Name]** フィールドに **Main** と入力します。

   ![[New Java Class] ウィンドウのスクリーンショット](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. `Main.java` のすべてのコードを次のスニペットに置き換えます。

   ```Java
   package quickstart;

   import java.io.IOException;
   import java.util.concurrent.Future;
   import java.util.Map;
   import java.util.concurrent.ExecutionException;
   import com.microsoft.cognitiveservices.speech.*;
   import com.microsoft.cognitiveservices.speech.translation.*;

   public class Main {

       public static void translationWithMicrophoneAsync() throws InterruptedException, ExecutionException, IOException
       {
           // Creates an instance of a speech translation config with specified
           // subscription key and service region. Replace with your own subscription key
           // and region identifier from here: https://aka.ms/speech/sdkregion

           int exitCode = 1;
           SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey",  "YourServiceRegion");
           assert(config != null);

           // Sets source and target languages.
           String fromLanguage = "en-US";
           config.setSpeechRecognitionLanguage(fromLanguage);
           config.addTargetLanguage("de");
           config.addTargetLanguage("fr");

           // Creates a translation recognizer using the default microphone audio input device.
           TranslationRecognizer recognizer = new TranslationRecognizer(config);
           assert(recognizer != null);

           System.out.println("Say something...");

           // Starts translation, and returns after a single utterance is recognized. The end of a
           // single utterance is determined by listening for silence at the end or until a maximum of 15
           // seconds of audio is processed. The task returns the recognized text as well as the translation.
           // Note: Since recognizeOnceAsync() returns only a single utterance, it is suitable only for single
           // shot recognition like command or query.
           // For long-running multi-utterance recognition, use startContinuousRecognitionAsync() instead.
           Future<TranslationRecognitionResult> task = recognizer.recognizeOnceAsync();
           assert(task != null);

           TranslationRecognitionResult result = task.get();
           assert(result != null);

           if (result.getReason() == ResultReason.TranslatedSpeech) {
               System.out.println("RECOGNIZED '" + fromLanguage + "': " + result.getText());

               Map<String, String> map = result.getTranslations();
               for(String toLanguage: map.keySet()) {
                   System.out.println("TRANSLATED into '" + toLanguage + "': " + map.get(toLanguage));
               }
               exitCode = 0;
           }
           else if (result.getReason() == ResultReason.RecognizedSpeech) {
               System.out.println("RECOGNIZED '" + fromLanguage + "': " + result.getText() + "(text could not be  translated)");
               exitCode = 0;
           }
           else if (result.getReason() == ResultReason.NoMatch) {
               System.out.println("NOMATCH: Speech could not be recognized.");
           }
           else if (result.getReason() == ResultReason.Canceled) {
               CancellationDetails cancellation = CancellationDetails.fromResult(result);
               System.out.println("CANCELED: Reason=" + cancellation.getReason());

               if (cancellation.getReason() == CancellationReason.Error) {
                   System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                   System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                   System.out.println("CANCELED: Did you update the subscription info?");
               }
           }

           recognizer.close();

           System.exit(exitCode);
       }

       public static void main(String[] args) {
           try {
               translationWithMicrophoneAsync();
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());
               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. 文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](~/articles/cognitive-services/Speech-Service/regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

F11 キーを押すか、 **[Run]**  >  **[Debug]** の順に選択します。

1. 英語の語句または文を読み上げます。 その音声が Speech サービスに転送され、翻訳されてテキストに文字起こしされます (このケースではフランス語とドイツ語に)。 そのテキストが Speech サービスからアプリケーションに返されて表示されます。

````
Say something...
RECOGNIZED 'en-US': What's the weather in Seattle?
TRANSLATED into 'de': Wie ist das Wetter in Seattle?
TRANSLATED into 'fr': Quel temps fait-il à Seattle ?
````

## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]
