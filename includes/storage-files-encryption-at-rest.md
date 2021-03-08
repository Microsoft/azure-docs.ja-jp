---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 814bf085ca910ef07a13df779eef25e0e6510e57
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011740"
---
Azure Files に格納されるすべてのデータは、Azure Storage Service Encryption (SSE) を使用して保存時に暗号化されます。 Storage Service Encryption は Windows の BitLocker と同様に機能し、データはファイル システム レベルで暗号化されます。 データは Azure ファイル共有のファイル システムで暗号化されるため、ディスクにエンコードされた場合、Azure ファイル共有を読み書きするために、基になるクライアント上のキーにアクセスできる必要はありません。 保存時の暗号化は、SMB と NFS の両方のプロトコルに適用されます。

規定では、Azure Files に格納されるデータは、Microsoft マネージド キーで暗号化されます。 Microsoft マネージド キーを使用すると、Microsoft によってデータの暗号化および暗号化解除のためのキーが保持され、定期的にローテーションが行われます。 また、お客様が自分でキーを管理することもでき、その場合はお客様がローテーション プロセスを制御できます。 お客様が管理するキーによるファイル共有の暗号化を選択した場合、クライアントからの読み取りと書き込みの要求を処理するため、Azure Files にはキーへのアクセスが承認されます。 お客様管理のキーを使用すると、お客様はこの承認をいつでも取り消すことができますが、これは、SMB または FileREST API を使用して Azure ファイル共有にアクセスできなくなることを意味します。

Azure Files では、Azure Blob Storage などの他の Azure ストレージ サービスと同じ暗号化スキームが使用されます。 Azure Storage Service Encryption (SSE) の詳細については、「[保存データに対する Azure Storage 暗号化](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」をご覧ください。