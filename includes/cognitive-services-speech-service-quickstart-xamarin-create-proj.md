---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 6d20df031633df4642ce9fb5cbbc469fd7f0a5da
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188499"
---
Xamarin を使用したクロスプラットフォーム モバイル アプリ .NET 開発用の Visual Studio プロジェクトを作成するには、次のことを行う必要があります。
- Visual Studio の開発オプションを設定する。
- プロジェクトを作成してターゲット アーキテクチャを選択する。 
- Speech SDK をインストールする。

### <a name="set-up-visual-studio-development-options"></a>Visual Studio の開発オプションを設定する

最初に、.NET を使用したクロスプラットフォーム モバイル開発向けに Visual Studio が正しく設定されていることを確認します。

1. Visual Studio 2019 を開きます。

1. Visual Studio のメニュー バーから、 **[ツール]**  >  **[ツールと機能を取得]** の順に選択して Visual Studio インストーラーを開き、 **[変更中]** ダイアログ ボックスを表示します。

   ![[ワークロード] タブ、[変更中] ダイアログ ボックス、および Visual Studio インストーラーを示すスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. **[ワークロード]** タブの **[Windows]** で、 **[.NET によるモバイル開発]** ワークロードを見つけます。 そのワークロードの横のチェック ボックスが既にオンになっている場合は、 **[Modifying]\(変更中\)** ダイアログ ボックスを閉じて、手順 5. に進んでください。

1. **[.NET によるモバイル開発]** チェック ボックスをオンにし、 **[変更]** を選択します。 **[開始する前]** ダイアログ ボックスで **[続行]** を選択し、.NET によるモバイル開発ワークロードをインストールします。 新しい機能のインストールにはしばらく時間がかかります。

1. Visual Studio インストーラーを閉じます。

### <a name="create-the-project"></a>プロジェクトを作成する

1. Visual Studio メニュー バーで、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択して **[新しいプロジェクトの作成]** ウィンドウを表示します。

   ![Visual Studio で新しいプロジェクトを作成する方法を示すスクリーンショット。](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. **[モバイル アプリ (Xamarin Forms)]** を見つけて選択します。

1. **[次へ]** を選択して、 **[新しいプロジェクトの構成]** 画面を表示します。

   ![Visual Studio で新しいプロジェクトを構成する方法を示すスクリーンショット。](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. **[プロジェクト名]** に「*helloworld*」と入力します。

1. **[場所]** で、ご自分のプロジェクトの保存先フォルダーに移動して選択するか、フォルダーを作成します。

1. **[作成]** を選択して **[新しいモバイル アプリ Xamarin Forms プロジェクト]** ウィンドウに移動します。

   ![Visual Studio の [新しいモバイル アプリ Xamarin Forms プロジェクト] ダイアログ ボックスを示すスクリーンショット。](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. **[空白]** テンプレートを選択します。

1. **[プラットフォーム]** で、 **[Android]** 、 **[iOS]** 、および **[Windows (UWP)]** のチェック ボックスをオンにします。

1. **[OK]** を選択します。 Visual Studio IDE に戻ります。このとき、新しいプロジェクトが作成されて **[ソリューション エクスプローラー]** ウィンドウに表示された状態になっています。

   ![helloworld プロジェクト - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

次に、ターゲット プラットフォーム アーキテクチャとスタートアップ プロジェクトを選択します。 Visual Studio ツール バーの **[ソリューション プラットフォーム]** ドロップダウン ボックスを見つけます。 (表示されない場合は、 **[表示]**  >  **[ツール バー]**  >  **[標準]** の順に選択すると、 **[ソリューション プラットフォーム]** を含んだツール バーが表示されます)。64 ビット Windows を実行している場合は、ドロップダウン ボックスで **[x64]** を選択します。 64 ビット Windows では 32 ビット アプリケーションも実行できるため、必要に応じて **[x86]** を選択してもかまいません。 **[Start-up Projects]\(スタートアップ プロジェクト\)** ドロップダウン ボックスで、**helloworld.UWP (ユニバーサル Windows)** を設定します。

### <a name="install-the-speech-sdk"></a>Speech SDK のインストール

[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールし、自分のプロジェクト内で Speech SDK を参照します。

1. **ソリューション エクスプローラー** で、対象のソリューションを右クリックします。 **[ソリューションの NuGet パッケージの管理]** を選択して、 **[NuGet - ソリューション]** ウィンドウに移動します。

1. **[参照]** を選択します。

   ![Speech SDK をインストールする際の [ソリューションのパッケージの管理] ダイアログ ボックスのスクリーンショット。](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. **[パッケージ ソース]** で nuget.org を選択します。

1. **検索** ボックスに「*Microsoft.CognitiveServices.Speech*」と入力します。 このパッケージが検索結果に表示されたらそれを選択します。

   ![Microsoft.CognitiveServices.Speech パッケージが強調表示されているスクリーンショット。](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > `Microsoft.CognitiveServices.Speech` NuGet 内の iOS ライブラリでは、ビットコードが有効になっていません。 アプリケーションでビットコード ライブラリを有効にする必要がある場合は、特に iOS プロジェクト用に `Microsoft.CognitiveServices.Speech.Xamarin.iOS` NuGet を使用します。

1. 検索結果の横のパッケージ状態ウィンドウで、すべてのプロジェクトを選択します: **helloworld**、**helloworld.Android**、**helloworld.iOS**、および **helloworld.UWP**。

1. **[インストール]** を選択します。

1. **[変更のプレビュー]** ダイアログ ボックスで **[OK]** を選択します。

1. **[ライセンスへの同意]** ダイアログ ボックスでライセンスを確認し、 **[同意する]** を選択します。 すべてのプロジェクトに Speech SDK パッケージ参照をインストールします。 インストールが正常に完了すると、helloworld.iOS に対して次の警告が表示される場合があります。 これは既知の問題であり、アプリの機能に影響を与えることはありません。

   > 参照 "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a" を解決できませんでした。 この参照がコードで必要とされる場合、コンパイル エラーが発生する可能性があります。
