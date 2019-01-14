---
title: サポート - Azure の専用 HSM | Microsoft Docs
description: さまざまなシナリオにおける Azure の専用 HSM のサポート オプションと責任の範囲
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 2ed6a79b8736a1d3b472e31ce643c0d1ee085bbb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085911"
---
# <a name="azure-dedicated-hsm-supportability"></a>Azure の専用 HSM のサポート

Azure の専用 HSM サービスでは、完全な管理制御と管理責任が備わった、お客様専用の物理デバイスが提供されます。 使用できるようになったデバイスは、[Gemalto SafeNet Luna 7 HSM モデル A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) です。 お客様がプロビジョニングを行った後は、監視ロールで物理シリアル ポートに接続することを除き、Microsoft による管理アクセスはできなくなります。  アクセス権がないため、Microsoft には継続的なソフトウェア レベルのメンテナンスまたはシステム管理の責任はありません。 そのため、お客様が一般的な運用アクティビティを担当する必要があります。
お客様は、Gemalto からサポートまたはコンサルティング ベースの支援を受け、HSM を使用しているアプリケーションについての全責任を負います。 運用の健全性はお客様の責任であるため、Microsoft はこのサービスについていかなる高可用性の保証も行うことはできません。 高可用性を実現するためにアプリケーションが正しく構成されていることを確認するのは、お客様の責任です。 Microsoft は、デバイスの正常性とネットワーク接続の監視および管理を行います。

## <a name="gemalto-support"></a>Gemalto のサポート

専用 HSM サービスを使用するお客様は、Gemalto とサポート契約を結ぶ必要があります。 サポート契約の一部として、Gemalto はお客様に直接ガイダンス、サポート、およびサービスを提供します。 Gemalto のサポートは、[カスタマー サポート ポータル](https://supportportal.gemalto.com/csm/)を通して受けることができます。
Gemalto は、HSM を使用するために必要なすべてのソフトウェア コンポーネント (たとえば、クライアント アクセス ソフトウェアや SDK) を提供します。 構成のサポートや、SafeNet Luna 7 HSM を使用するアプリケーションの設計、開発、およびデプロイに関するコンサルティング サービスも提供されます。

### <a name="software-components"></a>ソフトウェア コンポーネント

HSM デバイスの構成には、さまざまなソフトウェア コンポーネントが使用されています。

* クライアント ソフトウェア
* SDK
* ツール

### <a name="guidance"></a>ガイダンス

Gemalto は、[カスタマー サポート ポータル](https://supportportal.gemalto.com/csm/)で管理および構成についてのガイダンスを公開しています。 有効な顧客 ID を使用してサインインすると、これらのドキュメントをダウンロードできます。 Gemalto は、さまざまなシナリオやソフトウェアの統合を支援する一連の統合ガイドも提供しています。 詳細については、[Gemalto の Microsoft パートナー サイト](https://safenet.gemalto.com/partners/microsoft/)を参照してください。

### <a name="support"></a>サポート

専用 HSM サービスの一部で、ソフトウェア レベルの問題や HSM の使用に関する質問が発生した場合、Gemalto のサポートが直接対応します。 上記の一覧のすべてのソフトウェア コンポーネントおよびプロビジョニング後のカスタム HSM 構成は、Gemalto が対応します。 詳細については、[Gemalto カスタマー サポート ポータル](https://supportportal.gemalto.com/csm/)を参照してください。

### <a name="consulting-services"></a>コンサルティング サービス

HSM を使用するカスタム アプリケーションの設計、開発、およびデプロイのサポートについては、Gemalto のアカウント担当者にお問い合わせください。

## <a name="microsoft-support"></a>Microsoft のサポート

Microsoft は、物理 HSM デバイスが到達可能で、単一のお客様が占有して使用できる動作状態とする責任を負います。 お客様は、デバイスの管理責任を負います。 Microsoft の責任は次のとおりです。

* デバイスの電源および冷却
* HSM の動作状態の維持 (障害シナリオなど)
* ネットワーク経由でのデバイスへのアクセス

次のような問題は、Microsoft に報告されます。

* コンポーネント障害
* 完全なデバイス障害
* ネットワーク アクセスの問題
* プロビジョニングおよびプロビジョニング解除の問題

Microsoft は、監視ロール (つまり、管理ロールではない) でデバイスの物理シリアル ポートにアクセスすることで、基本的な正常性テレメトリを実施します。  これにより、Microsoft は、お客様に問題の事前通知を行えるようになります。ただし、お客様がこのアクセス許可を制限した場合は除きます。 

### <a name="provisioning-and-decommissioning"></a>プロビジョニングとプロビジョニング解除

お客様が専用 HSM サービスの登録を承認すると、HSM リソースを作成できるようになります (現在のところ、PowerShell またはコマンド ライン インターフェイスを使用します。Azure portal は使用できません)。 リソースの割り当てプロセスが実行され、指定されたリージョンの物理デバイスがお客様によって事前に定義された仮想ネットワーク (VNet) にマップされます。 デバイスが VNet に表示されると、デバイスにアクセスして要件に従って構成できるようになります。 お客様は、Gemalto クライアント ソフトウェアおよびツールを使用して、専用 HSM にアクセスします。 リソース作成プロセスは、Microsoft がサポートを行います。 カスタム構成プロセス以降は、Gemalto がサポートを行います。 (前述の Gemalto のサポートを参照してください。) HSM の使用が終わった際には、データが永続化されないように、リセット (またはゼロ埋め) を行う必要があります。 デバイスをリセットするプロセスによって、すべてのカスタム構成とデータが削除されます。 Microsoft は、デバイスの割り当てを解除し、きれいな状態でプールに返します。 つまり、デバイスがプールに返される際には、以前のお客様のアクティビティの証拠は残されません。 

### <a name="hardware-issues"></a>ハードウェアの問題

HSM デバイスの電源とファン ユニットは、冗長かつ交換可能になっています。 デバイスの電源を入れたままファン ユニットを外すと、改ざんイベントが発生します。 コンポーネントの障害が発生した場合、Microsoft は中断時間が最も短く、お客様のサービス可用性に対するリスクが最も低くなるように、最適なプロセスを使用してコンポーネント レベルの問題に対処します。
デバイスにより深刻な障害が発生した場合、そのデバイスを空きプールの新しいデバイスと交換します。 お客様が新しいデバイスを既存の HA ペアに含めると、デバイスが同期されて完全な運用状態に戻ります。 障害が発生したデバイスは、データ関連のデバイスを切り離させ、データ センターのサイト上に細分化させます。 シャーシのみが、リサイクル用に Gemalto に返却されます。


### <a name="networking-issues"></a>ネットワークの問題

HSM デバイスに対するネットワーク アクセスの問題が発生した場合は、Microsoft のサポートに問い合わせる必要があります。 ネットワーク アクセスを確認する簡単なテストでは、SSH を使用して HSM デバイスに接続します。 これに失敗した場合は、Microsoft サポートにお問い合わせください。

## <a name="service-level-expectations-for-support"></a>サポートで期待されるサービス レベル

Microsoft のサポートのサービス レベルについては、[Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を参照してください
Gemalto のサポートのサービス レベルについては、[Gemalto サポートの要点に関するページ](https://azure.microsoft.com/support/plans/)を参照してください。

## <a name="next-steps"></a>次の手順

デバイスのプロビジョニングやアプリケーションの設計、アプリケーションのデプロイ前に、高可用性やセキュリティの主要概念を十分に理解しておくことをお勧めします。

* [デプロイ アーキテクチャ](deployment-architecture.md)
* [高可用性](high-availability.md)
* [物理的なセキュリティ](physical-security.md)
* [ネットワーク](networking.md)
* [監視](monitoring.md)