---
title: Microsoft Azure を Oracle Cloud Infrastructure と統合する | Microsoft Docs
description: Microsoft Azure 上で実行されている Oracle アプリを Oracle Cloud Infrastructure (OCI) のデータベースと統合するソリューションについて説明します。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 0f35f22d6bd32d75d5c3b07d8a20b4bb7714e247
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71799555"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Microsoft Azure と Oracle Cloud Infrastructure を統合した Oracle アプリケーション ソリューション (プレビュー)

Microsoft と Oracle は、低待機時間、高スループットのクロスクラウド接続を提供し、双方のクラウドの長所を活用できるようにするためにパートナー関係を結びました。 

このクロスクラウド接続を使用すると、多層アプリケーションを分割し、Oracle Cloud Infrastructure (OCI) 上ではデータベース層を、Microsoft Azure 上ではアプリケーション層と他の層を実行できます。 このエクスペリエンスは、ソリューション スタック全体を 1 つのクラウドで実行する場合と似ています。 

> [!IMPORTANT]
> このクロスクラウド機能は現在プレビュー段階であり、[制限事項が適用されます](#preview-limitations)。 Azure と OCI の間で低待機時間の接続を確立するには、最初にお使いの Azure サブスクリプションでこの機能を有効にする必要があります。 この簡単な[アンケート フォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu)を完了することで、プレビューに登録する必要があります。 サブスクリプションが登録されると、メールが届きます。 確認メールを受信するまで、この機能を使用することはできません。 Microsoft の担当者に問い合わせて、このプレビューを有効にすることもできます。 プレビュー機能へのアクセスは可用性の対象であり、Microsoft の独自の判断によって制限されます。 アンケートを完了しても、アクセスが保証されるわけではありません。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

Oracle ソリューションを完全に Azure インフラストラクチャにデプロイすることに興味がある場合は、「[Oracle VM images and their deployment on Microsoft Azure (Oracle VM イメージとその Microsoft Azure へのデプロイ)](oracle-vm-solutions.md)」を参照してください。

## <a name="scenario-overview"></a>シナリオの概要

クロスクラウド接続によって、OCI のホステッド データベース サービスの利点を活用しながら、Azure 仮想マシン上で Oracle の業界最先端のアプリケーションとお客様独自のカスタム アプリケーションを実行できるソリューションが実現します。 

クロスクラウド構成では、次のようなアプリケーションを実行できます。

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail アプリケーション
* Oracle Hyperion Financial Management

次の図は、接続ソリューションの概要です。 わかりやすくするために、この図ではアプリケーション層とデータ層のみを示しています。 アプリケーション アーキテクチャによっては、Azure の Web 層などのその他の層をソリューションに含めることができます。 詳細については、次のセクションを参照してください。

![Azure OCI ソリューションの概要](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>プレビューの制限事項

* プレビューでのクロスクラウド接続は、Azure 米国東部 (eastus) および英国南部 (uksouth) リージョンと OCI Ashburn (米国東部) およびロンドン (英国南部) リージョンに限定されています。 英国南部については、待機時間が短くなるように相互接続をデプロイするときに、OCI の可用性ドメイン 1 (AD 1) を使用してください。

## <a name="networking"></a>ネットワーク

企業のお客様は、ビジネス上および運用上のさまざまな理由から、複数のクラウドにワークロードを分散し、デプロイすることを選択することがよくあります。 分散するために、お客様はインターネット、IPSec VPN、またはオンプレミス ネットワーク経由のクラウド プロバイダーの直接接続ソリューションを使用して、クラウド ネットワークを相互接続します。 クラウド ネットワークを相互接続するには、時間、費用、設計、調達、インストール、テスト、および運用に多大な投資が必要になることがあります。 

このようなお客様の課題に対処するために、Oracle と Microsoft は統合されたマルチクラウド エクスペリエンスを実現しました。 クロスクラウド ネットワーキングは、Microsoft Azure の [ExpressRoute](../../../expressroute/expressroute-introduction.md) 回線を OCI の [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) 回線に接続して確立されます。 この接続は、Azure ExpressRoute のピアリング場所が OCI FastConnect と近接している、または同じピアリング場所にある場合に実現できます。 この設定により、中間のサービス プロバイダーを必要とすることなく、2 つのクラウド間にセキュリティで保護された高速な接続が可能になります。

ExpressRoute と FastConnect を使用すると、プライベート IP アドレス空間が重複しない限り、お客様は Azure の仮想ネットワークを OCI の仮想クラウド ネットワークとピアリングできます。 2 つのネットワークをピアリングすると、同じネットワーク内にあるかのように、仮想ネットワーク内のリソースが OCI 仮想クラウド ネットワーク内のリソースと通信できるようになります。

## <a name="network-security"></a>ネットワークのセキュリティ

ネットワーク セキュリティは、あらゆるエンタープライズ アプリケーションの重要なコンポーネントであり、このマルチクラウド ソリューションの中心です。 ExpressRoute および FastConnect を経由するトラフィックはすべてプライベート ネットワークを経由します。 この構成で、Azure 仮想ネットワークと Oracle 仮想クラウド ネットワーク間のセキュリティで保護された通信を実現できます。 Azure の仮想マシンにパブリック IP アドレスを提供する必要はありません。 同様に、OCI にインターネット ゲートウェイは必要ありません。 通信はすべてマシンのプライベート IP アドレスを介して行われます。

さらに、OCI 仮想クラウド ネットワーク上の[セキュリティ リスト](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm)と、セキュリティ ルール (Azure [ネットワーク セキュリティ グループ](../../../virtual-network/security-overview.md)に添付) を設定できます。 これらのルールは、仮想ネットワーク内のマシン間を流れるトラフィックを制御するために使用します。 ネットワーク セキュリティ ルールは、マシン レベル、サブネット レベル、および仮想ネットワーク レベルで追加できます。
 
## <a name="identity"></a>ID

ID は、Microsoft と Oracle 間のパートナーシップの中心的な柱の 1 つです。 [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) と [Azure Active Directory](../../../active-directory/index.yml) (Azure AD) を統合するための重要な作業が行われました。 Azure AD は、Microsoft のクラウドベースの ID およびアクセス管理サービスです。 これにより、ユーザーがサインインし、さまざまなリソースにアクセスできるようになります。 Azure AD では、ユーザーとそのアクセス許可を管理することもできます。

現在は、この統合により、Azure Active Directory という 1 つの場所で集中管理できます。 Azure AD によってディレクトリ内のすべての変更が対応する Oracle ディレクトリと同期されます。また、Azure AD はクロスクラウド Oracle ソリューションへのシングル サインオンに使用されます。

## <a name="next-steps"></a>次の手順

Azure と OCI 間の[クロスクラウド ネットワーク](configure-azure-oci-networking.md)を使ってみます。 

OCI の詳細およびホワイトペーパーについては、[Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) のドキュメントを参照してください。
