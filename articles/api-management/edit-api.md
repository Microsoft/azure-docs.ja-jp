---
title: Azure Portal を使用した API の編集 | Microsoft Docs
description: API Management (APIM) を使用して API を編集する方法について説明します。 APIM インスタンスで操作を追加、削除、名前変更したり、API の Swagger を編集したりします。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 8d3f476f4a6ecf05d63223f3ba2c2b5173e1d0bf
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905161"
---
# <a name="edit-an-api"></a>API の編集

このチュートリアルの手順では、API Management (APIM) を使用して API を編集する方法を示します。 

+ これは、APIM インスタンスで操作を追加、削除、名前変更することで行うことができます。 
+ API の Swagger を編集することができます。

## <a name="prerequisites"></a>前提条件

+ [Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
+ [最初の API をインポートして発行する](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>APIM で API を編集する

![API の編集](./media/edit-api/edit-api001.png)

1. **[API]** タブをクリックします。
2. 前にインポートした API の 1 つを選びます。
3. **[デザイン]** タブを選択します。
4. 編集する操作を選びます。
5. 操作の名前を変更するには、**[フロントエンド]** ウィンドウの**鉛筆**を選択します。

## <a name="update-the-swagger"></a>Swagger を更新する

次の手順に従って、Azure Portal からバックエンド API を更新できます。

1. **[すべての操作]** を選択します
2. **[フロントエンド]** ウィンドウの鉛筆をクリックします。

    ![API の編集](./media/edit-api/edit-api002.png)

    API の Swagger が表示されます。

    ![API の編集](./media/edit-api/edit-api003.png)

3. Swagger を更新します。
4. **[保存]** をクリックします。

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [APIM ポリシーのサンプル](policy-samples.md)
> [発行された API の変換と保護](transform-api.md)