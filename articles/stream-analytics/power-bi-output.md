---
title: Azure Stream Analytics からの Power BI 出力
description: この記事では、Azure Stream Analytics から Power BI にデータを出力する方法について説明します。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 0fc4e9a794429e7f1c1609fac287e67dabb3c878
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875505"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Azure Stream Analytics からの Power BI 出力

[Power BI](https://powerbi.microsoft.com/) を Stream Analytics ジョブの出力として使用して、分析結果の豊富な視覚化エクスペリエンスを提供できます。 この機能は、操作ダッシュボード、レポート生成、およびメトリックドリブン レポート作成に使用できます。

Stream Analytics からの Power BI 出力は、現在、Azure China 21Vianet および Azure Germany (T-Systems International) リージョンでは利用できません。

## <a name="ouput-configuration"></a>出力構成

次の表に、ご自分の Power BI 出力を構成するためのプロパティの名前とその説明を示します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス |クエリの出力をこの Power BI 出力に出力するためにクエリ内で使用されるフレンドリ名を指定します。 |
| グループ ワークスペース |他の Power BI ユーザーとのデータの共有を有効にするには、ご自分の Power BI アカウント内のグループを選択できます。グループに書き込む必要がない場合は、 **[マイ ワークスペース]** を選択します。 既存のグループを更新するには、Power BI の認証を更新する必要があります。 |
| データセットの名前 |Power BI 出力で使用するデータセット名を指定します。 |
| テーブル名 |Power BI 出力のデータセットの下にテーブル名を入力します。 現在、Stream Analytics ジョブからの Power BI 出力では、1 つのデータセット内に 1 つのテーブルのみを保持できます。 |
| 接続の承認 | Power BI に出力設定を構成することを承認する必要があります。 この出力アクセスを Power BI ダッシュボードに付与した後は、ユーザー アカウントのパスワードを変更するか、ジョブ出力を削除するか、または Stream Analytics ジョブを削除することによってアクセスを取り消すことができます。 | 

Power BI の出力とダッシュボードを構成するチュートリアルについては、[Azure Stream Analytics と Power BI](stream-analytics-power-bi-dashboard.md) に関するチュートリアルを参照してください。

> [!NOTE]
> Power BI ダッシュボードにデータセットとテーブルを明示的に作成しないでください。 ジョブが開始され、このジョブによって出力が Power BI に流し込まれるときに、データセットとテーブルが自動的に設定されます。 ジョブ クエリで結果が生成されない場合、データセットとテーブルは作成されません。 また、この Stream Analytics ジョブで指定したものと同じ名前のデータセットとテーブルが Power BI に既に存在する場合は、既存のデータが上書きされます。
>

### <a name="create-a-schema"></a>スキーマの作成

Power BI データセットとテーブル スキーマがまだ存在しない場合は、ユーザーのために Azure Stream Analytics がデータセットとテーブル スキーマを作成します。 それ以外の場合、テーブルは新しい値で更新されます。 現時点では、1 つのデータセット内に存在できるテーブルは 1 つのみです。 

Power BI では、先入れ先出し (FIFO) のアイテム保持ポリシーが使用されます。 200,000 行に到達するまでデータがテーブルに収集されます。

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Stream Analytics から Power BI にデータ型を変換する

Azure Stream Analytics では、出力スキーマが変更されると、データ モデルが実行時に動的に更新されます。 列名の変更、列の型の変更、列の追加または削除は、すべて追跡されます。

次の表は、Power BI データセットとテーブルが存在しない場合の、[Stream Analytics データ型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)から Power BI の [Entity Data Model (EDM) 型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)へのデータ型の変換を示します。

Stream Analytics から | Power BI へ
-----|-----
bigint | Int64
nvarchar(max) | String
DATETIME | Datetime
float | Double
レコードの配列 | 文字列型、定数値 "IRecord" または "IArray"

### <a name="update-the-schema"></a>スキーマを更新する

Stream Analytics では、出力内の最初のイベント セットに基づいてデータ モデルのスキーマを推論します。 その後、データ モデルのスキーマは、必要に応じて、元のスキーマに適合しない可能性のある受信イベントに対応できるように更新されます。

複数行にわたってスキーマが動的に更新されるのを防ぐために、`SELECT *` クエリの使用を避ける必要があります。 パフォーマンスが低下する可能性があるだけでなく、結果を得るまでの所要時間が不確実になる恐れもあります。 Power BI ダッシュボードに表示する必要があるフィールドを厳密に選択してください。 また、データ値が、選択したデータ型に準拠している必要があります。

以前/現在 | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
Datetime | String | String |  Datetime | String

## <a name="output-batch-size"></a>出力バッチ サイズ

出力バッチ サイズについては、「[Power BI REST API の制限事項](https://msdn.microsoft.com/library/dn950053.aspx)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
* [クイック スタート:Azure CLI を使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-cli.md)
* [クイック スタート: ARM テンプレートを使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-resource-manager.md)
* [クイック スタート: Azure PowerShell を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-powershell.md)
* [クイック スタート:Visual Studio を使用して Azure Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [クイック スタート: Visual Studio Code で Azure Stream Analytics ジョブを作成する](quick-create-vs-code.md)
