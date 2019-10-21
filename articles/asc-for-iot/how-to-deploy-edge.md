---
title: Azure Security Center for IoT Edge モジュールをデプロイする | Microsoft Docs
description: IoT Edge に Azure Security Center for IoT セキュリティ エージェントをデプロイする方法について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2019
ms.author: mlottner
ms.openlocfilehash: 128265cd3e69cd27bab6538c9eb376410439824d
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176671"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>IoT Edge デバイスにセキュリティ モジュールをデプロイする


**Azure Security Center for IoT** モジュールは、IoT Edge デバイスの包括的なセキュリティ ソリューションを提供します。
セキュリティ モジュールは、オペレーティング システムおよびコンテナー システムから未加工のセキュリティ データを収集、集約して分析し、実践的なセキュリティ推奨事項とアラートに変換します。
詳細については、[IoT Edge 用のセキュリティ モジュール](security-edge-architecture.md)に関する記事を参照してください。

この記事では、IoT Edge デバイスにセキュリティ モジュールをデプロイする方法について説明します。

## <a name="deploy-security-module"></a>セキュリティ モジュールをデプロイする

次の手順を使用して、IoT Edge 用の Azure Security Center for IoT セキュリティ モジュールをデプロイします。

### <a name="prerequisites"></a>前提条件

1. IoT Hub で、ご使用のデバイスが [IoT Edge デバイスとして登録されている](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)ことを確認してください。

1. Azure Security Center for IoT Edge モジュールでは、[AuditD フレームワーク](https://linux.die.net/man/8/auditd)が IoT Edge デバイスにインストールされている必要があります。

    - IoT Edge デバイスで次のコマンドを実行して、そのフレームワークをインストールします。
   
    `sudo apt-get install auditd audispd-plugins`

    - 次のコマンドを実行して、AuditD がアクティブであることを確認します。 
   
    `sudo systemctl status auditd`<br>
    - 予期される応答: `active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Azure portal を使用したデプロイ

1. Azure portal から **Marketplace** を開きます。

1. **[モ ノのインターネット]** を選択し、 **[Azure Security Center for IoT]** を検索して選択します。

   ![Azure Security Center for IoT を選択します](media/howto/edge-onboarding-8.png)

1. **[作成]** をクリックして、デプロイを構成します。 

1. ご使用の IoT Hub のAzure **サブスクリプション**を選択してから、ご使用の **IoT Hub** を選択します。<br>単一のデバイスをターゲットにするには **[デバイスへのデプロイ]** を選択し、複数のデバイスをターゲットにするには **[大規模にデプロイする]** を選択します。その後、 **[作成]** をクリックします。 大規模デプロイの詳細については、[デプロイ方法](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)に関する記事を参照してください。 

    >[!Note] 
    >**[大規模にデプロイする]** を選択した場合は、以下の手順にある **[モジュールの追加]** タブに進む前にデバイス名と詳細を追加してください。     

Azure Security Center for IoT 用の IoT Edge デプロイを作成するには、3 つの手順があります。 次のセクションで、手順ごとに説明します。 

#### <a name="step-1-add-modules"></a>手順 1:モジュールを追加する

1. **[モジュールの追加]** タブの **[デプロイ モジュール]** 領域で、 **[AzureSecurityCenterforIoT]** の **[構成]** オプションをクリックします。 
   
1. **名前**を **azureiotsecurity** に変更します。
1. **[イメージの URI]** を **mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.0** に変更します。
1. **[コンテナーの作成オプション]** の値が次のように設定されていることを確認します。      
    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
1. **[モジュール ツインの必要なプロパティの設定]** が選択されていることを確認し、構成オブジェクトを次のように変更します。
      
    ``` json
    { 
       "properties.desired":{ 
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{ 

          }
       }
    }
    ```

1. **[Save]** をクリックします。
1. タブの一番下までスクロールし、 **[Edge ランタイムの詳細設定を構成する]** を選択します。 
   
1. **[Edge ハブ]** の下の **[イメージ]** を **mcr.microsoft.com/azureiotedge-hub:1.0.9-rc2** に変更します。

1. **[作成オプション]** が次のように設定されていることを確認します。 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
1. **[Save]** をクリックします。
   
1. **[次へ]** をクリックします。

#### <a name="step-2-specify-routes"></a>手順 2:ルートを指定する 

1. **[ルートの指定]** タブで、以下の例に従い、**azureiotsecurity** モジュールから **$upstream** にメッセージを転送するルート (明示的または暗黙的) があることを確認し、その後 **[次へ]** をクリックします。 

~~~Default implicit route
"route": "FROM /messages/* INTO $upstream" 
~~~

~~~Explicit route
"ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
~~~

#### <a name="step-3-review-deployment"></a>手順 3:デプロイを確認する

- **[デプロイの確認]** タブでデプロイ情報を確認し、 **[送信]** を選択してデプロイを完了します。

## <a name="diagnostic-steps"></a>診断手順

問題が発生した場合に IoT Edge セキュリティ モジュール デバイスの状態を知るには、コンテナー ログを使用するのが最適です。 情報を収集するには、このセクションのコマンドとツールを使います。

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>必要なコンテナーがインストールされており、期待どおりに機能していることを確認する

1. IoT Edge デバイスで次のコマンドを実行します。
    
    `sudo docker ps`
   
1. 次のコンテナーが実行中であることを確認します。
   
   | Name | イメージ |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.0 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.9-rc2 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   最低限必要なコンテナーが存在しない場合は、IoT Edge のデプロイ マニフェストが推奨設定と一致しているかどうかを確認してください。 詳細については、[IoT Edge モジュールのデプロイ](#deployment-using-azure-portal)に関する記事を参照してください。

### <a name="inspect-the-module-logs-for-errors"></a>モジュール ログでエラーを調べる
   
1. IoT Edge デバイスで次のコマンドを実行します。

   `sudo docker logs azureiotsecurity`
   
1. より詳細なログについては、環境変数 `logLevel=Debug` を **azureiotsecurity** モジュール デプロイに追加してください。

## <a name="next-steps"></a>次の手順

構成オプションの詳細については、モジュール構成のハウツー ガイドに進んでください。 
> [!div class="nextstepaction"]
> [モジュール構成のハウツー ガイド](./how-to-agent-configuration.md)
