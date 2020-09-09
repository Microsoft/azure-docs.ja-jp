---
title: Azure Service Fabric Explorer を使用したクラスターの視覚化
description: Service Fabric Explorer は、Microsoft Azure Service Fabric クラスター内のクラウド アプリケーションとノードを検査および管理するためのアプリケーションです。
author: mikkelhegn
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 92f6f495f6aac23785dd2a21672747bffb07e2e3
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86256256"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Service Fabric Explorer を使用したクラスターの視覚化

Service Fabric Explorer (SFX) は、Azure Service Fabric クラスターを検査および管理するためのオープン ソース ツールです。 Service Fabric Explorer は、Windows、macOS、Linux 用のデスクトップ アプリケーションです。

## <a name="service-fabric-explorer-download"></a>Service Fabric Explorer のダウンロード

デスクトップ アプリケーションとして Service Fabric Explorer をダウンロードするには、次のリンクを使ってください。

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Service Fabric Explorer のデスクトップ バージョンでは、クラスター サポートより機能が多い場合や少ない場合があります。 クラスターに展開される Service Fabric Explorer バージョンにフォールバックすることで、完全な機能の互換性が保証されます。
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>クラスターからの Service Fabric Explorer の実行

Service Fabric Explorer は、Service Fabric クラスターの HTTP 管理エンドポイントでもホストされています。 Web ブラウザーで SFX を起動するには、任意のブラウザーからクラスターの HTTP 管理エンドポイント (例: https:\//clusterFQDN:19080) を参照します。

開発者ワークステーションのセットアップでは、 https://localhost:19080/Explorer に移動することにより、ローカル クラスターで Service Fabric Explorer を起動できます。 詳しくは、「[開発環境を準備する](service-fabric-get-started.md)」をご覧ください。

> [!NOTE]
> クラスターが自己署名証明書によってセキュリティで保護されている場合は、Web ブラウザーから、"This site is not secure (このサイトはセキュリティで保護されていません)" というエラー メッセージが表示されます。 ほとんどの最新の Web ブラウザーでは、この警告をオーバーライドすることによって単純に続行できます。 運用環境では、共通名と証明機関によって発行された証明書を使用して、クラスターをセキュリティで保護する必要があります。 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Service Fabric クラスターに接続する
Service Fabric クラスターに接続するには、クラスター管理エンドポイント (FQDN/IP) と HTTP 管理エンドポイント ポート (既定では 19080) が必要です。 たとえば、https\://mysfcluster.westus.cloudapp.azure.com:19080 のように指定します。 ワークステーションでローカル クラスターに接続するには、[ローカルホストに接続] チェック ボックスを使います。

### <a name="connect-to-a-secure-cluster"></a>セキュリティ保護されたクラスターに接続する
証明書または Azure Active Directory (AAD) を使用して、クライアントによる Service Fabric クラスターへのアクセスを制御できます。

セキュリティで保護されたクラスターに接続しようとすると、そのクラスターの構成によっては、クライアント証明書を指定するか、または AAD を使用してサインインすることが必要になります。

## <a name="understand-the-service-fabric-explorer-layout"></a>Service Fabric Explorer のレイアウトについて
左側のツリーを使用して、Service Fabric Explorer 内を移動できます。 ツリーのルートでは、クラスター ダッシュボードにクラスターの概要 (アプリケーションとノードの正常性の概要など) が表示されます。

![Service Fabric Explorer のクラスター ダッシュボード][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>クラスターのレイアウトを表示する
Service Fabric クラスターのノードは、障害ドメインとアップグレード ドメインの 2 次元グリッドにわたって配置されます。 このように配置することで、ハードウェアの障害やアプリケーションのアップグレード時にもアプリケーションを継続して利用できます。 現在のクラスターの配置を表示するには、クラスター マップを使用します。

![Service Fabric Explorer のクラスター マップ][sfx-cluster-map]

### <a name="view-applications-and-services"></a>アプリケーションとサービスを表示する
クラスターには、2 つのサブツリーが含まれています。1 つはアプリケーション、もう 1 つはノードのサブツリーです。

アプリケーション ビューでは、Service Fabric の論理階層 (アプリケーション、サービス、パーティション、レプリカ) 内を移動できます。

次の例では、**MyApp** アプリケーションは、**MyStatefulService** と **WebService** の 2 つのサービスで構成されています。 **MyStatefulService** はステートフルであるため、1 つのプライマリ レプリカと 2 つのセカンダリ レプリカがあるパーティションが 1 つ含まれています。 これに対して、WebSvcService はステートレスであり、インスタンスが 1 つだけ含まれています。

![Service Fabric Explorer のアプリケーション ビュー][sfx-application-tree]

ツリーの各レベルでは、メイン ウィンドウにその項目の関連情報が表示されます。 たとえば、特定のサービスの正常性状態とバージョンを確認できます。

![Service Fabric Explorer のメイン ウィンドウ][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>クラスターのノードを表示する
ノード ビューには、クラスターの物理的なレイアウトが表示されます。 特定のノードについて、そのノードでコードがデプロイされているアプリケーション、 正確に言うと、そこで現在実行されているレプリカを調べることができます。

## <a name="actions"></a>アクション
Service Fabric Explorer では、クラスター内のノード、アプリケーション、サービスに対する操作を簡単に呼び出すことができます。

たとえば、アプリケーション インスタンスを削除するには、左側のツリーでアプリケーションを選択し、 **[アクション]**  >  **[アプリケーションの削除]** の順に選択します。

![Service Fabric Explorer でのアプリケーションの削除][sfx-delete-application]

> [!TIP]
> 同じ操作を実行するには、各要素の横にある省略記号をクリックします。
>
> Service Fabric Explorer を使用して実行できる操作はすべて、PowerShell または REST API を使用して実行し、自動化することもできます。
>
>

Service Fabric Explorer を使用して、特定のアプリケーションの種類とバージョンのアプリケーション インスタンスを生成することもできます。 ツリー ビューでアプリケーションの種類を選択し、右側のウィンドウでバージョンの横にある **[Create app instance (アプリ インスタンスの作成)]** リンクをクリックします。

![Service Fabric Explorer でのアプリケーション インスタンスの作成][sfx-create-app-instance]

> [!NOTE]
> アプリケーション インスタンスを作成するとき、Service Fabric Explorer ではパラメーターはサポートされません。 アプリケーション インスタンスは、既定のパラメーター値を使います。
>
>

## <a name="event-store"></a>EventStore
EventStore は Service Fabric Explorer と REST API で使用可能な Service Fabric プラットフォーム イベントを提供する、プラットフォームによって提供される機能です。 ノード、サービス、アプリケーション、クエリなどの各エンティティに対してクラスター内で行われていることの時刻に基づくスナップショット ビューを表示できます。 「[EventStore の概要](service-fabric-diagnostics-eventstore.md)」で、EventStore の詳細を確認することもできます。   

![EventStore][sfx-eventstore]

>[!NOTE]
>Service Fabric バージョン 6.4 の時点で、 EventStore は既定では有効になりません。Resource Manager テンプレートで有効にする必要があります。

>[!NOTE]
>Service Fabric バージョン 6.4 の時点で、 EventStore API は現在、Azure 上で実行されている Windows クラスターでに対してのみ使用できます。 この機能を Linux およびスタンドアロン クラスターに移植する作業を進めています。

## <a name="image-store-viewer"></a>Image Store Viewer
Image Store Viewer は、Native Image Store を使用している場合に提供される機能であり、これを使用して、イメージ ストアの現在の内容を表示したり、ファイルやフォルダーの情報を取得したり、ファイルやフォルダーを削除したりできます。

![Service Fabric Explorer のクラスター マップ][sfx-imagestore]

## <a name="backup-and-restore"></a>バックアップと復元
Service Fabric Explorer には、[バックアップと復元](./service-fabric-reliable-services-backup-restore.md)とのインターフェイスを提供する機能が用意されています。 SFX でバックアップと復元の機能を表示するには、詳細設定モードを有効にする必要があります。

![詳細設定モードの有効化][0]
 
次の操作を実行できます。

* バックアップ ポリシーを作成、編集、削除します。
* アプリケーション、サービス、またはパーティションのバックアップを有効または無効にします。
* アプリケーション、サービス、またはパーティションのバックアップを中断および再開します。
* パーティションのバックアップをトリガーして追跡します。
* パーティションの復元をトリガーして追跡します。

バックアップと復元サービスの詳細については、[REST API リファレンス](/rest/api/servicefabric/sfclient-index-backuprestore)に関するページを参照してください。
## <a name="next-steps"></a>次のステップ
* [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)
* [PowerShell を使用した Service Fabric アプリケーションのデプロイメント](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png
