---
title: Microsoft Azure Stack Edge デバイスに Windows PowerShell インターフェイス経由で接続し、管理する | Microsoft Docs
description: Azure Stack Edge に Windows PowerShell インターフェイス経由で接続し、管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: 973c618b46d1b6be902d9629ca63ee120cae6855
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85313207"
---
# <a name="manage-an-azure-stack-edge-device-via-windows-powershell"></a>Windows PowerShell を使用して Azure Stack Edge デバイスを管理する

Azure Stack Edge ソリューションにより、データを処理してネットワーク経由で Azure に送信できます。 この記事では、Azure Stack Edge デバイスの構成と管理のタスクをいくつか説明します。 Azure portal、ローカル Web UI、または Windows PowerShell インターフェイスを使用してデバイスを管理できます。

この記事では、PowerShell インターフェイスを使用して行うタスクに重点を置いて説明します。 

この記事には、次の手順が含まれています。

- PowerShell インターフェイスに接続する
- サポート パッケージを作成する
- 証明書のアップロード
- デバイスをリセットする
- デバイス情報を表示する
- コンピューティング ログを取得する
- コンピューティング モジュールの監視とトラブルシューティングを行う

## <a name="connect-to-the-powershell-interface"></a>PowerShell インターフェイスに接続する

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>サポート パッケージを作成する

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>証明書のアップロード

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

ご使用の IoT Edge デバイスと、そのデバイスに接続できるダウンストリーム デバイスとの間にセキュリティで保護された接続を確立するために、IoT Edge 証明書をアップロードすることもできます。 インストールする必要がある IoT Edge 証明書は 3 つあります ( *.pem* 形式)。

- ルート CA 証明書または所有者 CA
- デバイス CA 証明書
- デバイス キー証明書

このコマンドレットを使用して、IoT Edge 証明書をインストールする例を次に示します。

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
このコマンドレットを実行するときは、ネットワーク共有のためのパスワードを指定するよう求められます。

証明書の詳細については、[Azure IoT Edge 証明書](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs)に関するページまたは「[ゲートウェイに証明書をインストール](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)」に移動してください。

## <a name="view-device-information"></a>デバイス情報を表示する
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>デバイスをリセットする

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>コンピューティング ログを取得する

デバイスにコンピューティング ロールが構成されている場合は、PowerShell インターフェイス経由でコンピューティング ログを取得することもできます。

1. [PowerShell インターフェイスに接続する](#connect-to-the-powershell-interface)。
2. `Get-AzureDataBoxEdgeComputeRoleLogs` を使用してデバイスのコンピューティング ログを取得します。

    このコマンドレットの使用例を次に示します。

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    このコマンドレットで使用されるパラメーターの説明を次に示します。
    - `Path`:コンピューティング ログ パッケージを作成する共有へのネットワーク パスを指定します。
    - `Credential`:ネットワーク共有のユーザー名を指定します。 このコマンドレットを実行するときには、共有のパスワードを指定する必要があります。
    - `FullLogCollection`:このパラメーターにより、ログ パッケージにすべてのコンピューティング ログが確実に含まれます。 既定では、ログ パッケージに含まれるものは、ログのサブセットのみです。

## <a name="monitor-and-troubleshoot-compute-modules"></a>コンピューティング モジュールの監視とトラブルシューティングを行う

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/azure-stack-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>リモート セッションを終了します。

リモート PowerShell セッションを終了するには、PowerShell ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- Azure portal で [Azure Stack Edge](azure-stack-edge-deploy-prep.md) を配置します。
