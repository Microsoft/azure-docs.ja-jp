---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2bfb700ac5c220b780c05c8d415a4506c7a2f871
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001185"
---
`Logging` の設定では、お客様のコンテナーの ASP.NET Core ログ サポートを管理します。 お客様が ASP.NET Core アプリケーションに対して使用するのと同じ構成設定と値をお客様のコンテナーに使用できます。 

コンテナーでは、次のログ プロバイダーがサポートされています。

|プロバイダー|目的|
|--|--|
|[コンソール](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` ログ プロバイダー。 このログ プロバイダーのすべての ASP.NET Core 構成設定と既定値がサポートされています。|
|[デバッグ](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` ログ プロバイダー。 このログ プロバイダーのすべての ASP.NET Core 構成設定と既定値がサポートされています。|
|[ディスク](#disk-logging)|JSON ログ プロバイダー。 このログ プロバイダーは、ログ データを出力マウントに書き込みます。|

このコンテナー コマンドは、ログ情報を JSON 形式で出力マウントに格納します。

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

このコンテナー コマンドは、コンテナーの実行中に、`dbug` で始まるデバッグ情報を表示します。

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Disk ログ

`Disk` ログ プロバイダーでは、次の構成設定がサポートされます。

| 名前 | データ型 | 説明 |
|------|-----------|-------------|
| `Format` | String | ログ ファイルの出力形式。<br/> **注:** ログ プロバイダーを有効にするためにこの値を `json` に設定する必要があります。 コンテナーのインスタンス化中に、出力マウントを指定せずに、この値を指定した場合、エラーが発生します。 |
| `MaxFileSize` | Integer | ログ ファイルの最大サイズ (メガバイト (MB))。 現在のログ ファイルのサイズがこの値を満たしているか、または超えている場合、ログ プロバイダーによって新しいログ ファイルが開始されます。 -1 が指定されている場合、ログ ファイルのサイズは、出力マウントの最大ファイル サイズ (存在する場合) によってのみ制限されます。 既定値は 1 です。 |

ASP.NET Core ログのサポートを構成する方法の詳細については、[設定ファイル構成](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)に関するページを参照してください。