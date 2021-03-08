---
title: セキュリティ エージェントの概要
description: Azure Defender for IoT サービスで使用されるエージェントのセキュリティ エージェントのアーキテクチャについて説明します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: ff837fe88f878c522366b2b6bc19a1ef3954b667
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820655"
---
# <a name="security-agent-reference-architecture"></a>セキュリティ エージェントの参照アーキテクチャ

Azure Defender for IoT には、IoT Hub を介してセキュリティ データのログを収集、処理、集計、および送信する、セキュリティ エージェントの参照アーキテクチャがあります。

セキュリティ エージェントは、制約のある IoT 環境で機能するように設計されており、消費するリソースと比較した場合、提供する価値という観点で高度にカスタマイズ可能です。

セキュリティ エージェントは、次の機能をサポートしています。

- 既存のデバイス ID、または専用のモジュール ID で認証する。 詳細については、 [セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)に関するページを参照してください。

- 基になるオペレーティング システム (Linux、Windows) から未処理のセキュリティ イベントを収集する。 使用可能なセキュリティ データ コレクターの詳細については、[Defender for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

- 未加工のセキュリティ イベントを収集して、IoT Hub を介して送信されるメッセージにする。

- **azureiotsecurity** モジュール ツインを使用してリモートで構成する。 詳細については、[Defender for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

Defender for IoT セキュリティ エージェントはオープン ソース プロジェクトとして開発されており、次のように GitHub から入手できます。

- [Defender for IoT の C ベースのエージェント](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender for IoT の C# ベースのエージェント](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>エージェントでサポートされているプラットフォーム

Defender for IoT では、32 ビットと 64 ビットの Windows のエージェントには異なるインストーラー、32 ビットと 64 ビットの Linux のエージェントには同じインストーラーが用意されています。 次の表に従って、ご使用のデバイスごとに適切なエージェント インストーラーがあることを確認してください。

| Architecture | Linux | Windows | 詳細 |
|--|--|--|--|
| 32 ビット | C | C# |  |
| 64 ビット | C# または C | C# | デバイス リソースがさらに制限されているか少ないデバイスについては、C エージェントを使用することをお勧めします。 |


## <a name="next-steps"></a>次のステップ

この記事では、Defender for IoT セキュリティ モジュールのアーキテクチャと利用できるインストーラーについて概要を学習しました。

Defender for IoT のデプロイの概要について引き続き学習するには、次の記事を参照してください。

- [セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)を理解する
- [セキュリティ エージェント](how-to-deploy-agent.md)を選択してデプロイする
- Defender for IoT の[システムの要件](quickstart-system-prerequisites.md)を確認する
- [IoT Hub で Defender for IoT サービスを有効にする](quickstart-onboard-iot-hub.md)方法について確認する
- [Defender for IoT についてよく寄せられる質問](resources-frequently-asked-questions.md)からサービスの詳細について確認する
