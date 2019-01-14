---
title: Azure Digital Twins で監視を構成する方法 | Microsoft Docs
description: Azure Digital Twins で監視を構成する方法。
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 2749a5c6c4e6003c51523d83c46b48d3b55b3d45
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807586"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Azure Digital Twins で監視を構成する方法

Azure Digital Twins は堅牢なログ記録、監視、分析をサポートします。 ソリューションの開発者は Azure Log Analytics、診断ログ、アクティビティ ログ、その他のサービスを使用して、IoT アプリの監視の複雑なニーズをサポートします。 ログ記録オプションを組み合わせて使用することで、複数のサービス間でクエリを実行またはレコードを表示できるほか、数多くのサービスにわたって詳細なログを記録する機能を提供します。

この記事ではログ記録と監視のオプションの概要と、Azure Digital Twins に合わせてそれらを組み合わせる方法について説明します。

## <a name="review-activity-logs"></a>アクティビティ ログを確認する

Azure の[アクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md)はサブスクリプション レベルのイベントと Azure の各サービス インスタンスの操作履歴に関する簡単な分析情報を提供します。

サブスクリプション レベルのイベントには次のようなものがあります。

* リソースの作成
* リソースの削除
* アプリ シークレットの作成
* 他のサービスとの統合

Azure Digital Twins のアクティビティ ログの記録は既定で有効になっており、Azure portal で次の操作を実行することで確認できます。

1. Azure Digital Twins インスタンスを選択します。
1. **[アクティビティ ログ]** を選択してディスプレイ パネルを起動します。

    ![アクティビティ ログ][1]

詳細なアクティビティ ログを記録するには、次の操作を実行します。

1. **[ログ]** オプションを選択して **[Activity Log Analytics Overview]\(Activity Log Analytics の概要\)** を表示します。

    ![選択肢番号][2]

1. **[Activity Log Analytics Overview]\(Activity Log Analytics の概要\)** には必須のアクティビティ ログ データがまとめられています。

    ![Activity Log Analytics の概要][3]

>[!TIP]
>**アクティビティ ログ**を使用して、サブスクリプション レベルのイベントを簡単に分析します。

## <a name="enable-customer-diagnostic-logs"></a>顧客の診断ログを有効にする

Azure の[診断設定](../azure-monitor/platform/diagnostic-logs-overview.md)は、アクティビティ ログの記録を補足するために、各 Azure インスタンスに対して設定できます。 アクティビティ ログはサブスクリプション レベルのイベントに関連する一方で、診断ログはリソース自体の操作履歴に関する洞察が提供されます。

診断ログの例は次のとおりです。

* ユーザー定義関数の実行時間
* API の要求が成功した場合の応答状態コード
* 資格情報コンテナーからアプリ キーを取得する

インスタンスの診断ログを有効にするには、次の手順を実行します。

1. Azure portal でリソースを開きます。
1. **[診断設定]** をクリックします。

    ![[診断設定] 1][4]

1. **[診断をオンにする]** をクリックしてデータを収集します (前に有効にしていなかった場合)。
1. 要求されたフィールドに入力し、データの保存場所と保存方法を選択します。

    ![[診断設定] 2][5]

    多くの場合、診断ログは [Azure File Storage](../storage/files/storage-files-deployment-guide.md) を使用して保存され、[Azure Log Analytics](../azure-monitor/log-query/get-started-portal.md) と共有されます。 両方のオプションを選択することができます。

>[!TIP]
>**診断ログ**を使用してリソースの操作の分析情報を確認してください。

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor と Azure Log Analytics

IoT アプリケーションはさまざまなリソース、デバイス、場所、データを 1 つに結合します。 きめ細かいログ記録により、アプリケーション アーキテクチャ全体における各部分、サービス、コンポーネントの詳細な情報を確認できますが、多くの場合メンテナンスやデバッグには統合された概要が必要です。

Azure Monitor には強力な Log Analytics サービスが含まれており、ログに記録されたソースを 1 か所で表示して分析できます。 このため、Azure Monitor は高度な IoT アプリ内でログを分析するのに非常に便利です。

次の用途で利用できます。

* 複数の診断ログ履歴のクエリを実行する
* 複数のユーザー定義関数のログを表示する
* 特定のタイム フレーム内で 2 つ以上のサービスのログを表示する

完全なログ クエリ機能は [Azure Log Analytics](../azure-monitor/log-query/log-query-overview.md) を通じて提供されます。 これらの強力な機能は、次のように設定します。

1. Azure portal で「**Log Analytics**」を検索します。
1. 利用できる **Log Analytics** インスタンスが表示されます。 いずれかを選択し、**[ログ]** を選択してクエリを実行します。

    ![Log Analytics][6]

1. まだ **Log Analytics** インスタンスがない場合は、**[追加]** ボタンをクリックしてワークスペースを作成できます。

    ![OMS の作成][7]

**Log Analytics** インスタンスがプロビジョニングされると、強力なクエリ機能を使用して複数のログからエントリを探し、**ログの管理**で特定の条件を使用して検索を実行できるようになります。

   ![ログの管理][8]

強力なクエリ操作について詳しくは、[クエリの概要](../azure-monitor/log-query/get-started-queries.md)に関するページをご覧ください。

> [!NOTE]
> 初めて **Log Analytics** にイベントを送信するときに 5 分の遅延が発生することがあります。

また、Azure Log Analytics にはエラーやアラートを通知する強力なサービスが用意されており、**[問題の診断と解決]** をクリックすることで表示されます。

   ![アラートとエラーの通知][9]

>[!TIP]
>**Log Analytics** を使用して、複数のアプリの機能、サブスクリプション、サービスのログ履歴に対してクエリを実行できます。

## <a name="other-options"></a>その他のオプション

Azure Digital Twins はアプリケーション固有のログ記録やセキュリティ監査もサポートします。 Azure Digital Twins インスタンスで利用できる Azure のすべてのログ記録オプションの概要については、[Azure のログの監査](../security/azure-log-audit.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

- Azure [アクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md)の詳細を確認する。

- [診断ログの概要](../azure-monitor/platform/diagnostic-logs-overview.md)を確認して Azure 診断の設定の詳細を確認する。

- [Azure Log Analytics ](../azure-monitor/log-query/get-started-portal.md) の詳細を確認する。

<!-- Images -->
[1]: media/how-to-configure-monitoring/activity-log.png
[2]: media/how-to-configure-monitoring/activity-log-select.png
[3]: media/how-to-configure-monitoring/log-analytics-overview.png
[4]: media/how-to-configure-monitoring/diagnostic-settings-one.png
[5]: media/how-to-configure-monitoring/diagnostic-settings-two.png
[6]: media/how-to-configure-monitoring/log-analytics.png
[7]: media/how-to-configure-monitoring/log-analytics-oms.png
[8]: media/how-to-configure-monitoring/log-analytics-management.png
[9]: media/how-to-configure-monitoring/log-analytics-notifications.png
