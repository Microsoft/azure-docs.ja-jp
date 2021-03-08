---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001182"
---
`ApplicationInsights` 設定を使用すると、[Azure Application Insights](/azure/application-insights) テレメトリのサポートをお客様のコンテナーに追加できます。 Application Insights によってお客様のコンテナーを詳細に監視できます。 コンテナーの可用性、パフォーマンス、利用状況を簡単に監視できます。 さらに、お客様のコンテナーのエラーを迅速に特定して診断することもできます。

次の表に、`ApplicationInsights` セクションでサポートされている構成設定について説明します。

|必須| Name | データ型 | 説明 |
|--|------|-----------|-------------|
|いいえ| `InstrumentationKey` | String | コンテナーのテレメトリ データの送信先の Application Insights インスタンスのインストルメンテーション キー。 詳細については、「[Application Insights for ASP.NET Core](../articles/azure-monitor/app/asp-net-core.md)」を参照してください。 <br><br>例:<br>`InstrumentationKey=123456789`|