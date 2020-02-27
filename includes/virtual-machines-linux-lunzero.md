---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590777"
---
データ ディスクを Linux VM に追加するときに、LUN 0 にディスクが存在しないと、エラーが発生することがあります。 `az vm disk attach -new` コマンドを使用して手動でディスクを追加していて、Azure プラットフォームに適切な LUN を判定させるのではなく、LUN を指定する (`--lun`) 場合は、LUN 0 にディスクが既に存在するようにするか、今後存在するようにしてください。 

`lsscsi`からの出力のスニペットを示す次の例を考えてみましょう。

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

2 つのデータ ディスクは LUN 0 と LUN 1 に存在します (`lsscsi` の出力詳細の最初の列は `[host:channel:target:lun]` です)。 両方のディスクには、VM 内からアクセスできる必要があります。 LUN 1 で追加対象の最初のディスク、LUN 2 で 2 番目のディスクを手動で指定した場合は、VM 内からこれらのディスクを正しく見ることはできません。

> [!NOTE]
> これらの例では Azure の `host` 値は 5 ですが、選択したストレージの種類によっては変わる可能性があります。
> 
> 

このディスクの動作は Azure の問題ではなく、Linux カーネルが SCSI の仕様に従う仕組みです。 Linux カーネルが接続されているデバイスの SCSI バスをスキャンするときに、デバイスが LUN 0 で検出される必要があります。システムが他のデバイスのスキャンを続行できるようにするためです。 そのため、次のようにしてください。

* データ ディスクを追加した後に `lsscsi` の出力を確認し、LUN 0 にディスクがあることを確認します。
* ディスクが VM 内に正しく表示されない場合は、ディスクが LUN 0 に存在することを確認します。

