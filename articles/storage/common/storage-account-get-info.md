---
title: .NET を使用してストレージ アカウントの種類と SKU 名を取得する
titleSuffix: Azure Storage
description: .NET クライアント ライブラリを使用して Azure Storage アカウントの種類と SKU 名を取得する方法について説明します。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 8fa1e258b07ab98040cbbc5217be789e0bb1b783
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020135"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>.NET を使用してストレージ アカウントの種類と SKU 名を取得する

この記事では、[.NET 用 Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage?view=azure-dotnet)を使用して、BLOB の Azure Storage アカウントの種類と SKU 名を取得する方法について説明します。

アカウント情報は、バージョン 2018-03-28 以降のサービス バージョンで使用できます。

## <a name="about-account-type-and-sku-name"></a>アカウントの種類と SKU 名の概要

**アカウントの種類**:有効なアカウントの種類には、`BlobStorage`、`BlockBlobStorage`、`FileStorage`、`Storage`、および `StorageV2` があります。 「[Azure ストレージ アカウントの概要](storage-account-overview.md)」には、さまざまなストレージ アカウントの説明などの詳細情報が記載されています。

**SKU 名**:有効な SKU 名 には、`Premium_LRS`、`Premium_ZRS`、`Standard_GRS`、`Standard_GZRS`、`Standard_LRS`、`Standard_RAGRS`、`Standard_RAGZRS`、および `Standard_ZRS` があります。 SKU 名は、[SkuName クラス](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet)の大文字と小文字が区別される文字列フィールドです。

## <a name="retrieve-account-information"></a>アカウント情報を取得する

BLOB に関連付けられているストレージ アカウントの種類と SKU 名を取得するには、[GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) または [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) メソッドを呼び出します。

次のコード例では、読み取り専用のアカウントプロパティを取得して表示します。

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>次のステップ

[Azure portal](https://portal.azure.com) と Azure REST API を使用してストレージ アカウントに対して実行できるその他の操作について学習します。

- [アカウント情報の取得操作 (REST)](/rest/api/storageservices/get-account-information)
