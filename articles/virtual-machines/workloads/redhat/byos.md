---
title: Red Hat Enterprise Linux のサブスクリプション持ち込み Azure イメージ | Microsoft Docs
description: Azure での Red Hat Enterprise Linux のサブスクリプション持ち込みイメージについて学習します
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: a54e1c9cbc9a80139d71a89f86ac51ecf5ce9902
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190499"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Azure での Red Hat Enterprise Linux のサブスクリプション持ち込み Gold Image

Red Hat Enterprise Linux (RHEL) イメージは、従量課金制 (PAYG) またはサブスクリプション持ち込み (Red Hat Gold Image) モデルを使用して Azure で利用できます。 このドキュメントでは、Azure の Red Hat Gold Image イメージの概要について説明します。

## <a name="important-points-to-consider"></a>考慮すべき重要な点

- このプログラムで提供される Red Hat Gold Image は、Azure ギャラリー/Marketplace の RHEL PAYG イメージとよく似た実稼働対応の RHEL イメージです。

- イメージは、[Azure の Red Hat Enterprise Linux イメージ](./redhat-images.md)に関するページで説明されている現在のポリシーに従います。

- Standard サポート ポリシーは、これらのイメージから作成された VM に適用されます。

- Red Hat Gold Image からプロビジョニングされた VM は、RHEL PAYG イメージに関連付けられている RHEL 料金がかかりません

- イメージには権利がないため、Red Hat から直接更新プログラムを取得するために、サブスクリプション マネージャーを使用して VM を登録およびサブスクライブする必要があります。

- 現時点では、Linux イメージの BYOS と PAYG の課金モデルを動的に切り替えることはできません。 課金モデルを切り替えるには、それぞれのイメージから VM を再デプロイする必要があります。

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Red Hat Gold Image にアクセスするための要件と条件

1. [Red Hat Cloud Access プログラム](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)についてよく理解し、[Red Hat Subscription Manager](https://access.redhat.com/management/cloud) で Cloud Access に対して Red Hat サブスクリプションを有効にします。 Cloud Access に登録する予定の Azure サブスクリプションを手元に用意しておく必要があります。

1. 適切な資格要件を満たす Red Hat サブスクリプションを Cloud Access に対して有効にしている場合は、Azure サブスクリプションが Gold Image アクセスに対して自動的に有効になります。

### <a name="expected-time-for-image-access"></a>イメージへのアクセスに予想される時間

Cloud Access を有効にする手順が完了すると、Red Hat によって Red Hat Gold Image の資格が検証されます。 検証が成功した場合、3 時間以内に Gold Image にアクセスできるようになります。

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Azure portal から Red Hat Gold Image を使用する

1. Azure サブスクリプションが Red Hat Gold Image にアクセスできるようになると、[Azure portal](https://portal.azure.com) でそれを検索できます。それには、 **[リソースの作成]** に移動し、 **[すべて表示]** に移動します。

1. ページの一番上に、プライベート プランがあることが表示されます。

    ![Marketplace プライベート プラン](./media/rhel-byos-privateoffers.png)

1. 紫色のリンクをクリックするか、ページの一番下までスクロールしてプライベート プランを表示することができます。

1. UI でのプロビジョニングの残り部分は、他の既存の Red Hat イメージと変わりません。 RHEL バージョンを選択し、画面の指示に従って VM をプロビジョニングします。 このプロセスでは、最後の手順でイメージの使用条件に同意することもできます。

>[!NOTE]
>ここまでの手順では、プログラムによるデプロイ用に Red Hat Gold Image イメージを有効化できません。後述の「関連情報」に説明されている追加手順が必要です。

このドキュメントの残りの部分では、CLI の方法を使用して、イメージのプロビジョニングとその使用条件への同意を行います。 UI と CLI は、最終的な結果 (プロビジョニングされた RHEL Gold Image VM) に関する限り、完全に同等です。

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Azure CLI から Red Hat Gold Image を使用する
次の一連の手順では、Azure CLI を使用した RHEL VM の初期デプロイ プロセスについて説明します。 この手順では、[Azure CLI がインストール済み](https://docs.microsoft.com/cli/azure/install-azure-cli)であることを前提としています。

>[!IMPORTANT]
>次のすべてのコマンドでは、発行元、オファー、プラン、およびイメージの参照ですべて小文字を使用してください。

1. 目的のサブスクリプションを使用していることを確認します。
    ```azurecli
    az account show -o=json
    ```

1. Red Hat Gold Image VM のリソース グループを作成します。
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. イメージの使用条件に同意します。
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >これらの使用条件は、"*Azure サブスクリプションごと、イメージ SKU ごとに 1 回*" 同意する必要があります。

1. (省略可能) 次のコマンドを使用して、VM のデプロイを検証します。
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. `--validate` 引数を指定せずに上記と同じコマンドを実行して、VM をプロビジョニングします。
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. VM に SSH で接続し、権利のないイメージがあることを確認します。 これを行うには `sudo yum repolist` を実行します (RHEL 8 の場合は、`sudo dnf repolist` を使用します)。 出力で、サブスクリプション マネージャーを使用して、VM を Red Hat に登録するよう求められます。

>[!NOTE]
>RHEL 8 では、`dnf` と `yum` を入れ替えることができます。詳細については、[RHEL 8 管理者ガイド](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings)を参照してください。


## <a name="use-the-red-hat-gold-images-from-powershell"></a>PowerShell から Red Hat Gold Image を使用する
次にスクリプトの例を示します。 リソース グループ、場所、VM 名、ログイン情報、その他の変数を、選択した構成で置き換える必要があります。 発行元とプランの情報は、小文字にする必要があります。

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Red Hat Enterprise Linux のサブスクリプションの持ち込み Gold イメージの暗号化

Red Hat Enterprise Linux のサブスクリプションの持ち込み Gold イメージは [Azure Disk Encryption](../../linux/disk-encryption-overview.md) を利用したセキュリティで保護できます。 ただし、暗号化を有効にする前にサブスクリプションを登録する**必要があります**。  RHEL BYOS Gold イメージの登録の詳細は Red Hat サイトにあります。 「[How to register and subscribe a system to the Red Hat Customer Portal using Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273)」(Red Hat Subscription-Manager を使用し、Red Hat Customer Portal にシステムを登録し、サブスクライブする方法) を参照してください。アクティブな Red Hat サブスクリプションをお持ちの場合は、「[Creating Red Hat Customer Portal Activation Keys](https://access.redhat.com/articles/1378093)」(Red Hat Customer Portal アクティベーション キーを作成する) も参照してください。

[Red Hat カスタム イメージ](../../linux/redhat-create-upload-vhd.md)では、Azure Disk Encryption はサポートされていません。 追加の ADE の要件と前提条件については、[Linux VM 向けの Azure Disk Encryption](../../linux/disk-encryption-overview.md#additional-vm-requirements) に関するページに記載されています。

Azure Disk Encryption の適用手順は「[Linux VM での Azure Disk Encryption シナリオ](../../linux/disk-encryption-linux.md)」や関連記事をご覧ください。

## <a name="additional-information"></a>関連情報

- このプランで有効になっていないサブスクリプションで VM をプロビジョニングしようとすると、次のエラーが表示されます。

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    この場合、Microsoft か Red Hat にお問い合わせいただき、サブスクリプションを有効にしてください。

- RHEL BYOS イメージのスナップショットを変更し、そのカスタム イメージを [Shared Image Gallery](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries) に公開する場合、スナップショットの元のソースに一致するプラン情報を指定する必要があります。 たとえば、コマンドは次のようになります。

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```
    上の最後の行にある plan パラメーターに注意してください。

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) はカスタム イメージではサポートされていません。

- RHEL BYOS イメージから VM をプロビジョニングするために Automation を使用している場合、上に示したような plan パラメーターを指定する必要があります。 たとえば、Terraform を使用している場合は、[plan ブロック](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)にプラン情報を指定します。

## <a name="next-steps"></a>次のステップ
- Cloud Access のステップバイステップ ガイドとプログラムの詳細については、[Red Hat Cloud Access のドキュメント](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)を参照してください。
- [Azure Red Hat Update Infrastructure](./redhat-rhui.md) の詳細を参照してください。
- Azure でのすべての Red Hat イメージの詳細については、[ドキュメントのページ](./redhat-images.md)を参照してください。
- すべてのバージョンの RHEL に対する Red Hat のサポート ポリシーに関する情報は、「[Red Hat Enterprise Linux Life Cycle \(Red Hat Enterprise Linux のライフ サイクル\)](https://access.redhat.com/support/policy/updates/errata)」ページに記載されています。
- RHEL Gold Image に関するその他のドキュメントについては、[Red Hat のドキュメント](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)を参照してください。
