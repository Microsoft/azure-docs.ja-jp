---
title: Databricks Jar を使用してデータを変換する
description: Databricks Jar を実行してデータを処理または変換する方法を説明します。
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: shwang
ms.date: 03/15/2018
ms.openlocfilehash: 6b010000a674e351051c664dd5eeacd40e802439
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "81414621"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Azure Databricks で Jar アクティビティを実行してデータを変換する
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Data Factory パイプライン](concepts-pipelines-activities.md) の Azure Databricks Jar アクティビティは、Azure Databricks クラスターで Spark Jar を実行します。 この記事は、データ変換の概要とサポートされる変換アクティビティを説明している [データ変換アクティビティ](transform-data.md) に関する記事に基づいています。 Azure Databricks は、Apache Spark を実行するための管理されたプラットフォームです。

この機能の概要とデモンストレーションについては、以下の 11 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Databricks Jar アクティビティの定義

Databricks Jar アクティビティのサンプルの JSON 定義を次に示します。

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Databricks Jar アクティビティのプロパティ

次の表で、JSON 定義で使用される JSON プロパティについて説明します。

|プロパティ|説明|必須|
|:--|---|:-:|
|name|パイプラインのアクティビティの名前。|はい|
|description|アクティビティの動作を説明するテキスト。|いいえ|
|type|Databricks Jar アクティビティでは、アクティビティの種類は DatabricksSparkJar です。|はい|
|linkedServiceName|Jar アクティビティが実行されている Databricks リンク サービスの名前です。 このリンクされたサービスの詳細については、 [コンピューティングのリンクされたサービス](compute-linked-services.md) に関する記事をご覧ください。|はい|
|mainClassName|実行される main メソッドを含むクラスのフル ネーム。 このクラスは、ライブラリとして提供される JAR に含まれている必要があります。|はい|
|parameters|main メソッドに渡されるパラメーター。  文字列の配列です。|いいえ|
|libraries|ジョブを実行するクラスターにインストールされるライブラリのリスト。 <文字列, オブジェクト> の配列を指定できます。|はい (mainClassName メソッドを少なくとも 1 つ含む)|

> [!NOTE]
> **既知の問題** - 同時 Databricks Jar アクティビティの実行に同じ[対話型クラスター](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks)を使用する場合 (クラスターの再起動なし)、Databricks には、最初のアクティビティのパラメーターが、次のアクティビティでも使用されるという既知の問題があります。 そのため、後続のジョブに渡されるパラメーターが正しくありません。 これを回避するには、代わりに[ジョブ クラスター](compute-linked-services.md#example---using-new-job-cluster-in-databricks)を使用します。 

## <a name="supported-libraries-for-databricks-activities"></a>databricks アクティビティでサポートされるライブラリ

前述の Databricks アクティビティ定義では、*jar*、*egg*、*maven*、*pypi*、*cran* というライブラリの種類を指定しています。

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

ライブラリの種類の詳細については、[Databricks のドキュメント](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary)を参照してください。

## <a name="how-to-upload-a-library-in-databricks"></a>Databricks でライブラリをアップロードする方法

#### <a name="using-databricks-workspace-ui"></a>[Databricks ワークスペース UI の使用](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

UI を使用して追加されたライブラリの dbfs パスを取得するには、[Databricks CLI (インストール)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) を使用します。 

UI を使用する場合、通常、Jar ライブラリは dbfs:/FileStore/jars に保存されます。 CLI *databricks fs ls dbfs:/FileStore/job-jars* を使用してすべてを一覧表示することができます 



#### <a name="copy-library-using-databricks-cli"></a>[Databricks CLI を使用したライブラリのコピー](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Databricks CLI を使用します [(インストール手順)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)。 

例 - JAR を dbfs にコピーする: *dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar*
