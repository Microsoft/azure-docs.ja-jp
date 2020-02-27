---
title: Windows Virtual Desktop サービス プリンシパル ロールの割り当て - Azure
description: Windows Virtual Desktop で PowerShell を使用してサービス プリンシパルを作成する方法とロールを割り当てる方法を説明します。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: helohr
ms.openlocfilehash: 1e26af6c4bf86cfd412df7435379e610ffd69e85
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367419"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>チュートリアル:PowerShell を使用してサービス プリンシパルとロールの割り当てを作成する

サービス プリンシパルとは、特定の目的に使用するロールやアクセス許可を割り当てるために Azure Active Directory に作成できる ID です。 Windows Virtual Desktop では、次の用途でサービス プリンシパルを作成できます。

- Windows Virtual Desktop の特定の管理タスクを自動化する。
- Windows Virtual Desktop 用の Azure Resource Manager テンプレートを実行する際、MFA が求められるユーザーの代わりとなる資格情報として使用する。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Active Directory にサービス プリンシパルを作成します。
> * Windows Virtual Desktop にロールの割り当てを作成します。
> * サービス プリンシパルを使って Windows Virtual Desktop にサインインします。

## <a name="prerequisites"></a>前提条件

サービス プリンシパルとロールの割り当てを作成するには、次の 3 つのことを行う必要があります。

1. AzureAD モジュールをインストールします。 このモジュールをインストールするには、PowerShell を管理者として実行し、次のコマンドレットを実行します。

    ```powershell
    Install-Module AzureAD
    ```

2. [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します。

3. 同じ PowerShell セッションで、この記事のすべての手順を実行します。 ウィンドウを閉じてから開き直すことによって PowerShell セッションを中断すると、手順がうまくいかない場合があります。

## <a name="create-a-service-principal-in-azure-active-directory"></a>Azure Active Directory にサービス プリンシパルを作成する

これらの前提条件を PowerShell セッションで満たしたら、次の PowerShell コマンドレットを実行して、マルチテナント サービス プリンシパルを Azure に作成します。

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>PowerShell で資格情報を確認する

サービス プリンシパルのロールの割り当てを作成する前に、自分の資格情報を確認し、後で参照できるよう書き留めておいてください。 特にパスワードは、この PowerShell セッションを閉じた後は取得できなくなるので注意が必要です。

次に示したのは、書き留めておくべき 3 つの資格情報と、それらを取得するために実行する必要のあるコマンドレットです。

- Password (パスワード):

    ```powershell
    $svcPrincipalCreds.Value
    ```

- テナント ID:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- アプリケーション ID:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Windows Virtual Desktop プレビューにロールの割り当てを作成する

次に、サービス プリンシパルが Windows Virtual Desktop にサインインできるよう、ロールの割り当てを作成する必要があります。 必ずロールの割り当てを作成するアクセス許可があるアカウントでサインインしてください。

まず、まだ行っていない場合は、PowerShell セッション内で使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します。

次の PowerShell コマンドレットを実行して、Windows Virtual Desktop に接続し、テナントを表示します。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

ロールの割り当てを作成するテナントの名前を見つけたら、その名前を次のコマンドレットで使用します。

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>サービス プリンシパルでサインインする

サービス プリンシパルに使用するロールの割り当てを作成したら、そのサービス プリンシパルが Windows Virtual Desktop にサインインできることを次のコマンドレットを実行して確認します。

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

サインインできたら、そのサービス プリンシパルで、いくつかの Windows Virtual Desktop PowerShell コマンドレットを試し、すべてがうまく機能することを確認します。

## <a name="next-steps"></a>次のステップ

サービス プリンシパルを作成して Windows Virtual Desktop テナント内のロールに割り当てたら、それを使用してホスト プールを作成できます。 ホスト プールについて詳しく確認するために、Windows Virtual Desktop でホスト プールを作成するためのチュートリアルに進んでください。

 > [!div class="nextstepaction"]
 > [Windows Virtual Desktop のホスト プールのチュートリアル](./create-host-pools-azure-marketplace.md)
