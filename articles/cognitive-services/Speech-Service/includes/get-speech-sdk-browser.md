---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: ab5a106d077713c1332bfb1ba4608d9410086c9f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186277"
---
:::row:::
    :::column span="3":::
        JavaScript 用 Speech SDK は npm パッケージとして提供されています。<a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span></a> とそれに付随する GitHub リポジトリ <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js <span class="docon docon-navigate-external x-hidden-focus"></span></a> を参照してください。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> JavaScript 用 Speech SDK は npm パッケージとして提供されており、クライアントの Web ブラウザーと Node.js の両方で使用できますが、各環境のさまざまなアーキテクチャの影響を考慮してください。 例えば、<a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">ドキュメントオブジェクトモデル (DOM)<span class="docon docon-navigate-external x-hidden-focus"></span></a>は、<a href="https://nodejs.org/api/fs.html" target="_blank">ファイルシステム<span class="docon docon-navigate-external x-hidden-focus"></span></a>がクライアント側アプリケーションで使用できない場合と同様に、サーバー側アプリケーションでは使用できません。

### <a name="nodejs-package-manager-npm"></a>Node.js パッケージマネージャー (NPM)

JavaScript 用 Speech SDK をインストールするには、次の `npm install` コマンドを実行します。

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML スクリプトタグ

または、HTMLs`<head>`要素に`<script>`タグを直接含めることもできます。この場合,<a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">**JSDelivr**NPM シンジケート に依存します<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

詳細については、<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">Web ブラウザー音声SDK のクイックスタート を参照してください <span class="docon docon-navigate-external x-hidden-focus"></span></a>。
