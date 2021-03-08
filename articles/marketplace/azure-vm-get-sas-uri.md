---
title: VM イメージの SAS URI を生成する - Azure Marketplace
description: Azure Marketplace で仮想ハード ディスク (VHD) の Shared Access Signature (SAS) URI を生成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 1/5/2021
ms.openlocfilehash: 560699296b8cae83413c36820106eedf7fef7414
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914163"
---
# <a name="how-to-generate-a-sas-uri-for-a-vm-image"></a>VM イメージの SAS URI を生成する方法

発行プロセス中に、プラン (以前は SKU と呼ばれていました) に関連付けられている各 VHD の SAS (Shared Access Signature) URI を指定する必要があります。 マイクロソフトは、認定プロセスでこれらの VHD にアクセスします。 この URI は、パートナー センターの **[プラン]** タブで入力します。

VHD の SAS URI の生成には、次の要件があります。

- アンマネージド VHD のみがサポートされます。
- リストおよび読み取りアクセス許可のみが必要です。 書き込みまたは削除アクセス権を付与しないでください。
- アクセスするための期間 (有効期限) は、SAS URI の作成時から最低 3 週間必要です。
- UTC 時刻の変更から保護するには、開始日を現在の日付の 1 日前に設定します。 たとえば、現在の日付が 2020 年 6 月 16 日である場合は、6/15/2020 を選択します。

## <a name="generate-the-sas-address"></a>SAS アドレスを生成する

SAS アドレス (URL) の作成には、次の 2 つの一般的なツールが使用されます。

1. **Azure Storage Explorer** – Azure portal で利用できます。
2. **Azure CLI** – Windows 以外のオペレーティング システムや自動化または継続的インテグレーションの環境に推奨されます。

### <a name="using-tool-1-azure-storage-explorer"></a>ツール 1 の使用: Azure ストレージ エクスプローラー

1. ご利用の **ストレージ アカウント** に移動します。
1. **[ストレージ エクスプローラー]** を開きます。

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="[ストレージ アカウント] ウィンドウ。":::

3. **[コンテナー]** で VHD ファイルを右クリックして、 **[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。
4. **[共有アクセス署名]** ダイアログ ボックスで、次のフィールドに入力します。

    1. [開始時刻] - VHD のアクセス許可の開始日。 現在の日付の 1 日前の日付を指定します。
    2. [有効期限] – VHD アクセスのためのアクセス許可の有効期限。 現在の日付から 3 週間以上先の日付を指定します。
    3. [アクセス許可] - [読み取り] と [一覧表示] のアクセス許可を選択します。
    4. [Container-level]\(コンテナーレベル\) - [コンテナー レベルの Shared Access Signature URI の生成] チェック ボックスをオンにします。

    ![[共有アクセス署名] ダイアログ ボックス。](media/vm/create-sas-uri-storage-explorer.png)

5. この VHD に関連付けられている SAS URI を作成するには、 **[作成]** を選択します。
6. [URI] をコピーし、テキスト ファイルとして安全な場所に保存します。 ここで生成された SAS URI のアクセス範囲は、コンテナーレベルとなります。 これを固有のものにするには、テキスト ファイルを編集して VHD 名を追加します。
7. SAS URI の vhds 文字列の後に VHD 名を挿入します (スラッシュを含めます)。 最終的な SAS URI は次のようになります。

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. 発行するプラン内の各 VHD に対して、これらの手順を繰り返します。

### <a name="using-tool-2-azure-cli"></a>ツール 2 の使用: Azure CLI

1. [Microsoft Azure CLI](/cli/azure/install-azure-cli) をダウンロードしてインストールします。 Windows と macOS のほか、各種 Linux ディストリビューション向けのバージョンが用意されています。
2. PowerShell ファイル (.ps1 ファイル拡張子) を作成し、次のコードをコピーして、ローカルに保存します。

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <container-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. 次のパラメーター値を使用するようにファイルを編集します。 UTC 日時形式の日付 (2020-04-01T00:00:00Z など) を指定します。

    - account-name – Azure ストレージ アカウント名。
    - account-key – Azure ストレージ アカウント キー。
    - start-date – VHD アクセスのためのアクセス許可の開始日。 現在の日付の 1 日前の日付を指定します。
    - expiry-date – VHD アクセスのためのアクセス許可の有効期限。 現在の日付から 3 週間以上先の日付を指定します。

    適切なパラメーター値の例を次に示します (この記事の作成時点)。

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name <container-name> -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’
    ```

1. 変更を保存します。
2. 次のいずれかの方法を使用して、管理者特権でこのスクリプトを実行し、コンテナーレベルのアクセスのための SAS 接続文字列を作成します。

    - コンソールからスクリプトを実行します。 Windows で、スクリプトを右クリックし、 **[管理者として実行]** を選択します。
    - [Windows PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise) などの PowerShell スクリプト エディターからスクリプトを実行します。 この画面には、このエディター内の SAS 接続文字列の作成が示されています。

    [![PowerShell エディター内での SAS 接続文字列の作成](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. SAS 接続文字列をコピーし、テキスト ファイルとして安全な場所に保存します。 この文字列を編集して、最終的な SAS URI を作成するために VHD の場所情報を追加します。
7. Azure portal で、新しい URI に関連付けられている VHD を含む BLOB ストレージにアクセスします。
8. BLOB サービス エンドポイントの URL をコピーします。

    ![BLOB サービス エンドポイントの URL のコピー。](media/vm/create-sas-uri-blob-endpoint.png)

9. 手順 6. で得た SAS 接続文字列でテキスト ファイルを編集します。 次の形式を使用して、完全な SAS URI を作成します。

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>SAS URI の検証

要求を送信した後に SAS URI に関連した問題が発生しないようにするために、パートナー センターで発行する前に SAS URI を確認します。 このプロセスは省略可能ですが、実行することをお勧めします。

- URI には、VHD イメージのファイル名 (ファイル名拡張子 `.vhd` を含む) が含まれています。
- `Sp=rl` が URI の中間付近に出現します。 この文字列は、読み取りおよび一覧表示アクセスが指定されていることを示します。
- `sr=c` が出現する場合は、コンテナーレベルのアクセスが指定されていることを意味します。
- URI をコピーしてブラウザーに貼り付け、BLOB のテストダウンロードを行うことができます (ダウンロードが完了する前に操作を取り消すことができます)。

## <a name="next-steps"></a>次の手順

- 問題が発生した場合は、[VM SAS のエラー メッセージ](azure-vm-sas-failure-messages.md)に関するページを参照してください。
- [パートナー センターにサインインする](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
- [Azure Marketplace で仮想マシン オファーを作成する](azure-vm-create.md)
