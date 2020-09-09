---
title: Azure Analysis Services サーバー名のエイリアス | Microsoft Docs
description: Azure Analysis Services サーバー名エイリアスを作成する方法について説明します。 ユーザーはサーバー名ではなく、短いエイリアス名でサーバーに接続できるようになります。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 435649c5431ff14461245fee88cebe4a2c571663
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361440"
---
# <a name="alias-server-names"></a>サーバー名のエイリアス

サーバー名のエイリアスを作成しておくと、ユーザーがサーバー名の代わりに短い*エイリアス*を使用して Azure Analysis Services サーバーに接続できます。 クライアント アプリケーションから接続する際、エイリアスは **link://** プロトコル形式を使用してエンドポイントとして指定されます。 エンドポイントは、アプリケーションが接続できるように実際のサーバー名を返します。

サーバー名のエイリアスには、次のようなメリットがあります。

- ユーザーに影響を与えずに、サーバー間の移行モデルを実行できます。 
- ユーザーが覚えやすいフレンドリ名をサーバーに設定できます。 
- 1 日のうちの時間帯ごとに異なるサーバーにユーザーを転送できます。 
- Azure Traffic Manager を使用する場合のように、異なるリージョンのユーザーを地理的に近いインスタンスに転送できます。 

有効な Azure Analysis Services サーバー名を返す任意の HTTP エンドポイントをエイリアスとして使用できます。 エンドポイントはポート 443 上で HTTPS をサポートする必要があり、このポートは URI で指定されていない必要があります。

![リンク形式を使用するエイリアス](media/analysis-services-alias/aas-alias-browser.png)

クライアントから接続する際、サーバー名のエイリアスは **link://** プロトコル形式を使用して入力されます。 たとえば、Power BI Desktop の場合は、次のようになります。

![Power BI Desktop の接続](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>エイリアスを作成する

エイリアス エンドポイントを作成するには、有効な Azure Analysis Services サーバー名を返す任意のメソッドを使用できます。 たとえば、実際のサーバー名が含まれた Azure Blob Storage 内のファイルを参照したり、ASP.NET Web フォーム アプリケーションを作成して発行したりできます。

この例では、ASP.NET Web フォーム アプリケーションが Visual Studio で作成されています。 ページの参照とユーザー コントロールは、Default.aspx ページから削除されています。 Default.aspx の内容は、次の Page ディレクティブだけです。

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Default.aspx.cs の Page_Load イベントによって、Response.Write() メソッドを使用して、Azure Analysis Services サーバー名が返されます。

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>関連項目

[クライアント ライブラリ](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)   
[Power BI Desktop からの接続](analysis-services-connect-pbi.md)
