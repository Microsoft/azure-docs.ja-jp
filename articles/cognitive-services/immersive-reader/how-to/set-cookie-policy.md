---
title: イマーシブ リーダーの Cookie ポリシーを設定する
titleSuffix: Azure Cognitive Services
description: この記事では、イマーシブ リーダーの Cookie ポリシーを設定する方法について説明します。
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.custom: devx-track-javascript
ms.openlocfilehash: 6bc55416c335f370151e54d2b09ccf00bd799fdb
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406896"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>イマーシブ リーダーの Cookie ポリシーを設定する方法

イマーシブ リーダーでは、Cookie の使用が既定では無効にされます。 Cookie の使用がユーザーによって有効にされた場合、イマーシブ リーダーは Cookie を使用して、ユーザーの基本設定を保存したり、機能の使用状況を追跡したりします。 イマーシブ リーダーで Cookie の使用を有効にする場合は、EU の Cookie コンプライアンス ポリシーの要件を考慮してください。 EU の Cookie コンプライアンス ポリシーに従って必要なユーザーの同意を取得するのは、ホスト アプリケーションの責任となります。

Cookie ポリシーは、イマーシブ リーダーの[オプション](../reference.md#options)で設定できます。

## <a name="enable-cookie-usage"></a>Cookie の使用を有効にする

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Cookie の使用を無効にする

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>次のステップ

* [Node.js クイックスタート](../quickstarts/client-libraries.md?pivots=programming-language-nodejs)で、Node.js とイマーシブ リーダー SDK を使用して他にできることを確認する
* [Android チュートリアル](../tutorial-android.md)を参照して、Android 用の Java または Kotlin を使用してイマーシブ リーダー SDK で他にできることを確認する
* [iOS チュートリアル](../tutorial-ios.md)を参照して、iOS 用の Swift を使用してイマーシブ リーダー SDK で他にできることを確認する
* [Python チュートリアル](../tutorial-python.md)で、Python と Immersive Reader SDK を使用して他にできることを確認する
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) と [Immersive Reader SDK リファレンス](../reference.md)を探索する