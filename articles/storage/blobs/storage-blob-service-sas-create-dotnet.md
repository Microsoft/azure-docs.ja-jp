---
title: .NET を使用してコンテナーまたは BLOB のサービス SAS を作成する
titleSuffix: Azure Storage
description: .NET クライアント ライブラリを使用して、コンテナーまたは BLOB のサービスの Shared Access Signature (SAS) を作成する方法を学習します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/07/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: e0d442624448cfe48fdd9ab426147599acbb940d
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88034722"
---
# <a name="create-a-service-sas-for-a-container-or-blob-with-net"></a>.NET を使用してコンテナーまたは BLOB のサービス SAS を作成する

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

この記事では、ストレージ アカウント キーを使用して、[.NET 用の Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage?view=azure-dotnet)でコンテナーまたは BLOB のサービス SAS を作成する方法を示します。

## <a name="create-a-service-sas-for-a-blob-container"></a>BLOB コンテナーのサービス SAS を作成する

次のコード例では、コンテナーに SAS を作成します。 既存の保存されているアクセス ポリシーの名前が指定されている場合、そのポリシーは、SAS に関連付けられます。 保存されているアクセス ポリシーがない場合、コードは、アドホック SAS をコンテナーに作成します。

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

新しい [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) を作成します。 その後、[ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) を呼び出して、SAS トークン文字列が取得されます。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetContainerSasUri":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

コンテナーのサービス SAS を作成するには、[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) メソッドを呼び出します。

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}

```
---

## <a name="create-a-service-sas-for-a-blob"></a>BLOB のサービス SAS を作成する

次のコード例では、BLOB に SAS を作成します。 既存の保存されているアクセス ポリシーの名前が指定されている場合、そのポリシーは、SAS に関連付けられます。 保存されているアクセス ポリシーがない場合、コードは、アドホック SAS を BLOB に作成します。

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

新しい [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) を作成します。 その後、[ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) を呼び出して、SAS トークン文字列が取得されます。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetBlobSasUri":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

BLOB のサービス SAS を作成するには、[CloudBlob.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) メソッドを呼び出します。

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```
---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>次の手順

- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../common/storage-sas-overview.md)
- [サービス SAS を作成する](/rest/api/storageservices/create-service-sas)
