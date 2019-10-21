---
title: Azure DNS プライベート ゾーンの仮想ネットワーク リンク サブリソースとは
description: Azure DNS プライベート ゾーンの仮想ネットワーク リンク サブリソースの概要
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: c5675dea2676122d96276b7cdb90b4c3c5340c7a
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71963727"
---
# <a name="what-is-a-virtual-network-link"></a>仮想ネットワーク リンクとは

Azure にプライベート DNS ゾーンを作成したら、どの仮想ネットワークからもすぐにアクセスできるわけではありません。 そのネットワークでホストされている VM がプライベート DNS ゾーンにアクセスできるようにするには、仮想ネットワークにリンクする必要があります。
プライベート DNS ゾーンを仮想ネットワークにリンクするには、プライベート DNS ゾーンの下に仮想ネットワーク リンクを作成する必要があります。 すべてのプライベート DNS ゾーンには、仮想ネットワーク リンクの子リソースのコレクションがあります。 これらのリソースは、仮想ネットワークへの接続をそれぞれ表しています。

仮想ネットワークは、登録仮想ネットワークまたは解決仮想ネットワークとして、プライベート DNS ゾーンにリンクできます。

## <a name="registration-virtual-network"></a>登録仮想ネットワーク

プライベート DNS ゾーンと仮想ネットワークの間の[リンクを作成する](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)ときに、仮想マシンの DNS レコードの[自動登録](./private-dns-autoregistration.md)をオンにすることができます。 このオプションを選択すると、仮想ネットワークはプライベート DNS ゾーンの登録仮想ネットワークになります。 ネットワークにデプロイする仮想マシンに対して、DNS レコードが自動的に作成されます。 DNS レコードは、仮想ネットワークに既にデプロイ済みの仮想マシンに対して作成されます。 仮想ネットワークの観点からは、プライベート DNS ゾーンはその仮想ネットワークの登録ゾーンになります。
1 つのプライベート DNS ゾーンは複数の登録仮想ネットワークを持つことができますが、各仮想ネットワークに関連付けることができるのは、1 つの登録ゾーンだけになります。

## <a name="resolution-virtual-network"></a>解決仮想ネットワーク

プライベート DNS ゾーンの下に仮想ネットワーク リンクを作成し、DNS レコードの自動登録を有効にしないことを選択した場合、仮想ネットワークは、解決専用の仮想ネットワークとして扱われます。 このようなネットワークにデプロイされた仮想マシンの DNS レコードは、リンクされたプライベート DNS ゾーンに自動的に作成されません。 ただし、このようなネットワークにデプロイされた仮想マシンは、プライベート DNS ゾーンから DNS レコードに対して正常にクエリを実行できます。 これらのレコードは、ユーザーが手動で作成することも、登録ネットワークとしてプライベート DNS ゾーンにリンクされている他の仮想ネットワークから取り込むこともできます。
1 つのプライベート DNS ゾーンは複数の解決仮想ネットワークを持つことができ、仮想ネットワークには複数の解決ゾーンを関連付けることができます。

## <a name="limits"></a>制限

プライベート DNS ゾーンにリンクできる登録ネットワークと解決ネットワークの数を把握するには、「[Azure DNS の制限](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)」を参照してください。

## <a name="other-considerations"></a>その他の考慮事項

* クラシック デプロイ モデルを使用してデプロイされた仮想ネットワークはサポートされていません。

* プライベート DNS ゾーンと仮想ネットワークの間に作成できるリンクは 1 つだけです。

* 1 つのプライベート DNS ゾーンの下にある各仮想ネットワーク リンクは、プライベート DNS ゾーンのコンテキスト内で一意の名前を持っている必要があります。 異なるプライベート DNS ゾーンには、同じ名前のリンクを含めることができます。

* 仮想ネットワーク リンクを作成したら、仮想ネットワーク リンク リソースの [リンクの状態] フィールドを確認します。 仮想ネットワークのサイズによっては、リンクが操作可能になって、リンクの状態が *[完了]* に変わるまでに数分かかる場合があります。

* 仮想ネットワークを削除すると、さまざまなプライベート DNS ゾーンにある、それに関連付けられたすべての仮想ネットワーク リンクと自動登録された DNS レコードが自動的に削除されます。

## <a name="next-steps"></a>次の手順

* [Azure portal](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) を使用して仮想ネットワークをプライベート DNS ゾーンにリンクする方法について確認します。

* [Azure PowerShell](./private-dns-getstarted-powershell.md) または [Azure CLI](./private-dns-getstarted-cli.md) を使用して Azure DNS のプライベート ゾーンを作成する方法を確認します。

* [プライベート ゾーンのシナリオ](./private-dns-scenarios.md)に関しては、Azure DNS のプライベート ゾーンで実現できるいくつかの一般的なシナリオを参照してください。

* Azure DNS のプライベート ゾーンに関する一般的な質問と回答については、[プライベート DNS の FAQ](./dns-faq-private.md) に関する記事をご覧ください。特定の種類の操作で期待できる固有の動作についても説明があります。
