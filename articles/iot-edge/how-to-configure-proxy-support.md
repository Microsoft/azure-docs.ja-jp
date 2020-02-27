---
title: ネットワーク プロキシ対応のデバイスを構成する - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge ランタイムおよびインターネット対応の任意の IoT Edge モジュールを構成して、プロキシ サーバー経由で通信する方法。
author: kgremban
ms.author: kgremban
ms.date: 11/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a8ee1e07dafac46467aa26f89b609cd499346974
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186575"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>IoT Edge デバイスを構成してプロキシ サーバー経由で通信する

IoT Edge デバイスでは、HTTPS 要求を送信して IoT Hub と通信します。 お使いのデバイスがプロキシ サーバーを使用するネットワークに接続されている場合、IoT Edge ランタイムを構成してサーバー経由で通信する必要があります。 また、プロキシ サーバーは、IoT Edge ハブ経由でルーティングされない HTTP または HTTPS 要求を行った場合、個々 の IoT Edge モジュールに影響を及ぼす可能性があります。

この記事では、プロキシ サーバーの内側で IoT Edge デバイスを構成して管理する以下の 4 つの手順について説明します。

1. **デバイスに IoT Edge ランタイムをインストールします。**

   IoT Edge のインストール スクリプトによってインターネットからパッケージとファイルが取得されるので、デバイスではこれらの要求を行うためにプロキシ サーバーを介して通信する必要があります。 詳細な手順については、この記事の「[プロキシ経由でランタイムをインストールする](#install-the-runtime-through-a-proxy)」セクションを参照してください。 Windows デバイスの場合、インストール スクリプトには[オフライン インストール](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation) オプションもあります。

   この手順は、最初に設定するときに IoT Edge デバイスで実行される 1 回限りのプロセスです。 IoT Edge ランタイムを更新するときにも同じ接続が必要です。

2. **デバイス上に Docker デーモンと IoT Edge デーモンを構成します。**

   IoT Edge にはデバイス上の 2 つのデーモンが使用されますが、いずれもプロキシ サーバーを経由して Web 要求を行う必要があります。 IoT Edge デーモンは、IoT Hub との通信を担当します。 Moby デーモンはコンテナー管理を担当しているため、コンテナー レジストリと通信します。 詳細な手順については、この記事の「[デーモンの構成](#configure-the-daemons)」セクションを参照してください。

   この手順は、最初に設定するときに IoT Edge デバイスで実行される 1 回限りのプロセスです。

3. **デバイス上の config.yaml ファイルで IoT Edge エージェントのプロパティを構成します。**

   IoT Edge デーモンによって最初に edgeAgent モジュールが開始されますが、edgeAgent モジュールは IoT Hub から配置マニフェストを取得し、他のすべてのモジュールを開始する処理を担当します。 IoT Edge エージェントが IoT Hub に初めて接続するには、デバイス上で手動で edgeAgent モジュールの環境変数を構成します。 最初の接続後は、edgeAgent モジュールをリモートで構成できます。 詳細な手順については、この記事の「[IoT Edge エージェントを構成する](#configure-the-iot-edge-agent)」セクションを参照してください。

   この手順は、最初に設定するときに IoT Edge デバイスで実行される 1 回限りのプロセスです。

4. **今後のすべてのモジュールのデプロイのために、プロキシ経由で通信するすべてのモジュールの環境変数を設定します。**

   IoT Edge デバイスが設定され、プロキシ サーバー経由で IoT Hub に接続されたら、今後すべてのモジュールのデプロイで接続を維持する必要があります。 詳細な手順については、この記事の「[配置マニフェストを構成する](#configure-deployment-manifests)」セクションを参照してください。

   この手順は、リモートで実行される継続的なプロセスです。そのため、新しいモジュールまたはデプロイの更新ごとに、プロキシ サーバー経由で通信するデバイスの機能が維持されます。

## <a name="know-your-proxy-url"></a>プロキシの URL を確認する

この記事の手順を始める前に、プロキシ URL について理解しておく必要があります。

プロキシ URL の形式は、**protocol**://**proxy_host**:**proxy_port** です。

* **protocol** は HTTP または HTTPS のいずれかです。 Docker デーモンはコンテナー レジストリの設定に応じてどちらのプロトコルも使用できますが、IoT Edge デーモンとランタイム コンテナーは常に HTTP を使用してプロキシに接続する必要があります。

* **proxy_host** は、プロキシ サーバーのアドレスです。 プロキシ サーバーで認証が必要な場合は、プロキシ ホストの一部として、**user**:**password**\@**proxy_host** の形式で資格情報を指定できます。

* **proxy_port** は、ネットワーク トラフィックにプロキシが応答するネットワーク ポートです。

## <a name="install-the-runtime-through-a-proxy"></a>プロキシ経由でランタイムをインストールする

IoT Edge デバイスが Windows または Linux のどちらで動作している場合でも、プロキシ サーバー経由でインストール パッケージにアクセスする必要があります。 オペレーティング システムに応じて、プロキシ サーバー経由で IoT Edge ランタイムをインストールする手順を実行します。

### <a name="linux-devices"></a>Linux デバイス

Linux デバイス上に IoT Edge ランタイムをインストールしている場合、プロキシ サーバーを経由してインストール パッケージにアクセスするように、パッケージ マネージャーを構成します。 たとえば、[http-proxy を使用するように apt-get を設定](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy)します。 パッケージ マネージャーが構成されたら、通常どおり「[Linux に Azure IoT Edge ランタイムをインストールする (x64)](how-to-install-iot-edge-linux.md)」の手順に従います。

### <a name="windows-devices"></a>Windows デバイス

Windows デバイス上に IoT Edge ランタイムをインストールしている場合、プロキシ サーバーを 2 回通過する必要があります。 最初の接続では、インストーラー スクリプト ファイルをダウンロードするためのもので、2 回目の接続はインストール中に必要なコンポーネントをダウンロードします。 Windows 設定でプロキシ情報を構成するか、または PowerShell コマンドにプロキシ情報を直接含めることができます。

以下の手順は、`-proxy` 引数を使用した Windows インストールの例です。

1. Invoke-WebRequest コマンドは、インストーラー スクリプトにアクセスするためにプロキシ情報が必要です。 次に、Deploy-IoTEdge コマンドで、インストール ファイルをダウンロードするためにプロキシ情報が必要になります。

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Initialize IoTEdge コマンドはプロキシ サーバーを経由する必要がないため、2 番目の手順では、Invoke-WebRequest のプロキシ情報のみが必要になります。

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

プロキシ サーバーの資格情報が複雑で URL に含めることができない場合は、`-InvokeWebRequestParameters` 内で `-ProxyCredential` パラメーターを使用してください。 たとえば、次のように入力します。

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

プロキシのパラメーターについて詳しくは、「[Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)」を参照してください。 オフライン インストールなど、Windows インストール オプションの詳細については、[Windows への Azure IoT Edge ランタイムのインストール](how-to-install-iot-edge-windows.md)に関するページを参照してください。

## <a name="configure-the-daemons"></a>デーモンを構成する

IoT Edge は、IoT Edge デバイス上で実行されている 2 つのデーモンに依存しています。 Moby デーモンは、コンテナー レジストリからコンテナー イメージをプルするための Web 要求を行います。 IoT Edge デーモンは、IoT Hub と通信するための Web 要求を行います。

Moby と IoT Edge の両方のデーモンは、継続的なデバイス機能のためにプロキシ サーバーを使用するように構成する必要があります。 この手順は、デバイスの初期設定中に IoT Edge デバイス上で行われます。

### <a name="moby-daemon"></a>Moby デーモン

Moby は Docker 上に構築されるため、環境変数を使用して Moby デーモンを構成するには、Docker のドキュメントを参照してください。 ほとんどのコンテナー レジストリ (DockerHub および Azure Container Registry を含む) では HTTPS 要求をサポートしているので、設定する必要があるパラメーターは **HTTPS_PROXY** です。 トランスポート層セキュリティ (TLS) をサポートしていないレジストリからイメージをプルしている場合は、**HTTP_PROXY** パラメーターを設定する必要があります。

お使いの IoT Edge デバイス オペレーティング システムに該当する記事を選択します。

* [Linux で Docker デーモンを構成する](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Linux デバイス上の Moby デーモンは Docker の名前を保持します。
* [Windows で Docker デーモンを構成する](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Windows デバイス上の Moby デーモンは iotedge-moby と呼ばれます。 名前が異なるのは、Docker Desktop と Moby の両方が 1 台の Windows デバイスで並列実行される可能性があるためです。

### <a name="iot-edge-daemon"></a>IoT Edge デーモン

IoT Edge デーモンは、Moby デーモンとほぼ同じ方法で構成されています。 お使いのオペレーティング システムに基づいて、次の手順に従って、サービス用に環境変数を設定します。

IoT Edge デーモンは、常に HTTPS を使用して IoT Hub に要求を送信します。

#### <a name="linux"></a>Linux

IoT Edge デーモンを構成するために、ターミナルでエディターを開きます。

```bash
sudo systemctl edit iotedge
```

次のテキストを入力して、 **\<proxy URL>** をお使いのプロキシ サーバーのアドレスとポートに置き換えます。 その後、保存して終了します。

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

サービス マネージャーを更新して、IoT Edge 用の新しい構成を選択します。

```bash
sudo systemctl daemon-reload
```

IoT Edge を再起動して、変更を有効にします。

```bash
sudo systemctl restart iotedge
```

環境変数が作成され、新しい構成が読み込まれたことを確認します。

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

管理者として PowerShell ウィンドウを開き、次のコマンドを実行して、新しい環境変数でレジストリを編集します。 **\<proxy url>** をお使いのプロキシ サーバーのアドレスとポートに置き換えます。

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

IoT Edge を再起動して、変更を有効にします。

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>IoT Edge エージェントを構成する

IoT Edge エージェントは、すべての IoT Edge デバイス上で最初に起動するモジュールです。 最初は、IoT Edge config.yaml ファイルの情報に基づいて起動されます。 その後、IoT Edge エージェントは IoT Hub に接続して、デバイスへの配置が必要な他のモジュールを宣言する配置マニフェストを取得します。

この手順は、デバイスの初期設定中に IoT Edge デバイス上で 1 回行われます。

1. お使いの IoT Edge デバイス上で、config.yaml ファイルを開きます。 Linux システムの場合、このファイルは **/etc/iotedge/config.yaml** に配置されています。 Windows システムの場合、このファイルは **C:\ProgramData\iotedge\config.yaml** に配置されています。 構成ファイルは保護されているため、アクセスするには管理者権限が必要です。 Linux システムの場合、好みのテキスト エディターでファイルを開く前に `sudo` コマンドを使用します。 Windows の場合、管理者としてメモ帳などのテキスト エディターを開いてから、ファイルを開きます。

2. config.yaml ファイルで、**Edge Agent module spec** セクションを探します。 IoT Edge エージェント定義には、環境変数を追加できる **env** パラメーターが組み込まれています。

3. env パラメーターのプレースホルダ―である中かっこを削除して、新しい行に新しい変数を追加します。 YAML では、インデントはスペース 2 つであることに注意してください。

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. IoT Edge ランタイムでは既定で、AMQP を使用して IoT Hub と通信します。 一部のプロキシ サーバーでは、AMQP ポートをブロックします。 この場合は、WebSocket 経由で AMQP を使用するように、edgeAgent を構成することも必要になります。 2 番目の環境変数を追加します。

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![環境変数を使用した edgeAgent 定義](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. config.yaml への変更を保存して、エディターを閉じます。 IoT Edge を再起動して、変更を有効にします。

   * Linux:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>配置マニフェストを構成する  

プロキシ サーバーを利用するように IoT Edge デバイスが構成されたら、以降の配置マニフェストでも引き続き、環境変数を宣言する必要があります。 配置マニフェストを編集するには、Azure portal ウィザードを使用するか、配置マニフェストの JSON ファイルを編集します。

edgeAgent と edgeHub の 2 つのランタイム モジュールは、IoT Hub との接続を維持できるよう、必ずプロキシ サーバー経由で通信するように構成してください。 edgeAgent モジュールからプロキシ情報を削除した場合、前のセクションで説明したように、接続を再確立する唯一の方法は、デバイス上の config.yaml ファイルを編集することです。

インターネットに接続する他の IoT Edge モジュールも、プロキシ サーバー経由で通信するように構成する必要があります。 ただし、edgeHub 経由でメッセージをルーティングするモジュールや、デバイス上の他のモジュールとのみ通信するモジュールには、プロキシ サーバーの詳細は不要です。

この手順は、IoT Edge デバイスの有効期間を通して継続されます。

### <a name="azure-portal"></a>Azure portal

**モジュールの設定**ウィザードを使用して IoT Edge デバイスの配置を作成する場合、どのモジュールにも、プロキシ サーバー接続の構成に使用できる **[環境変数]** セクションがあります。

IoT Edge エージェントおよび IoT Edge ハブ モジュールを構成するには、ウィザードの最初の手順で **[Runtime Settings]\(ランタイムの設定\)** を選択します。

![Edge ランタイムの詳細設定を構成する](./media/how-to-configure-proxy-support/configure-runtime.png)

IoT Edge エージェントおよび IoT Edge ハブ モジュールの両方の定義に、**https_proxy** 環境変数を追加します。 お使いの IoT Edge デバイス上の config.yaml ファイルに **UpstreamProtocol** 環境変数を含めた場合は、IoT Edge エージェント モジュールの定義にもこの環境変数を追加します。

![https_proxy environment 変数を設定する](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

配置マニフェストに追加する他のモジュールはすべて、同じパターンに従います。 モジュール名およびイメージを設定するページには、環境変数のセクションがあります。

### <a name="json-deployment-manifest-files"></a>JSON 配置マニフェスト ファイル

Visual Studio Code のテンプレートを使用するか、または手動で JSON ファイルを作成して、IoT Edge デバイスの配置を作成した場合、各モジュール定義に環境変数を直接追加できます。

次の JSON 形式を使用します。

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

環境変数が含まれる場合、モジュール定義は次の edgeHub 例のようになります。

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

お使いの IoT Edge デバイス上の config.yaml ファイルに **UpstreamProtocol** 環境変数を含めた場合は、IoT Edge エージェント モジュールの定義にもこの環境変数を追加します。

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>次のステップ

[IoT Edge ランタイム](iot-edge-runtime.md)のロールに関する詳細を確認する

「[Azure IoT Edge での一般的な問題と解決](troubleshoot.md)」でインストールと構成に関するエラーをトラブルシューティングする
