---
title: チュートリアル:新しい HoloLens Unity アプリを作成する
description: このチュートリアルでは、Azure Spatial Anchors を使用して新しい HoloLens Unity アプリを作成する方法について説明します。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 06/22/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ee8b8c2931d006dbb3d472b545030d3aff79c56a
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2020
ms.locfileid: "85297989"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>チュートリアル:Azure Spatial Anchors を使用して新しい HoloLens Unity アプリを作成する詳細な手順

このチュートリアルでは、Azure Spatial Anchors を使用して新しい HoloLens Unity アプリを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

1. <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 以降</a>、**ユニバーサル Windows プラットフォーム開発**ワークロード、**Windows 10 SDK (10.0.18362.0 以降)** コンポーネント、および <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> がインストールされた Windows マシン。
2. [Visual Studio Marketplace](https://marketplace.visualstudio.com/) から Visual Studio 用の [C++/WinRT Visual Studio 拡張機能 (VSIX)](https://aka.ms/cppwinrt/vsix) をインストールする必要があります。
3. [開発者モード](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio)が有効になっている HoloLens デバイス。 この記事では、[Windows 10 October 2018 Update](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (別名 RS5) が適用された HoloLens デバイスが必要です。 HoloLens を最新のリリースに更新するには、 **[設定]** アプリを開き、 **[更新とセキュリティ]** を選択し、 **[更新プログラムの確認]** ボタンをクリックします。

## <a name="getting-started"></a>作業の開始

最初に、プロジェクトと Unity シーンを設定します。
1. Unity を起動します。
2. **[新規]** を選択します。
4. **[3D]** が選択されていることを確認します。
5. プロジェクトに名前を付け、保存**場所**を入力します。
6. **[プロジェクトの作成]** をクリックします。
7. 空の既定のシーンを新しいファイルに保存します (使用するのは **[File]\(ファイル\)**  >  **[Save As]\(名前を付けて保存\)** )。
8. 新しいシーンに「**Main**」という名前を付け、 **[Save]\(保存\)** をクリックします。

**プロジェクト設定をセットアップする**

次に、開発で Windows Holographic SDK をターゲットにするのに役立つ、Unity プロジェクトの設定をいくつか行います。

まず、アプリケーションの品質設定を行いましょう。
1. **[Edit]\(編集\)**  >  **[Project Settings]\(プロジェクト設定\)**  >  **[Quality]\(品質\)** の順に選択します。
2. **Windows ストア** ロゴの下の列で、 **[Default]\(既定値\)** 行の矢印をクリックし、 **[Very Low]\(非常に低い\)** を選択します。 **[Windows Store]\(Windows ストア\)** 列のボックスと **[Very Low]\(非常に低い\)** 行が緑色の場合、設定が適切に適用されていることがわかります。

エクスポートしようとしているアプリでは 2D ビューではなく没入型ビューを作成することを Unity に伝える必要があります。 Windows 10 SDK に対して Unity でバーチャル リアリティのサポートを有効にし、没入型ビューを作成します。

1. **[Edit]\(編集\)**  >  **[Project Settings]\(プロジェクト設定\)**  >  **[Player]\(プレーヤー\)** の順に移動します。
2. **[Player Settings]\(プレーヤー設定\)** の **[Inspector]\(インスペクター\) パネル**で、**Windows ストア** アイコンを選択します。
3. **[XR Settings]\(XR 設定\)** グループを展開します。
4. **[Rendering]\(レンダリング\)** セクションで、 **[Virtual Reality Supported]\(バーチャル リアリティ サポート\)** チェック ボックスをオンにして、新しい**バーチャル リアリティ SDK** の一覧を追加します。
5. **[Windows Mixed Reality]** が一覧に表示されていることを確認します。 されていない場合は、一覧の下部にある **[+]** ボタンを選択し、 **[Windows Mixed Reality]** を選択します。

> [!NOTE]
> Windows ストア アイコンが表示されない場合は、インストールの前に Windows ストア .NET スクリプト バックエンドを選択したことをもう一度確認します。 していない場合は、適切な Windows インストールを使用して、Unity を再インストールする必要があります。

**スクリプト バックエンド構成を検証する**
1. **[Edit]\(編集\)**  >  **[Project Settings]\(プロジェクト設定\)**  >  **[Player]\(プレーヤー\)** の順に移動します ( **[Player]\(プレーヤー\)** は、前の手順から開いたままになっている場合があります)。
2. **[Player Settings]\(プレーヤー設定\)** の **[Inspector]\(インスペクター\) パネル**で、**Windows ストア** アイコンを選択します。
3. **[Other Settings]\(その他の設定\)** 構成セクションで、 **[Scripting Backend]\(スクリプト バックエンド\)** が **[IL2CPP]** に設定されていることを確認します。

**機能を設定する**
1. **[Edit]\(編集\)**  >  **[Project Settings]\(プロジェクト設定\)**  >  **[Player]\(プレーヤー\)** の順に移動します ( **[Player]\(プレーヤー\)** は、前の手順から開いたままになっている場合があります)。
2. **[Player Settings]\(プレーヤー設定\)** の **[Inspector]\(インスペクター\) パネル**で、**Windows ストア** アイコンを選択します。
3. **[Publishing Settings]\(公開の設定\)** 構成セクションで、 **[InternetClientServer]** と **[SpatialPerception]** をオンにします。

**メインの仮想カメラを設定する**
1. **[Hierarchy]\(階層\) パネル**で、 **[Main Camera]\(メイン カメラ\)** を選択します。
2. **[Inspector]\(インスペクター\)** で、変換座標を **0、0、0** に設定します。
3. **[Clear Flags]\(フラグをクリア\)** プロパティを探して、ドロップダウンを **[Skybox]\(スカイボックス\)** から **[Solid Color]\(ソリッド カラー\)** に変更します。
4. **[Background]\(背景\)** フィールドをクリックして、カラー ピッカーを開きます。
5. **R、G、B、A** を **0** に設定します。
6. **[コンポーネントの追加]** を選択し、**Spatial Mapping Collider** を追加します。

**スクリプトを作成する**
1. **[Project]\(プロジェクト\)** ウィンドウで、**Assets** フォルダーの下に新しいフォルダー (**Scripts**) を作成します。
2. そのフォルダーを右クリックして、 **[Create]\(作成\) >** **[C# Script]\(C# スクリプト\)** の順に選択します。 タイトルは「**AzureSpatialAnchorsScript**」にします。
3. **[GameObject]\(ゲームオブジェクト\)**  ->  **[Create Empty]\(空のオブジェクトの作成\)** の順に移動します。
4. それを選択し、 **[Inspector]\(インスペクター\)** でその名前を **GameObject** から **MixedRealityCloud** に変更します。 **[Add Component]\(コンポーネントの追加\)** を選択し、**AzureSpatialAnchorsScript** を検索して追加します。

**球のプレハブを作成する**
1. **[GameObject]**  ->  **[3D オブジェクト]**  ->  **[Sphere]\(球\)** に移動します。
2. **[インスペクター]** で、そのスケールを **0.25、0.25、0.25** に設定します。
3. **[階層]** ウィンドウで**球**オブジェクトを見つけます。 それをクリックして、 **[プロジェクト]** ウィンドウの **[資産]** フォルダーにドラッグします。
4. 右クリックして、 **[階層]** ウィンドウで作成した元の球を**削除**します。

**[プロジェクト]** ウィンドウに球のプレハブが作成されているのがわかります。

## <a name="trying-it-out"></a>試してみる
問題ないことをテストするために、**Unity** でアプリをビルドし、**Visual Studio** でそれをデプロイします。 そのためには、「[**MR Basics 100: Getting started with Unity (MR の基本 100: Unity の概要)」** コース](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio)の 6 章に従ってください。 Unity のスタート画面、そしてクリアな表示が示されます。

## <a name="place-an-object-in-the-real-world"></a>現実世界でのオブジェクトの配置
アプリを使用してオブジェクトを作成し、配置しましょう。 [アプリのデプロイ](#trying-it-out)時に作成した Visual Studio ソリューションを開きます。

まず、以下の import を `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs` に追加します。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

それから、次のメンバー変数を `AzureSpatialAnchorsScript` クラスに追加します。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-47,53-57,65-84)]

続行する前に、spherePrefab メンバー変数で作成した球のプレハブを設定する必要があります。 **[Unity]** に戻ります。
1. **[Unity]** の **[階層]** ウィンドウで **[MixedRealityCloud]** オブジェクトを選択します。
2. **[プロジェクト]** ウィンドウに保存した**球**のプレハブをクリックします。 **[インスペクター]** ウィンドウで、 **[Azure Spatial Anchors (スクリプト)]** の下の **Sphere Prefab** 領域にクリックした **Sphere** をドラッグします。

これで**球**がスクリプト上のプレハブとして設定されます。 **Unity** からビルドし、結果の **Visual Studio** ソリューションを[試してみる](#trying-it-out)で行ったのと同じように再度開きます。

**Visual Studio** で、`AzureSpatialAnchorsScript.cs` を再度開きます。 次のコードを `Start()` メソッドに追加します。 このコードでは `GestureRecognizer` をフックします。これは、エア タップが行われたことを検出して `HandleTap` を呼び出します。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-95,98&highlight=4-10)]

ここで、次の `HandleTap()` メソッドを `Update()` の下に追加する必要があります。 これにより、レイ キャストを実行し、球体を配置するヒット ポイントを取得します。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-283,305-306,310-318)]

ここで、球体を作成する必要があります。 球体は最初白になります。しかし、この値は後で調整します。 次の `CreateAndSaveSphere()` メソッドを追加します。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-331,396)]

**Visual Studio** で自分のアプリを実行して、もう一度検証します。 今回は、画面をタップして白い球体を作成し、任意のサーフェス上に配置します。

## <a name="set-up-the-dispatcher-pattern"></a>ディスパッチャー パターンの設定

Unity を使用する際には、Unity API (UI を更新するために使用する API など) はすべて、メイン スレッドで実行される必要があります。 しかし、これから記述するコードでは、他のスレッドでコールバックを取得します。 これらのコールバックで UI を更新したいため、サイド スレッドからメイン スレッドに移動する手段が必要です。 サイド スレッドからメイン スレッド上でコードを実行するには、ディスパッチャー パターンを使用します。

メンバー変数 dispatchQueue を追加してみましょう。これはアクションのキューです。 アクションをキューにプッシュしてからデキューし、そのアクションをメイン スレッドで実行します。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-56&highlight=6-9)]

次に、アクションをキューに追加する方法を追加しましょう。 `QueueOnUpdate()` を `Update()` の直後に追加します。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=112-122)]

次は Update() ループを使用して、キューに入れられたアクションがあるかどうかを確認しましょう。 ある場合は、そのアクションをデキューして実行します。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=100-110&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Azure Spatial Anchors SDK の取得

## <a name="via-unity-package"></a>[Unity パッケージ経由](#tab/UnityPackage)

次に Azure Spatial Anchors SDK をダウンロードします。 [GitHub の Azure Spatial Anchors リリース ページ](https://github.com/Azure/azure-spatial-anchors-samples/releases)に移動します。 [Assets]\(アセット\) で、**AzureSpatialAnchors.unitypackage** をダウンロードします。 Unity で **[Assets]\(アセット\)** に移動し、 **[Import Package]\(パッケージのインポート\)**  >  **[Custom Package...]\(カスタム パッケージ\)** の順にクリックします。パッケージに移動して、 **[Open]\(開く\)** を選択します。

表示される新しい **[Import Unity Package]\(Unity パッケージのインポート\)** ウィンドウで、 **[Plugins]\(プラグイン\)** の選択を解除し、右下隅の **[Import]\(インポート\)** をクリックします。

Azure Spatial Anchors SDK を取得するために、ここで Nuget パッケージを復元する必要があります。 **Unity** でビルドし、その結果生成された **Visual Studio** ソリューションを開いてもう一度ビルドします (詳細は「[試してみる](#trying-it-out)」を参照)。

## <a name="via-nugetforunity"></a>[NuGetForUnity 経由](#tab/NuGetForUnity)

まず NuGetForUnity をインストールする必要があります。 [GitHub の NuGetForUnity リリース ページ](https://github.com/GlitchEnzo/NuGetForUnity/releases)に移動します。 [Assets]\(資産\) で、最新の **NuGetForUnity.unitypackage** をダウンロードします。 Unity で **[Assets]\(アセット\)** に移動し、 **[Import Package]\(パッケージのインポート\)**  >  **[Custom Package...]\(カスタム パッケージ\)** の順にクリックします。パッケージに移動して、 **[Open]\(開く\)** を選択します。 Unity によって NugetForUnity がインストールされます。 Unity に新しい **NuGet** ドロップダウンが表示されない場合は、必要に応じて **[Projects]\(プロジェクト\)**  >  **[Assets]\(資産\)** を右クリックします。 次に、 **[Reimport All]\(すべてを再インポート\)** を選択します。

NuGetForUnity をインストールしたら、 **[NuGet]**  >  **[Manage NuGet Packages]\(NuGet パッケージの管理\)** を選択します。 Microsoft.Azure.SpatialAnchors.Unity を検索し、 **[Install]\(インストール\)** を選択します。

ダウンロードしたばかりの NuGet パッケージにはヘルパー スクリプトが含まれているだけなので、実際の Azure Spatial Anchors SDK を取得するにはビルドする必要があります。 **Unity** でビルドし、その結果生成された **Visual Studio** ソリューションを開いてもう一度ビルドします (詳細は「[試してみる](#trying-it-out)」を参照)。

---

自分の **Visual Studio** ソリューションで、次のインポートを `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs` に追加します。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

続いて、以下のメンバー変数を `AzureSpatialAnchorsScript` クラスに追加します。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=53-68&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>ローカル アンカーへのローカル Azure 空間アンカーのアタッチ

Azure Spatial Anchor の CloudSpatialAnchorSession を設定しましょう。 まず、次の `InitializeSession()` メソッドを `AzureSpatialAnchorsScript` クラス内に追加します。 これが呼び出されると、Azure Spatial Anchors セッションが作成され、アプリの起動時に適切に初期化されます。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=179-208,211-215)]

ここで、デリゲート呼び出しを処理するコードを記述する必要があります。 引き続き、これらにさらに追加していきます。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=217-232)]

次に、`initializeSession()` メソッドを `Start()` メソッドにフックしましょう。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-98&highlight=12)]

最後に、以下のコードを `CreateAndSaveSphere()` メソッドに追加します。 これにより、現実世界に配置している球体に、ローカル Azure 空間アンカーがアタッチされます。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-344,396&highlight=14-25)]

先に進む前に、アカウント識別子、キー、ドメインがまだない場合は、Azure Spatial Anchors アカウントを作成してこれらを取得する必要があります。 次のセクションに従ってこれらを取得します。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>クラウドへのローカル アンカーのアップロード

Azure Spatial Anchors アカウント識別子、キー、ドメインを作成したら、先に進んで `Account Id` を `SpatialAnchorsAccountId` に、`Account Key` を `SpatialAnchorsAccountKey` に、`Account Domain` を `SpatialAnchorsAccountDomain` に貼り付けます。

最後に、すべてをつなげましょう。 `SpawnNewAnchoredObject()` メソッドに次のコードを追加します。 これにより、球体が作成されるとすぐに `CreateAnchorAsync()` メソッドが呼び出されます。 メソッドから戻ると、次のコードによって球体への最終的な更新が実行され、色が青色に変わります。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-397&highlight=26-77)]

**Visual Studio** で自分のアプリをもう一度実行します。 頭を動かしてみてから、エア タップして球体を配置します。 十分なフレームを取得すると、球体が黄色に変わり、クラウドのアップロードが開始されます。 アップロードの完了後に、球体は青色になります。 必要に応じて、**Visual Studio** 内の出力ウィンドウを使用し、アプリによって送信されているログ メッセージを監視することもできます。 作成の進行状況に関する推奨事項のほか、アップロード完了時にクラウドから返されるアンカー識別子を確認できるようになります。

> [!NOTE]
> "DllNotFoundException: Unable to load DLL 'AzureSpatialAnchors': The specified module could not be found. (DllNotFoundException: DLL 'AzureSpatialAnchors' を読み込めません: 指定されたモジュールが見つかりません)" が表示される場合、ソリューションを**削除**してもう一度**ビルド**する必要があります。

## <a name="locate-your-cloud-spatial-anchor"></a>クラウド空間アンカーの配置

アンカーがクラウドにアップロードされると、それを配置する準備ができています。 次のコードを `HandleTap()` メソッドに追加しましょう。 このコードでは、次のことが行われます。

* `ResetSession()` を呼び出します。これは `CloudSpatialAnchorSession` を停止して、既存の青い球体を画面から削除します。
* `CloudSpatialAnchorSession` をもう一度初期化します。 これにより、配置しようとしているアンカーは、作成したローカル アンカーではなくクラウドから確実に取得されます。
* Azure Spatial Anchors にアップロードしたアンカーを検索する**ウォッチャー**を作成します。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-311&highlight=13-31,35-36)]

次に `ResetSession()` および `CleanupObjects()` メソッドを追加しましょう。 これらは `QueueOnUpdate()` の下に配置できます。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=124-177)]

ここで、クエリの実行対象であるアンカーが配置されたときに呼び出されるコードをフックする必要があります。 `InitializeSession()` 内に、次のコールバックを追加します。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-212&highlight=4-5)]


次に、CloudSpatialAnchor が配置されると緑色の球体を作成して配置するコードを追加しましょう。 これにより、画面をもう一度タップできるようになるので、シナリオ全体をもう一度繰り返すことができます。別のローカル アンカーを作成し、アップロードして、もう一度配置します。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=234-271)]

これで完了です。 最後にもう一度 **Visual Studio** でアプリを実行し、シナリオ全体をエンドツーエンドで試します。 お使いのデバイスを動かしてみて、白い球体を配置します。 次に、球体が黄色に変わるまで頭を動かし続けて環境データをキャプチャします。 ローカル アンカーがアップロードされ、球体が青色に変わります。 最後に、ローカル アンカーが削除されるように画面をもう一度タップしてから、対応するクラウド アンカーに対してクエリを実行します。 クラウド空間アンカーが配置されるまで、デバイスの移動を続けます。 正しい場所に緑色の球体が表示され、シナリオ全体をもう一度繰り返すことができます。

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]