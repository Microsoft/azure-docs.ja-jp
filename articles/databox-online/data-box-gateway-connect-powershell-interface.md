---
title: Microsoft Azure Data Box Gateway デバイスに Windows PowerShell インターフェイス経由で接続し、管理する | Microsoft Docs
description: Data Box Gateway に Windows PowerShell インターフェイス経由で接続し、管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 67caaa2c6c9bd615d0b88bdd5de3442b46aa32cb
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403495"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Azure Data Box Gateway デバイスを Windows PowerShell 経由で管理する

Azure Data Box Gateway ソリューションでは、ネットワーク経由で Azure にデータを送信できます。 この記事では、Data Box Gateway デバイスについての構成と管理のタスクをいくつか説明します。 Azure portal、ローカル Web UI、または Windows PowerShell インターフェイスを使用してデバイスを管理できます。

この記事では、PowerShell インターフェイスを使用して行うタスクに重点を置いて説明します。

この記事には、次の手順が含まれています。

- PowerShell インターフェイスに接続する
- サポート セッションを開始する
- サポート パッケージを作成する
- 証明書のアップロード
- 非 DHCP 環境で起動する
- デバイス情報を表示する

## <a name="connect-to-the-powershell-interface"></a>PowerShell インターフェイスに接続する

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>サポート パッケージを作成する

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>証明書のアップロード

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>非 DHCP 環境で起動する

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>デバイス情報を表示する

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>次の手順

- Azure portal で [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) を配置する。
