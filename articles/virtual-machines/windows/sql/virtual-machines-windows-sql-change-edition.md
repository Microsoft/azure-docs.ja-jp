---
title: SQL Server エディションのインプレース変更
description: Azure で SQL Server 仮想マシンのエディションを変更する方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605448"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Azure VM での SQL Server エディションのインプレース変更

この記事では、Azure の Windows 仮想マシン上で SQL Server のエディションを変更する方法について説明します。 

SQL Server のエディションは、プロダクト キーによって決定され、インストール メディアを使用したインストール プロセス中に指定されます。 エディションにより、SQL Server 製品内で使用できる[機能](/sql/sql-server/editions-and-components-of-sql-server-2017)が指定されます。 インストール メディアで SQL Server のエディションを変更し、ダウングレードしてコストを削減したり、アップグレードして機能を増やしたりすることができます。

SQL Server のエディションが内部で SQL Server VM に変更された後は、課金のために Azure portal で SQL Server のエディション プロパティを更新する必要があります。 

## <a name="prerequisites"></a>前提条件

SQL Server のエディションのインプレース変更を行うには、以下のものが必要です。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [SQL VM リソース プロバイダー](virtual-machines-windows-sql-register-with-resource-provider.md)に登録された [Windows 上の SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。
- SQL Server の**目的のエディション**が収められたセットアップ メディア。 [ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)をお持ちのお客様は、[ボリューム ライセンス サービス センター](https://www.microsoft.com/Licensing/servicecenter/default.aspx)からインストール メディアを入手できます。 ソフトウェア アシュアランスをお持ちでないお客様は、目的のエディション (通常、`C:\SQLServerFull` にある) を含む Azure Marketplace の SQL Server VM イメージから、セットアップ メディアを使用できます。 


## <a name="upgrade-an-edition"></a>エディションをアップグレードする

> [!WARNING]
> SQL Server のエディションをアップグレードすると、SQL Server のサービスと、Analysis Services や R Services などのすべての関連するサービスが、再起動されます。 

SQL Server のエディションをアップグレードするには、SQL Server の目的のエディションの SQL Server セットアップ メディアを入手した後、次のようにします。

1. SQL Server のインストール メディアから Setup.exe を開きます。 
1. **[メンテナンス]** に移動し、 **[エディションのアップグレード]** オプションを選択します。 

   ![SQL Server のエディションをアップグレードするための選択](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. **[次へ]** を何回か選択して **[エディションのアップグレードの準備完了]** ページまで進み、 **[アップグレード]** を選択します。 変更が有効になるまで数分間、セットアップ ウィンドウの応答が停止することがあります。 **[完了]** ページで、エディションのアップグレードが完了したことが確認されます。 

SQL Server のエディションをアップグレードした後、Azure portal で SQL Server 仮想マシンのエディション プロパティを変更します。 これにより、この VM に関連付けられているメタデータと課金が更新されます。

## <a name="downgrade-an-edition"></a>エディションをダウングレードする

SQL Server のエディションをダウングレードするには、SQL Server を完全にアンインストールし、目的のエディションのセットアップ メディアでもう一度再インストールする必要があります。 

> [!WARNING]
> SQL Server をアンインストールすると、追加のダウンタイムが発生する可能性があります。 

次の手順に従って、SQL Server のエディションをダウングレードできます。

1. システム データベースを含むすべてのデータベースをバックアップします。 
1. システム データベース (master、model、msdb) を新しい場所に移動します。 
1. SQL Server および関連付けられているすべてのサービスを、完全にアンインストールします。 
1. 仮想マシンを再起動します。 
1. SQL Server の目的のエディションが収められたメディアを使って、SQL Server をインストールします。
1. 最新のサービス パックと累積的な更新プログラムをインストールします。  
1. インストールの間に作成された新しいシステム データベースを、前に別の場所に移動したシステム データベースに置き換えます。 

SQL Server のエディションをダウングレードした後、Azure portal で SQL Server 仮想マシンのエディション プロパティを変更します。 これにより、この VM に関連付けられているメタデータと課金が更新されます。

## <a name="change-edition-in-portal"></a>ポータルでエディションを変更する 

インストール メディアを使用して SQL Server のエディションを変更し、[SQL VM リソース プロバイダー](virtual-machines-windows-sql-register-with-resource-provider.md)に SQL Server VM を登録した後、Azure portal を使用して、課金のために SQL Server VM のエディション プロパティを変更できます。 これを行うには、次のステップに従います。 

1. [Azure portal](https://portal.azure.com) にサインインします。 
1. SQL Server の仮想マシン リソースに移動します。 
1. **[設定]** の **[構成]** を選択します。 次に、 **[エディション]** のドロップダウン リストから、必要な SQL Server のエディションを選択します。 

   ![エディションのメタデータを変更する](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. SQL Server のエディションを最初に変更する必要があること、およびエディション プロパティが SQL Server のエディションと一致する必要があることを示す警告を確認します。 
1. **[適用]** を選択して、エディション メタデータの変更を適用します。 


## <a name="remarks"></a>解説

- SQL Server VM のエディション プロパティは、すべての SQL Server 仮想マシン用にインストールされている SQL Server インスタンスのエディションと一致する必要があります。これには、従量課金制とライセンス持ち込みの両方のライセンスの種類が含まれます。
- SQL Server VM リソースを削除した場合は、イメージのハード コーディングされたエディション設定に戻ります。
- エディションを変更する機能は、SQL VM リソース プロバイダーの機能です。 Azure portal を介して Azure Marketplace イメージをデプロイすると、SQL Server VM がリソース プロバイダーに自動的に登録されます。 ただし、SQL Server を自分でインストールするお客様は、手動で [SQL Server VM を登録](virtual-machines-windows-sql-register-with-resource-provider.md)する必要があります。
- SQL Server VM を可用性セットに追加するには、VM を再作成する必要があります。 可用性セットに追加されたすべての VM は既定のエディションに戻るので、エディションをもう一度変更する必要があります。

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上の SQL Server の価格ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)


