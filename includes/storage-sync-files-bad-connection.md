---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4407ffb9ab16a720ef00a288d72b0fb4c2dbced1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774570"
---
このエラーは、サーバーから Azure File Sync サービスにアクセスできない場合に発生します。 次の手順を行うと、このエラーを解決できます。

1. Windows サービス `FileSyncSvc.exe` がファイアウォールによってブロックされていないことを確認します。
2. Azure File Sync サービスへの発信接続に対して、ポート 443 が開いていることを確認します。 この確認には、`Test-NetConnection` コマンドレットを使用します。 以下の `<azure-file-sync-endpoint>` プレースホルダーの URL については、[Azure File Sync のプロキシとファイアウォールの設定](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall)に関するページを参照してください。 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. プロキシ構成が意図したとおりに設定されていることを確認します。 この確認には、`Get-StorageSyncProxyConfiguration` コマンドレットを使用します。 Azure File Sync のプロキシ構成の詳細については、[Azure File Sync のプロキシとファイアウォールの設定](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#firewall)に関するページを参照してください。

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. このサービス エンドポイントへのネットワーク接続を確認するには、Test-StorageSyncNetworkConnectivity コマンドレットを使用します。 詳細については、[サービス エンドポイントへのネットワーク接続のテスト](../articles/storage/file-sync/file-sync-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)に関するページを参照してください。    

5. ネットワーク接続のトラブルシューティングについてさらにサポートが必要な場合は、ネットワーク管理者に相談してください。