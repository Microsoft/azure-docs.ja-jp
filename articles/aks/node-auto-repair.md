---
title: Azure Kubernetes Service (AKS) ノードの自動修復
description: ノードの自動修復機能と壊れたワーカー ノードを AKS で修復するしくみについて説明します。
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 7fcb7b380f3694aaf34328019c3e09f5157c9e64
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542044"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes Service (AKS) ノードの自動修復

AKS によってワーカー ノードの正常性状態が継続的に確認され、正常ではなくなった場合、ノードが自動修復されます。 このドキュメントでは、ノードの自動修復機能の動作についてオペレーターに説明します。 AKS の修復に加えて、Azure プラットフォームでは、問題が発生した[仮想マシンに対してメンテナンスが実行されます][vm-updates]。 AKS と Azure VM が連携し、クラスターのサービス中断が最小限に抑えられます。

## <a name="limitations"></a>制限事項

* Windows ノード プールは現在サポートされていません。

## <a name="how-aks-checks-for-unhealthy-nodes"></a>正常ではないノードを AKS で確認する方法

AKS では、ノードが正常ではなく、修復が必要かどうかを、ルールを利用して判断します。 AKS では、自動修復が必要かどうかを、次のルールで決定します。

* 10 分という時間枠内で連続して確認した結果、**NotReady** という状態がノードから報告されます
* ノードから、10 分以内に状態が報告されません

kubectl を利用し、ノードの正常性状態を手動確認できます。

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>自動修復のしくみ

> [!Note]
> AKS が、ユーザー アカウント **AKS-remediator** を使用して修復操作を開始します。

上記のルールに基づいてノードが異常であると判断され、異常が 10 分間継続した場合、AKS はノードを再起動します。 初期修復操作が実行されてもノードが異常な状態のままである場合は、AKS エンジニアが追加の修復について調査します。
  
正常性チェック中に複数のノードで異常が検出された場合は、各ノードを個別に修復してから、別の修復が開始されます。

## <a name="next-steps"></a>次のステップ

[Availability Zones][availability-zones] を使用し、AKS クラスター ワークロードで高可用性を上げます。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
