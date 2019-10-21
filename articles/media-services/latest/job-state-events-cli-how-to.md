---
title: CLI を使用した Event Grid による Azure Media Services イベントの監視 | Microsoft Docs
description: この記事では、Azure Media Services イベントを監視するために Event Grid をサブスクライブする方法を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 619d40ab56715b4444d8e5649c7fb3401b3f57ff
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937278"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Azure CLI を使用した Event Grid による Media Services イベントの作成と監視

Azure Event Grid は、クラウドのイベント処理サービスです。 このサービスは、[イベント サブスクリプション](../../event-grid/concepts.md#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。 Media Services イベントには、データの変更に対応するために必要なすべての情報が含まれます。 Media Services イベントは、eventType プロパティが "Microsoft.Media" で始まっていることで識別できます。 詳細については、「[Media Services イベントのスキーマ](media-services-event-schemas.md)」を参照してください。

この記事では、Azure CLI を使用して、Azure Media Services アカウントのイベントをサブスクライブします。 次に、イベントをトリガーして結果を表示します。 通常は、イベント データを処理し、アクションを実行するエンドポイントにイベントを送信します。 この記事では、メッセージを収集して表示する Web アプリにイベントを送信します。

## <a name="prerequisites"></a>前提条件

- 有効な Azure サブスクリプション Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。
- CLI をローカルにインストールして使用します。この記事では、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

    現在、一部の [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) コマンドが Azure Cloud Shell では正常に動作しません。 CLI はローカルで使用することをお勧めします。

- [Media Services アカウントを作成する](create-account-cli-how-to.md)

    Media Services アカウント名、ストレージ名、およびリソース名として使用した値を覚えておいてください。

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

Media Services アカウントのイベントをサブスクライブする前に、イベント メッセージのエンドポイントを作成しましょう。 通常、エンドポイントは、イベント データに基づくアクションを実行します。 この記事では、イベント メッセージを表示する[構築済みの Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をデプロしします。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

1. **[Deploy to Azure]\(Azure にデプロイ\)** を選択して、ソリューションをサブスクリプションにデプロイします。 Azure portal で、パラメーターの値を指定します。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. デプロイが完了するまでに数分かかる場合があります。 デプロイが成功した後で、Web アプリを表示して、実行されていることを確認します。 Web ブラウザーで `https://<your-site-name>.azurewebsites.net` にアクセスします

"Azure Event Grid ビューアー" サイトに切り替えると、まだイベントがないことがわかります。
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Azure サブスクリプションを設定する

次のコマンドで、Media Services アカウントで使用する Azure サブスクリプション ID を指定します。 [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) に移動することで、アクセス権があるサブスクリプションの一覧を表示できます。

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Media Services イベントのサブスクライブ

どのイベントを追跡するかを Event Grid に通知するアーティクルをサブスクライブします。次の例では、作成した Media Services アカウントをサブスクライブし、イベント通知のエンドポイントとして作成した Web サイトの URL を渡します。 

`<event_subscription_name>` をイベント サブスクリプションの一意の名前に置き換えます。 `<resource_group_name>`と`<ams_account_name>`、Media Services アカウントを作成するときに使用した値を使用します。 `<endpoint_URL>` に対して、Web アプリの URL を入力し、ホーム ページの URL に `api/updates` を追加します。 サブスクライブ時にエンドポイントを指定することによって、そのエンドポイントに対するイベントのルーティングが Event Grid によって行われます。 

1. リソース ID を取得する

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    例:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. イベントをサブスクライブする

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    例:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > 検証ハンドシェイク警告が表示されることがあります。 数分待つと、ハンドシェイクが検証されます。

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。

## <a name="send-an-event-to-your-endpoint"></a>エンドポイントへのイベントの送信

エンコード ジョブを実行して、Media Services アカウントのイベントをトリガーできます。 [このクイック スタート](stream-files-dotnet-quickstart.md)に従って、ファイルをエンコードし、イベントの送信を開始できます。 

Web アプリをもう一度表示し、その Web アプリにサブスクリプションの検証イベントが送信されたことに注目します。 Event Grid は検証イベントを送信するので、エンドポイントはイベント データを受信することを確認できます。 エンドポイントは、`validationResponse` から `validationCode` を設定する必要があります。 詳細については、「[Event Grid security and authentication](../../event-grid/security-authentication.md)」(Event Grid のセキュリティと認証) を参照してください。 Web アプリ コードを表示して、サブスクリプションがどのように検証されるかを確認できます。

> [!TIP]
> 目のアイコンを選択してイベント データを展開します。 すべてのイベントを表示したい場合は、ページを最新の情報に更新しないでください。

![サブスクリプション イベントの表示](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>次の手順

[アップロード、エンコード、およびストリーミング](stream-files-tutorial-with-api.md)

