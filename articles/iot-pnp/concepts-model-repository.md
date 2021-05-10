---
title: デバイス モデル リポジトリの概念の理解 | Microsoft Docs
description: ソリューション開発者や IT プロフェッショナル向けに、デバイス モデル リポジトリの基本的な概念について説明します。
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33ff96b4e51dbf80bfdb924bc37786a344cdfdc6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582648"
---
# <a name="device-models-repository"></a>デバイス モデル リポジトリ

デバイス モデル リポジトリ (DMR) を使用すると、デバイス ビルダーは IoT プラグ アンド プレイ デバイス モデルを管理および共有できます。 デバイス モデルは、[Digital Twins モデリング言語 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) を使用して定義された JSON LD ドキュメントです。

DMR によって、デバイス ツイン モデル識別子 (DTMI) に基づいて DTDL インターフェイスをフォルダー構造に格納するパターンが定義されます。 DTMI を相対パスに変換することで、DMR 内のインターフェイスを特定できます。 たとえば、`dtmi:com:example:Thermostat;1` DTMI は `/dtmi/com/example/thermostat-1.json` に変換されます。

## <a name="public-device-models-repository"></a>パブリック デバイス モデル リポジトリ

Microsoft によって、これらの特性を持つパブリック DMR がホストされます:

- キュレーション モデル。 Microsoft によって、GitHub のプル要求 (PR) 検証ワークフローを使用して、使用可能なすべてのインターフェイスがレビューおよび承認されます。
- 不変性。  公開後、インターフェイスを更新することはできません。
- ハイパースケール。 Microsoft は、デバイス モデルを公開して使用できる、安全でスケーラブルなエンドポイントを作成するために必要なインフラストラクチャを提供しています。

## <a name="custom-device-models-repository"></a>カスタム デバイス モデル リポジトリ

ローカル ファイル システムやカスタム HTTP Web サーバーなどの任意のストレージ メディア内で同じ DMR パターンを使用して、カスタム DMR を作成します。 DMR にアクセスするために使用するベース URL を変更して、パブリック DMR と同じ方法でカスタム DMR からデバイス モデルを取得できます。

> [!NOTE]
> Microsoft は、パブリック DMR のデバイス モデルを検証するためのツールを提供しています。 これらのツールはカスタム リポジトリで再利用できます。

## <a name="public-models"></a>パブリック モデル

モデル リポジトリに格納されているパブリック デバイス モデルは、すべてのユーザーがアプリケーション内で使用および統合することができます。 パブリック デバイス モデルを使用すると、オープンなエコシステムでデバイス ビルダーおよびソリューション開発者が、IoT プラグ アンド プレイ デバイス モデルを共有したり、再利用したりすることができます。

モデル リポジトリにモデルを公開してパブリックにする方法については、「[モデルを公開する](#publish-a-model)」セクションを参照してください。

ユーザーは、公式の [GitHub リポジトリ](https://github.com/Azure/iot-plugandplay-models)からパブリック インターフェイスを参照、検索、表示できます。

`dtmi` フォルダー内のすべてのインターフェイスは、パブリック エンドポイント [https://devicemodels.azure.com](https://devicemodels.azure.com) からも使用できます

### <a name="resolve-models"></a>モデルを解決する

これらのインターフェイスにプログラムでアクセスするには、NuGet パッケージ [Azure.IoT.ModelsRepository](https://www.nuget.org/packages/Azure.IoT.ModelsRepository) で入手可能な `ModelsRepositoryClient` を使用できます。 既定では、このクライアントは [devicemodels.azure.com](https://devicemodels.azure.com/) で入手可能なパブリック DMR に対してクエリを実行するように構成されており、任意のカスタム リポジトリに対して構成できます。

クライアントは `DTMI` を入力として受け取り、必要なすべてのインターフェイスを含むディクショナリを返します。

```cs
using Azure.IoT.ModelsRepository;

var client = new ModelsRepositoryClient();
IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");
models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

予想される出力には、依存関係チェーンで見つかった 3 つのインターフェイスの `DTMI` が表示されます。

```txt
dtmi:com:example:TemperatureController;1
dtmi:com:example:Thermostat;1
dtmi:azure:DeviceManagement:DeviceInformation;1
```

`ModelsRepositoryClient` は、カスタム モデル リポジトリ (http(s) を介して利用可能) に対してクエリを実行するように構成できます。また、利用可能な `ModelDependencyResolution` のいずれかを使用して依存関係の解決を指定します。

- 無効。 指定されたインターフェイスだけを返します。依存関係はありません。
- 有効にします。 依存関係チェーン内のすべてのインターフェイスを返します。
- TryFromExpanded。 `.expanded.json` ファイルを使用して、事前に計算された依存関係を取得します。 

> [!Tip] 
> カスタム リポジトリで `.expanded.json` ファイルが公開されない場合があります。使用できない場合、クライアントはフォールバックして各依存関係をローカルに処理します。

次のサンプル コードは、カスタム リポジトリのベース URL を使用して `ModelsRepositoryClient` を初期化する方法を示しています。この例では、`expanded` フォームを使用せずに (`raw` エンドポイントで利用できないため)、GitHub API からの `raw` URL を使用しています。 `AzureEventSourceListener` は、クライアントによって実行される HTTP 要求を検査するために初期化されます。

```cs
using AzureEventSourceListener listener = AzureEventSourceListener.CreateConsoleLogger();

var client = new ModelsRepositoryClient(
    new Uri("https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main"),
    new ModelsRepositoryClientOptions(dependencyResolution: ModelDependencyResolution.Enabled));

IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");

models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

Azure SDK GitHub リポジトリ ([Azure.Iot.ModelsRepository/samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/modelsrepository/Azure.IoT.ModelsRepository/samples)) のソース コード内で、さらに多くのサンプルを入手できます。

## <a name="publish-a-model"></a>モデルを公開する

> [!Important]
> パブリック DMR にモデルを送信できるようにするには、GitHub アカウントが必要です。

1. パブリック GitHub リポジトリ ([https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models)) をフォークします。
1. フォークされたリポジトリをクローンします。 必要に応じて、新しいブランチを作成して、変更を `main` ブランチから分離します。
1. フォルダーまたはファイル名の規則を使用して、新しいインターフェイスを `dtmi` フォルダーに追加します。 詳細については、「[`dtmi/` フォルダーへのモデルのインポート](#import-a-model-to-the-dtmi-folder)」を参照してください。
1. `dmr-client` ツールを使用して、ローカルでモデルを検証します。 詳細については、「[モデルを検証する](#validate-models)」を参照してください。
1. 変更をローカルでコミットし、フォークにプッシュします。
1. フォークから、`main` ブランチを対象とするプル要求を作成します。 「[Issue もしくはプル リクエストの作成](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request)」のドキュメントを参照してください。
1. [プル要求の要件](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)を確認します。

送信されたインターフェイスを検証する一連の GitHub アクションがプル要求によってトリガーされ、プル要求がすべての要件を満たしていることが確認されます。

Microsoft によって、3 営業日以内にすべてのチェックが行われ、プル要求への応答が行われます。

### <a name="dmr-client-tools"></a>`dmr-client` のツール

PR チェック中にモデルの検証に使用されるツールは、DTDL インターフェイスをローカルに追加して検証する際にも使用できます。

> [!NOTE]
> このツールには、[.NET SDK](https://dotnet.microsoft.com/download) バージョン 3.1 以上が必要です。

### <a name="install-dmr-client"></a>`dmr-client` のインストール

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>`dtmi/` フォルダーへのモデルのインポート

モデルが既に json ファイルに格納されている場合は、`dmr-client import` コマンドを使用して、`dtmi/` フォルダーに正しいファイル名で追加できます。

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> `--local-repo` 引数を使用して、ローカル リポジトリのルート フォルダーを指定できます。

### <a name="validate-models"></a>モデルを検証する

以下の `dmr-client validate` コマンドを使用して、モデルを検証できます。

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> 検証では、すべてのインターフェイスが DTDL 言語仕様と互換性があることを確認するために、最新の DTDL パーサー バージョンが使用されます。

外部の依存関係を検証するには、それらがローカル リポジトリに存在している必要があります。 モデルを検証するには、`--repo` オプションを使用して `local` フォルダーまたは `remote` フォルダーを指定し、依存関係を解決します。

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>厳密な検証

DMR には追加の[要件](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)が含まれています。その要件に対してモデルを検証するには、`stict` フラグを使用します。

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

コンソール出力にエラー メッセージが表示されていないかを確認します。

### <a name="export-models"></a>モデルをエクスポートする

JSON 配列を使用することで、モデルを特定リポジトリ (ローカルまたはリモート) から 1 つのファイルにエクスポートできます。

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>次のステップ

次の手順では、「[IoT プラグ アンド プレイのアーキテクチャ](concepts-architecture.md)」を確認することをお勧めします。
