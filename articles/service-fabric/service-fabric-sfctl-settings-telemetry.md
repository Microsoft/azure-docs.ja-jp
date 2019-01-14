---
title: Azure Service Fabric CLI- sfctl settings telemetry | Microsoft Docs
description: Service Fabric CLI の sfctl settings telemetry コマンドについて説明します。
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
ms.openlocfilehash: e0e229f0edb078fe9ce289d0089f34d7cbf9dbf8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283768"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
sfctl のこのインスタンスに対してローカルなテレメトリ設定を構成します。

Sfctl テレメトリでは、パラメーターやパラメーター値が指定されていないコマンドの名前、sfctl バージョン、OS の種類、python のバージョン、コマンドの成否、返されたエラー メッセージが収集されます。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| set-telemetry | テレメトリを有効または無効にします。 |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
テレメトリを有効または無効にします。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --off | テレメトリを無効にします。 |
| --on | テレメトリを有効にします。 これが既定値です。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

### <a name="examples"></a>例

テレメトリを無効にします。

```
sfctl settings telemetry set_telemetry --off
```

テレメトリを有効にします。

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。