---
title: Azure Security Center におけるアクセス許可 | Microsoft Docs
description: この記事では、Azure Security Center でロールベースのアクセス制御を使用してアクセス許可をユーザーに割り当て、ロールごとに許可するアクションを特定する方法について説明します。
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: memildin
ms.openlocfilehash: e5728b220f12c3305aa896e64010fc7e5828d6fb
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067224"
---
# <a name="permissions-in-azure-security-center"></a>Azure Security Center におけるアクセス許可

Azure Security Center では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) が使用されており、これによって提供される[組み込みのロール](../role-based-access-control/built-in-roles.md)は、Azure でユーザー、グループ、サービスに割り当てることができます。

Security Center は、リソースの構成を評価して、セキュリティの問題と脆弱性を特定します。 Security Center では、リソースが属するサブスクリプションまたはリソース グループの所有者、共同作業者、閲覧者のいずれかのロールが割り当てられているときにリソースに関連した情報のみが表示されます。

これらのロールに加え、Security Center には、次の 2 つの固有のロールがあります。

* **セキュリティ閲覧者**:このロールに属しているユーザーには、Security Center に対する閲覧権限があります。 推奨事項、警告、セキュリティ ポリシー、セキュリティの状態を閲覧できますが、変更することはできません。
* **セキュリティ管理者**:このロールに属しているユーザーは、セキュリティ閲覧者と同じ権限があり、セキュリティ ポリシーの更新、アラートと推奨事項の無視も可能になります。

> [!NOTE]
> セキュリティ閲覧者とセキュリティ管理者という 2 つのセキュリティ ロールには、Security Center のみでのアクセス権が付与されています。 ストレージ、Web + モバイル、モノのインターネットなど、Azure の他のサービス領域へのアクセス権はありません。
>

## <a name="roles-and-allowed-actions"></a>ルールと許可されているアクション

次の表では、Security Center のロールと許可されているアクションが表示します。

|アクション|セキュリティ閲覧者または <br> Reader |セキュリティ管理者  |リソース グループの共同作成者または <br> リソース グループの所有者  |サブスクリプションの共同作成者  |サブスクリプションの所有者  |
|:--- |:---:|:---:|:---:|:---:|:---:|
|セキュリティ ポリシーを編集する|-|✔|-|-|✔|
|イニシアティブを追加する、または割り当てる (規制コンプライアンス標準を含む)|-|-|-|-|✔|
|サブスクリプション価格レベルを変更する|-|✔|-|-|✔|
|自動プロビジョニングを有効または無効にする|-|✔|-|✔|✔|
|セキュリティに関する推奨事項をリソースに適用する</br> (および[クイック修復](security-center-remediate-recommendations.md#quick-fix-remediation)を使用する)|-|-|✔|✔|✔|
|アラートを無視する|-|✔|-|✔|✔|
|アラートと推奨事項を表示する|✔|✔|✔|✔|✔|

> [!NOTE]
> タスクを実行するために必要となる最小限の権限ロールをユーザーに割り当てることをお勧めします。 たとえば、リソースのセキュリティ正常性に関する情報の表示のみが必要で、推奨事項の適用やポリシーの編集などの操作を行う必要がないユーザーには、閲覧者ロールを割り当ててください。
>
>

## <a name="next-steps"></a>次のステップ
この記事では、Security Center で RBAC を使用してユーザーにアクセス許可を割り当てる方法について説明し、各ロールに許可されているアクションを示しました。 サブスクリプションのセキュリティ状態を監視するために必要なロールの割り当てについて理解したら、セキュリティ ポリシーを編集し、推奨事項を適用して、次の方法を学習してください。

- [Security Center でセキュリティ ポリシーを設定する](tutorial-security-policy.md)
- [Security Center でセキュリティに関する推奨事項を管理する](security-center-recommendations.md)
- [Azure リソースのセキュリティ正常性を監視する](security-center-monitoring.md)
- [Security Center でセキュリティのアラートの管理と対応を行う](security-center-managing-and-responding-alerts.md)
- [パートナー セキュリティ ソリューションを監視する](security-center-partner-solutions.md)
