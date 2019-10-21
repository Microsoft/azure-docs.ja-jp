---
title: クイック スタート:音声を認識する、C# (Xamarin) - Speech Service
titleSuffix: Azure Cognitive Services
description: この記事では、Cognitive Services Speech SDK を使用して、Windows UWP、Android、および iOS 向けのクロスプラットフォーム C# Xamarin アプリケーションを作成します。 デバイスまたはシミュレーターのマイクからの音声を、リアルタイムでテキストに変換します。 このアプリケーションは、Speech SDK NuGet パッケージと Microsoft Visual Studio 2019 を使用して作成します。
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: 02031e8e2800329134eaf34f5f99c93a20b99aac
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803779"
---
# <a name="quickstart-recognize-speech-using-cross-platform-xamarin-app-by-using-the-speech-sdk"></a>クイック スタート:Speech SDK を使用したクロスプラットフォーム Xamarin アプリで音声を認識する

[音声テキスト変換](quickstart-csharp-uwp.md)、[テキスト読み上げ](quickstart-text-to-speech-csharp-uwp.md)、[音声翻訳](quickstart-translate-speech-uwp.md)のクイックスタートも利用できます。

この記事では、Xamarin で [Speech SDK](speech-sdk.md) を使用して、Universal Windows Platform (UWP)、Android、および iOS 向けのクロスプラットフォーム C# アプリケーションを開発します。 このプログラムは、デバイスのマイクからリアルタイムで音声をテキストに変換します。 このアプリケーションは、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2019 (任意のエディション) を使用して作成します。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* Speech Service 用の Azure サブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。
* Windows 10 Fall Creators Update (10.0、ビルド 16299) 以降がインストールされ、マイクが動作する Windows PC。
* [Visual Studio への Xamarin のインストール](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows)。
* [Windows への Xamarin Android のインストール](https://docs.microsoft.com/xamarin/android/get-started/installation/windows)。
* [Windows への Xamarin iOS のインストール](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows)。
* Android をターゲットにする場合: 
   * [開発に対応しており](https://developer.android.com/studio/debug/dev-options)、マイクが動作する Android デバイス (ARM32/64、x86。API 23: Android 6.0 Marshmallow 以上)。
* iOS をターゲットにする場合: [開発に対応しており](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/)、マイクが動作する iOS デバイス (ARM64) または iOS シミュレーター (x64)。
* Windows ARM64 ビルドをサポートするために、[オプションのビルド ツールと ARM/ARM64 用の Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) をインストールします。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>共通 `helloworld` プロジェクトのサンプル コードを追加する

共通 `helloworld` プロジェクトには、クロスプラットフォーム アプリケーション向けのプラットフォーム非依存の実装が含まれています。
ここで、アプリケーションのユーザー インターフェイスを定義する XAML コードを追加し、C# のコード ビハインド実装を追加します。

1. **ソリューション エクスプローラー**で、共通 `helloworld` プロジェクト下の `MainPage.xaml` を開きます。

1. デザイナーの XAML ビューで、次の XAML スニペットを **Grid** タグに挿入します (`<StackLayout>` と `</StackLayout>` の間)。

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. **ソリューション エクスプローラー**で、コードビハインドのソース ファイル `MainPage.xaml.cs` を開きます (`MainPage.xaml` にグループ化されています)。

1. その中のすべてのコードを次のスニペットに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. ソース ファイルの `OnRecognitionButtonClicked` ハンドラーから `YourSubscriptionKey` という文字列を見つけて、ご利用のサブスクリプション キーに置き換えます。

1. `OnRecognitionButtonClicked` ハンドラーから `YourServiceRegion` という文字列を見つけて、ご利用のサブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。 たとえば、無料試用版サブスクリプションの場合は `westus` を使用します。

1. 次に、各プラットフォーム プロジェクト (UWP、Android、iOS) にマイクへのアクセス許可を照会する [Xamarin サービス](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)を作成する必要があります。 そのためには、`helloworld` プロジェクト下に `Services` という新規フォルダーを追加し、そこに新しい C# ソース ファイルを作成して (`Services` フォルダーを右クリックして **[追加]**  >  **[新しいアイテム]**  >  **[コード ファイル]** ) 名前を `IMicrophoneService.cs` に変更してから、次のスニペットの全コードをそのファイル内に入力します: [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>`helloworld.Android` プロジェクトのサンプル コードを追加する

今度は、アプリケーションの Android 固有の部分を定義した C# コードを追加します。

1. **ソリューション エクスプローラー**で、`helloworld.Android` プロジェクトにある `MainActivity.cs` を開きます。

1. その中のすべてのコードを次のスニペットに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. 次に、`MicrophoneService` の Android 固有の実装を追加するために、`helloworld.Android` プロジェクト下に新しい `Services` フォルダーを作成します。 その後、そこに新しい C# ソース ファイルを作成して `MicrophoneService.cs` という名前を付け、次のコード スニペットをコピーしてそのファイル内に貼り付けます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. 次に、`Properties` フォルダー下にある `AndroidManifest.xml` を開き、`<manifest>` と `</manifest>` の間に、次のマイクの uses-permission 設定を追加します。
```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
```

## <a name="add-sample-code-for-the-helloworldios-project"></a>`helloworld.iOS` プロジェクトのサンプル コードを追加する

今度は、helloworld.iOS プロジェクトにアプリケーションの iOS 固有の部分を定義した C# コードを追加し、さらに Apple デバイス固有の構成も作成します。

1. **ソリューション エクスプローラー**で、`helloworld.iOS` プロジェクトにある `AppDelegate.cs` を開きます。

1. その中のすべてのコードを次のスニペットに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. 次に、`MicrophoneService` の iOS 固有の実装を追加するために、`helloworld.iOS` プロジェクト下に新しい `Services` フォルダーを作成します。 その後、そこに新しい C# ソース ファイルを作成して `MicrophoneService.cs` という名前を付け、次のコード スニペットをコピーしてそのファイル内に貼り付けます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. `helloworld.iOS` プロジェクト下にある Info.plist をテキスト エディターで開き、dict セクションにキーと値のペア <key>NSMicrophoneUsageDescription</key>
   <string>This sample app requires microphone access</string> を追加します
   > 注:iPhone デバイス向けのビルドをターゲットにする場合は、`Bundle Identifier` とデバイスのプロビジョニング プロファイルのアプリ ID が一致していることを確認してください。一致していない場合、ビルドは失敗します。 iPhoneSimulator を使用する場合は、そのままにしてかまいません。

1. Windows PC でビルドする場合は、 **[ツール]**  >  **[iOS]**  >  **[Mac とペアリング]** で、ビルド用に Mac デバイスとの接続を確立する必要があります。 Visual Studio で表示されるウィザードの指示に従って、Mac デバイスとの接続を有効にします。

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>`helloworld.UWP` プロジェクトのサンプル コードを追加する

今度は、アプリケーションの UWP 固有の部分を定義した C# コードを追加します。

1. **ソリューション エクスプローラー**で、`helloworld.UWP` プロジェクトにある `MainPage.xaml.cs` を開きます。

1. その中のすべてのコードを次のスニペットに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. 次に、`MicrophoneService` の UWP 固有の実装を追加するために、`helloworld.UWP` プロジェクト下に新しい `Services` フォルダーを作成します。 その後、そこに新しい C# ソース ファイルを作成して `MicrophoneService.cs` という名前を付け、次のコード スニペットをコピーしてそのファイル内に貼り付けます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. 次に、Visual Studio 内で `helloworld.UWP` プロジェクト下にある `Package.appxmanifest` ファイルをダブルクリックし、 **[機能]**  >  **[マイク]** をオンにして、ファイルを保存します。
   > 注:"証明書ファイルが存在しません: helloworld.UWP_TemporaryKey.pfx" という警告が表示された場合は、[音声テキスト変換](quickstart-csharp-uwp.md)のサンプルで詳細を確認してください。

1. メニュー バーから **[ファイル]**  >  **[すべて保存]** を選択して変更内容を保存します。

## <a name="build-and-run-the-uwp-application"></a>UWP アプリケーションをビルドして実行する

1. `helloworld.UWP` をスタートアップ プロジェクトに設定し、マウスで `helloworld.UWP` プロジェクトを右クリックして **[ビルド]** を選択し、アプリケーションをビルドします。 

1. **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押して、アプリケーションを起動します。 **[helloworld]** ウィンドウが表示されます。

   ![サンプル UWP 音声認識アプリケーション (C#) - クイックスタート](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. **[Enable Microphone]** を選択し、アクセス許可要求がポップアップ表示されたら、 **[はい]** を選択します。

   ![マイクへのアクセス許可要求](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. **[Start Speech recognition]\(音声認識を開始する\)** を選択し、デバイスのマイクに向かって英語のフレーズを話します。 音声が Speech Services に送信されてテキストに変換され、ウィンドウに表示されます。

   ![音声認識ユーザー インターフェイス](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Android アプリケーションと iOS アプリケーションをビルドして実行する

デバイスまたはシミュレーターで Android および iOS アプリケーションをビルドおよび実行する方法は、UWP の場合と同様です。 ただし、このドキュメントの「`Prerequisites`」セクションで必須とされているすべての SDK を適切にインストールしておく必要があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
