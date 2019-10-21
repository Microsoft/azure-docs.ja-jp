---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 10/02/2019
ms.custom: include file
ms.openlocfilehash: f67f271ee02e38e168ddfaa84ed1309321cf8702
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949114"
---
> [!NOTE]
> マルチパート要求には、一般的に次の 3 つの情報が必要です。
> * **Content-Type** ヘッダー:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * **Content-Disposition**:
>   * `form-data; name="metadata"`
> * アップロードするファイルの内容
>
> **Content-Type** と **Content-Disposition** は使用シナリオによって異なる場合があります。

マルチパート要求は、プログラム (C# を使用)、REST クライアント、または [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request) などのツールを使用して行うことができます。 REST クライアント ツールには、複雑なマルチパート要求をサポートするさまざまなレベルがある場合があります。 構成設定はツールによって若干異なる場合もあります。 どのツールがニーズに最適であるかを確認してください。

> [!IMPORTANT]
> Azure Digital Twins Management API に対して行われるマルチパート要求には、一般的に 2 つの部分があります。
> * **Content-Type** および **Content-Disposition** で宣言される (関連付けられている MIME の種類などの) BLOB メタデータ
> * アップロードされるファイルの非構造化コンテンツを含む BLOB コンテンツ
>
> 2 つの部分のどちらも **PATCH** 要求には不要です。 どちらも **POST** または作成操作に必要です。

[占有率のクイック スタートのソース コード](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs)には、Azure Digital Twins Management API に対するマルチパート要求を行う方法を示す完全な C# の例が示されています。