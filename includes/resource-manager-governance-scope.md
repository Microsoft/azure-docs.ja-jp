---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 14ac8ff54f5627509fe10a90bfdd0a01c07762d9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027015"
---
項目を作成する前に、スコープの概念について確認しましょう。 Azure には、管理グループ、サブスクリプション、リソース グループ、およびリソースという 4 つのレベルの管理が用意されています。 現在、[管理グループ](../articles/governance/management-groups/overview.md)はプレビュー リリースです。 次の図に、これらのレイヤーの例を示します。

![スコープ](./media/resource-manager-governance-scope/scope-levels.png)

これらのスコープ レベルのいずれかに管理設定を適用します。 選択するレベルで、設定の適用範囲が決まります。 上位レベルの設定が下位レベルに継承されます。 サブスクリプションに設定を適用すると、その設定はサブスクリプション内のすべてのリソース グループとリソースに適用されます。 リソース グループに設定を適用すると、その設定はリソース グループとそのすべてのリソースに適用されます。 ただし、別のリソース グループにその設定はありません。

通常、より高位のレベルで重要な設定を適用し、より低位のレベルでプロジェクト固有の要件を適用することをお勧めします。 たとえば、組織のすべてのリソースを確実に特定のリージョンに配置したい場合などがあります。 この要件を満たすには、許可された場所を指定するポリシーをサブスクリプションに適用します。 組織内の他のユーザーが新しいリソース グループとリソースを追加すると、許可された場所が自動的に適用されます。