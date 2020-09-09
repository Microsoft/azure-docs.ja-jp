---
title: インスタンスと認証を設定する (CLI)
titleSuffix: Azure Digital Twins
description: CLI を使用して Azure Digital Twins サービスのインスタンスを設定する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3c7e4887610f30113b81421396500416d04c5e5e
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88078514"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Azure Digital Twins インスタンスと認証を設定する (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

この記事では、**新しい Azure Digital Twins インスタンスを設定する**手順 (インスタンスの作成と認証の設定を含む) について説明します。 この記事を完了すると、Azure Digital Twins インスタンスのプログラミングを開始する準備が完了します。

この記事のこのバージョンでは、CLI を使用して、これらの手順を 1 つずつ手動で実行します。
* Azure portal を使用してこれらの手順を手動で実行するには、この記事のポータル バージョンである[*方法: インスタンスと認証の設定 (ポータル)* ](how-to-set-up-instance-portal.md) に関するページを参照してください。
* デプロイ スクリプトのサンプルを使用して自動化された設定を実行するには、この記事のスクリプト化バージョンである[*方法: インスタンスと認証の設定 (スクリプト化)* ](how-to-set-up-instance-scripted.md) に関するページを参照してください。

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Cloud Shell セッションの設定
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを作成する

このセクションでは、Cloud Shell コマンドを使用して、**Azure Digital Twins の新しいインスタンスを作成**します。 次のものを指定する必要があります。
* デプロイ先のリソース グループ。 既存のリソース グループがまだ念頭にない場合は、ここで次のコマンドを使用して作成できます。
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* デプロイのためのリージョン。 Azure Digital Twins がどのリージョンでサポートされているかを確認するには、[*リージョン別の利用可能な Azure 製品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)に関するページを参照してください。
* インスタンスの名前。 新しいインスタンスの名前は、サブスクリプションのリージョン内で一意である必要があります (つまり、サブスクリプションのリージョン内に、選択した名前を既に使用している別の Azure Digital Twins インスタンスが存在する場合は、異なる名前を選択するよう求められます)。

次のコマンドでこれらの値を使用してインスタンスを作成します。

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>正常に実行されたことを確認して重要な値を収集する

インスタンスが正常に作成された場合、Cloud Shell の結果は次のようになり、作成したリソースに関する情報が出力されます。

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="リソース グループと Azure Digital Twins インスタンスが正常に作成されたコマンド ウィンドウ":::

Azure Digital Twins インスタンスの *hostName*、*name*、*resourceGroup* を出力から確認します。 これらはすべて、認証および関連する Azure リソースを設定するために、Azure Digital Twins インスタンスを引き続き操作する場合に必要になる可能性がある重要な値です。 他のユーザーがこのインスタンスに対してプログラミングする場合は、これらの値をそのユーザーと共有する必要があります。

> [!TIP]
> これらのプロパティは、インスタンスのすべてのプロパティと共に、`az dt show --dt-name <your-Azure-Digital-Twins-instance>` を実行することでいつでも確認できます。

これで、Azure Digital Twins インスタンスの準備が完了しました。 次に、適切な Azure ユーザーにそれを管理するためのアクセス許可を付与します。

## <a name="set-up-user-access-permissions"></a>ユーザーのアクセス許可を設定する

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

ロールを割り当てるには、次のコマンドを使用します (Azure サブスクリプションで、[十分なアクセス許可](#prerequisites-permission-requirements)を持つユーザーによって実行される必要があります)。 このコマンドを実行するには、ロールを割り当てる必要があるユーザーの Azure AD アカウントで、"*ユーザー プリンシパル名*" を渡す必要があります。 ほとんどの場合、これは Azure AD アカウントのユーザーの電子メールと一致します。

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

このコマンドの結果として、作成されたロールの割り当てに関する情報が出力されます。

> [!NOTE]
> このコマンドによって、CLI が**グラフ データベースでユーザーまたはサービス プリンシパルを見つけることができない**というエラーが返される場合は、次のことを行います。
>
> 代わりに、ユーザーの "*オブジェクト ID*" を使用して、ロールを割り当てます。 これは、個人の [Microsoft アカウント (MSA)](https://account.microsoft.com/account) を使用するユーザーの場合に当てはまります。 
>
> [Azure Active Directory ユーザーの Azure portal ページ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)を使用して、ユーザー アカウントを選択し、その詳細を開きます。 そのユーザーの "*ObjectID*" をコピーします。
>
> :::image type="content" source="media/includes/user-id.png" alt-text="[オブジェクト ID] フィールドで GUID が強調表示されている、Azure portal のユーザー ページの表示" lightbox="media/includes/user-id.png":::
>
> 次に、前述の `assignee` パラメーターにユーザーの "*オブジェクト ID*" を使用して、ロールの割り当てリスト コマンドを繰り返します。

### <a name="verify-success"></a>成功を確認する

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

これで Azure Digital Twins インスタンスの準備が完了し、それを管理するためのアクセス許可が割り当てられました。 次に、それにアクセスするためのクライアント アプリのアクセス許可を設定します。

## <a name="set-up-access-permissions-for-client-applications"></a>クライアント アプリケーションのアクセス許可を設定する

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

アプリの登録を作成するには、Azure Digital Twins API のリソース ID と、API に対するベースライン アクセス許可を指定する必要があります。

作業ディレクトリで、新しいファイルを作成し、次の JSON スニペットを入力してこれらの詳細を構成します。 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

このファイルを _**manifest.json**_ として保存します。

> [!NOTE] 
> GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` ではなく、"わかりやすい" 人間が判読できる文字列 `https://digitaltwins.azure.net` を Azure Digital Twins のリソース アプリ ID に使用できる場所がいくつかあります。 たとえば、このドキュメント セット全体の多くの例では、MSAL ライブラリでの認証が使用されており、わかりやすい文字列を使用できます。 しかし、アプリの登録を作成するこの手順では、上に示されているように、GUID 形式の ID が必要です。 

次に、このファイルを Cloud Shell にアップロードします。 Cloud Shell ウィンドウで、[ファイルのアップロード/ダウンロード] アイコンをクリックし、[アップロード] を選択します。

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="[アップロード] オプションが選択されていることを示す Cloud Shell ウィンドウ":::
先ほど作成した *manifest.json* に移動し、[開く] をクリックします。

次に、以下のコマンドを実行して、アプリの登録を作成します (必要に応じて、プレースホルダーを置き換える)。

```azurecli
az ad app create --display-name <name-for-your-app-registration> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

作成した登録に関する情報を示す、このコマンドからの出力の抜粋を次に示します。

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/new-app-registration.png" alt-text="新しい Azure AD アプリ登録の Cloud Shell 出力":::

### <a name="verify-success"></a>成功を確認する

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

最初に、アップロードした *manifest.json* の設定が登録時に正しく設定されたことを確認します。 これを行うには、メニュー バーから *[マニフェスト]* を選択して、アプリ登録のマニフェスト コードを表示します。 コード ウィンドウの一番下までスクロールし、*manifest.json* の `requiredResourceAccess` の下のフィールドを探します。

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>重要な値を収集する

次に、メニュー バーから *[概要]* を選択して、アプリ登録の詳細を表示します。

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="アプリ登録の重要な値のポータル ビュー":::

**実際の**ページに表示される、"*アプリケーション (クライアント) ID*" と "*ディレクトリ (テナント) ID*" をメモしておきます。 これらの値は、後で [Azure Digital Twins API に対してクライアント アプリを認証する](how-to-authenticate-client.md)ために必要になります。 自分がこのようなアプリケーションのコードを作成するユーザーでない場合は、これらの値をそのユーザーと共有する必要があります。

### <a name="other-possible-steps-for-your-organization"></a>組織でのその他の考えられる手順

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>次のステップ

クライアント アプリの認証コードを作成してクライアント アプリケーションをインスタンスに接続する方法について説明します。
* [*方法: アプリ認証コードを作成する*](how-to-authenticate-client.md)