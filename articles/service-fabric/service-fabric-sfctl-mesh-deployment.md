---
title: Azure Service Fabric CLI- sfctl mesh deployment
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 Service Fabric Mesh リソースを作成するためのコマンドの一覧が含まれます。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906031"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Service Fabric Mesh リソースを作成します。

## <a name="commands"></a>コマンド

|command|説明|
| --- | --- |
| 作成 | Service Fabric Mesh リソースのデプロイを作成します。 |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Service Fabric Mesh リソースのデプロイを作成します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --input-yaml-files [必須] | すべての yaml ファイルのコンマ区切りの相対または絶対ファイル パス、または yaml ファイルを含むディレクトリ (再帰的) の相対または絶対パス。 |
| --parameters | オーバーライドする必要があるパラメーターを含む yaml ファイルまたは json オブジェクトに対する相対または絶対パス。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

### <a name="examples"></a>例

yaml ファイルに記述されているパラメーターをオーバーライドすることで、すべてのリソースを統合してクラスターにデプロイします
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

yaml ファイルに記述されているパラメーターをオーバーライドすることで、ディレクトリ内のすべてのリソースを統合してクラスターにデプロイします

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

json オブジェクトとして直接渡されるパラメーターをオーバーライドすることで、ディレクトリ内のすべてのリソースを統合してクラスターにデプロイします
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。
