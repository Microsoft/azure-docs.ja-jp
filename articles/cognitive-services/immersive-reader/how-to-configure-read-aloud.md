---
title: 音声読み上げを構成する
titleSuffix: Azure Cognitive Services
description: この記事では、音声読み上げのさまざまなオプションを構成する方法について説明します。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 648227521e5e4e8feecd864d3e572a4758e551ca
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633265"
---
# <a name="how-to-configure-read-aloud"></a>音声読み上げを構成する方法

この記事では、イマーシブ リーダーで音声読み上げのさまざまなオプションを構成する方法について説明します。

## <a name="automatically-start-read-aloud"></a>自動的に音声読み上げを開始する

`options` パラメーターには、音声読み上げを構成するために使用できるすべてのフラグが含まれています。 `autoplay` を `true` に設定して、イマーシブ リーダーを起動した後に自動的に音声読み取りが開始されるようにします。

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> ブラウザーの制限により、Safari では自動再生はサポートされていません。

## <a name="configure-the-voice"></a>音声を構成する

`voice` を `male` または `female` のいずれかに設定します。 すべての言語で両方の音声がサポートされているわけではありません。 詳細については、[言語サポート](./language-support.md)に関するページを参照してください。

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>再生速度を構成する

`speed` を `0.5` (50%) と `2.5` (250%) の間の数値 (これらの値を含む) に設定します。 この範囲外の値は、0.5 または 2.5 に固定されます。

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>次のステップ

* [イマーシブ リーダー SDK リファレンス](./reference.md)を参照する