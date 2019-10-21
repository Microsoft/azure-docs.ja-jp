---
title: NIST SP 800-53 R4 ブループリント サンプル - 概要
description: NIST SP 800-53 R4 のブループリント サンプルの概要。 このブループリント サンプルは、お客様が特定の NIST SP 800-53 R4 コントロールを評価するのに役立ちます。
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: 2aa8b413ff6489e9d893b17348f7a0760527dbda
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299062"
---
# <a name="overview-of-the-nist-sp-800-53-r4-blueprint-sample"></a>NIST SP 800-53 R4 のブループリント サンプルの概要

NIST SP 800-53 R4 のブループリント サンプルでは、特定の NIST SP 800-53 R4 コントロールの評価に役立つ、[Azure Policy](../../../policy/overview.md) を使用したガバナンス ガードレールが提供されます。 このブループリントは、NIST SP 800-53 R4 コントロールの実施が必要とされる、Azure でデプロイされたアーキテクチャのために、一連の主要なポリシーをデプロイするのに役立ちます。

## <a name="control-mapping"></a>コントロール マッピング

コントロール マッピングのセクションでは、このブループリント内に含まれるポリシーについてと、それらのポリシーが NIST SP 800-53 R4 のさまざまなコントロールにどのように対応するかについて詳しく説明します。 リソースはアーキテクチャに割り当てられると、割り当て済みのポリシーに違反していないかどうかを Azure Policy によって評価されます。 詳細については、[Azure Policy](../../../policy/overview.md) に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

NIST SP 800-53 R4 のブループリント サンプルの概要を確認しました。 次に、コントロール マッピングと、このサンプルをデプロイする方法を確認するには、次の記事を参照してください。

> [!div class="nextstepaction"]
> [NIST SP 800-53 R4 ブループリント - コントロール マッピング](./control-mapping.md)
> [NIST SP 800-53 R4 ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
