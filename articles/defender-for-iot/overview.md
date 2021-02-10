---
title: サービスの概要
description: Defender for IoT の機能とサービスについて詳しく学習し、Defender for IoT によって包括的な IoT セキュリティがどのように実現されるかを理解します。
services: defender-for-iot
ms.service: azure
documentationcenter: na
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2020
ms.openlocfilehash: 5b4ab207462955be3876dc1a25fae491e48a9cd2
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621712"
---
# <a name="welcome-to-azure-defender-for-iot"></a>Azure Defender for IoT へようこそ

運用テクノロジ (OT) ネットワークは、社会の最も重要な側面の多くを支えています。 しかし、これらのテクノロジの多くはセキュリティを考慮して設計されたものではなく、従来の IT セキュリティ制御では保護できません。 一方、モノのインターネット (IoT) により、接続された数十億のデバイスによるイノベーションの新しい波が実現されていますが、攻撃対象領域とリスクが増加しています。  

Azure Defender for IoT は、IoT/OT デバイス、脆弱性、脅威を特定するための統合セキュリティ ソリューションです。 これを使用すれば、既存の IoT/OT デバイスを保護する必要があるか、新しい IoT イノベーションにセキュリティを組み込む必要があるかにかかわらず、IoT/OT 環境全体をセキュリティで保護することができます。  

Azure Defender for IoT には、お使いの環境のニーズに適合するように、2 つの機能セットが用意されています。

IoT/OT 環境を使用するエンドユーザー組織の場合、Azure Defender for IoT では、次のようなエージェントレスのネットワーク層監視がもたらされます。

- 迅速にデプロイできる。
- さまざまな産業機器や SOC ツールと簡単に統合する。
- IoT/OT ネットワークのパフォーマンスや安定性に影響を与えない。 

このプラットフォームは、完全にオンプレミスでデプロイすることも、Azure に接続されたハイブリッド環境にデプロイすることもできます。  

IoT デバイス ビルダーにとっては、Azure Defender for IoT により、Linux や RTO などの標準的な IoT オペレーティング システムをサポートする軽量のマイクロ エージェントも得られます。 この軽量エージェントを使用すると、エッジからクラウドへの IoT/OT イニシアチブにセキュリティを確実に組み込むことができます。 柔軟でカスタマイズ可能なデプロイのためのソース コードが含まれています。 

## <a name="agentless-solution-for-organizations"></a>組織向けのエージェントレス ソリューション 

以前の IoT デバイスと OT デバイスでは、エージェントがサポートされておらず、多くの場合、パッチが未適用で、正しく構成されておらず、IT チームから見えていません。 このような特質のために、これらのデバイスは、企業ネットワークに深く入り込もうとする脅威アクターにとって格好の標的になっています。 

企業の IT ネットワーク用に開発された従来のネットワーク セキュリティ監視ツールには、IoT および OT の環境で検出された特殊なプロトコル、デバイス、マシン間 (M2M) の動作に関する認識が十分ではないため、これらの環境に対処することができません。 

Azure Defender for IoT のエージェントレス監視機能を使用すれば、これらのネットワークを可視でき、セキュリティで保護できます。 したがって、これらの環境の主要な問題に対処することができます。 

### <a name="automatic-device-discovery"></a>自動デバイス検出  

パッシブでエージェントレスのネットワーク監視を使用すると、IoT/OT ネットワークに影響を与えずに、すべての IoT/OT デバイスの完全なインベントリ、詳細、および通信方法を取得できます。  

### <a name="proactive-visibility-into-risk-and-vulnerabilities"></a>リスクと脆弱性に対するプロアクティブな可視性
 
IoT/OT 環境のリスクと脆弱性を特定します。 たとえば、パッチ未適用のデバイス、開いているポート、未認可のアプリケーション、および未認可の接続を特定します。 デバイス構成、PLC コード、ファームウェアに対する変更を特定することもできます。 

### <a name="iotot-threat-detection"></a>IoT/OT の脅威検出  

特殊な IoT/OT 対応の脅威インテリジェンスと行動分析によって、異常なアクティビティや未認可のアクティビティを検出します。 また、ゼロデイ マルウェア、ファイルレス マルウェア、現地調達型戦術など、静的な IOC では見逃されていた高度な脅威を検出することもできます。 

### <a name="unified-security-management-across-iotot"></a>IoT/OT にわたる統合セキュリティ管理

組織全体の鳥瞰図を表示するために Azure Sentinel に統合します。 Splunk、IBM QRadar、ServiceNow などのサードパーティ製ツールを含む既存のワークフローと一体化した統合 IoT/OT セキュリティ ガバナンスを実装します。 

## <a name="agent-based-solution-for-device-builders"></a>デバイス ビルダー向けのエージェントベースのソリューション 

セキュリティは、IoT 実装者にとってほぼ普遍的な問題です。 IoT デバイスには、エンドポイント監視、セキュリティ体制管理、および脅威検出 (これらすべてに非常に独特なパフォーマンス要件があります) に対する固有のニーズがあります。 

Azure Defender for IoT セキュリティ エージェントを使用すると、新しい IoT デバイスと Azure IoT プロジェクトにセキュリティを直接組み込むことができます。 マイクロ エージェントには、バイナリ パッケージとしてデプロイする機能やソース コードを変更する機能など、柔軟なデプロイ オプションが用意されています。 また、マイクロ エージェントは、Linux や Azure RTOS などの標準的な IoT オペレーティング システムで利用できます。  

Azure Defender for IoT マイクロ エージェントは、統合セキュリティ管理のため、セキュリティ体制管理に対するエンドポイントの可視性、脅威検出、および Microsoft の他のセキュリティ ツールへの統合を提供します。 

### <a name="security-posture-management"></a>セキュリティ体制管理

IoT デバイスのセキュリティ体制を事前に監視します。 Azure Defender for IoT では、CIS ベンチマークに基づくセキュリティ体制の推奨事項と、デバイス固有の推奨事項が提供されます。 OS 構成、ファイアウォール構成、アクセス許可など、オペレーティング システムのセキュリティを可視化できます。 

### <a name="endpoint-iotot-threat-detection"></a>エンドポイント IoT/OT の脅威検出

ボットネット、ブルート フォース試行、クリプト マイナー、疑わしいネットワーク アクティビティなどの脅威を検出します。 独自の組織で最も重要な脅威を対象とするカスタム アラートを作成します。 

### <a name="flexible-distribution-and-deployment-models"></a>柔軟な分散モデルとデプロイ モデル 

Azure Defender for IoT マイクロ エージェントにはソース コードが含まれているため、このマイクロ エージェントをファームウェアに組み込んだり、必要なものだけを含めるようにカスタマイズしたりすることができます。 また、バイナリ パッケージとして使用することも、他の Azure IoT ソリューションに直接統合することもできます。 

## <a name="see-also"></a>関連項目

[Azure Defender for IoT のアーキテクチャ](architecture.md)