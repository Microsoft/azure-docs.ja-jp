---
title: Azure Functions のネットワーク オプション
description: Azure Functions で利用可能なすべてのネットワーク オプションの概要。
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 7b47e7b0672716141f62e3f7df4b0d3ed95c663d
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114286"
---
# <a name="azure-functions-networking-options"></a>Azure Functions のネットワーク オプション

この記事では、Azure Functions のホスティング オプションで利用できるネットワーク機能について説明します。 次のネットワーク オプションはすべて、インターネットのルーティング可能アドレスを使用せずにリソースにアクセスする機能、または関数アプリへのインターネット アクセスを制限する機能を提供します。

ホスティング モデルには、さまざまなレベルのネットワーク分離機能があります。 正しいものを選択すると、ネットワーク分離の要件を満たすために役立ちます。

関数アプリは、いくつかの方法でホストできます。

* さまざまなレベルの仮想ネットワーク接続とスケーリングのオプションが設定された、マルチテナント インフラストラクチャ上で実行される一連のプラン オプションがあります。
    * [従量課金プラン](functions-scale.md#consumption-plan)。負荷に応じて動的にスケーリングし、最小限のネットワークの分離オプションを提供します。
    * [Premium プラン](functions-scale.md#premium-plan)。このプランでも動的なスケーリングが行われますが、より包括的なネットワークの分離が提供されます。
    * Azure [App Service プラン](functions-scale.md#app-service-plan)。固定されたスケールで動作し、Premium プランと同様のネットワーク分離を提供します。
* [App Service Environment](../app-service/environment/intro.md) で関数を実行できます。 この方法では、関数を仮想ネットワークにデプロイし、完全なネットワーク制御と分離を提供します。

## <a name="matrix-of-networking-features"></a>ネットワーク機能のマトリックス

|                |[従量課金プラン](functions-scale.md#consumption-plan)|[Premium プラン](functions-scale.md#premium-plan)|[[App Service プラン]](functions-scale.md#app-service-plan)|[App Service 環境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[受信 IP の制限とプライベート サイトへのアクセス](#inbound-ip-restrictions)|✅はい|✅はい|✅はい|✅はい|
|[仮想ネットワークの統合](#virtual-network-integration)|❌いいえ|✅はい (リージョン)|✅はい (リージョンとゲートウェイ)|✅はい|
|[仮想ネットワーク トリガー (非 HTTP)](#virtual-network-triggers-non-http)|❌いいえ| ✅はい |✅はい|✅はい|
|[ハイブリッド接続](#hybrid-connections) (Windows のみ)|❌いいえ|✅はい|✅はい|✅はい|
|[送信 IP の制限](#outbound-ip-restrictions)|❌いいえ| ❌いいえ|❌いいえ|✅はい|

## <a name="inbound-ip-restrictions"></a>受信 IP の制限

IP 制限を使用すると、アプリへのアクセスを許可または拒否される IP アドレスの優先順位付きリストを定義できます。 このリストには、IPv4 アドレスと IPv6 アドレスを含めることができます。 1 つ以上のエントリがある場合、リストの最後にあるものは暗黙的に "すべて拒否" になります。 IP 制限は、すべての関数ホスティング オプションで有効です。

> [!NOTE]
> ネットワーク制限が適用されている場合、ポータル エディターを使用できるのは、仮想ネットワーク内から、または Azure portal へのアクセスに使用しているコンピューターの IP アドレスを [信頼できる宛先のリスト] に入れている場合のみになります。 ただし、 **[プラットフォーム機能]** タブの機能にはすべてのコンピューターから引き続きアクセスできます。

詳細については、「[Azure App Service の静的なアクセス制限](../app-service/app-service-ip-restrictions.md)」を参照してください。

## <a name="private-site-access"></a>プライベート サイトへのアクセス

プライベート サイトへのアクセスとは、Azure 仮想ネットワークなどのプライベート ネットワークからのみアプリにアクセスできるようにすることです。

* プライベート サイトへのアクセスは、サービス エンドポイントが構成されている場合に [Premium](./functions-premium-plan.md)、[従量課金](functions-scale.md#consumption-plan)、および [App Service](functions-scale.md#app-service-plan) の各プランで利用できます。
    * サービス エンドポイントは、 **[プラットフォーム機能]**  >  **[ネットワーク]**  >  **[アクセス制限を構成する]**  >  **[ルールの追加]** から、アプリごとに構成できます。 これで、仮想ネットワークをルールの種類として選択できるようになりました。
    * 詳細については、「[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)」を参照してください。
    * サービス エンドポイントがあれば、仮想ネットワーク統合が構成されていても、関数はインターネットへの完全な送信アクセスを引き続き持つことに注意してください。
* プライベート サイトへのアクセスは、内部ロード バランサー (ILB) を使用して構成されている App Service Environment 内でも使用可能です。 詳細については、「[App Service Environment で内部ロード バランサーを作成して使用する](../app-service/environment/create-ilb-ase.md)」を参照してください。

## <a name="virtual-network-integration"></a>仮想ネットワークの統合

仮想ネットワーク統合により、関数アプリは仮想ネットワーク内のリソースにアクセスできます。 この機能は、Premium プランと App Service プランの両方で使用できます。 アプリが App Service Environment 内にある場合は、既に仮想ネットワーク内にあり、同じ仮想ネットワーク内のリソースに到達するために仮想ネットワーク統合は必要はありません。

仮想ネットワーク統合を使用すると、アプリから、仮想ネットワークで実行されているデータベースや Web サービスにアクセスできます。 仮想ネットワーク統合では、VM 上でアプリケーション用にパブリック エンドポイントを公開する必要はありません。 代わりに、インターネット ルーティングできないプライベート アドレスを使用できます。

仮想ネットワーク統合には、次の 2 つの形式があります。

+ **リージョンでの仮想ネットワーク統合 (プレビュー)** :同じリージョン内の仮想ネットワークとの統合を有効にします。 この種類の統合では、同じリージョンの仮想ネットワーク内にサブネットが必要です。 この機能はまだプレビュー段階ですが、Windows で実行されている関数アプリでサポートされています。ただし、次の問題/ソリューションの表の後に記載されている注意事項があります。
+ **ゲートウェイが必要な仮想ネットワーク統合**:リモート リージョン内の仮想ネットワーク、またはクラシック仮想ネットワークとの統合を可能にします。 この種類の統合では、VNet への仮想ネットワーク ゲートウェイのデプロイが必要になります。 これはポイント対サイト VPN ベースの機能であり、Windows で実行されている関数アプリでのみサポートされています。

アプリでは、一度に 1 種類の仮想ネットワーク統合機能しか使用できません。 どちらも多くのシナリオで役に立ちますが、次の表にそれぞれを使用すべき場合を示します。

| 問題  | 解決策 |
|----------|----------|
| 同じリージョン内の RFC 1918 アドレス (10.0.0.0/8、172.16.0.0/12、192.168.0.0/16) に到達する必要がある | リージョンでの仮想ネットワーク統合 |
| クラシック仮想ネットワーク内、または別のリージョンの仮想ネットワーク内のリソースに到達する必要がある | ゲートウェイが必要な仮想ネットワーク統合 |
| Azure ExpressRoute で RFC 1918 エンドポイントに到達する必要がある | リージョンでの仮想ネットワーク統合 |
| サービス エンドポイントにまたがるリソースに到達する必要がある | リージョンでの仮想ネットワーク統合 |

どちらの機能を使用しても、ExpressRoute で RFC 1918 以外のアドレスに到達することはできません。 これを行うには、現時点では、App Service Environment を使用する必要があります。

リージョンでの仮想ネットワーク統合を使用しても、仮想ネットワークがオンプレミスのエンドポイントに接続されたり、サービス エンドポイントが構成されたりすることはありません。 それは別のネットワーク構成です。 リージョンでの仮想ネットワーク統合は単に、アプリがそれらの接続の種類を越えて呼び出しを行うことができるようにします。

使用されているバージョンに関係なく、仮想ネットワーク統合により、関数アプリは仮想ネットワーク内のリソースにアクセスできるようになりますが、仮想ネットワークからその関数アプリへのプライベート サイト アクセスは許可されません。 プライベート サイトへのアクセスとは、Azure 仮想ネットワークなどのプライベート ネットワークからのみアプリにアクセスできるようにすることです。 仮想ネットワーク統合は、アプリから仮想ネットワークへの送信呼び出しを行うためだけのものです。

仮想ネットワーク統合の機能:

* Standard、Premium、または PremiumV2 の App Service プランが必要
* TCP と UDP をサポート
* App Service アプリや関数アプリで動作可能

以下に、仮想ネットワーク統合でサポートされていないことの例を示します。

* ドライブのマウント
* Active Directory 統合
* NetBIOS

Azure Functions の仮想ネットワーク統合では、App Service Web アプリと共有インフラストラクチャを使用します。 2 種類の仮想ネットワーク統合の詳細については、次を参照してください。

* [リージョンでの仮想ネットワーク統合](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [ゲートウェイが必要な仮想ネットワーク統合](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

仮想ネットワーク統合の使用について詳しくは、「[関数アプリを Azure 仮想ネットワークに統合する](functions-create-vnet.md)」を参照してください。

## <a name="connecting-to-service-endpoint-secured-resources"></a>サービス エンドポイントのセキュリティで保護されたリソースへの接続

> [!NOTE]
> 現在、ダウンストリーム リソースにアクセス制限を構成してから、新しいサービス エンドポイントが関数アプリで使用できるようになるまでに最大 12 時間かかる場合があります。 この期間中は、アプリでリソースを完全に使用できなくなります。

より高度なセキュリティを実現するために、サービス エンドポイントを使用して、仮想ネットワークに対する Azure サービス数を制限することができます。 次に、関数アプリをその仮想ネットワークと統合して、リソースにアクセスする必要があります。 この構成は、仮想ネットワークの統合をサポートするすべての計画でサポートされています。

[仮想ネットワーク サービス エンドポイントの詳細を確認する](../virtual-network/virtual-network-service-endpoints-overview.md)。

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>お使いのストレージ アカウントを仮想ネットワークに制限する

関数アプリを作成するときは、BLOB、Queue、および Table Storage をサポートする汎用の Azure Storage アカウントを作成またはリンクする必要があります。 現在、このアカウントに対して仮想ネットワークの制限を使用することはできません。 関数アプリに使用しているストレージ アカウントに仮想ネットワーク サービス エンドポイントを構成すると、アプリが中断されます。この機能は現在、Premium プランと仮想ネットワーク統合を使用して利用できます。

[ストレージ アカウントの要件の詳細を確認する](./functions-create-function-app-portal.md#storage-account-requirements)。

### <a name="using-key-vault-references"></a>Key Vault 参照の使用 

Key Vault 参照を使用すると、コードを変更せず、Azure Functions アプリケーションの Azure Key Vault のシークレットを使用することができます。 Azure Key Vault は、アクセス ポリシーと監査履歴を完全制御する、一元化されたシークレット管理を提供するサービスです。

現在、Key Vault がサービス エンドポイントで保護されている場合、[Key Vault 参照](../app-service/app-service-key-vault-references.md)は機能しません。 仮想ネットワーク統合を使用して Key Vault に接続するには、アプリケーション コードで Key Vault を呼び出す必要があります。

## <a name="virtual-network-triggers-non-http"></a>仮想ネットワーク トリガー (非 HTTP)

現時点では、次の 2 つの方法のいずれかで、仮想ネットワーク内から非 HTTP トリガー関数を使用できます。 
+ Premium プランで関数アプリを実行し、仮想ネットワーク トリガーのサポートを有効にする。
+ App Service プランまたは App Service 環境で関数アプリを実行する。

### <a name="premium-plan-with-virtual-network-triggers"></a>仮想ネットワーク トリガーを使用した Premium プラン

Premium プランで実行する場合は、仮想ネットワーク内で実行されているサービスに非 HTTP トリガー関数を接続できます。 これを行うには、関数アプリの仮想ネットワーク トリガーのサポートを有効にする必要があります。 **仮想ネットワーク トリガーのサポート**の設定は、[Azure portal](https://portal.azure.com) の **[関数アプリの設定]** にあります。

![VNETToggle](media/functions-networking-options/virtual-network-trigger-toggle.png)

次の Azure CLI コマンドを使用して、仮想ネットワーク トリガーを有効にすることもできます。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

仮想ネットワーク トリガーは、バージョン 2.x 以降の Functions ランタイムでサポートされています。 次の非 HTTP トリガーの種類がサポートされています。

| 拡張機能 | 最小バージョン |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 以降 |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 以降|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 以降|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 以降|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 以降|

> [!IMPORTANT]
> 仮想ネットワーク トリガーのサポートを有効にすると、上記の種類のトリガーのみがアプリケーションで動的にスケーリングされます。 上記以外のトリガーも使用できますが、事前ウォーミングされたインスタンス数を超えてスケーリングされることはありません。 トリガーの全一覧については、「[トリガーとバインド](./functions-triggers-bindings.md#supported-bindings)」を参照してください。

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>仮想ネットワーク トリガーを使用した App Service プランと App Service 環境

関数アプリを App Service プランまたは App Service 環境のいずれかで実行する場合は、非 HTTP トリガー関数を使用できます。 関数が正しくトリガーされるようにするには、トリガー接続で定義されているリソースにアクセスできる仮想ネットワークに接続する必要があります。 

たとえば、仮想ネットワークからのみトラフィックを受け入れるように Azure Cosmos DB を構成するとします。 この場合、その仮想ネットワークとの仮想ネットワーク統合を提供する App Service プランで関数アプリをデプロイする必要があります。 これにより、その Azure Cosmos DB リソースによって関数がトリガーされるようになります。 

## <a name="hybrid-connections"></a>ハイブリッド接続

[ハイブリッド接続](../service-bus-relay/relay-hybrid-connections-protocol.md)は、他のネットワークのアプリケーション リソースにアクセスするために使用できる Azure Relay の機能です。 アプリからアプリケーション エンドポイントにアクセスできます。 アプリケーションにアクセスするために使用することはできません。 ハイブリッド接続は、Windows で実行されている、従量課金プラン以外の関数に使用できます。

Azure Functions で使用されるとき、各ハイブリッド接続は、単一の TCP ホストとポートの組み合わせに相互に関連付けられます。 つまり、TCP リッスン ポートにアクセスしている限り、任意のオペレーティング システムの任意のアプリケーションがハイブリッド接続のエンドポイントになることができます。 ハイブリッド接続機能では、アプリケーション プロトコルやアクセス先は認識されません。 ネットワーク アクセスを提供するだけです。

詳細については、[ハイブリッド接続に関する App Service ドキュメント](../app-service/app-service-hybrid-connections.md)を参照してください。 これらの同じ構成手順を Azure Functions に使用できます。

>[!IMPORTANT]
> ハイブリッド接続は、Windows プランでのみサポートされています。 Linux はサポートされていません。

## <a name="outbound-ip-restrictions"></a>送信 IP の制限

送信 IP の制限は、App Service Environment にデプロイされた関数でのみ利用可能です。 App Service Environment が展開されている仮想ネットワークの送信制限を構成することができます。

Premium プランまたは App Service プランの関数アプリを仮想ネットワークと統合した場合でも、アプリはインターネットへの送信呼び出しを行うことができます。

## <a name="next-steps"></a>次のステップ

ネットワークと Azure Functions の詳細については、以下を参照してください。

* [仮想ネットワーク統合の概要に関するチュートリアル](./functions-create-vnet.md)
* [関数のネットワークに関する FAQ](./functions-networking-faq.md)
* [仮想ネットワーク統合と App Service/Functions に関する詳細情報](../app-service/web-sites-integrate-with-vnet.md)
* [Azure の仮想ネットワークに関する詳細情報](../virtual-network/virtual-networks-overview.md)
* [App Service Environment でさらなるネットワーク機能と制御を可能にする](../app-service/environment/intro.md)
* [ハイブリッド接続を使用して、ファイアウォールを変更せずに個々のオンプレミス リソースに接続する](../app-service/app-service-hybrid-connections.md)
