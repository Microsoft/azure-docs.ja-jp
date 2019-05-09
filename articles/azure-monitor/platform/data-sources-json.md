---
title: Azure Monitor でのカスタム JSON データの収集 | Microsoft Docs
description: カスタム JSON データ ソースは、Linux 用 Log Analytics エージェントを使用して Azure Monitor に収集できます。  これらのカスタム データ ソースは、curl や FluentD の 300 を超えるプラグインのいずれかなどの、JSON を返す単純なスクリプトでかまいません。 この記事では、このデータ収集に必要な構成について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 101719668fee155e84b7a767647a662ca845f0f2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58848980"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Azure Monitor での Linux 用 Log Analytics エージェントを使用したカスタム JSON データ ソースの収集
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

カスタム JSON データ ソースは、Linux 用 Log Analytics エージェントを使用して [Azure Monitor](data-platform.md) に収集できます。  これらのカスタム データ ソースは、[curl](https://curl.haxx.se/) や [FluentD の 300 を超えるプラグイン](https://www.fluentd.org/plugins/all)のいずれかなどの、JSON を返す単純なスクリプトでかまいません。 この記事では、このデータ収集に必要な構成について説明します。


> [!NOTE]
> カスタム JSON データには Linux 用 Log Analytics エージェント v1.1.0-217 以降が必要です

## <a name="configuration"></a>構成

### <a name="configure-input-plugin"></a>入力プラグインを構成する

Azure Monitor で JSON データを収集するには、入力プラグイン内の FluentD タグの先頭に `oms.api.` を追加します。

たとえば、`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` 内の個別の構成ファイル `exec-json.conf` を次に示します。  これは、FluentD プラグイン `exec` を使用して 30 秒ごとに curl コマンドを実行します。  このコマンドからの出力は、JSON 出力プラグインによって収集されます。

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` の下に追加された構成ファイルは、次のコマンドを使用して所有権を変更する必要があります。

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>出力プラグインを構成する 
次の出力プラグイン構成を `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 内のメインの構成に追加するか、または `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` に配置された個別の構成ファイルとして追加します

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Linux 用 Log Analytics エージェントを再起動する
次のコマンドを使用して、Linux 用 Log Analytics エージェント サービスを再起動します。

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Output
データは、レコードの種類を `<FLUENTD_TAG>_CL` として、Azure Monitor に収集されます。

たとえば、カスタム タグ `tag oms.api.tomcat` は `tomcat_CL` のレコードの種類を使用して Azure Monitor で収集されます。  次のログ クエリを使用すると、この種類のすべてのレコードを取得できます。

    Type=tomcat_CL

入れ子になった JSON データ ソースはサポートされますが、親フィールドに基づいてインデックスが作成されます。 たとえば、ログ クエリ `tag_s : "[{ "a":"1", "b":"2" }]` から次の JSON データが返されます。

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>次の手順
* [ログ クエリ](../log-query/log-query-overview.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。 