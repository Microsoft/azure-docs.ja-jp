---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 6b16dea3c4f9241133b91b092c90c9056da57de0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100514922"
---
### <a name="standard-and-neural-voices"></a>標準およびニューラル音声

このテーブルを使用して、リージョン/エンドポイントによる標準およびニューラル音声の可用性を決定します。

| リージョン | エンドポイント | ニューラル音声 | 標準音声 |
|--------|----------|-----------------|---------------|
| オーストラリア東部 | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| ブラジル南部 | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | いいえ | はい |
| カナダ中部 | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 米国中部 | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | いいえ | はい |
| 東アジア | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | いいえ | はい |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 米国東部 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | いいえ | はい |
| フランス中部 | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | いいえ | はい |
| インド中部 | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 東日本 | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | いいえ | はい |
| 西日本 | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | いいえ | はい |
| 韓国中部 | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | いいえ | はい |
| 米国中北部 | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | いいえ | はい |
| 北ヨーロッパ | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | いいえ | はい |
| 米国中南部 | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 東南アジア | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 英国南部 | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 西ヨーロッパ | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 米国西部 | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | いいえ | はい |
| 米国西部 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |

> [!TIP]
> [プレビュー段階の音声](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview)は 3 つのリージョン (米国東部、西ヨーロッパ、東南アジア) でのみ使用できます。

### <a name="custom-voices"></a>カスタム音声

カスタム音声フォントを作成した場合は、作成したエンドポイントを使用します。 また、以下の一覧に示したエンドポイントを使用して、`{deploymentId}` を音声モデル用のデプロイ ID に置き換えます。

| リージョン | エンドポイント |
|--------|----------|
| オーストラリア東部 | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| ブラジル南部 | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| カナダ中部 | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国中部 | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 東アジア | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国東部 | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国東部 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| フランス中部 | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| インド中部 | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 東日本 | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 西日本 | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 韓国中部 | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国中北部 | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 北ヨーロッパ | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国中南部 | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 東南アジア | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 英国南部 | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 西ヨーロッパ | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国西部 | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国西部 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |

### <a name="custom-neural-voice"></a>カスタム ニューラル音声

次の表は、カスタム ニューラル音声機能のリージョン サポートの詳細を示しています。

| 機能 | サポートされているリージョン |
|---|---|
| 音声モデル ホスティング | 米国東部、米国西部 2、米国中南部、東南アジア、英国南部、西ヨーロッパ、オーストラリア東部 |
| リアルタイムの文字 | 米国東部、米国西部 2、米国中南部、東南アジア、英国南部、西ヨーロッパ、オーストラリア東部 |
| 文字数 (長い音声出力) | 米国東部、西ヨーロッパ、英国南部、東南アジア、インド中部 |
| カスタム ニューラル トレーニング | 米国東部、英国南部 |

### <a name="long-audio-api"></a>Long Audio API

Long Audio API は、一意のエンドポイントを持つ複数のリージョンで使用できます。

| リージョン | エンドポイント |
|--------|----------|
| 米国東部 | `https://eastus.customvoice.api.speech.microsoft.com` |
| インド中部 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 東南アジア | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 英国南部 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 西ヨーロッパ | `https://westeurope.customvoice.api.speech.microsoft.com` |
