---
title: Azure Service Fabric Mesh の状態ストレージ オプション
description: Azure Service Fabric Mesh 上で実行されている Service Fabric Mesh アプリケーションに状態を確実に格納する方法について説明します。
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: d56ce811155f7b7f60fa870dbdae2550afab2eac
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246828"
---
# <a name="state-management-with-service-fabric"></a>Service Fabric を使用した状態の管理

Service Fabric では、状態ストレージのさまざまなオプションをサポートします。 状態管理のパターンと Service Fabric の概念的概要については、[Service Fabric の概念の状態](../service-fabric/service-fabric-concepts-state.md)に関する記事を参照してください。 サービスが実行されているのが Service Fabric Mesh の中か外にかかわらず、これらと同じ概念が適用されます。 

Service Fabric Mesh を使用することにより、新しいアプリケーションを簡単にデプロイし、Azure 上でホストされている既存のデータ ストアに接続できます。 任意のリモート データベースを使用するだけでなく、サービスで使用するローカルまたはリモートのストレージに応じて、データを格納するためのいくつかのオプションがあります。 

## <a name="volumes"></a>ボリューム

多くの場合、コンテナーでは一時ディスクが利用されます。 ただし、一時ディスクは一時的なので、コンテナーがクラッシュすると、新しい一時ディスクが取得され、情報は失われます。 また、一時ディスク上の情報を他のコンテナーと共有することは困難でもあります。 ボリュームは、コンテナー インスタンス内にマウントされ、状態を保持するために使用できるディレクトリです。 ボリュームを利用することで、汎用目的のファイル ストレージが提供され、通常のディスク I/O ファイル API を利用してファイルを読み書きすることができます。 ボリューム リソースには、ディレクトリをマウントする方法と、使用するバッキング ストレージについて説明されています。 データを保存するには、Azure File Storage または Service Fabric ボリューム ディスクを選択できます。

![ボリューム][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric Reliable Volume

Service Fabric Reliable Volume は、コンテナーにローカル ボリュームをマウントするために使用される Docker ボリューム ドライバーです。 読み取りと書き込みはローカル操作であり、高速です。 データはセカンダリ ノードにレプリケートされるため、可用性が高くなります。 フェールオーバーも高速です。 コンテナーがクラッシュすると、データのコピーが既にあるノードにフェールオーバーします。 例については、[Service Fabric Reliable Volume を使用してアプリをデプロイする方法](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md)に関するページを参照してください。

### <a name="azure-files-volume"></a>Azure Files Volume

Azure Files Volume は、コンテナーに Azure Files 共有をマウントするために使用される Docker ボリューム ドライバーです。 Azure File Storage にはネットワーク ストレージが使用されるため、読み取りと書き込みはネットワーク経由で行われます。 Service Fabric Reliable Volume と比較すると、Azure Files Storage はパフォーマンスが劣りますが、より安価で完全に信頼できるデータ オプションが用意されています。 例については、[Azure Files Volume を使用してアプリをデプロイする方法](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

アプリケーション モデルについては、[Service Fabric のリソース](service-fabric-mesh-service-fabric-resources.md)に関する記事を参照してください。

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
