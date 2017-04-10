---
title: "新しい Azure Application Insights リソースの作成 | Microsoft Docs"
description: "新しいライブ アプリケーションを対象にした Application Insights 監視を手動でセットアップします。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 003db6e1479be1007dd292555ce5997f1c138809
ms.openlocfilehash: f73a24993fdeaced422b2f7a1283722a82c2be77
ms.lasthandoff: 12/13/2016


---
# <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成
Azure Application Insights には、Microsoft Azure *リソース*のアプリケーションに関するデータが表示されます。 したがって、新しいリソースの作成は、[新しいアプリケーションを監視するための Application Insights の設定][start]の一部です。 多くの場合、これは IDE によって自動的に行うことができ、可能な場合には常にそうするよう勧められています。 ただし、たとえば、アプリケーションの開発ビルドと運用ビルドそれぞれに個別にリソースを設定するために、リソースを手動で作成することがあります。

リソースを作成した後、インストルメンテーション キーを取得し、それを使用してアプリケーション内の SDK を構成します。 これは、リソースにテレメトリを送信します。

## <a name="sign-up-to-microsoft-azure"></a>Microsoft Azure へのサインアップ
取得していない場合、[Microsoft アカウントをここで取得してください](http://live.com)。 (Outlook.com、OneDrive、Windows Phone、XBox Live などのサービスを利用している場合、Microsoft アカウントを持っています。)

[Microsoft Azure](http://azure.com)のサブスクリプションも必要になります。 チームまたは組織で Azure サブスクリプションを所有している場合、所有者は Windows Live ID を使用してあなたを追加できます。 課金されるのは使用分に対してのみで、既定の Basic プランでは、試験段階の一定量を無料で使用できます。

サブスクリプションへのアクセスを取得したら、Application Insights ( [http://portal.azure.com](https://portal.azure.com)) にログインし、Live ID を使用してログインしてください。

## <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成
[portal.azure.com](https://portal.azure.com)で、Application Insights リソースを追加します。

![[新規]、[Application Insights] の順にクリックする](./media/app-insights-create-new-resource/01-new.png)

* **アプリケーションの種類**に応じて、概要ブレードに表示されるものと、[メトリック エクスプローラー][metrics]で使用できるプロパティが決まります。 お使いの種類のアプリが表示されない場合は、[General (汎用)] を選択します。
* **サブスクリプション** は、Azure での支払いアカウントです。
* **リソース グループ** は、アクセス制御などのプロパティを管理するのに便利です。 その他の Azure リソースが既に作成されている場合、新しいリソースを同じグループに入れることもできます。
* **場所** は、データの保存場所です。
* **[ダッシュボードにピン留めする]** をオンにすると、Azure のホーム ページにそのリソースに対するクイック アクセス タイルを追加できます。 推奨。

アプリが作成されると、新しいブレードが開きます。 そのブレードには、アプリに関するパフォーマンスと使用状況データが表示されます。 

次回 Azure にログインするときにそこへ戻るには、スタート画面 (ホーム画面) 上のアプリのクイック スタート タイルを探してください。 あるいは、[参照] ボタンをクリックして探します。

## <a name="copy-the-instrumentation-key"></a>インストルメンテーション キーのコピー
インストルメンテーション キーは作成したリソースを識別します。 これは、SDK に渡すために必要です。

![Essentials、インストルメンテーション キーの順にクリックし、Ctrl キーを押しながら C キーを押します。](./media/app-insights-create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>アプリケーションでの SDK のインストール
アプリで Application Insights SDK をインストールします。 この手順は、アプリケーションの種類に大きく依存します。 

インストルメンテーション キーを使用して、[アプリケーションにインストールする SDK][start] を構成します。

SDK には、コードを記述せずにテレメトリを送信する標準的なモジュールが含まれています。 ユーザーの操作を追跡したり、問題をより詳しく診断したりするには、[API を使用][api]して、独自のテレメトリを送信します。

## <a name="monitor"></a>テレメトリ データを参照
クイック スタート ブレードを閉じ、Azure ポータルのアプリケーション ブレードに戻ります。

[検索] タイルをクリックして [[診断検索]][diagnostic] を確認します。ここには、最初のイベントが表示されます。 

大量のデータが予想される場合は、数秒後に [最新の情報に更新] をクリックします。

## <a name="creating-a-resource-automatically"></a>リソースの自動作成
[PowerShell スクリプト](app-insights-powershell.md) を作成して、リソースを自動で作成できます。

## <a name="next-steps"></a>次のステップ
* [ダッシュボードを作成する](app-insights-dashboards.md)
* [診断検索](app-insights-diagnostic-search.md)
* [メトリックを探索する](app-insights-metrics-explorer.md)
* [Analytics クエリを作成する](app-insights-analytics.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md


