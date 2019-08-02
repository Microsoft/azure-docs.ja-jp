---
title: Custom Speech Service から Speech Service に移行する
titleSuffix: Azure Cognitive Services
description: Custom Speech Service は Speech Service の一部になっています。 Speech Service に切り替えると、最新の品質と機能の更新のベネフィットがあります。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 01b853c59723a8ed79cb32b0ee9c245c9c3ffb3f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562761"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Custom Speech Service から Speech Service に移行する

アプリケーションを Custom Speech Service から Speech Service に移行するには、この記事を参考にしてください。

Custom Speech Service は Speech Service の一部になっています。 Speech Services に切り替えると、品質と機能の最新の更新によるベネフィットがあります。

## <a name="migration-for-new-customers"></a>新しいお客様の移行

Speech Service の価格モデルは時間ベースになっており、より単純です。  

1. お使いのアプリケーションが使用可能なリージョンごとに Azure リソースを作成します。 Azure リソース名は **Speech** です。 同じリージョン内では、以下のサービスについて、個別のリソースを作成する代わりに、1 つの Azure リソースを使用できます。

    * 音声テキスト変換
    * カスタム音声テキスト変換
    * テキスト読み上げ
    * 音声翻訳

2. [Speech SDK](speech-sdk.md) をダウンロードします｡

3. クイック スタート ガイドと SDK サンプルに従って、適切な API を使用します。 REST API を使用する場合は、正しいエンドポイントとリソース キーを使用する必要もあります。

4. Speech Services と API を使用するようにクライアント アプリケーションを更新します。

## <a name="migration-for-existing-customers"></a>既存のお客様の移行

Speech Services ポータルで Speech Services にご自分の既存のリソース キーを移行します。 次の手順に従います。

> [!NOTE]
> リソース キーは、同じリージョン内でのみ移行できます。

1. [cris.ai](https://cris.ai/Home/CustomSpeech) ポータルにサインインし、右上のメニューでサブスクリプションを選択します。

2. **[Migrate selected subscription]\(選択したサブスクリプションの移行\)** を選択します。

3. テキスト ボックスにサブスクリプション キーを入力し、 **[Migrate]\(移行\)** を選択します。

## <a name="next-steps"></a>次の手順

* [Speech Services を無料で試す](get-started.md)
* [Speech to Text API](./speech-to-text.md) の概念を学習する

## <a name="see-also"></a>関連項目

* [Speech Service とは](overview.md)
* [Speech Services と Speech SDK のドキュメント](speech-sdk.md#get-the-sdk)
