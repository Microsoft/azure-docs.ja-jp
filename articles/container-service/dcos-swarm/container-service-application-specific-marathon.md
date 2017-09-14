---
title: "アプリケーションまたはユーザー固有の Marathon サービス | Microsoft Docs"
description: "アプリケーションまたはユーザー固有の Marathon サービスの作成"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "コンテナー, Marathon, マイクロサービス, DC/OS, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2016
ms.author: rogardle
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 3134872eb59f2f6219499f3d5a92673f680af04d
ms.contentlocale: ja-jp
ms.lasthandoff: 07/25/2017

---
# <a name="create-an-application-or-user-specific-marathon-service"></a>アプリケーションまたはユーザー固有の Marathon サービスの作成
Azure コンテナー サービスは、Apache Mesos と Marathon が事前構成されている、一連のマスター サーバーを提供します。 これらはクラスター上のアプリケーションを調整するために使用できますが、この目的のためにはマスター サーバーを使用しないことをお勧めします。 たとえば、Marathon の構成を調整するには、マスター サーバー自体にログインし、変更を行う必要があります。そのため、標準的なマスター サーバーとは少し異なる特殊なものになりやすく、個別に注意して管理する必要があります。 さらに、あるチームで必要とされる構成が、別のチームにとっては最適な構成でない場合もあります。

この記事では、アプリケーションまたはユーザー固有の Marathon サービスを追加する方法について説明します。

このサービスは単一のユーザーまたはチームに属するため、好きな方法で自由に構成できます。 また、Azure コンテナー サービスは、サービスの実行が続行されるようにします。 サービスで障害が発生すると、Azure コンテナー サービスが自動的にサービスを再開します。 ほとんどの場合、ダウンタイムがあったことにさえ気付きません。

## <a name="prerequisites"></a>前提条件
オーケストレーターの種類が DC/OS の [Azure Container Service のインスタンスをデプロイ](container-service-deployment.md)し、[クライアントがクラスターに接続できるようにします](../container-service-connect.md)。 また、次の手順を実行します。

[!INCLUDE [install the DC/OS CLI](../../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>アプリケーションまたはユーザー固有の Marathon サービスの作成
最初に、作成するアプリケーション サービスの名前を定義する JSON 構成ファイルを作成します。 ここでは、フレームワーク名として `marathon-alice` を使用します。 `marathon-alice.json`のような名前でファイルを保存します。

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

次に、DC/OS CLI を使用して、構成ファイルで設定されているオプションで Marathon インスタンスをインストールします。

```bash
dcos package install --options=marathon-alice.json marathon
```

これで、実行されている `marathon-alice` サービスが、DC/OS UI のサービス タブに表示されます。 直接アクセスする場合、UI は `http://<hostname>/service/marathon-alice/` です。

## <a name="set-the-dcos-cli-to-access-the-service"></a>サービスにアクセスするための DC/OS CLI の設定
必要に応じて、この新しいサービスにアクセスするように DC/OS CLI を構成することができます。そのためには、次のように、`marathon-alice` インスタンスを指すように `marathon.url` プロパティを設定します。

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

CLI の操作対象となる Marathon インスタンスを確認するには、 `dcos config show` コマンドを使用できます。 また、マスター Marathon サービスを使用するように戻すには、 `dcos config unset marathon.url`コマンドを使用できます。


