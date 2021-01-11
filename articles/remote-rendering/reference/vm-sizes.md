---
title: サーバーのサイズ
description: 割り当てることができる個別のサーバー サイズについて説明します。
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: 8843f24f27f8973ad99989f743d1b3fae568679e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997151"
---
# <a name="server-sizes"></a>サーバーのサイズ

Azure Remote Rendering は、`Standard` と `Premium` の 2 つのサーバー構成で使用できます。

## <a name="polygon-limits"></a>多角形の制限

サーバーのサイズが `Standard` の Remote Rendering には、2,000 万多角形の最大シーン サイズがあります。 サイズが `Premium` の Remote Rendering に最大のハード制限は適用されませんが、コンテンツがサービスのレンダリング機能を超えた場合はパフォーマンスが低下する可能性があります。

'Standard' のサーバー サイズでレンダラーがこの制限に達すると、レンダリングがチェッカーボードの背景に切り替えられます。

![チェッカーボード](media/checkerboard.png)

## <a name="specify-the-server-size"></a>サーバーのサイズを指定する

レンダリング セッションの初期化時に、望ましいサーバー構成の種類を指定する必要があります。 実行中のセッション内では変更できません。 次のコード例は、サーバーのサイズを指定する必要がある場所を示しています。

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

[PowerShell スクリプトの例](../samples/powershell-example-scripts.md)では、`arrconfig.json` ファイル内で望ましいサーバーのサイズを指定する必要があります。

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>レンダラーによる多角形の数の評価方法

制限テストで考慮される多角形の数は、実際にレンダラーに渡される多角形の数です。 このジオメトリは、通常、インスタンス化されたすべてのモデルの合計ですが、例外もあります。 次のジオメトリは**含まれません**。
* ビューの視錐台の完全に外側にある読み込まれたモデル インスタンス。
* [階層状態のオーバーライド コンポーネント](../overview/features/override-hierarchical-state.md)を使用して非表示に切り替えられたモデルまたはモデル パーツ。

したがって、すべての単一モデルに上限に近い数の多角形が含まれる複数のモデルが読み込まれる、`standard` サイズを対象とするアプリケーションを作成することができます。 アプリケーションで一度に 1 つのモデルのみが表示される場合、チェッカーボードはトリガーされません。

### <a name="how-to-determine-the-number-of-polygons"></a>多角形の数を確認する方法

`standard` の構成サイズの予算制限に寄与するモデルまたはシーンの多角形の数を確認するには、次の 2 つの方法があります。
* モデル変換側で、[変換出力 json ファイル](../how-tos/conversion/get-information.md)を取得し、[*inputStatistics* セクション](../how-tos/conversion/get-information.md#the-inputstatistics-section)の `numFaces` エントリを調べます
* アプリケーションで動的なコンテンツが処理されている場合、レンダリングされるポリゴンの数は実行時に動的に照会できます。 [パフォーマンス評価クエリ](../overview/features/performance-queries.md#performance-assessment-queries)を使用し、`FrameStatistics` 構造体の `polygonsRendered` メンバーを調べます。 レンダラーが多角形の制限に達した場合、`polygonsRendered` フィールドは `bad` に設定されます。 この非同期クエリの後でユーザーが操作を実行できるようにするため、チェッカーボードの背景のフェードインでは常に遅延が発生します。 たとえば、ユーザーは、モデルのインスタンスを非表示にしたり削除したりすることができます。

## <a name="pricing"></a>価格

構成の種類ごとの価格の詳細な内訳については、「[Remote Rendering の価格](https://azure.microsoft.com/pricing/details/remote-rendering)」のページを参照してください。

## <a name="next-steps"></a>次のステップ
* [PowerShell スクリプトの例](../samples/powershell-example-scripts.md)
* [モデルの変換](../how-tos/conversion/model-conversion.md)

