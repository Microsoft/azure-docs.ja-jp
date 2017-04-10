
Microsoft Azure クラウド サービスに関する問題を診断するためには、仮想マシン上で問題の発生に伴って生成されるクラウド サービスのログ ファイルを収集する必要があります。 AzureLogCollector 拡張機能をオンデマンドで使用し、クラウド サービスの VM (Web ロールと worker ロールの両方) からログを都度収集して、Azure ストレージ アカウントにその収集したファイルを転送することができます。このとき、リモートから VM にログオンする必要は一切ありません。

> [!NOTE]
> ログに記録される主な情報については、http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp を参照してください。
> 
> 

収集モードは 2 つあり、どちらが使用されるかは、収集するファイルの種類によって異なります。

* Azure ゲスト エージェント ログのみ (GA)。 Azure のゲスト エージェントとその他の Azure コンポーネントに関連したすべてのログが対象となります。
* すべてのログ (Full)。 GA モードで収集されるすべてのファイルに加え、次のファイルが収集されます。
  
  * システムとアプリケーションのイベント ログ
  * HTTP エラー ログ
  * IIS ログ
  * セットアップ ログ
  * その他のシステム ログ

いずれの収集モードも、次の構造体を使用してデータ収集フォルダーを追加指定できます。

* **Name**: 収集の名前。収集対象となる zip ファイル内のサブフォルダーの名前として使用されます。
* **Location**: 収集されたファイルが格納される、仮想マシン上のフォルダーのパス。
* **SearchPattern**: 収集対象ファイル名のパターン。 既定値は “*” です。
* **Recursive**: 指定されたフォルダー内のファイルを再帰的に収集するかどうか。

## <a name="prerequisites"></a>前提条件
* 生成された zip ファイルを保存するための、拡張機能のストレージ アカウントを所有している必要があります。
* Azure PowerShell コマンドレット V0.8.0 以降を使用している必要があります。 詳細については、 [Azure のダウンロード](https://azure.microsoft.com/downloads/)に関するページを参照してください。

## <a name="add-the-extension"></a>拡張機能の追加
AzureLogCollector 拡張機能は、[Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) コマンドレットまたは [Service Management REST API](https://msdn.microsoft.com/library/ee460799.aspx) を使用して追加することができます。

Cloud Services に関しては、 **Set-AzureServiceExtension**という既存の Azure PowerShell コマンドレットを使用して、クラウド サービス ロール インスタンス上で拡張機能を有効にすることができます。 このコマンドレットを使ってこの拡張機能を有効にすると、そのたびに、選択したロールの選択したロール インスタンス上でログ収集が開始されます。

Virtual Machines に関しては、 **Set-AzureVMExtension**という既存の Azure PowerShell コマンドレットを使用して、Virtual Machines 上で拡張機能を有効にすることができます。 このコマンドレットを使ってこの拡張機能を有効にすると、そのたびに、各インスタンス上でログ収集が開始されます。

この拡張機能の内部では、JSON ベースの PublicConfiguration と PrivateConfiguration が使用されています。 以下に示したのは、パブリック構成とプライベート構成に使用されるサンプル JSON のレイアウトです。

### <a name="publicconfiguration"></a>PublicConfiguration
    {
        "Instances":  "*",
        "Mode":  "Full",
        "SasUri":  "SasUri to your storage account with sp=wl",
        "AdditionalData":
        [
          {
                  "Name":  "StorageData",
                  "Location":  "%roleroot%storage",
                  "SearchPattern":  "*.*",
                  "Recursive":  "true"
          },
          {
                "Name":  "CustomDataFolder2",
                "Location":  "c:\customFolder",
                "SearchPattern":  "*.log",
                "Recursive":  "false"
          },
        ]
    }

### <a name="privateconfiguration"></a>PrivateConfiguration
    {

    }

> [!NOTE]
> この拡張機能に **privateConfiguration**は必要ありません。 **–PrivateConfiguration** 引数には空の構造体を指定してください。
> 
> 

以下に示すいずれかの手順に従い、選択したロールのクラウド サービスまたは仮想マシンのインスタンスに AzureLogCollector を追加すると、それぞれの VM でログ収集が開始され、収集されたファイルが指定の Azure アカウントに送信されます。

## <a name="adding-as-a-service-extension"></a>サービス拡張機能として追加
1. 適切な手順に従って Azure PowerShell を自分のサブスクリプションに接続します。
2. AzureLogCollector 拡張機能を追加して有効にするサービスの名前、スロット、ロール、ロール インスタンスを指定します。
   
        #Specify your cloud service name
        $ServiceName = 'extensiontest2'
   
        #Specify the slot. 'Production' or 'Staging'
        $slot = 'Production'
   
        #Specified the roles on which the extension will be installed and enabled
        $roles = @("WorkerRole1","WebRole1")
   
        #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
        $instances = @("*")
   
        #Specify the collection mode, "Full" or "GA"
        $mode = "GA"
3. ファイルの収集対象となるデータ フォルダーを追加指定します (この手順は省略可能です)。
   
        #add one location
        $a1 = New-Object PSObject
   
        $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
        $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
        $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
        $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"
   
        $AdditionalDataList+= $a1
              #more locations can be added....
   
   > [!NOTE]
   > このロールには固定ドライブが使用されていないため、トークン `%roleroot%` を使用してルート ドライブを指定します。
   > 
   > 
4. 収集されたファイルのアップロード先となる Azure ストレージ アカウントの名前とキーを指定します。
   
        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'
5. 次のように SetAzureServiceLogCollector.ps1 (記事の最後に記載) を呼び出して、クラウド サービスの AzureLogCollector 拡張機能を有効にします。 実行後、アップロードされたファイルは `https://YouareStorageAccountName.blob.core.windows.net/vmlogs` で確認できます。
   
        .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList

以下に示したのは、スクリプトに渡すパラメーターの定義です。 (この定義が、以下のスクリプト ファイルにも使用されています。)

    [CmdletBinding(SupportsShouldProcess = $true)]

    param (
      [Parameter(Mandatory=$true)]
    [string]   $ServiceName,

    [Parameter(Mandatory=$false)]
    [string[]] $Roles ,

    [Parameter(Mandatory=$false)]
    [string[]] $Instances,

    [Parameter(Mandatory=$false)]
    [string]   $Slot = 'Production',

    [Parameter(Mandatory=$false)]
    [string]   $Mode = 'Full',

    [Parameter(Mandatory=$false)]
    [string]   $StorageAccountName,

    [Parameter(Mandatory=$false)]
    [string]   $StorageAccountKey,

    [Parameter(Mandatory=$false)]
    [PSObject[]] $AdditionDataLocationList = $null
    )

* *ServiceName*: クラウド サービスの名前。
* *Roles*: 一連のロール (“WebRole1”、”WorkerRole1” など)。
* *Instances*: 一連のロール インスタンスの名前をコンマで区切って指定します。すべてのロール インスタンスを指定するときは、ワイルドカード文字列 (“*”) を使用してください。
* *Slot*: スロット名。 “Production” または “Staging”。
* *Mode*: 収集モード。 “Full” または “GA”。
* *StorageAccountName*: 収集されたデータを格納するための Azure ストレージ アカウントの名前。
* *StorageAccountKey*: Azure ストレージ アカウント キーの名前。
* *AdditionalDataLocationList*: 次の構造体のリスト。
  
      {
      String Name,
      String Location,
      String SearchPattern,
      Bool   Recursive
      }

## <a name="adding-as-a-vm-extension"></a>VM 拡張機能として追加
適切な手順に従って Azure PowerShell を自分のサブスクリプションに接続します。

1. サービスの名前、VM、収集モードを指定します。
   
        #Specify your cloud service name
        $ServiceName = 'YourCloudServiceName'
   
        #Specify the VM name
        $VMName = "'YourVMName'"
   
        #Specify the collection mode, "Full" or "GA"
        $mode = "GA"
   
        Specify the additional data folder for which files will be collected (this step is optional).
   
        #add one location
        $a1 = New-Object PSObject
   
        $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
        $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
        $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
        $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"
   
        $AdditionalDataList+= $a1
              #more locations can be added....
2. 収集されたファイルのアップロード先となる Azure ストレージ アカウントの名前とキーを指定します。
   
        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'
3. 次のように SetAzureVMLogCollector.ps1 (記事の最後に記載) を呼び出して、クラウド サービスの AzureLogCollector 拡張機能を有効にします。 実行が完了すると、アップロードされたファイルを https://YouareStorageAccountName.blob.core.windows.net/vmlogs で見つけることができます。

以下に示したのは、スクリプトに渡すパラメーターの定義です。 (この定義が、以下のスクリプト ファイルにも使用されています。)

    [CmdletBinding(SupportsShouldProcess = $true)]

    param (
        [Parameter(Mandatory=$true)]
      [string]   $ServiceName,

      [Parameter(Mandatory=$false)]
      [string] $VMName ,

        [Parameter(Mandatory=$false)]
      [string]   $Mode = 'Full',

      [Parameter(Mandatory=$false)]
      [string]   $StorageAccountName,

      [Parameter(Mandatory=$false)]
      [string]   $StorageAccountKey,

      [Parameter(Mandatory=$false)]
      [PSObject[]] $AdditionDataLocationList = $null
      )

* ServiceName: クラウド サービスの名前。
* VMName: VM の名前。
* Mode: 収集モード。 “Full” または “GA”。
* StorageAccountName: 収集されたデータを格納するための Azure ストレージ アカウントの名前。
* StorageAccountKey: Azure ストレージ アカウント キーの名前。
* AdditionalDataLocationList: 次の構造体のリスト。

```
      {
        String Name,
        String Location,
        String SearchPattern,
        Bool   Recursive
      }
```

## <a name="extention-powershell-script-files"></a>拡張機能の PowerShell スクリプト ファイル
SetAzureServiceLogCollector.ps1

    [CmdletBinding(SupportsShouldProcess = $true)]

    param (
                  [Parameter(Mandatory=$true)]
                  [string]   $ServiceName,

                  [Parameter(Mandatory=$false)]
                  [string[]] $Roles ,

                  [Parameter(Mandatory=$false)]
                  [string[]] $Instances = '*',

                  [Parameter(Mandatory=$false)]
                  [string]   $Slot = 'Production',

                  [Parameter(Mandatory=$false)]
                  [string]   $Mode = 'Full',

                  [Parameter(Mandatory=$false)]
                  [string]   $StorageAccountName,

                  [Parameter(Mandatory=$false)]
                  [string]   $StorageAccountKey,

                  [Parameter(Mandatory=$false)]
                  [PSObject[]] $AdditionDataLocationList = $null
            )

    $publicConfig = New-Object PSObject

    if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be seperated by ,
    {
        $instanceText = $Instances[0]
        for ($i = 1;$i -lt $Instances.Count;$i++)
        {
              $instanceText = $instanceText+ "," + $Instances[$i]
          }
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
    }
    else  #For all instances if not specified.  The value should be a space or *
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
    }

    if ($Mode -ne $null )
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
    }
    else
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
    }

    #
    #we need to get the Sasuri from StorageAccount and containers
    #
    $context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    $ContainerName = "azurelogcollectordata"
    $existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
    if ($existingContainer -eq $null)
    {
        "Container ($ContainerName) doesn't exist. Creating it now.."
        New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
    }

    $ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
    $publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

    #
    #Add AdditionalData to collect data from additional folders
    #
    if ($AdditionDataLocationList -ne $null )
    {
      $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
    }

    #
    # Convert it to JSON format
    #
    $publicConfigJSON = $publicConfig | ConvertTo-Json
    "publicConfig is:  $publicConfigJSON"

    #we just provide a empty privateConfig object
    $privateconfig = "{
    }"

    if ($Roles -ne $null)
    {
          Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
    }
    else
    {
          Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
    }

    #
    #This is an optional step: generate a sasUri to the container so it can be shared with other people if nened
    #
    $SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
    $SasUri = $SasUri + "&restype=container&comp=list"
    Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"


SetAzureVMLogCollector.ps1

    [CmdletBinding(SupportsShouldProcess = $true)]

    param (
                  [Parameter(Mandatory=$true)]
                  [string]   $ServiceName,

                  [Parameter(Mandatory=$false)]
                  [string] $VMName ,

                  [Parameter(Mandatory=$false)]
                  [string]   $Mode = 'Full',

                  [Parameter(Mandatory=$false)]
                  [string]   $StorageAccountName,

                  [Parameter(Mandatory=$false)]
                  [string]   $StorageAccountKey,

                  [Parameter(Mandatory=$false)]
                  [PSObject[]] $AdditionDataLocationList = $null
            )

    $publicConfig = New-Object PSObject
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

    if ($Mode -ne $null )
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
    }
    else
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
    }

    #
    #we need to get the Sasuri from StorageAccount and containers
    #
    $context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    $ContainerName = "azurelogcollectordata"
    $existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
    if ($existingContainer -eq $null)
    {
        "Container ($ContainerName) doesn't exist. Creating it now.."
        New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
    }

    $ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
    $publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

    #
    #Add AdditionalData to collect data from additional folders
    #
    if ($AdditionDataLocationList -ne $null )
    {
      $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
    }

    #
    # Convert it to JSON format
    #
    $publicConfigJSON = $publicConfig | ConvertTo-Json

    Write-Output "PublicConfigurtion is: \r\n$publicConfigJSON"

    #
    #we just provide a empty privateConfig object
    #
    $privateconfig = "{
    }"

    if ($VMName -ne $null )
    {
          $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
          $VM.VM.OSVirtualHardDisk.OS

          if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
          {
                Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

                #
                #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
                #the presence of SubstatusList field.
                #
                $Completed = $false
                while ($Completed -ne $true)
                {
                        $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                        $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                        if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                        {
                            Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                              $Completed = $true
                        }
                        elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                        {
                              $Completed = $false
                              Write-Output "Waiting for operation to complete..."
                        }
                        else
                        {
                              $Completed = $true
                              Write-Output "Operation completed."

                        $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                              $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                      #
                            # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                              #
                              $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                              $ReadSasUri = New-AzureStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                            Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                              #
                              #This is an optional step:  Remove the extension after we are done
                              #
                              Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                        }
                        Start-Sleep -s 5
                }
          }
          else
          {
              Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
          }

    }
    else
    {
      Write-Output "VM name is not specified, the extension cannot be enabled"
    }

## <a name="next-steps"></a>次のステップ
ログは、すっきりと 1 か所から調査またはコピーすることができます。



<!--HONumber=Nov16_HO3-->


