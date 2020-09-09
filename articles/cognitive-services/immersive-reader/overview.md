---
title: イマーシブ リーダー API とは
titleSuffix: Azure Cognitive Services
description: イマーシブ リーダー API は、学習障碍者に対応したり、新しい読者や言語学習者をサポートしたりするために使用できるツールです。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: b9efe70e8658e25d61decffbe44dec776890b17b
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267275"
---
# <a name="what-is-immersive-reader"></a>Immersive Reader とは

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

[イマーシブ リーダー](https://www.onenote.com/learningtools)は、新しい読者、言語学習者、ディスレクシア (失読症) などの学習障碍者の読解力向上のために実証済みの手法を実装する、包括的に設計されたツールです。

Immersive Reader は、Immersive Reader SDK を使用して Web アプリケーションで使用することができます。

## <a name="what-does-immersive-reader-do"></a>イマーシブ リーダーの機能

イマーシブ リーダーは、あらゆるユーザーの読む力を支援するように設計されています。

* 最小限の閲覧ビューでコンテンツを表示する

  ![Immersive Reader](./media/immersive-reader.png)

* 一般的な単語の画像を表示する

  ![絵辞書](./media/picture-dictionary.png)

* 名詞、動詞、形容詞、副詞を強調表示する

  ![品詞](./media/parts-of-speech.png)

* コンテンツを読み上げる

  ![音声読み上げ](./media/read-aloud.png)

* コンテンツを別の言語に翻訳する

  ![翻訳](./media/translation.png)

* 単語を音節に分割する

  ![分節法](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>イマーシブ リーダーのしくみ

イマーシブ リーダーはスタンドアロンの Web アプリです。Immersive Reader JavaScript SDK を使用して呼び出すと、`iframe` を介して既存の Web アプリ上に表示されます。 イマーシブ リーダーを起動する API を呼び出すときに、イマーシブ リーダーで表示するコンテンツを指定します。 SDK では、`iframe` の作成とスタイル設定のほか、品詞、テキスト読み上げ、翻訳などのコンテンツ処理を行うイマーシブ リーダーのバックエンド サービスとの通信が行われます。

## <a name="next-steps"></a>次のステップ

Immersive Reader を使ってみましょう。

* [クイックスタート](./quickstarts/client-libraries.md?pivots=programming-language-csharp)を始める
* [GitHub 上の Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) を探索する
* [Immersive Reader SDK リファレンス](./reference.md)を読む
