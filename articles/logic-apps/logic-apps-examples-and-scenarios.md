---
title: "例と一般的なシナリオ - Azure Logic Apps | Microsoft Docs"
description: "ロジック アプリを例、シナリオ、およびチュートリアルで詳しく説明します"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/9/2017
ms.author: LADocs; jehollan
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 50df1e3db239a6aa34ac91bfbd582625c5b0041b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="examples-and-common-scenarios-for-azure-logic-apps"></a>Azure Logic Apps の例と一般的なシナリオ

ここでは、Azure Logic Apps に用意されている数多くのパターンと機能を理解するうえで役立つ、一般的な例とシナリオを紹介します。

## <a name="key-scenarios-for-logic-apps"></a>ロジック アプリの主要シナリオ

Azure Logic Apps には、さまざまなサービスに対して、耐障害性のあるオーケストレーションと統合を提供します。 Logic Apps サービスは "サーバーレス" であるため、スケールやインスタンスについて心配する必要はありません。必要なのは、ワークフロー (トリガーとアクション) を定義することだけです。 拡張性、可用性、およびパフォーマンスは、基になるプラットフォームによって処理されます。 複数のアクションを、特に複数のシステムで調整する必要があるシナリオはすべて、Azure Logic Apps のユースケースとして適しています。 そのパターンと例をいくつか次に示します。

## <a name="respond-to-triggers-and-extend-actions"></a>トリガーへの応答とアクションの拡張

すべてのロジック アプリがトリガーによって開始します。 たとえば、ワークフローは、スケジュール イベント、手動の呼び出し、または外部システムからのイベント ( "ファイルが FTP サーバーに追加されたとき" トリガーなど) によって開始できます。 Azure Logic Apps では現時点で、オンプレミスの SAP から Microsoft Cognitive Services に及ぶ、100 を超える数のすぐに使用可能なコネクタがサポートされています。 コネクタを発行していない可能性があるシステムおよびサービスについては、ロジック アプリを拡張することもできます。

* [カスタムのトリガーまたはアクションを作成する](../logic-apps/logic-apps-create-api-app.md)
* [ワークフロー実行に対して実行時間の長いアクションを設定する](../logic-apps/logic-apps-create-api-app.md)
* [webhook で外部イベントとアクションに応答する](../logic-apps/logic-apps-create-api-app.md)
* [HTTP 要求に対する同期応答によってワークフローを呼び出したり、トリガーしたり、または入れ子にする](../logic-apps/logic-apps-http-endpoint.md)
* [チュートリアル: Twilio SMS webhook に応答してテキストで返信する](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)
* [チュートリアル: AI で動くソーシャル ダッシュボードを Logic Apps と Power BI を使用して数分で構築する](http://aka.ms/logicappsdemo)

## <a name="error-handling-logging-and-control-flow-capabilities"></a>エラー処理、ログ、および制御フローの機能

ロジック アプリには、条件、スイッチ、ループ、スコープなど、高度な制御フローの機能が多数用意されています。 耐障害性を備えたソリューションを実現するために、ワークフローにエラーおよび例外処理を実装することもできます。 ワークフロー実行ステータスの通知および診断ログについては、Azure Logic Apps によって監視やアラートも提供されます。

* [スイッチ ステートメントでさまざまなアクションを実行する](../logic-apps/logic-apps-switch-case.md)
* [ロジック アプリのループとバッチを使用して配列とコレクションで項目を処理する](../logic-apps/logic-apps-loops-and-scopes.md)
* [ワークフローでエラーおよび例外処理を作成する](../logic-apps/logic-apps-exception-handling.md)
* [既存のロジック アプリで監視、ログ記録、アラートをオンにする](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [ロジック アプリの作成時に監視と診断ログ記録をオンにする](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)
* [ユース ケース: 医療関連企業が HL7 FHIR ワークフローのロジック アプリの例外処理を使用する方法](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="deploy-and-manage-logic-apps"></a>ロジック アプリのデプロイと管理

Visual Studio、Visual Studio Team Services、またはその他のソース管理および自動ビルド ツールで、ロジック アプリを完全に開発しデプロイできます。 リソース テンプレートでワークフローおよび依存接続のデプロイをサポートするために、ロジック アプリでは、Azure リソース デプロイ テンプレートが使用されます。 こうしたテンプレートは、Visual Studio ツールによって自動的に生成され、バージョン管理のためにソース管理機能にチェックインできます。

* [自動デプロイ テンプレートを作成する](../logic-apps/logic-apps-create-deploy-template.md)
* [Visual Studio でのロジック アプリのビルドとデプロイ](../logic-apps/logic-apps-deploy-from-vs.md)
* [ロジック アプリの正常性を監視する](../logic-apps/logic-apps-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>実行時のコンテンツ タイプ、変換

Azure Logic Apps [ワークフロー定義言語](http://aka.ms/logicappsdocs)でさまざまな関数を使用して、複数のコンテンツ タイプにアクセスしたり、そのコンテンツ タイプを変換したりできます。 たとえば、文字列、JSON、および XML を、`@json()` および `@xml()` ワークフロー式に変換することができます。 Logic Apps エンジンではコンテンツ タイプが保持され、サービス間での無損失のコンテンツ転送がサポートされます。

* [JSON 以外のコンテンツ タイプを処理する](../logic-apps/logic-apps-content-type.md)。`application/xml`、`application/octet-stream`、`multipart/formdata` など
* [ロジック アプリでのワークフロー式の動作](../logic-apps/logic-apps-author-definitions.md)
* [リファレンス: Azure Logic Apps ワークフロー定義言語](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>その他の統合と機能

ロジック アプリにより、Azure Functions、Azure API Management、Azure App Service 、カスタム HTTP エンドポイント (例: REST、SOAP) など、多くのサービスとの統合も実現します。

* [Azure Serverless でリアルタイムのソーシャル ダッシュ ボードを作成する](../logic-apps/logic-apps-scenario-social-serverless.md)
* [ロジック アプリから Azure Functions を呼び出す](../logic-apps/logic-apps-azure-functions.md)
* [シナリオ: Azure Functions を使用してロジック アプリをトリガーする](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [ブログ: ロジック アプリから SOAP エンドポイントを呼び出す](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>エンド ツー エンドのシナリオ

* [ホワイト ペーパー: Logic Apps などの Azure サービスを使用したエンタープライズ統合のエンド ツー エンド ケース管理](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="next-steps"></a>次のステップ

- [ロジック アプリでエラーと例外を処理する](../logic-apps/logic-apps-exception-handling.md)
- [ワークフロー定義言語でワークフロー定義を作成する](../logic-apps/logic-apps-author-definitions.md)
- [Azure Logic Apps を強化する方法に関するコメント、質問、フィードバック、提案を送信する](https://feedback.azure.com/forums/287593-logic-apps)
