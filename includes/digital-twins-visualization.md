---
author: baanders
description: Azure Digital Twins のインクルード ファイル - Azure Digital Twins エクスプローラーを使用した視覚化
ms.service: digital-twins
ms.topic: include
ms.date: 7/10/2020
ms.author: baanders
ms.openlocfilehash: b637ef2be51ef2bbbe9fa7e1a2374927647ca382
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87061725"
---
## <a name="visualization"></a>グラフ

自分の Azure Digital Twins インスタンスは主に、[API と SDK](../articles/digital-twins/how-to-use-apis-sdks.md) を使用して操作しますが、自分のインスタンスで作成しているツインとグラフの視覚化を確認できると便利な場合があります。

[**Azure Digital Twins エクスプローラー**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)は、Azure Digital Twins を視覚化して操作するためのサンプル アプリケーションです。 サンプルにある手順に従って、アプリケーション コードをダウンロードして実行します。 

このサンプルでは、他に作成および管理アクティビティがある中、ご自分のデジタル ツインとそれによって作成されるグラフを視覚的に表示できます。

次に、視覚化の例を示します。

:::image type="content" source="../articles/digital-twins/media/includes/azure-digital-twins-explorer.png" alt-text="デジタル ツインを表すノードのグラフを示す Azure Digital Twins エクスプローラーのサンプル アプリケーションのスクリーンショット" lightbox="../articles/digital-twins/media/includes/azure-digital-twins-explorer.png":::

サンプルは次に使用できます。
* モデルのアップロードおよび確認
* ツイン グラフのアップロードと編集
* さまざまなレイアウト手法を使用したツイン グラフの視覚化
* ツインのプロパティの編集
* ツイン グラフに対するクエリの実行