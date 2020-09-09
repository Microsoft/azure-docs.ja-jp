---
title: Qualys Vulnerability Management データを Azure Sentinel に接続する |Microsoft Docs
description: Qualys Vulnerability Management データを Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 34f2cfa06cbdbb75b8fd610cd1f76fb33dde4cde
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059845"
---
# <a name="connect-your-qualys-vm-to-azure-sentinel-with-azure-function"></a>Azure Functions を使用して Qualys VM を Azure Sentinel に接続する

> [!IMPORTANT]
> Azure Sentinel の Qualys VM データ コネクタは、現在パブリック プレビュー段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Qualys Vulnerability Management (VM) コネクタを使用すると、Azure Sentinel にすべての [Qualys VM](https://www.qualys.com/apps/vulnerability-management/) セキュリティ ソリューションのログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 Qualys VM と Azure Sentinel の統合では、Azure Functions を使用して REST API によりログ データを取得します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-qualys-vm"></a>Qualys VM の構成と接続

Azure Functions は、Qualys VM から直接イベントとログを統合およびプルし、Azure Sentinel に転送できます。

1. Azure Sentinel ポータルで、 **[データ コネクタ]** をクリックし、 **[Qualys Vulnerability Management]** コネクタを選択します。

1. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. **Qualys Vulnerability Management** のページに記載されている手順に従います。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、Log Analytics の **QualysHostDetection_CL** テーブルの下にデータが表示されます。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Functions アプリを使用して Qualys VM を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
