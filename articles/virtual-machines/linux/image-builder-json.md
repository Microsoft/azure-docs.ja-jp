---
title: Azure Image Builder テンプレートを作成する (プレビュー)
description: Azure Image Builder で使用するテンプレートを作成する方法について説明します。
author: danis
ms.author: danis
ms.date: 01/23/2020
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 4ac2c1266933716697a658b1ba88efd8c2f05d34
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905763"
---
# <a name="preview-create-an-azure-image-builder-template"></a>プレビュー:Azure Image Builder テンプレートを作成する 

Azure Image Builder では、.json ファイルを使って Image Builder サービスに情報を渡します。 この記事では、独自のテンプレートを作成できるように、json ファイルの各セクションについて説明します。 完全な .json ファイルの例を確認するには、[Azure Image Builder の GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts) をご覧ください。

テンプレートの基本的な形式を次に示します。

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>"
            },
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>種類と API のバージョン

`type` はリソースの種類であり、`"Microsoft.VirtualMachineImages/imageTemplates"` にする必要があります。 `apiVersion` は API の変化に合わせて時間と共に変わりますが、プレビューでは `"2019-05-01-preview"` にする必要があります。

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

location は、カスタム イメージを作成するリージョンです。 Image Builder プレビューでは、次のリージョンがサポートされています。

- East US
- 米国東部 2
- 米国中西部
- 米国西部
- 米国西部 2


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
既定では Image Builder は "Standard_D1_v2" ビルド VM を使用しますが、これはオーバーライドできます。たとえば GPU VM 用にイメージをカスタマイズする場合は、GPU VM サイズが必要になります。 これは省略可能です。

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

既定では Image Builder はイメージのサイズを変更しません。ソース イメージのサイズが使用されます。 OS ディスク (Win および Linux) のサイズを調整できます。ただし、OS に最低限必要な領域よりも小さくしないでください。 これは省略可能であり、値 0 はソース イメージと同じサイズを維持することを意味します。 これは省略可能です。

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="tags"></a>Tags

これらは、生成されるイメージに対して指定できるキー/値ペアです。

## <a name="depends-on-optional"></a>依存関係 (省略可能)

この省略可能なセクションを使って、先に進む前に、依存関係が揃っていることを確認できます。 

```json
    "dependsOn": [],
```

詳しくは、「[リソースの依存関係を定義する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)」をご覧ください。

## <a name="identity"></a>ID
既定の Image Builder では、スクリプトの使用、または GitHub や Azure Storage などの複数の場所からのファイルのコピーがサポートされています。 これらを使うには、それらにパブリックからアクセスできる必要があります。

また、Azure ユーザー割り当てマネージド ID を自分で定義し、ID に Azure ストレージ アカウントで少なくとも "ストレージ BLOB データ閲覧者" を付与することによって、Image Builder で Azure Storage にアクセスできるようにすることもできます。 つまり、ストレージ BLOB を外部からアクセス可能にしたり、SAS トークンをセットアップしたりする必要はありません。


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

詳しい例については、「[Use an Azure User-Assigned Managed Identity to access files in Azure Storage (Azure ユーザー割り当てマネージド ID を使用して Azure Storage 内のファイルにアクセスする)](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)」をご覧ください。

ユーザー割り当て ID に対する Image Builder のサポート: •   単一の ID のみがサポートされます •   カスタム ドメイン名はサポートされません

詳しくは、「[Azure リソースのマネージド ID とは](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)」をご覧ください。
この機能のデプロイについて詳しくは、「[Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)」をご覧ください。

## <a name="properties-source"></a>プロパティ: source

`source` セクションには、Image Builder によって使われるソース イメージについての情報が含まれます。

API ではイメージ ビルド用のソースを定義する "SourceType" が必要であり、現在は次の 3 つの種類があります。
- ISO - ソースが RHEL ISO のときは、これを使います。
- PlatformImage - ソース イメージが Marketplace イメージであることを示します。
- ManagedImage - 標準のマネージド イメージから始めるときは、これを使います。
- SharedImageVersion - ソースとして共有イメージ ギャラリー内のイメージのバージョンを使うときは、これを使います。

### <a name="iso-source"></a>ISO ソース

Azure Image Builder のプレビューでは、公開された Red Hat Enterprise Linux 7.x Binary DVD ISO の使用のみがサポートされます。 Image Builder では以下がサポートされます。
- RHEL 7.3 
- RHEL 7.4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

`sourceURI` と `sha256Checksum` の値を取得するには、`https://access.redhat.com/downloads` に移動した後、製品として **Red Hat Enterprise Linux** を選択し、サポートされているバージョンを選択します。 

**[Installers and Images for Red Hat Enterprise Linux Server]\(Red Hat Enterprise Linux Server のインストーラーとイメージ\)** の一覧で、Red Hat Enterprise Linux 7.x Binary DVD のリンクとチェックサムをコピーする必要があります。

> [!NOTE]
> リンクのアクセス トークンは頻繁に更新されるので、テンプレートを送信するたびに、RH リンクのアドレスが変更されているかどうかを確認する必要があります。
 
### <a name="platformimage-source"></a>PlatformImage ソース 
Azure Image Builder では、Windows Server とクライアント、および Linux Azure Marketplace のイメージがサポートされます。完全な一覧については、[こちら](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support)を参照してください。 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


ここでのプロパティは VM の作成に使われるものと同じであり、プロパティを取得するには AZ CLI を使って以下を実行します。 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> バージョンを "latest" にすることはできません。上記のコマンドを使って、バージョン番号を取得する必要があります。 

### <a name="managedimage-source"></a>ManagedImage ソース

ソース イメージを、一般化された VHD または VM の既存のマネージド イメージとして設定します。 ソース マネージド イメージは、サポート対象の OS のものでなければならず、Azure Image Builder テンプレートと同じリージョンに存在する必要があります。 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` は、マネージド イメージの ResourceId にする必要があります。 使用可能なイメージの一覧を表示するには、`az image list` を使います。


### <a name="sharedimageversion-source"></a>SharedImageVersion ソース
ソース イメージを、共有イメージ ギャラリー内の既存のイメージ バージョンに設定します。 イメージのバージョンはサポート対象の OS のものでなければならず、イメージを Azure Image Builder テンプレートと同じリージョンにレプリケートする必要があります。 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` は、イメージ バージョンの ResourceId にする必要があります。 イメージ バージョンの一覧を表示するには、[az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list) を使います。

## <a name="properties-buildtimeoutinminutes"></a>プロパティ: buildTimeoutInMinutes

既定では、Image Builder は 240 分間実行されます。 その後、イメージのビルドが完了しているかどうかにかかわらず、タイムアウトして停止します。 タイムアウトに達した場合は、次のようなエラーが表示されます。

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

buildTimeoutInMinutes の値を指定しなかった場合、または 0 に設定した場合は、既定値が使用されます。 値は、最大で 960 分 (16 時間) まで増減できます。 Windows の場合、この値を 60 分未満に設定することはお勧めしません。 タイムアウトに達していることがわかった場合は、[ログ](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)を確認し、カスタマイズの手順がユーザー入力などで待機しているかどうかを確認します。 

カスタマイズを完了するためにより多くの時間が必要な場合は、オーバーヘッドが小さい必要と思われるものに設定します。 ただし、エラーが表示される前にタイムアウトするまで待機する必要があるため、設定値を大きくしすぎないでください。 


## <a name="properties-customize"></a>プロパティ: customize

Image Builder では、複数の "カスタマイザー" がサポートされています。 カスタマイザーは、スクリプトの実行やサーバーの再起動など、イメージのカスタマイズに使われる機能です。 

`customize` を使うとき: 
- 複数のカスタマイザーを使用できますが、一意の `name` を持っている必要があります。
- カスタマイザーは、テンプレートで指定されている順序で実行されます。
- 1 つのカスタマイザーが失敗した場合、カスタマイズ コンポーネント全体が失敗し、エラーが報告されます。
- テンプレートで使う前に、スクリプトを十分にテストすることを強くお勧めします。 独自の VM でスクリプトをデバッグする方が簡単です。
- スクリプト内には機密データを記述しないでください。 
- [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage) を使っていない場合は、パブリックにアクセスできる場所にスクリプトを置く必要があります。

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
customize セクションは配列です。 Azure Image Builder では、カスタマイザーが順番に実行されます。 いずれかのカスタマイザーでエラーが発生すると、ビルド プロセスが失敗します。 
 
 
### <a name="shell-customizer"></a>シェル カスタマイザー

シェル カスタマイザーではシェル スクリプトの実行がサポートされ、スクリプトは IB がアクセスできるようにパブリックにアクセス可能である必要があります。

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

OS のサポート: Linux 
 
カスタマイズのプロパティ:

- **type** - Shell 
- **name**- カスタマイズを追跡するための名前 
- **scriptUri** - ファイルの場所への URI 
- **inline** - コンマで区切られたシェル コマンドの配列。
- **sha256Checksum** - ファイルの sha256 チェックサムの値。これをローカルで生成すると、Image Builder によってチェックサムと検証が実行されます。
    * Mac/Linux のターミナルを使用して sha256Checksum を生成するには、`sha256sum <fileName>` を実行します。


スーパー ユーザー特権で実行するコマンドについては、`sudo` を先頭に付ける必要があります。

> [!NOTE]
> RHEL ISO ソースでシェル カスタマイザーを実行するときは、カスタマイズを行う前に、最初のカスタマイズ シェルで、Red Hat エンタイトルメント サーバーへの登録を処理する必要があります。 カスタマイズが完了したら、スクリプトでエンタイトルメント サーバーへの登録を解除する必要があります。

### <a name="windows-restart-customizer"></a>Windows 再起動カスタマイザー 
再起動カスタマイザーでは、Windows VM を再起動して、オンラインに戻るのを待機することができます。これにより、再起動が必要なソフトウェアをインストールできます。  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0 /c", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

OS のサポート: Windows
 
カスタマイズのプロパティ:
- **[種類]** :WindowsRestart
- **restartCommand** - 再起動を実行するコマンド (省略可能)。 既定では、 `'shutdown /r /f /t 0 /c \"packer restart\"'`です。
- **restartCheckCommand** - 再起動が成功したかどうかを確認するコマンド (省略可能)。 
- **restartTimeout** - 大きさと単位の文字列として指定された再起動のタイムアウト。 たとえば、`5m` (5 分) や `2h` (2 時間) などです。 既定値は "5m" です

### <a name="linux-restart"></a>Linux の再起動  
Linux 再起動カスタマイザーはありませんが、ドライバーまたは再起動が必要なコンポーネントをインストールする場合、それらをインストールし、シェル カスタマイザーを使用して再起動を呼び出すことができます。ビルド VM の SSH タイムアウトは 20 分です。

### <a name="powershell-customizer"></a>PowerShell カスタマイザー 
シェル カスタマイザーでは PowerShell スクリプトとインライン コマンドの実行がサポートされており、スクリプトは IB がアクセスできるようにパブリックにアクセス可能である必要があります。

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

OS のサポート: Windows と Linux

カスタマイズのプロパティ:

- **type** - PowerShell。
- **scriptUri** - PowerShell スクリプト ファイルの場所への URI。 
- **inline** - コンマで区切って指定された、実行するインライン コマンド。
- **valid_exit_codes** - 省略可能。スクリプト/インライン コマンドから返すことができる有効なコード。これにより、スクリプト/インライン コマンドの報告されたエラーが回避されます。
- **runElevated** - 省略可能。ブール値。昇格されたアクセス許可でコマンドとスクリプトを実行するためのサポート。
- **sha256Checksum** - ファイルの sha256 チェックサムの値。これをローカルで生成すると、Image Builder によってチェックサムと検証が実行されます。
    * Windows で PowerShell を使用して sha256Checksum を生成するには、[Get-Hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6) を実行します。


### <a name="file-customizer"></a>ファイル カスタマイザー

ファイル カスタマイザーを使うと、Image Builder で GitHub または Azure Storage からファイルをダウンロードできます。 ビルド成果物に依存するイメージ ビルド パイプラインがある場合、ファイル カスタマイザーを設定して、成果物をビルド共有からダウンロードし、イメージに移動することができます。  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

OS のサポート: Linux、Windows 

ファイル カスタマイザーのプロパティ:

- **sourceUri** - アクセス可能なストレージ エンドポイント。GitHub または Azure Storage を指定できます。 ダウンロードできるのは 1 つのファイルだけであり、ディレクトリ全体はできません。 ディレクトリをダウンロードする必要がある場合は、圧縮されたファイルを使用し、シェルまたは PowerShell カスタマイザーを使って圧縮を解除します。 
- **destination** - これは、ターゲットの完全なパスとファイル名です。 参照されているすべてのパスとサブディレクトリが存在する必要があり、事前にたこれらを設定するにはシェルまたは PowerShell カスタマイザーを使います。 スクリプト カスタマイザーを使ってパスを作成できます。 

これは Windows のディレクトリと Linux のパスでサポートされていますが、いくつか違いがあります。 
- Linux OS – Image builder で書き込むことができる唯一のパスは /tmp です。
- Windows – パスの制限はありませんが、パスが存在する必要があります。
 
 
ファイルのダウンロードまたは指定されたディレクトリへの格納を行おうとしてエラーが発生した場合、そのカスタマイズ ステップは失敗し、customization.log にそれが記録されます。

> [!NOTE]
> ファイル カスタマイザーは、小さいファイルのダウンロード (20 MB 未満) にのみ適しています。 大きいファイルをダウンロードする場合は、スクリプトまたはインライン コマンド (ファイルをダウンロードするための使用コード) を使用します。たとえば、Linux の場合は `wget` または `curl`、Windows の場合は `Invoke-WebRequest` などがあります。

ファイル カスタマイザーのファイルは、[MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage) を使って Azure Storage からダウンロードできます。

### <a name="generalize"></a>Generalize 
既定の Azure Image Builder では、イメージを "一般化" するため、各イメージ カスタマイズ フェーズの最後に "プロビジョニング解除" コードも実行されます。 一般化とは、複数の VM を作成するために再利用できるようにイメージを設定するプロセスです。 Windows VM の Azure Image Builder では、Sysprep が使われます。 Linux の Azure Image Builder では、"waagent -deprovision" が使われます。 

状況によっては Image Builder で一般化に使われるコマンドが適していない可能性があるので、Azure Image Builder では、必要に応じてこのコマンドをカスタマイズできます。 

既存のカスタマイズを移行していて、異なる Sysprep/waagent コマンドを使っている場合、Image Builder の汎用コマンドを使うと VM の作成が失敗するときは、独自の Sysprep または waagent コマンドを使うことができます。

Azure Image Builder で正常に作成された Windows のカスタム イメージから VM を作成した後、VM の作成が失敗したこと、または正常に完了しなかったことがわかった場合は、Windows Server Sysprep のドキュメントを確認するか、Windows Server Sysprep のカスタマー サービス サポート チームにサポート要求を送る必要があります。チームは、トラブルシューティングを行って、Sysprep の適切な使用方法をアドバイスできます。


#### <a name="default-sysprep-command"></a>既定の Sysprep コマンド
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>既定の Linux プロビジョニング解除コマンド

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>コマンドのオーバーライド
コマンドをオーバーライドするには、PowerShell またはシェル スクリプトのプロビジョナーを使って正確なファイル名のコマンド ファイルを作成し、適切なディレクトリに置きます。

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder では、これらのコマンドが読み取られて、AIB ログ "customization.log" に書き込まれます。 ログを収集する方法については、[troubleshooting (トラブルシューティング)](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) に関する記事をご覧ください。
 
## <a name="properties-distribute"></a>プロパティ: distribute

Azure Image Builder では、次の 3 つの配布ターゲットがサポートされています。 

- **managedImage** - マネージド イメージ。
- **sharedImage** - 共有イメージ ギャラリー。
- **VHD** - ストレージ アカウント内の VHD。

すべてのターゲットの種類に同じ構成でイメージを配布できます。[例](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)をご覧ください。

複数のターゲットに配布できるので、Image Builder ではすべての配布ターゲットの状態が維持されており、`runOutputName` のクエリを実行することによってアクセスできます。  配布の後で `runOutputName` オブジェクトのクエリを実行して、その配布に関する情報を取得できます。 たとえば、VHD の場所、イメージ バージョンがレプリケートされたリージョン、または作成された SIG イメージのバージョンのクエリを実行できます。 これは、すべての配布ターゲットのプロパティです。 `runOutputName` は配布ターゲットごとに一意である必要があります。 次に例を示します。これは、Shared Image Gallery の配布に対するクエリです。

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
```

出力:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>配布: managedImage

イメージの出力は、マネージド イメージ リソースになります。

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
配布のプロパティ:
- **type** – managedImage 
- **imageId** – 配布先イメージのリソース ID。必要な形式: /subscriptions/\<subscriptionId>/resourceGroups/\<destinationResourceGroupName>/providers/Microsoft.Compute/images/\<imageName>
- **location** - マネージド イメージの場所。  
- **runOutputName** – 配布を示す一意の名前。  
- **artifactTags** -省略可能なユーザー指定のキー値ペアのタグ。
 
 
> [!NOTE]
> 配布先のリソース グループは存在している必要があります。
> 別のリージョンにイメージを配布する場合は、デプロイ時間が長くなります。 

### <a name="distribute-sharedimage"></a>配布: sharedImage 
Azure 共有イメージ ギャラリーは新しいイメージ管理サービスであり、イメージのリージョン レプリケーションの管理、バージョン管理、カスタム イメージの共有を行うことができます。 Azure Image Builder ではこのサービスによる配布がサポートされているので、共有イメージ ギャラリーでサポートされているリージョンにイメージを配布できます。 
 
共有イメージ ギャラリーは次のもので構成されます。 
 
- ギャラリー - 複数の共有イメージ用のコンテナー。 ギャラリーは、1 つのリージョンにデプロイされます。
- イメージ定義 - イメージの概念的なグループ化。 
- イメージ バージョン - VM またはスケール セットのデプロイに使われるイメージの種類。 イメージ バージョンは、VM をデプロイする必要がある他のリージョンにレプリケートできます。
 
イメージ ギャラリーに配布するには、その前にギャラリーとイメージの定義を作成しておく必要があります。[共有イメージ](shared-images.md)に関する記事をご覧ください。 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

共有イメージ ギャラリーの配布プロパティ:

- **type** - sharedImage  
- **galleryImageId** – 共有イメージ ギャラリーの ID。 形式は /subscriptions/\<subscriptionId>/resourceGroups/\<resourceGroupName>/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName>/images/\<imageGalleryName> です。
- **runOutputName** – 配布を示す一意の名前。  
- **artifactTags** -省略可能なユーザー指定のキー値ペアのタグ。
- **replicationRegions** -レプリケーション用のリージョンの配列。 リージョンの 1 つは、ギャラリーがデプロイされているリージョンでなければなりません。
 
> [!NOTE]
> ギャラリーとは別のリージョンで Azure Image Builder を使用できますが、Azure Image Builder サービスはデータセンター間でイメージを転送する必要があり、これには長い時間がかかります。 Image Builder ではモノトニックな整数に基づいて自動的にイメージのバージョンが設定され、それを指定することは現在はできません。 

### <a name="distribute-vhd"></a>配布: VHD  
VHD に出力することができます。 その後、VHD をコピーし、それを使って Azure MarketPlace に発行したり、Azure Stack で使ったりできます。  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
OS のサポート: Windows、Linux

VHD 配布のパラメーター:

- **type** - VHD。
- **runOutputName** – 配布を示す一意の名前。  
- **tags** -省略可能なユーザー指定のキー値ペアのタグ。
 
Azure Image Builder ではユーザーはストレージ アカウントの場所を指定できませんが、`runOutputs` の状態のクエリを実行して場所を取得できます。  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> VHD が作成されたら、できるだけ早く別の場所にそれをコピーします。 VHD は、イメージ テンプレートが Azure Image Builder サービスに送信されるときに作成される一時的なリソース グループのストレージ アカウントに格納されます。 イメージ テンプレートを削除すると、VHD が失われます。 
 
## <a name="next-steps"></a>次のステップ

さまざまなシナリオの .json ファイルのサンプルが、[Azure Image Builder の GitHub](https://github.com/danielsollondon/azvmimagebuilder) にあります。
 
