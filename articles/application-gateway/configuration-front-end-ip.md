---
title: Azure Application Gateway のフロントエンド IP アドレスの構成
description: この記事では、Azure Application Gateway のフロントエンド IP アドレスを構成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 732054ae2cd8f93d17694e9ea1bdd85680e9c314
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2021
ms.locfileid: "98058652"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>Application Gateway のフロントエンド IP アドレスの構成

アプリケーション ゲートウェイは、パブリック IP アドレス、プライベート IP アドレス、またはその両方を持つように構成できます。 パブリック IP アドレスが必要となるのは、インターネットに接続している仮想 IP (VIP) を介してインターネット上でクライアントがアクセスする必要があるバックエンドをホストするときです。

## <a name="public-and-private-ip-address-support"></a>パブリック IP アドレスとプライベート IP アドレスのサポート

現在、Application Gateway V2 はプライベート IP モードのみをサポートしていません。 次の組み合わせをサポートしています。

* プライベート IP アドレスとパブリック IP アドレス
* パブリック IP アドレスのみ

詳細については、「[Application Gateway に関してよく寄せられる質問](application-gateway-faq.yml#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address)」を参照してください。


パブリック IP アドレスは、インターネットに公開されない内部エンドポイントには必要ありません。 これは、"*内部ロード バランサー*" (ILB) エンドポイントまたはプライベート フロントエンド IP と呼ばれます。 アプリケーション ゲートウェイ ILB は、インターネットに接続されていない内部の基幹業務アプリケーションで便利です。 また、インターネットに接続されていないセキュリティの境界にある多階層アプリケーション内のサービスや階層に役立ちますが、ラウンド ロビンの負荷分散、セッションの持続性、または TLS ターミネーションが必要です。

1 つのパブリック IP アドレスまたは 1 つのプライベート IP アドレスしかサポートされません。 アプリケーション ゲートウェイを作成するときにフロントエンド IP を選択します。

- パブリック IP アドレスの場合は、新しいパブリック IP アドレスの作成またはアプリケーション ゲートウェイと同じ場所にある既存のパブリック IP を使用できます。 詳細については、[静的なパブリック IP アドレスと動的なパブリック IP アドレス](./application-gateway-components.md#static-versus-dynamic-public-ip-address)に関するページを参照してください。

- プライベート IP アドレスの場合は、アプリケーション ゲートウェイが作成されるサブネットのプライベート IP アドレスを指定できます。 指定しないと、サブネットから任意の IP アドレスが自動的に選択されます。 選択した IP アドレスの種類 (静的または動的) は後で変更できません。 詳細については、[内部ロード バランサーを使用したアプリケーション ゲートウェイの作成](./application-gateway-ilb-arm.md)に関するページを参照してください。

フロントエンド IP アドレスが関連付けられた "*リスナー*" が、フロントエンド IP での着信要求をチェックします。

## <a name="next-steps"></a>次の手順

- [リスナーの構成について](configuration-listeners.md)