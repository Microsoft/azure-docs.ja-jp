---
title: Azure リソースの種類別の移動操作のサポート
description: 新しいリソース グループまたはサブスクリプションに移動できる Azure リソースの種類を一覧表示します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 10/03/2019
ms.author: tomfitz
ms.openlocfilehash: 81096a4299ec190cb4299f1b8ea4f9c2533d37ad
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948040"
---
# <a name="move-operation-support-for-resources"></a>リソースの操作のサポートの移動
この記事では、Azure リソースの種類は、移動操作をサポートしているかどうかを示します。 また、リソースを移動するときに考慮すべき特別な条件に関する情報も提供します。

リソース プロバイダーの名前空間に移動します。
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Backup](#microsoftbackup)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | いいえ | いいえ |
> | domainservices/replicasets | いいえ | いいえ |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | いいえ | いいえ |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | はい | はい |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | servers | はい | はい |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | service | はい | はい |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | はい | はい |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | いいえ | いいえ |
> | appidentities | いいえ | いいえ |
> | gateways | いいえ | いいえ |

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | policyassignments | いいえ | いいえ |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | はい | はい |
> | automationaccounts/configurations | はい | はい |
> | automationaccounts/runbooks | はい | はい |

> [!IMPORTANT]
> Runbook は Automation アカウントと同じリソース グループに存在する必要があります。

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | はい | はい |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlserverregistrations | いいえ | いいえ |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | registrations | はい | はい |

## <a name="microsoftbackup"></a>Microsoft.Backup

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvault | いいえ | いいえ |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | はい | はい |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | いいえ | いいえ |
> | fileservers | いいえ | いいえ |
> | jobs | いいえ | いいえ |
> | workspaces | いいえ | いいえ |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | いいえ | いいえ |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | はい | はい |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | はい | はい |
> | watchers | いいえ | いいえ |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | いいえ | いいえ |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | はい | はい |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | はい | はい |

> [!IMPORTANT]
> 仮想ネットワークを使用して Azure Cache for Redis インスタンスが構成されている場合、インスタンスを別のサブスクリプションに移動させることはできません。 「[ネットワーク リソースの移動ガイダンス](./move-limitations/networking-move-limitations.md)」を参照してください。

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | いいえ | いいえ |
> | profiles | はい | はい |
> | profiles/endpoints | はい | はい |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | はい | はい |

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | はい | いいえ |
> | virtualmachines | はい | いいえ |

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | いいえ | いいえ |
> | reservedips | いいえ | いいえ |
> | virtualnetworks | いいえ | いいえ |

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | はい | いいえ |

> [!IMPORTANT]
> [Classic deployment move guidance (クラシック デプロイの移動のガイダンス)](./move-limitations/classic-model-move-limitations.md) に関する記事をご覧ください。 クラシック デプロイのリソースは、そのシナリオに固有の操作を使用して、サブスクリプション間で移動できます。

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | はい | はい |
> | diskencryptionsets | いいえ | いいえ |
> | disks | はい | はい |
> | galleries | いいえ | いいえ |
> | galleries/images | いいえ | いいえ |
> | galleries/images/versions | いいえ | いいえ |
> | hostgroups | いいえ | いいえ |
> | hostgroups/hosts | いいえ | いいえ |
> | images | はい | はい |
> | proximityplacementgroups | いいえ | いいえ |
> | restorepointcollections | いいえ | いいえ |
> | sharedvmimages | いいえ | いいえ |
> | sharedvmimages/versions | いいえ | いいえ |
> | スナップショット | はい | はい |
> | virtualmachines | はい | はい |
> | virtualmachines/extensions | はい | はい |
> | virtualmachinescalesets | はい | はい |

> [!IMPORTANT]
> [Virtual Machines move guidance (仮想マシンの移動のガイダンス)](./move-limitations/virtual-machines-move-limitations.md) に関する記事をご覧ください。

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | いいえ | いいえ |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | いいえ | いいえ |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | registries | はい | はい |
> | registries/buildtasks | はい | はい |
> | registries/replications | はい | はい |
> | registries/tasks | はい | はい |
> | registries/webhooks | はい | はい |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | いいえ | いいえ |
> | managedclusters | いいえ | いいえ |
> | openshiftmanagedclusters | いいえ | いいえ |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applications | はい | はい |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | connectors | はい | はい |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | hubs | はい | はい |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | いいえ | いいえ |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | いいえ | いいえ |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | いいえ | いいえ |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | catalogs | はい | はい |
> | datacatalogs | いいえ | いいえ |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | いいえ | いいえ |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | packages | いいえ | いいえ |
> | plans | いいえ | いいえ |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | はい | はい |
> | factories | はい | はい |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | いいえ | いいえ |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | いいえ | いいえ |
> | services/projects | いいえ | いいえ |
> | slots | いいえ | いいえ |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | servers | はい | はい |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | servers | はい | はい |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | いいえ | いいえ |
> | servers | はい | はい |
> | serversv2 | はい | はい |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | はい | はい |
> | rollouts | はい | はい |
> | servicetopologies | はい | はい |
> | servicetopologies/services | はい | はい |
> | servicetopologies/services/serviceunits | はい | はい |
> | steps | はい | はい |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | いいえ | いいえ |
> | elasticpools/iothubtenants | いいえ | いいえ |
> | iothubs | はい | はい |
> | provisioningservices | はい | はい |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | はい | はい |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | いいえ | いいえ |
> | labs | はい | いいえ |
> | labs/environments | はい | はい |
> | labs/servicerunners | はい | はい |
> | labs/virtualmachines | はい | いいえ |
> | schedules | はい | はい |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | はい | はい |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | はい | はい |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | はい | はい |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | はい | はい |
> | topics | はい | はい |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | はい | はい |
> | namespaces | はい | はい |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | いいえ | いいえ |
> | sapmonitors | はい | はい |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | はい | はい |

> [!IMPORTANT]
> HDInsight クラスターは、新しいサブスクリプションまたはリソース グループに移動できます。 ただし、HDInsight クラスターにリンクされているネットワーク リソース (仮想ネットワーク、NIC、ロード バランサーなど) をサブスクリプション間で移動することはできません。 また、クラスターの仮想マシンに接続されている NIC を新しいリソース グループに移動することはできません。
>
> HDInsight クラスターを新しいサブスクリプションに移動するときは、まず、他のリソース (ストレージ アカウントなど) を移動します。 その後、HDInsight クラスターを単独で移動します。

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | はい | はい |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | machines | いいえ | いいえ |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | はい | はい |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | はい | はい |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |
> | actiongroups | はい | はい |
> | activitylogalerts | いいえ | いいえ |
> | alertrules | はい | はい |
> | autoscalesettings | はい | はい |
> | components | はい | はい |
> | guestdiagnosticsettings | いいえ | いいえ |
> | metricalerts | いいえ | いいえ |
> | notificationgroups | いいえ | いいえ |
> | notificationrules | いいえ | いいえ |
> | scheduledqueryrules | はい | はい |
> | webtests | はい | はい |
> | Workbooks | はい | はい |

> [!IMPORTANT]
> 新しいサブスクリプションへの移動によって[サブスクリプション クォータ](../azure-subscription-service-limits.md#azure-monitor-limits)を超えないようにします。

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | はい | はい |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | はい | はい |
> | graph | はい | はい |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hsmpools | いいえ | いいえ |
> | vaults | はい | はい |

> [!IMPORTANT]
> ディスクの暗号化に使用されるキー コンテナーは、同じサブスクリプション内のリソース グループに移動したり、サブスクリプション間で移動したりすることはできません。

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | はい | はい |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | いいえ | いいえ |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | いいえ | いいえ |
> | integrationaccounts | はい | はい |
> | integrationserviceenvironments | いいえ | いいえ |
> | isolatedenvironments | いいえ | いいえ |
> | workflows | はい | はい |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | はい | はい |
> | webservices | はい | いいえ |
> | workspaces | はい | はい |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | はい | はい |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |
> | accounts/workspaces | いいえ | いいえ |
> | accounts/workspaces/projects | いいえ | いいえ |
> | teamaccounts | いいえ | いいえ |
> | teamaccounts/workspaces | いいえ | いいえ |
> | teamaccounts/workspaces/projects | いいえ | いいえ |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | いいえ | いいえ |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | いいえ | いいえ |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | いいえ | いいえ |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | はい | はい |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | いいえ | いいえ |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | はい | はい |
> | mediaservices/liveevents | はい | はい |
> | mediaservices/streamingendpoints | はい | はい |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | いいえ | いいえ |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | いいえ | いいえ |
> | migrateprojects | いいえ | いいえ |
> | projects | いいえ | いいえ |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | いいえ | いいえ |
> | netappaccounts/capacitypools | いいえ | いいえ |
> | netappaccounts/capacitypools/volumes | いいえ | いいえ |
> | netappaccounts/capacitypools/volumes/mounttargets | いいえ | いいえ |
> | netappaccounts/capacitypools/volumes/snapshots | いいえ | いいえ |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | いいえ | いいえ |
> | applicationgatewaywebapplicationfirewallpolicies | いいえ | いいえ |
> | applicationsecuritygroups | はい | はい |
> | azurefirewalls | はい | はい |
> | bastionhosts | いいえ | いいえ |
> | connections | はい | はい |
> | ddoscustompolicies | はい | はい |
> | ddosprotectionplans | いいえ | いいえ |
> | dnszones | はい | はい |
> | expressroutecircuits | いいえ | いいえ |
> | expressroutecrossconnections | いいえ | いいえ |
> | expressroutegateways | いいえ | いいえ |
> | expressrouteports | いいえ | いいえ |
> | frontdoors | いいえ | いいえ |
> | frontdoorwebapplicationfirewallpolicies | いいえ | いいえ |
> | loadbalancers | はい - Basic SKU<br>いいえ - Standard SKU | はい - Basic SKU<br>いいえ - Standard SKU |
> | localnetworkgateways | はい | はい |
> | natgateways | はい | はい |
> | networkintentpolicies | はい | はい |
> | networkinterfaces | はい | はい |
> | networkprofiles | いいえ | いいえ |
> | networksecuritygroups | はい | はい |
> | networkwatchers | はい | はい |
> | networkwatchers/connectionmonitors | はい | はい |
> | networkwatchers/lenses | はい | はい |
> | networkwatchers/pingmeshes | はい | はい |
> | p2svpngateways | いいえ | いいえ |
> | privatednszones | はい | はい |
> | privatednszones/virtualnetworklinks | はい | はい |
> | privateendpoints | いいえ | いいえ |
> | privatelinkservices | いいえ | いいえ |
> | publicipaddresses | はい - Basic SKU<br>いいえ - Standard SKU | はい - Basic SKU<br>いいえ - Standard SKU |
> | publicipprefixes | はい | はい |
> | routefilters | いいえ | いいえ |
> | routetables | はい | はい |
> | securegateways | はい | はい |
> | serviceendpointpolicies | はい | はい |
> | trafficmanagerprofiles | はい | はい |
> | virtualhubs | いいえ | いいえ |
> | virtualnetworkgateways | はい | はい |
> | virtualnetworks | はい | はい |
> | virtualnetworktaps | いいえ | いいえ |
> | virtualwans | いいえ | いいえ |
> | vpngateways (仮想 WAN) | いいえ | いいえ |
> | vpnsites (仮想 WAN) | いいえ | いいえ |
> | webapplicationfirewallpolicies | はい | はい |

> [!IMPORTANT]
> [ネットワークの移動ガイダンス](./move-limitations/networking-move-limitations.md)をご覧ください。

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | はい | はい |
> | namespaces/notificationhubs | はい | はい |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | はい | はい |

> [!IMPORTANT]
> 新しいサブスクリプションへの移動によって[サブスクリプション クォータ](../azure-subscription-service-limits.md#azure-monitor-limits)を超えないようにします。

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | はい | はい |
> | solutions | はい | はい |
> | views | はい | はい |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | peerings | いいえ | いいえ |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | はい | はい |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | いいえ | いいえ |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | はい | はい |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | はい | はい |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | いいえ | いいえ |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | vaults | はい | はい |

> [!IMPORTANT]
> [Recovery Services の移動のガイダンス](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)に関する記事をご覧ください。

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | はい | はい |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Query | はい | はい |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applications | はい | いいえ |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | flows | はい | はい |
> | jobcollections | はい | はい |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | はい | はい |

> [!IMPORTANT]
> 1 回の操作で異なるリージョンにあるいくつかの Search リソースを一度に移動することはできません。 代わりに、別の操作で移動します。

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotsecuritysolutions | はい | はい |
> | playbookconfigurations | いいえ | いいえ |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | いいえ | いいえ |
> | nodes | いいえ | いいえ |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | はい | はい |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applications | いいえ | いいえ |
> | clusters | はい | はい |
> | clusters/applications | いいえ | いいえ |
> | containergroups | いいえ | いいえ |
> | containergroupsets | いいえ | いいえ |
> | edgeclusters | いいえ | いいえ |
> | networks | いいえ | いいえ |
> | secretstores | いいえ | いいえ |
> | volumes | いいえ | いいえ |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applications | はい | はい |
> | containergroups | いいえ | いいえ |
> | gateways | はい | はい |
> | networks | はい | はい |
> | secrets | はい | はい |
> | volumes | はい | はい |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | はい | はい |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | siterecoveryvault | いいえ | いいえ |

> [!IMPORTANT]
> [Recovery Services の移動のガイダンス](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)に関する記事をご覧ください。

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | いいえ | いいえ |
> | appliances | いいえ | いいえ |
> | applicationdefinitions | いいえ | いいえ |
> | applications | いいえ | いいえ |
> | jitrequests | いいえ | いいえ |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | いいえ | いいえ |
> | managedinstances | いいえ | いいえ |
> | managedinstances/databases | いいえ | いいえ |
> | servers | はい | はい |
> | servers/databases | はい | はい |
> | servers/elasticpools | はい | はい |
> | virtualclusters | はい | はい |

> [!IMPORTANT]
> データベースとサーバーは同じリソース グループ内に存在する必要があります。 SQL Server を移動すると、そのデータベースもすべて移動されます。 この動作は、Azure SQL Database と Azure SQL Data Warehouse データベースに適用されます。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | はい | はい |
> | sqlvirtualmachines | はい | はい |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | いいえ | いいえ |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | はい | はい |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | caches | いいえ | いいえ |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | はい | はい |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | いいえ | いいえ |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | いいえ | いいえ |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | managers | いいえ | いいえ |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | はい | はい |

> [!IMPORTANT]
> 実行中状態の Stream Analytics ジョブは移動できません。

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | 環境 | いいえ | いいえ |
> | environments/eventsources | いいえ | いいえ |
> | instances | いいえ | いいえ |
> | instances/environments | いいえ | いいえ |
> | instances/environments/eventsources | いいえ | いいえ |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | いいえ | いいえ |
> | resources | いいえ | いいえ |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | サブスクリプション |
> | ------------- | ----------- | ---------- |
> | 環境 | はい | はい |
> | environments/eventsources | はい | はい |
> | environments/referencedatasets | はい | はい |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | いいえ | いいえ |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | いいえ | いいえ |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | はい | はい |
> | account / extension | はい | はい |
> | account/project | はい | はい |

> [!IMPORTANT]
> Azure DevOps のサブスクリプションを変更するには、[change the Azure subscription used for billing (課金に使用される Azure サブスクリプションの変更)](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json) に関する記事をご覧ください。

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | はい | はい |
> | dedicatedcloudservices | はい | はい |
> | virtualmachines | はい | はい |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | いいえ | はい |
> | connectiongateways | はい | はい |
> | connections | はい | はい |
> | customapis | はい | はい |
> | hostingenvironments | いいえ | いいえ |
> | serverfarms | はい | はい |
> | sites | はい | はい |
> | sites/premieraddons | はい | はい |
> | sites/slots | はい | はい |

> [!IMPORTANT]
> [App Service move guidance (App Service の移動のガイダンス)](./move-limitations/app-service-move-limitations.md) に関する記事をご覧ください。

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | いいえ | いいえ |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | リソースの種類 | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | いいえ | いいえ |
> | hostpools | いいえ | いいえ |
> | workspaces | いいえ | いいえ |

## <a name="third-party-services"></a>サード パーティーのサービス

サード パーティのサービスは現在、移動操作をサポートしていません。

## <a name="next-steps"></a>次の手順
リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。

コンマ区切りの値のファイルと同じデータを取得するには、[move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv) をダウンロードします。
