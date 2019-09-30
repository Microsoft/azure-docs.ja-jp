---
title: VLAN/サブネットの作成
description: CloudSimple による Azure VMware ソリューション - プライベート クラウドの VLAN/サブネットを作成して管理し、ファイアウォール規則を適用する方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0f8e605db3d647b5907c8c6741e4e2200bf14c4f
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772361"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>プライベート クラウドの VLAN/サブネットを作成して管理する

[ネットワーク] ページで [VLANs/Subnets]\(VLAN/サブネット\) タブを開き、プライベート クラウドの VLAN/サブネットを作成し、管理します。 VLAN/サブネットを作成したら、ファイアウォール規則を適用できます。

## <a name="create-a-vlansubnet"></a>VLAN/サブネットを作成する

1. [CloudSimple ポータルにアクセスし](access-cloudsimple-portal.md)、サイド メニューの **[ネットワーク]** を選択します。
2. **[VLANs/Subnets]\(VLAN/サブネット\)** を選択します。
3. **[Create VLAN/Subnet]\(VLAN/サブネットの作成\)** をクリックします。

    ![[VLANs/Subnets]\(VLAN/サブネット\) ページ](media/vlan-subnet-page.png)

4. 新しい VLAN/サブネットの [Private Cloud]\(プライベート クラウド\) を選択します。
5. VLAN ID を入力します。
6. サブネット名を入力します。
7. VLAN (サブネット) でルーティングを有効にするには、サブネットの CIDR 範囲を指定します。 CIDR 範囲がオンプレミスのサブネット、Azure サブネット、ゲートウェイ サブネットのいずれとも重複しないことを確認します。
8. **[送信]** をクリックします。

    ![VLAN/サブネットの作成](media/create-new-vlan-subnet-details.png)

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>VLAN 情報を使用し、vSphere で分散ポート グループを設定する

vSphere で分散ポート グループを作成するには、<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">vSphere Networking Guide</a> にある VMware トピックの「Add a distributed port group」 (分散ポート グループの追加) の指示に従います。 分散ポート グループを設定するとき、CloudSimple 構成から VLAN 情報を指定します。

![分散ポート グループ](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>ファイアウォール テーブルの選択

ファイアウォールのテーブルと関連ルールは、 **[ネットワーク] の [Firewall Tables]\(ファイアウォール テーブル\)** ページで定義されます。 プライベート クラウドの VLAN/サブネットに適用するファイアウォール テーブルを選択するには、 **[VLANs/Subnets]\(VLAN/サブネット\)** ページで **[Firewall table attachment]\(ファイアウォール テーブル接続\)** をクリックします。 ファイアウォール テーブルの設定方法とルールの定義方法については、「[ファイアウォール テーブル](firewall.md)」を参照してください。

![ファイアウォール テーブルのリンク](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> 1 つのサブネットは、1 つのファイアウォール テーブルに関連付けることができます。 1 つのファイアウォール テーブルは、複数のサブネットに関連付けることができます。

## <a name="edit-a-vlansubnet"></a>VLAN/サブネットの編集

VLAN/サブネットの設定を編集するには、 **[VLANs/Subnets]\(VLAN/サブネット\)** ページでそれを選択し、 **[編集]** アイコンをクリックします。 変更を加え、 **[送信]** をクリックします。

## <a name="delete-a-vlansubnet"></a>VLAN/サブネットの削除

VLAN/サブネットを削除するには、 **[VLANs/Subnets]\(VLAN/サブネット\)** ページでそれを選択し、 **[削除]** アイコンをクリックします。 **[削除]** をクリックして確定します。
