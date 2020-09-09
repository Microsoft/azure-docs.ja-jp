---
title: 管理テナントでの委任変更を監視する
description: 顧客テナントから管理テナントへの委任アクティビティを監視する方法について説明します。
ms.date: 08/18/2020
ms.topic: how-to
ms.openlocfilehash: 4d9d8b18634f94c355ea7fc0b5c125d631ec419c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589743"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>管理テナントでの委任変更を監視する

お客様はサービス プロバイダーとして、[Azure Lighthouse](../overview.md) を通して顧客のサブスクリプションまたはリソース グループがご利用のテナントに委任された日時、または以前に委任されているリソースが削除された日時を確認することが必要な場合があります。

管理テナントでは、[Azure アクティビ ティログ](../../azure-monitor/platform/platform-logs-overview.md)によって委任アクティビティがテナント レベルで追跡されます。 このログに記録されるアクティビティには、すべての顧客テナントに対する委任の追加または削除が含まれます。

このトピックでは、お客様のテナント (お客様のすべての顧客を対象とする) への委任アクティビティを監視するために必要なアクセス許可と、それを行う上でのベスト プラクティスについて説明します。 また、このデータに対してクエリを実行してレポートするための方法の 1 つを示すサンプル スクリプトも示します。

> [!IMPORTANT]
> これらの手順はすべて、顧客のテナントではなく、お客様の管理テナントで実行する必要があります。

## <a name="enable-access-to-tenant-level-data"></a>テナントレベルのデータへのアクセスを有効にする

テナントレベルのアクティビティ ログ データにアクセスするには、[監視閲覧者](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure 組み込みロールをルート スコープ (/) でアカウントに割り当てる必要があります。 この割り当てについては、全体管理者ロールと追加の昇格されたアクセス権を持つユーザーが実行する必要があります。

### <a name="elevate-access-for-a-global-administrator-account"></a>全体管理者アカウントのアクセス権を昇格する

ルート スコープ (/) でロールを割り当てるには、全体管理者ロールと共に昇格されたアクセス権を持っている必要があります。 この昇格されたアクセス権は、ロールの割り当てを行う必要がある場合にのみ追加され、完了したら削除される必要があります。

昇格を追加および削除する方法の詳細については、「[Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる](../../role-based-access-control/elevate-access-global-admin.md)」を参照してください。

自分のアクセス権を昇格させると、Azure でルート スコープでのユーザー アクセス管理者ロールがご利用のアカウントに付与されます。 このロールが割り当てられると、すべてのリソースを表示することおよびディレクトリ内の任意のサブスクリプションまたは管理グループでアクセス権を割り当てることに加えて、ルート スコープでロールを割り当てることができます。

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>テナントレベルのデータにアクセスするための新しいサービス プリンシパル アカウントを作成する

自分のアクセス権を昇格させたら、テナントレベルのアクティビティ ログ データに対してクエリを実行できるように、適切なアクセス許可をアカウントに割り当てることができます。 このアカウントには、ご利用の管理テナントのルート スコープで[監視閲覧者](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure 組み込みロールを割り当てる必要があります。

> [!IMPORTANT]
> ルート スコープでのロール割り当てを許可すると、テナント内のすべてのリソースに同じアクセス許可が適用されることになります。

これは広範なアクセス レベルであるため、個々のユーザーまたはグループに対してではなく、サービス プリンシパル アカウントにこのロールを割り当てることをお勧めします。

 さらに、推奨するベスト プラクティスを次に示します。

- この機能に対してのみ使用する[新しいサービス プリンシパル アカウントを作成](../../active-directory/develop/howto-create-service-principal-portal.md)し、他の自動化に使用する既存のサービス プリンシパルにこのロールを割り当てることはしません。
- このサービス プリンシパルに、委任された顧客リソースへのアクセス権がないことを確認してください。
- [証明書を使用して認証](../../active-directory/develop/howto-create-service-principal-portal.md#upload-a-certificate-or-create-a-secret-for-signing-in)を行い、[それを Azure Key Vault に安全に格納します](../../key-vault/general/best-practices.md)。
- サービス プリンシパルの代理として機能するアクセス権を持つユーザーを制限します。

次のいずれかの方法を使用して、ルート スコープの割り当てを行います。

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>全体管理者アカウントの昇格されたアクセス権を削除する

サービス プリンシパル アカウントを作成し、監視閲覧者ロールをルート スコープで割り当てたら、必ず全体管理者アカウントの[昇格されたアクセス権を削除](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access)します。このレベルのアクセス権はもはや不要だからです。

## <a name="query-the-activity-log"></a>アクティビティ ログに対してクエリを実行する

ご利用の管理テナントのルート スコープに対して監視閲覧者アクセス権を持つ新しいサービス プリンシパル アカウントを作成したら、それを使用して、ご利用のテナント内の委任アクティビティのクエリとレポートを行うことができます。

[この Azure PowerShell スクリプト](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes)を使用すると、過去 1 日間のアクティビティを照会し、追加または削除された委任 (または、成功しなかった試行) についてレポートすることができます。 [Tenant Activity Log](/rest/api/monitor/TenantActivityLogs/List) データに対してクエリが実行され、追加または削除された委任についてレポートするために次の値が構築されます。

- **DelegatedResourceId**: 委任されたサブスクリプションまたはリソース グループの ID
- **CustomerTenantId**: 顧客テナント ID
- **CustomerSubscriptionId**: 委任されたサブスクリプション ID、または委任されたリソース グループを含むサブスクリプション ID
- **CustomerDelegationStatus**: 委任されたリソースの状態の変更 (成功または失敗)
- **EventTimeStamp**: 委任の変更がログに記録された日付と時刻

このデータに対してクエリを実行する場合は、次の点に注意してください。

- 1 つのデプロイで複数のリソース グループが委任されている場合は、リソース グループごとに個別のエントリが返されます。
- 以前の委任に加えられた変更 (アクセス許可の構造の更新など) は、追加された委任としてログに記録されます。
- 前述のように、このテナントレベルのデータにアクセスするために、アカウントには、ルート スコープ (/) で監視閲覧者 Azure 組み込みロールが割り当てられている必要があります。
- このデータは、独自のワークフローおよびレポートで使用できます。 たとえば、[HTTP データ コレクター API (パブリック プレビュー)](../../azure-monitor/platform/data-collector-api.md) を使用すると、REST API クライアントから Azure Monitor にデータを記録し、次に[アクション グループ](../../azure-monitor/platform/action-groups.md)を使用して通知またはアラートを作成できます。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

> [!TIP]
> このトピックではサービス プロバイダーと顧客の場合について説明していますが、[複数のテナントを管理するエンタープライズ](../concepts/enterprise.md)も同じプロセスを使用できます。

## <a name="next-steps"></a>次のステップ

- [Azure Lighthouse](../concepts/azure-delegated-resource-management.md) への顧客のオンボード方法について説明します。
- [Azure Monitor](../../azure-monitor/index.yml) と [Azure アクティビティ ログ](../../azure-monitor/platform/platform-logs-overview.md)について説明します。
