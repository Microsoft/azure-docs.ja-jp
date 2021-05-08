---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "76963659"
---
パフォーマンスを最大化するには、関数アプリごとに個別のストレージ アカウントを使用します。 Durable Functions または Event Hub によってトリガーされる関数がある場合には、これは特に重要です。どちらも、大量のストレージ トランザクションを生成します。 アプリケーション ロジックが (Storage SDK を使用して) 直接、あるいは、ストレージ バインドの 1 つを経由して Azure Storage と対話する場合、専用のストレージ アカウントを使用する必要があります。 たとえば、Event Hub によってトリガーされ BLOB ストレージにデータを書き込む関数がある場合、2 つのストレージ アカウントを使用します&mdash;1 つは関数アプリ用、もう 1 つは関数によって格納されている BLOB 用になります。