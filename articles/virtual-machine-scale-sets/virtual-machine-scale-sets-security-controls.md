---
title: Azure Virtual Machine Scale Sets のセキュリティ コントロール
description: Azure Virtual Machine Scale Sets を評価するためのセキュリティ コントロールのチェックリスト
services: virtual-machine-scale-sets
ms.service: virtual-machine-scale-sets
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d7e69e8ad0c5b14ac7ed8b941a7949f4f675812
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176769"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets のセキュリティ コントロール

この記事では、Azure Virtual Machine Scale Sets に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | |
| VNet インジェクションのサポート| はい | |
| ネットワークの分離とファイアウォールのサポート| はい |  |
| 強制トンネリングのサポート| はい | 「[Azure Resource Manager デプロイ モデルを使用した強制トンネリングの構成](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)」を参照してください。 |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | 「[Azure で Linux 仮想マシンの監視と更新を行う](/azure/virtual-machines/linux/tutorial-monitoring)」および「[Azure で Windows 仮想マシンの監視と更新を行う](/azure/virtual-machines/windows/tutorial-monitoring)」を参照してください。 |
| コントロールと管理プレーンのログ記録と監査| はい |  |
| データ プレーンのログ記録と監査 | いいえ |  |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 認証| はい |  |
| Authorization| はい |  |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | はい | 「[Azure で Linux 仮想マシンを暗号化する方法](/azure/virtual-machines/linux/disk-encryption-linux)」および「[Windows VM の仮想ディスクを暗号化する](/azure/virtual-machines/windows/encrypt-disks)」を参照してください。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | Azure Virtual Machines では、[ExpressRoute](/azure/expressroute) と VNet の暗号化がサポートされています。 「[VM での転送中の暗号化](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)」を参照してください。 |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | はい | カスタマー マネージド キーは、サポートされている Azure の暗号化シナリオです。「[Azure の暗号化の概要](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | HTTPS および SSL 経由。 |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい |  | 

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。