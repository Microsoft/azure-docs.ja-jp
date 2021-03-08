---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: e177fabdad7a1517a8371fe1d3483cb5c9310d2c
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581323"
---
移動中のデータの場合:

- デバイスと Azure の間を移動するデータには標準 TLS 1.2 が使用されます。 TLS 1.1 以前へのフォールバックはありません。 TLS 1.2 がサポートされていない場合は、エージェント通信がブロックされます。 TLS 1.2 はまた、ポータルや SDK の管理にも必要です。
- クライアントがブラウザーのローカル Web UI 経由でデバイスにアクセスする場合は、標準 TLS 1.2 が既定のセキュリティで保護されたプロトコルとして使用されます。

    - ベスト プラクティスとして、TLS 1.2 を使用するようにブラウザーを構成することをお勧めします。
    - ブラウザーで TLS 1.2 がサポートされていない場合は、TLS 1.1 または TLS 1.0 を使用できます。
- データ サーバーからデータをコピーする場合は、そのデータを保護するために暗号化付き SMB 3.0 を使用することをお勧めします。
