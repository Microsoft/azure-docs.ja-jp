---
title: Azure Service Fabric CLI- sfctl mesh secretvalue | Microsoft Docs
description: Service Fabric CLI sfctl mesh secretvalue のコマンドについて説明します。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 064aeaea47dd59a1dd75cf19ea4060d8f9c2c4bf
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53559061"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
メッシュ secretvalue リソースを取得および削除します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| 削除 | 名前付きシークレット リソースの指定された値を削除します。 |
| list | 指定されたシークレット リソースのすべての値の名前を一覧表示します。 |
| show | 指定されたバージョンのシークレット リソースの値を取得します。 |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete
名前付きシークレット リソースの指定された値を削除します。

名前で識別されるシークレット値リソースを削除します。 リソースの名前は、通常、その値に関連付けられているバージョンです。 指定した値が使用中の場合、削除は失敗します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --secret-name -n [必須] | シークレット リソースの名前。 |
| --version -v     [必須] | シークレット バージョンの名前。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue list
指定されたシークレット リソースのすべての値の名前を一覧表示します。

指定されたシークレット リソースのすべてのシークレット値リソースに関する情報を取得します。 この情報には、実際の値ではなく、シークレット値リソースの名前が含まれています。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --secret-name -n [必須] | シークレット リソースの名前。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
指定されたバージョンのシークレット リソースの値を取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --secret-name -n [必須] | シークレット リソースの名前。 |
| --version -v     [必須] | シークレット バージョンの名前。 |
| --show-value | シークレット バージョンの実際の値を表示します。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |


## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。