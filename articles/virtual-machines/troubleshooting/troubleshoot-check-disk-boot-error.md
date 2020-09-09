---
title: Azure VM 起動時のファイル システムのチェック | Microsoft Docs
description: VM の起動時にファイル システムのチェック中と表示される問題を解決する方法 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: f80fbd803cbe4ae5c4ac381c8cdb2f72d0ede316
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86132935"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Azure VM の起動時に "ファイル システムをチェックしています" と表示される

この記事では、Microsoft Azure で Windows 仮想マシン (VM) を起動するときに発生する可能性のある "ファイル システムをチェックしています" エラーについて説明します。


## <a name="symptom"></a>症状 

Windows VM が起動しません。 [[ブート診断]](boot-diagnostics.md) で起動のスクリーン ショットを確認すると、次のメッセージの 1 つでチェック ディスク プロセス (chkdsk.exe) が実行されていることがわかります。

- ドライブ (C:) のスキャンと修復
- C: 上のファイル システムのチェック

## <a name="cause"></a>原因

ファイル システムで NTFS エラーが検出された場合、Windows では次回の再起動時にディスクの整合性がチェックされ、修復されます。 通常、予期しない再起動が VM に発生した場合、または VM シャットダウン プロセスが突然中断された場合にこのようなことが発生します。

## <a name="solution"></a>解決策 

チェック ディスク処理が完了すると、Windows は正常に起動します。 VM がチェック ディスク プロセスで停止している場合は、VM 上のチェック ディスクをオフラインで実行してみてください。
1. バックアップとして、影響を受ける VM の OS ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。
2. [復旧 VM に OS ディスクを接続します](troubleshoot-recovery-disks-portal-windows.md)。  
3. 復旧 VM 上で、接続されている OS ディスク対してチェック ディスクを実行します。 次のサンプルでは、接続された OS ディスクのドライバー文字は E: です 

    ```console
    chkdsk E: /f
    ```

4. チェック ディスクが完了したら、復旧 VM からディスクをデタッチし、影響を受ける VM に OS ディスクとしてディスクを再アタッチします。 詳細については、「[OS ディスクを復旧 VM に接続して Windows VM のトラブルシューティングを行う](troubleshoot-recovery-disks-portal-windows.md)」を参照してください。
