---
title: Azure Storage を使用してクラウドに画像データをアップロードする | Microsoft Docs
description: Web アプリで Azure Blob Storage を使用してアプリ データを格納します
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 882a43a75fa720b13d931740818e5ee6e893bcab
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753338"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>チュートリアル: Azure Storage を使用してクラウドに画像データをアップロードする

このチュートリアルは、シリーズの第 1 部です。 このチュートリアルでは、Azure Storage クライアント ライブラリを使用してストレージ アカウントに画像をアップロードする Web アプリのデプロイ方法を学習します。 終了すると、Azure Storage に画像を格納して表示する Web アプリが完成します。

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![images コンテナーの表示](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![images コンテナーの表示](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * ストレージ アカウントの作成
> * コンテナーを作成し、アクセス許可を設定する
> * アクセス キーを取得する
> * Web アプリを Azure にデプロイする
> * アプリケーションの設定の構成
> * Web アプリと対話する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、Azure サブスクリプションが必要です。 始める前に、[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

このチュートリアルでは、CLI をローカルにインストールして使用するには、Azure CLI バージョン 2.0.4 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="create-a-resource-group"></a>リソース グループの作成 

[az group create](/cli/azure/group#az_group_create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。  

次の例では、`myResourceGroup` という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia 
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

サンプルでは、Azure Storage アカウント内の BLOB コンテナーに画像をアップロードします。 ストレージ アカウントは、Azure Storage データ オブジェクトを格納してアクセスするための一意の名前空間を用意します。 [az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを使用して作成したリソース グループ内にストレージ アカウントを作成します。

> [!IMPORTANT]
> このチュートリアルの第 2 部では、BLOB ストレージで Azure Event Grid を使用します。 Event Grid がサポートされている Azure リージョンにストレージ アカウントを作成してください。 サポートされているリージョンの一覧は、[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)に関するページをご覧ください。

次のコマンドの `<blob_storage_account>` プレースホルダーを、BLOB ストレージ アカウントのグローバルな一意の名前に置き換えます。  

```azurecli-interactive
az storage account create --name <blob_storage_account> \
--location southeastasia --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
```

## <a name="create-blob-storage-containers"></a>BLOB ストレージ コンテナーを作成する

アプリでは、BLOB ストレージ アカウント内の 2 つのコンテナーを使用します。 コンテナーはフォルダーに似ており、BLOB を格納します。 "*images*" コンテナーは、アプリが高解像度のイメージをアップロードする場所です。 このシリーズの後半で、Azure 関数アプリで、サイズ変更した画像を *thumbnails* コンテナーにアップロードします。

[az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) コマンドを使用して、ストレージ アカウント キーを取得します。 次に、このキーを使用して、[az storage container create](/cli/azure/storage/container#az_storage_container_create) コマンドで 2 つのコンテナーを作成します。  

ここでは、`<blob_storage_account>` は、作成した BLOB ストレージ アカウントの名前です。 *images* コンテナーのパブリック アクセスは、`off` に設定されています。 *thumbnails* コンテナーのパブリック アクセスは、`container` に設定されています。 `container` パブリック アクセスに設定すると、Web ページにアクセスしたユーザーはサムネイルを表示できます。

```azurecli-interactive
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv)

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey
```

BLOB ストレージ アカウント名とキーをメモしておきます。 サンプル アプリでは、これらの設定を使用して、画像をアップロードするストレージ アカウントに接続します。 

## <a name="create-an-app-service-plan"></a>App Service プランを作成する

[App Service プラン](../../app-service/overview-hosting-plans.md)は、アプリのホストとなる Web サーバー ファームの場所、サイズ、機能を規定します。

[az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) コマンドで、App Service プランを作成します。

次の例では、**Free** 価格レベルの `myAppServicePlan` という名前の App Service プランを作成します。

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Web アプリを作成する

Web アプリでは、GitHub サンプル リポジトリからデプロイされるサンプル アプリ コード用のホスト領域を提供します。 [az webapp create](/cli/azure/webapp#az_webapp_create) コマンドを使って、`myAppServicePlan`App Service プランに [Web アプリ](../../app-service/overview.md)を作成します。  

次のコマンドで、`<web_app>` を一意の名前に置き換えます。 有効な文字は、`a-z`、`0-9`、および `-` です。 `<web_app>` が一意でない場合は、"_指定された名前 `<web_app>` の Web サイトは既に存在します_" というエラー メッセージが表示されます。 Web アプリの既定の URL は、`https://<web_app>.azurewebsites.net` です。  

```azurecli-interactive
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>GitHub リポジトリからサンプル アプリをデプロイする

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

App Service は、コンテンツを Web アプリにデプロイするさまざまな方法をサポートしています。 このチュートリアルでは、[パブリック GitHub サンプル リポジトリ](https://github.com/Azure-Samples/storage-blob-upload-from-webapp)から Web アプリをデプロイします。 [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) コマンドを使用して、Web アプリへの GitHub のデプロイを構成します。 `<web_app>` を、前の手順で作成した Web アプリの名前に置き換えます。

サンプル プロジェクトには、[ASP.NET MVC](https://www.asp.net/mvc) アプリが含まれています。 そのアプリは、画像を受け取り、ストレージ アカウントに保存して、サムネイル コンテナーから画像を表示します。 その Web アプリは、Azure Storage クライアント ライブラリの [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet)、[Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet)、および [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) 名前空間を使用して、Azure Storage と対話します。

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
App Service は、コンテンツを Web アプリにデプロイするさまざまな方法をサポートしています。 このチュートリアルでは、[パブリック GitHub サンプル リポジトリ](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node)から Web アプリをデプロイします。 [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) コマンドを使用して、Web アプリへの GitHub のデプロイを構成します。 `<web_app>` を、前の手順で作成した Web アプリの名前に置き換えます。

---

```azurecli-interactive
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

## <a name="configure-web-app-settings"></a>Web アプリの設定を構成する

サンプル Web アプリでは、[Azure Storage Client Library](/dotnet/api/overview/azure/storage?view=azure-dotnet) を使用して、画像をアップロードするために使用するアクセス トークンを要求します。 ストレージ SDK によって使用されるストレージ アカウントの資格情報は、Web アプリのアプリ設定で設定されています。 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) コマンドを使用して、デプロイされるアプリにアプリ設定を追加します。

次のコマンドでは、`<blob_storage_account>` を BLOB ストレージ アカウントの名前に置き換え、`<blob_storage_key>` を関連付けられているキーに置き換えます。 `<web_app>` を、前の手順で作成した Web アプリの名前に置き換えます。

```azurecli-interactive
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=<blob_storage_key>  
```

Web アプリのデプロイと構成が済むと、アプリで画像のアップロード機能をテストできます。

## <a name="upload-an-image"></a>イメージをアップロードする

Web アプリをテストするには、発行したアプリの URL に移動します。 Web アプリの既定の URL は、`https://<web_app>.azurewebsites.net` です。
**[写真のアップロード]** 領域を選択し、ファイルを選択してアップロードするか、ファイルを領域にドラッグします。 正常にアップロードされると、画像は表示されなくなります。

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

![ImageResizer アプリ](media/storage-upload-process-images/figure1.png)

サンプル コードでは、*Storagehelper.cs* ファイル内の `UploadFiletoStorage` タスクの [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) メソッドを使用して、ストレージ アカウント内の *images* コンテナーに画像をアップロードします。 次のコード サンプルに、`UploadFiletoStorage` タスクが含まれています。

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

上のタスクでは、次のクラスとメソッドが使用されています。

|クラス  |方法  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

![画像アップロード アプリ](media/storage-upload-process-images/upload-app-nodejs.png)

このサンプル コードでは、`post` ルートが画像を BLOB コンテナーにアップロードする処理を担当します。 このルートは、次のモジュールを使用してアップロードを処理します。

- [multer](https://github.com/expressjs/multer) には、ルート ハンドラーのアップロード戦略が実装されています。
- [into-stream](https://github.com/sindresorhus/into-stream) では、[createBlockBlobFromStream](http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html#createBlockBlobFromStream) の要求に応じてバッファーがストリームに変換されます。

ファイルがルートに送信されると、ファイルが BLOB コンテナーにアップロードされるまで、ファイルの内容はメモリに残ります。

> [!IMPORTANT]
> 大きいファイルをメモリに読み込むと、Web アプリのパフォーマンスが低下する可能性があります。 ユーザーが大きなファイルを投稿することが想定される場合は、Web サーバー ファイル システム上にファイルをステージングしてから、BLOB ストレージへのアップロードをスケジュールする処理の検討をお勧めします。 ファイルが BLOB ストレージに格納されたら、サーバー ファイル システムからそのファイルを削除できます。

```javascript
const
      express = require('express')
    , router = express.Router()

    , multer = require('multer')
    , inMemoryStorage = multer.memoryStorage()
    , uploadStrategy = multer({ storage: inMemoryStorage }).single('image')

    , azureStorage = require('azure-storage')
    , blobService = azureStorage.createBlobService()

    , getStream = require('into-stream')
    , containerName = 'images'
;

const handleError = (err, res) => {
    res.status(500);
    res.render('error', { error: err });
};

const getBlobName = originalName => {
    const identifier = Math.random().toString().replace(/0\./, ''); // remove "0." from start of string
    return `${originalName}-${identifier}`;
};

router.post('/', uploadStrategy, (req, res) => {

    const
          blobName = getBlobName(req.file.originalname)
        , stream = getStream(req.file.buffer)
        , streamLength = req.file.buffer.length
    ;

    blobService.createBlockBlobFromStream(containerName, blobName, stream, streamLength, err => {

        if(err) {
            handleError(err);
            return;
        }

        res.render('success', { 
            message: 'File uploaded to Azure Blob storage.' 
        });
    });
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>ストレージ アカウント内に画像が表示されることを確認する

[Azure Portal](https://portal.azure.com) にサインインします。 左側のメニューから **[ストレージ アカウント]** を選択し、ストレージ アカウントの名前を選択します。 **[Blob service]** で **[BLOB]** を選択して、**images** コンテナーを選択します。

コンテナー内に画像が表示されることを確認します。

![images コンテナーの表示](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>サムネイルの表示をテストする

サムネイルの表示をテストするには、**thumbnails** コンテナーに画像をアップロードして、アプリが **thumbnails** コンテナーを読み取れることを確認します。

[Azure Portal](https://portal.azure.com) にサインインします。 左側のメニューから **[ストレージ アカウント]** を選択し、ストレージ アカウントの名前を選択します。 **[Blob service]** で **[BLOB]** を選択して、**thumbnails** コンテナーを選択します。 **[アップロード]** を選択して **[BLOB のアップロード]** ウィンドウを開きます。

ファイル ピッカーでファイルを選択し、**[アップロード]** を選択します。

アプリに戻って、**thumbnails** コンテナーにアップロードした画像が表示されていることを確認します。

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![images コンテナーの表示](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![images コンテナーの表示](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

シリーズの第 2 部では、サムネイル画像の作成を自動化して、この画像が必要ないようにします。 Azure Portal の **thumbnails** コンテナーで、アップロードした画像を選択し、**[削除]** を選択して画像を削除します。 

CDN を有効にして、Azure ストレージ アカウントの内容をキャッシュできます。 Azure ストレージ アカウントで CDN を有効にする方法について詳しくは、「[Azure ストレージ アカウントと Azure CDN との統合](../../cdn/cdn-create-a-storage-account-with-cdn.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

シリーズの第 1 部では、ストレージと対話するように Web アプリを構成する方法について学習しました。

シリーズの第 2 部に進み、Event Grid を使用して画像のサイズを変更する Azure 関数をトリガーする方法を学習してください。

> [!div class="nextstepaction"]
> [イベント グリッドを使用して画像のサイズを変更する Azure 関数をトリガーする](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
