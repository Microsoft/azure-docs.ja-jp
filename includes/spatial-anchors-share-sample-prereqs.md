---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 07/31/2020
ms.author: rgarcia
ms.openlocfilehash: 310c0f547ee11a3243589c364755a30a84be1a25
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810178"
---
## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

* 「[Azure Spatial Anchors の概要](../articles/spatial-anchors/overview.md)」を読んでいる。
* [5 分間のクイック スタート](../articles/spatial-anchors/index.yml)のいずれかを完了している。
* C# と Unity に関する基本的な知識。
* <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a> (Android を使用する場合) または <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a> (iOS を使用する場合) に関する基本的な知識。
* **ASP.NET および Web の開発**ワークロードと共に <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> 以降がインストールされている Windows コンピューター。
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)。
* アプリのデプロイと実行用の 1 つ以上のデバイス (iOS または Android)。
  * Android を使用している場合は、以下が必要です。
    * <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3</a> 以降、<a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a>、および <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> がインストールされている Windows コンピューター。
    * <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">開発者向け</a>の <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 対応</a> Android デバイス。
  * iOS を使用している場合は、以下が必要です。
    * <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a> 以降、<a href="https://cocoapods.org" target="_blank">CocoaPods</a>、および <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a> がインストールされている macOS コンピューター。
    * 開発者向けの <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit 対応</a> iOS デバイス。
    * Homebrew を介してインストールされた Git。 次のコマンドをターミナルに 1 行で入力します。`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` 次に、`brew install git` を実行します。
