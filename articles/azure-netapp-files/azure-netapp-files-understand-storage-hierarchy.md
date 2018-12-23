---
title: Azure NetApp Files のストレージ階層を理解する | Microsoft Docs
description: Azure NetApp Files のアカウント、容量プール、ボリュームを含むストレージ階層について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: fa498c39123e09c212466c900e6000c0138db467
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413100"
---
# <a name="understand-the-storage-hierarchy-of-azure-netapp-files"></a>Azure NetApp Files のストレージ階層を理解する

Azure NetApp Files のボリュームを作成する前に、プロビジョニング容量のプールを購入して設定する必要があります。  容量プールを設定するには、NetApp アカウントが必要です。 ストレージ階層についての理解は、Azure NetApp Files のリソースを設定および管理するうえで役立ちます。

## <a name="azure_netapp_files_account"></a>NetApp アカウント

- NetApp アカウントは、構成容量プールの管理グループとしての役割を果たします。  
- NetApp アカウントは、一般的な Azure Storage アカウントとは異なります。 
- NetApp アカウントは、範囲がリージョン単位となります。   
- 1 つのリージョンで複数の NetApp アカウントを持つことはできますが、個々の NetApp アカウントが関連付けられるリージョンは 1 つだけです。

## <a name="capacity_pools"></a>容量プール

- 容量プールは、そのプロビジョニング容量で測定されます。  
- 容量は、購入した固定 SKU 単位でプロビジョニングされます (4 TiB 容量など)。
- 1 つの容量プールの最小サイズは 4 TiB で、最大サイズは 500 TiB です。 
- 容量プールに割り当てることができるサービス レベルは 1 つだけです。  
  現在利用できるのは Premium サービス レベルのみです。
- 各容量プールが属することができる NetApp アカウントは 1 つだけです。 ただし、1 つの NetApp アカウント内に複数の容量プールを持つことができます。  
- 1 つの容量プールを複数の NetApp アカウントにまたがって移動することはできません。   
  たとえば、下の「[ストレージ階層の概念図](#conceptual_diagram_of_storage_hierarchy)」で、容量プール 1 を米国東部の NetApp アカウントから米国西部 2 の NetApp アカウントへ移動することはできません。  

## <a name="volumes"></a>ボリューム

- ボリュームは、論理容量消費で測定され、スケーラブルです。 1 つのボリュームの最小サイズは 100 GiB で、最大サイズは 92 TiB です。
- ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。
-   各リージョンで Azure サブスクリプションごとに最大 100 個のボリュームを持つことができます。 
- 各ボリュームが従属するプールは 1 つだけですが、1 つのプールは複数のボリュームを含むことができます。 
- 同じ NetApp アカウント内であれば、プールの境界を越えてボリュームを移動することができます。    
  たとえば、下の「[ストレージ階層の概念図](#conceptual_diagram_of_storage_hierarchy)」で、容量プール 1 のボリュームを容量プール 2 に移動することができます。

## <a name="conceptual_diagram_of_storage_hierarchy"></a>ストレージ階層の概念図 
次の例は、Azure サブスクリプション、NetApp アカウント、容量プール、ボリュームの関係を示したものです。   

![ストレージ階層の概念図](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>次の手順

[Azure NetApp Files に登録する](azure-netapp-files-register.md)
