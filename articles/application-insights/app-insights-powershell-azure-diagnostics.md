---
title: "Azure での PowerShell を使用した Application Insights の設定 | Microsoft Docs"
description: "Application Insights にパイプするための Azure 診断の構成を自動化します。"
services: application-insights
documentationcenter: .net
author: sbtron
manager: carmonm
ms.assetid: 4ac803a8-f424-4c0c-b18f-4b9c189a64a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: df5fe5808a0fb6c2d580f35ed8430b52f34a4110
ms.contentlocale: ja-jp
ms.lasthandoff: 11/16/2016

---
# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>Azure Web アプリのための PowerShell を使用した Application Insights の設定
[Microsoft Azure](https://azure.com) は、[Azure Application Insights](app-insights-overview.md) に [Azure 診断を送信するように構成](app-insights-azure-diagnostics.md)できます。 診断は、Azure Cloud Services および Azure VM に関するものです。 このデータは、Application Insights SDK を使用するアプリケーション内から送信されるテレメトリを補完します。 Azure での新規リソース作成プロセスを自動化する一部として、PowerShell を使用して診断を構成できます。

## <a name="azure-template"></a>Azure テンプレート
Web アプリが Azure に存在するとき、Azure Resource Manager テンプレートを使用してリソースを作成する場合、resources ノードに次のコードを追加することで Application Insights を構成できます。

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` - Application Insights リソースの名前。
* `myWebAppName` - Web アプリの ID。

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Cloud Service のデプロイの一環としての診断拡張機能の有効化
`New-AzureDeployment` コマンドレットの `ExtensionConfiguration` パラメーターは、診断構成の配列を受け取ります。 この情報は、 `New-AzureServiceDiagnosticsExtensionConfig` コマンドレットを使用して作成できます。 次に例を示します。

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>既存の Cloud Service での診断拡張機能の有効化
既存のサービスでは、 `Set-AzureServiceDiagnosticsExtension`を使用します。

```ps

    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>診断拡張機能の現在の構成の取得
```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>診断拡張機能の削除
```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Role パラメーターを指定しないで `Set-AzureServiceDiagnosticsExtension` または `New-AzureServiceDiagnosticsExtensionConfig` を使用して診断拡張機能を有効にした場合、Role パラメーターを指定しないで `Remove-AzureServiceDiagnosticsExtension` を使用して拡張機能を削除できます。 拡張機能を有効にするときに Role パラメーターを使用した場合は、拡張機能を削除するときにもこのパラメーターを使用する必要があります。

個々のロールから診断拡張機能を削除するには、次のコマンドを実行します。

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>関連項目
* [Application Insights で Azure Cloud Services アプリを監視する](app-insights-cloudservices.md)
* [Azure 診断を Application Insights に送信する](app-insights-azure-diagnostics.md)
* [アラートの構成を自動化する](app-insights-powershell-alerts.md)


