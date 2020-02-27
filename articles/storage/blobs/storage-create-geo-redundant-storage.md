---
title: チュートリアル - Blob Storage を使用して高可用性アプリケーションを作成する
titleSuffix: Azure Storage
description: 読み取りアクセス geo 冗長ストレージを使用してアプリケーション データに高い可用性を確保します。
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: 0eabd918b5f8f52049792ceb28ef8055945d6475
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162176"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>チュートリアル:Blob Storage を使用して高可用性アプリケーションを作成する

このチュートリアルは、シリーズの第 1 部です。 ここでは、Azure でアプリケーション データを高可用にする方法について学習します。

このチュートリアルを完了すると、BLOB を[読み取りアクセス geo 冗長ストレージ](../common/storage-redundancy.md) (RA-GRS) ストレージ アカウントにアップロードし、取得するコンソール アプリケーションが作成されます。

RA-GRS は、プライマリ リージョンからセカンダリ リージョンにトランザクションをレプリケートすることによって機能します。 このレプリケーション プロセスにより、セカンダリ リージョンのデータの結果整合性が保証されます。 アプリケーションでは、[サーキット ブレーカー](/azure/architecture/patterns/circuit-breaker) パターンを使用して、接続先のエンドポイントを判断し、障害と復旧がシミュレートされたときに自動的にエンドポイント間を切り替えます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * ストレージ アカウントの作成
> * 接続文字列の設定
> * コンソール アプリケーションを実行する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

* **Azure 開発**ワークロードと共に [Visual Studio 2019](https://www.visualstudio.com/downloads/) をインストールします。

  ![Azure 開発 ([Web & Cloud]\(Web とクラウド\) 以下)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="pythontabpython"></a>[Python](#tab/python)

* [Python](https://www.python.org/downloads/) のインストール
* [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) をダウンロードしてインストールします。

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

* [Node.js](https://nodejs.org) をインストールします。

---

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

ストレージ アカウントは、Azure Storage データ オブジェクトを格納してアクセスするための一意の名前空間を提供します。

次の手順で、読み取りアクセス geo 冗長ストレージ アカウントを作成します。

1. Azure Portal の左上隅にある **[リソースの作成]** ボタンを選択します。
2. **[新規]** ページから **[ストレージ]** を選択します。
3. **[おすすめ]** の下にある **[ストレージ アカウント - Blob、File、Table、Queue]** を選択します。
4. 下図のように、ストレージ アカウント フォームに次の情報を入力し、 **[作成]** を選択します。

   | 設定       | 推奨値 | 説明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Name** | mystorageaccount | ストレージ アカウント用の一意の値 |
   | **デプロイ モデル** | リソース マネージャー  | Resource Manager には最新の機能が含まれています。|
   | **アカウントの種類** | StorageV2 | アカウントの種類の詳細については、「[ストレージ アカウントの種類](../common/storage-introduction.md#types-of-storage-accounts)」を参照してください |
   | **パフォーマンス** | Standard | このサンプル シナリオでは、標準で十分です。 |
   | **レプリケーション**| 読み取りアクセス geo 冗長ストレージ (RA-GRS) | サンプルが動作するには、この設定が必要です。 |
   |**サブスクリプション** | 該当するサブスクリプション |サブスクリプションの詳細については、[サブスクリプション](https://account.azure.com/Subscriptions)に関するページを参照してください。 |
   |**ResourceGroup** | myResourceGroup |有効なリソース グループ名については、[名前付け規則と制限](/azure/architecture/best-practices/resource-naming)に関するページを参照してください。 |
   |**場所** | East US | 場所を選択します。 |

![ストレージ アカウントの作成](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>サンプルのダウンロード

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[サンプル プロジェクトをダウンロード](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)し、storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip ファイルを抽出 (解凍) します。 [git](https://git-scm.com/) を使って、アプリケーションのコピーを開発環境にダウンロードすることもできます。 サンプル プロジェクトにはコンソール アプリケーションが含まれています。

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="pythontabpython"></a>[Python](#tab/python)

[サンプル プロジェクトをダウンロード](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)し、storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip ファイルを抽出 (解凍) します。 [git](https://git-scm.com/) を使って、アプリケーションのコピーを開発環境にダウンロードすることもできます。 サンプル プロジェクトには基本的な Python アプリケーションが含まれています。

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

[サンプル プロジェクトをダウンロード](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs)して、ファイルを解凍します。 [git](https://git-scm.com/) を使って、アプリケーションのコピーを開発環境にダウンロードすることもできます。 サンプル プロジェクトには基本的な Node.js アプリケーションが含まれています。

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>サンプルの構成

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

アプリケーションでは、ストレージ アカウントの接続文字列を指定する必要があります。 アプリケーションが実行されているローカル コンピューターの環境変数内に、この接続文字列を格納することができます。 環境変数を作成するオペレーティング システムに応じて、以下の例のいずれかに従います。

Azure Portal のストレージ アカウントに移動します。 ストレージ アカウントの **[設定]** の下にある **[アクセス キー]** を選択します。 プライマリ キーまたはセカンダリ キーの**接続文字列**をコピーします。 オペレーティング システムに基づいて次のコマンドのいずれかを実行します。\<yourconnectionstring\> は実際の接続文字列に置き換えてください。 このコマンドで、環境変数をローカル コンピューターに保存します。 Windows では、環境変数は、使用している**コマンド プロンプト**またはシェルを再度読み込むまで使用できません。

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="pythontabpython"></a>[Python](#tab/python)

アプリケーションには、実際のストレージ アカウントの資格情報を指定する必要があります。 この情報は、アプリケーションが実行されているローカル コンピューターの環境変数に格納することができます。 環境変数を作成するオペレーティング システムに応じて、以下のいずれかの例に従います。

Azure Portal のストレージ アカウントに移動します。 ストレージ アカウントの **[設定]** の下にある **[アクセス キー]** を選択します。 **[ストレージ アカウント名]** と **[キー]** の値を以下のコマンドに貼り付けて、プレースホルダー \<youraccountname\> と \<youraccountkey\> を置き換えてください。 このコマンドで、環境変数をローカル コンピューターに保存します。 Windows では、環境変数は、使用している**コマンド プロンプト**またはシェルを再度読み込むまで使用できません。

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

このサンプルを実行するには、実際のストレージ アカウントの資格情報を `.env.example` ファイルに追加し、その名前を `.env` に変更する必要があります。

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

この情報は、Azure portal で目的のストレージ アカウントに移動し、 **[設定]** セクションの **[アクセス キー]** を選択すると確認できます。

必要な依存関係をインストールします。 そのためには、コマンド プロンプトを開いてサンプル フォルダーに移動し、「`npm install`」と入力します。

---

## <a name="run-the-console-application"></a>コンソール アプリケーションを実行する

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Visual Studio で **F5** キーを押すか **[スタート]** を選択してアプリケーションのデバッグを開始します。 構成に応じて、不足している NuGet パッケージが Visual Studio で自動的に復元されます。詳細については、[パッケージの復元によるパッケージのインストールと再インストール](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview)に関するセクションを参照してください。

コンソール ウィンドウが起動し、アプリケーションの実行が開始されます。 アプリケーションは、ソリューションの **HelloWorld.png** イメージをストレージ アカウントにアップロードします。 アプリケーションは、イメージがセカンダリ RA-GRS エンドポイントにレプリケートされたことを確認します。 次に、イメージのダウンロードを開始し、最大 999 回試行します。 各読み取りは **P** または **S** で表されます。この **P** はプライマリ エンドポイント、**S** はセカンダリ エンドポイントを示します。

![コンソール アプリの実行](media/storage-create-geo-redundant-storage/figure3.png)

このサンプル コードで、`Program.cs` ファイルの `RunCircuitBreakerAsync` タスクは、[DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) メソッドを使用してストレージ アカウントからイメージをダウンロードするために使用されます。 そのダウンロードの前に、[OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext) が定義されています。 操作コンテキストにイベント ハンドラーが定義され、ダウンロードが正常に完了したとき、またはダウンロードが失敗して再試行する場合に呼び出されます。

# <a name="pythontabpython"></a>[Python](#tab/python)

ターミナルまたはコマンド プロンプトでアプリケーションを実行するには、**circuitbreaker.py** ディレクトリに移動して「`python circuitbreaker.py`」と入力します。 アプリケーションは、ソリューションの **HelloWorld.png** イメージをストレージ アカウントにアップロードします。 アプリケーションは、イメージがセカンダリ RA-GRS エンドポイントにレプリケートされたことを確認します。 次に、イメージのダウンロードを開始し、最大 999 回試行します。 各読み取りは **P** または **S** で表されます。この **P** はプライマリ エンドポイント、**S** はセカンダリ エンドポイントを示します。

![コンソール アプリの実行](media/storage-create-geo-redundant-storage/figure3.png)

このサンプル コードで、`circuitbreaker.py` ファイルの `run_circuit_breaker` メソッドは、[get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html) メソッドを使用してストレージ アカウントからイメージをダウンロードするために使用されます。

ストレージ オブジェクトの再試行関数は、線形的な再試行ポリシーに設定されています。 この再試行関数によって、要求を再試行するかどうかが判断され、要求が再試行されるまでの秒数が指定されます。 プライマリへの初回要求が失敗したときにセカンダリに対して要求を再試行する場合は、**retry\_to\_secondary** の値を true に設定してください。 サンプル アプリケーションのカスタム再試行ポリシーは、ストレージ オブジェクトの `retry_callback` 関数で定義されます。

ダウンロードの前に、サービス オブジェクトの [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) と [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) 関数が定義されています。 これらの関数に定義されているイベント ハンドラーが、ダウンロードが正常に完了したとき、またはダウンロードが失敗して再試行する場合に呼び出されます。

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

サンプルを実行するには、コマンド プロンプトを開いてサンプル フォルダーに移動し、「`node index.js`」と入力します。

BLOB ストレージ アカウントにコンテナーが作成されて、そこに **HelloWorld.png** がアップロードされ、コンテナーとイメージがセカンダリ リージョンにレプリケートされたかどうかが繰り返しチェックされます。 レプリケーション後、「**D**」(ダウンロードする場合) または「**Q**」(終了する場合) と入力して Enter キーを押すように求められます。 出力は次の例のようになります。

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

### <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>イベント ハンドラーを再試行する

`OperationContextRetrying` イベント ハンドラーは、イメージのダウンロードが失敗し、再試行するように設定されたときに呼び出されます。 アプリケーションに定義されている最大試行回数に達すると、要求の [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) は `SecondaryOnly` に変わります。 この設定で、アプリケーションはセカンダリ エンドポイントからイメージをダウンロードを試行するように強制されます。 この構成では、プライマリ エンドポイントは永続的に再試行されないので、イメージの要求にかかる時間が短縮されます。

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>要求が完了したイベント ハンドラー

`OperationContextRequestCompleted` イベント ハンドラーは、イメージのダウンロードが成功したときに呼び出されます。 アプリケーションがセカンダリ エンドポイントを使用している場合、アプリケーションはセカンダリ エンドポイントを最大 20 回継続して使用します。 20 回の後、アプリケーションは [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) を `PrimaryThenSecondary` に戻し、プライマリ エンドポイントを再試行します。 要求が成功した場合、アプリケーションはプライマリ エンドポイントからの読み取りを継続します。

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

### <a name="pythontabpython"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>イベント ハンドラーを再試行する

`retry_callback` イベント ハンドラーは、イメージのダウンロードが失敗し、再試行するように設定されたときに呼び出されます。 アプリケーションに定義されている最大試行回数に達すると、要求の [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) は `SECONDARY` に変わります。 この設定で、アプリケーションはセカンダリ エンドポイントからイメージをダウンロードを試行するように強制されます。 この構成では、プライマリ エンドポイントは永続的に再試行されないので、イメージの要求にかかる時間が短縮されます。

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>要求が完了したイベント ハンドラー

`response_callback` イベント ハンドラーは、イメージのダウンロードが成功したときに呼び出されます。 アプリケーションがセカンダリ エンドポイントを使用している場合、アプリケーションはセカンダリ エンドポイントを最大 20 回継続して使用します。 20 回の後、アプリケーションは [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) を `PRIMARY` に戻し、プライマリ エンドポイントを再試行します。 要求が成功した場合、アプリケーションはプライマリ エンドポイントからの読み取りを継続します。

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

### <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

Node.js V10 SDK では、コールバック ハンドラーは不要です。 代わりにこのサンプルでは、再試行オプションとセカンダリ エンドポイントを含むパイプラインを作成しています。 これによりアプリケーションは、プライマリ パイプラインで必要なデータに到達できなかった場合でも、自動的にセカンダリ パイプラインに切り替えることができます。

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>次のステップ

シリーズの第 1 部では、RA-GRS ストレージ アカウントでアプリケーションを高可用にする方法について学習しました。

シリーズの第 2 部に進んで、エラーをシミュレートし、アプリケーションがセカンダリ RA-GRS エンドポイントを使用するように強制する方法について学んでください。

> [!div class="nextstepaction"]
> [プライマリ リージョンからの読み取りで発生するエラーをシミュレートする](storage-simulate-failure-ragrs-account-app.md)
