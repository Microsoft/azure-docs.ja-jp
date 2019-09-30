---
title: 共通アラート スキーマを Logic Apps と統合する方法
description: 共通アラートのスキーマを利用してすべてのアラートを処理するロジック アプリを作成する方法について説明します。
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: f431e5e5f4537d1a5f889457eb81b881e47ee178
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091785"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>共通アラート スキーマを Logic Apps と統合する方法

この記事では、共通アラート スキーマを利用してすべてのアラートを処理するロジック アプリを作成する方法について説明します。

## <a name="overview"></a>概要

[共通アラート スキーマ](https://aka.ms/commonAlertSchemaDocs)では、さまざまな種類のアラート全体に標準化された拡張可能な JSON スキーマが提供されます。 共通アラート スキーマは、Webhook、Runbook、およびロジック アプリを通じたプログラムを使用した活用に最適です。 この記事では、1 つのロジック アプリを作成して、すべてのアラートを処理する方法について説明します。 同じ原則を、他のプログラムによる方法にも適用できます。 この記事で説明されているロジック アプリでは、['必須' フィールド](alerts-common-schema-definitions.md#essentials)用に明確に定義された変数が作成されます。また、[アラートの種類](alerts-common-schema-definitions.md#alert-context)に固有のロジックの操作方法についても説明します。


## <a name="prerequisites"></a>前提条件 

この記事は、以下を理解していることを前提としています。 
* アラート ルールの設定 ([メトリック](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric)、[ログ](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)、[アクティビティ ログ](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* [アクション グループ](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)の設定
* アクション グループ内からの[共通アラート スキーマ](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema)の有効化

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>共通アラート スキーマを活用するロジック アプリを作成する

1. [ロジック アプリを作成する手順](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app)に従います。 

1.  次のトリガーを選択します:**HTTP 要求の受信時**。

    ![ロジック アプリのトリガー](media/action-groups-logic-app/logic-app-triggers.png "ロジック アプリのトリガー")

1.  **[編集]** を選択して HTTP 要求トリガーを変更します。

    ![HTTP 要求トリガー](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP 要求トリガー")


1.  次のスキーマをコピーして貼り付けます。

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. **[+** **新しいステップ]** 、 **[アクションの追加]** の順に選択します。

    ![アクションの追加](media/action-groups-logic-app/add-action.png "アクションの追加")

1. この段階では、お客様固有のビジネス要件に基づいて、さまざまなコネクタ (Microsoft Teams、Slack、Salesforce など) を追加できます。 すぐに使える '必須フィールド' を使用できます。 

    ![必須フィールド](media/alerts-common-schema-integrations/logic-app-essential-fields.png "必須フィールド")
    
    または、'Expression' オプションを使用して、アラートの種類に基づいて条件付きロジックを作成できます。

    ![ロジック アプリの式](media/alerts-common-schema-integrations/logic-app-expressions.png "ロジック アプリの式")
    
     ['monitoringService' フィールド](alerts-common-schema-definitions.md#alert-context)では、条件付きロジックの作成の基になるアラートの種類を一意に識別できます。

    
    たとえば、以下のスニペットでは、アラートが Application Insights ベースのログ アラートであるかどうかがチェックされ、そうである場合、検索結果が出力されます。 それ以外の場合、'NA' が出力されます。

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     詳細については、[ロジック アプリの式の記述](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions)に関する記事をご覧ください。

    


## <a name="next-steps"></a>次の手順

* [アクション グループについて学習します](../../azure-monitor/platform/action-groups.md)。
* [共通アラート スキーマについて学習します](https://aka.ms/commonAlertSchemaDocs)。

