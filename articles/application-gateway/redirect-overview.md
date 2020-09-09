---
title: Azure Application Gateway のリダイレクトの概要
description: あるリスナーで受信したトラフィックを別のリスナーまたは外部サイトにリダイレクトする Azure Application Gateway のリダイレクト機能について説明します。
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 8c2bac1bee5a2301a29589f15d72d0f69b4a05b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253853"
---
# <a name="application-gateway-redirect-overview"></a>Application Gateway のリダイレクトの概要

Application Gateway は、トラフィックをリダイレクトするために使用できます。  あるリスナーが受信したトラフィックを別のリスナーまたは外部サイトにリダイレクトすることができる汎用的なリダイレクト メカニズムがあります。 これにより、アプリケーションの構成が簡単になり、リソースの使用が最適化され、グローバルなリダイレクトやパスに基づくリダイレクトなどの新しいリダイレクト シナリオがサポートされるようになります。

HTTP を HTTPS に自動的にリダイレクトして、アプリケーションとユーザーの間のすべての通信が暗号化されたパスで行われるようにすることは、多くの Web アプリケーションでよくあるリダイレクトのシナリオです。 これまでは、HTTP で受信した要求を HTTPS にリダイレクトすることが唯一の目的である専用のバックエンド プールの作成といった手法が使われていました。 Application Gateway ではリダイレクトがサポートされているため、ルーティング規則に新しいリダイレクト構成を追加し、HTTPS プロトコルを使用する別のリスナーをターゲット リスナーとして指定するだけでこれを実現できます。

次の種類のリダイレクトがサポートされています。

- 301 Permanent Redirect (301 永続的なリダイレクト)
- 302 Found (302 検出)
- 303 See Other (303 他を参照)
- 307 Temporary Redirect (307 一時的なリダイレクト)

Application Gateway のリダイレクトのサポートでは、次の機能が提供されます。

-  **グローバルなリダイレクト**

   ゲートウェイ上のあるリスナーから別のリスナーへのリダイレクト。 これにより、サイトでの HTTP から HTTPS へのリダイレクトが可能になります。
- **パスに基づくリダイレクト**

   このタイプのリダイレクトを使うと、特定のサイト領域でのみ HTTP から HTTPS へのリダイレクトが可能になります (たとえば、/cart/* で示されたショッピング カート領域など)。
- **外部サイトへのリダイレクト**

![リダイレクト](./media/redirect-overview/redirect.png)

この変更により、お客様は、リダイレクト先のターゲット リスナーまたは外部サイトを指定する新しいリダイレクト構成オブジェクトを作成する必要があります。 構成要素は、リダイレクトされる URL に URI パスとクエリ文字列を追加できるオプションもサポートしています。 リダイレクトの種類を選択することもできます。 このリダイレクト構成は、作成されると、新しいルールによってソース リスナーに関連付けられます。 基本的なルールを使うと、リダイレクト構成はソース リスナーに関連付けられて、グローバル リダイレクトになります。 パスベース ルールを使用する場合、リダイレクトの構成は URL パス マップで定義されます。 したがって、サイトの特定のパス領域にのみ適用されます。

### <a name="next-steps"></a>次のステップ

[アプリケーション ゲートウェイに URL リダイレクトを構成する](tutorial-url-redirect-powershell.md)
