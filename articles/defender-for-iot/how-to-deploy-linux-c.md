---
title: Linux C エージェントをインストールおよびデプロイする
description: Defender for IoT の C ベースのセキュリティ エージェントを Linux にインストールおよびデプロイする方法について説明します
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8c03f6b882c8b1a64c9f256493c5d586b5fa0f89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90931638"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Linux 用の Defender for IoT の C ベースのセキュリティ エージェントをデプロイする

このガイドでは、Defender for IoT の C ベースのセキュリティ エージェントを Linux にインストールおよびデプロイする方法について説明します。

このガイドでは、以下の方法について説明します。

> [!div class="checklist"]
> * インストール
> * デプロイの確認
> * エージェントのアンインストール
> * トラブルシューティング

## <a name="prerequisites"></a>前提条件

その他のプラットフォームとエージェントの種類については、[適切なセキュリティ エージェントの選択](how-to-deploy-agent.md)に関するページを参照してください。

1. セキュリティ エージェントをデプロイするには、インストール先のマシンでのローカル管理者権限が必要です (sudo)。

1. デバイスの[セキュリティ モジュールを作成](quickstart-create-security-twin.md)します。

## <a name="installation"></a>インストール

セキュリティ エージェントをインストールおよびデプロイするには、次のワークフローを使用してください。

1. [GitHub](https://aka.ms/iot-security-github-c) からマシンに最新バージョンをダウンロードします。

1. パッケージの内容を展開し、 _/src/installation_ フォルダーに移動します。

1. 以下のコマンドを実行して、**InstallSecurityAgent スクリプト**に実行アクセス許可を追加します。

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 次に、以下を実行します。

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   認証パラメーターの詳細については、[認証を構成する方法](concept-security-agent-authentication-methods.md)に関するページを参照してください。

このスクリプトは、次の関数を実行します。

1. 前提条件のインストール。

1. サービス ユーザーを追加する (対話型サインインは無効)。

1. エージェントを**デーモン**としてインストールする - デバイスがサービス管理に **systemd** を使用すると想定します。

1. 指定された認証パラメーターでエージェントを構成する。

追加のヘルプについては、–help パラメーターを指定してスクリプトを実行します。

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>エージェントのアンインストール

エージェントをアンインストールするには、–uninstall パラメーターを指定してスクリプトを実行します。

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>トラブルシューティング

以下を実行して、デプロイの状態を確認します。

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>次のステップ

- Defender for IoT サービスの[概要](overview.md)を確認する
- Defender for IoT の[アーキテクチャ](architecture.md)について詳細を確認する
- [サービス](quickstart-onboard-iot-hub.md)を有効にします
- [FAQ](resources-frequently-asked-questions.md) を読みます
- [セキュリティ アラート](concept-security-alerts.md)について理解します
