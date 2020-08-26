---
title: クラシックから Azure Resource Manager への移行に関してよく寄せられる質問
description: クラシックから Azure Resource Manager への移行に関してよく寄せられる質問
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 3d8c5d408ef2b958ca7d3454f23ce2c52fb1d1be
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88513046"
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>クラシックから Azure Resource Manager への移行に関してよく寄せられる質問

> [!IMPORTANT]
> 現在、IaaS VM の約 90% で [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) が使用されています。 2020 年 2 月 28 日の時点で、クラシック VM は非推奨とされており、2023 年 3 月 1 日に完全に廃止されます。 この非推奨の[詳細]( https://aka.ms/classicvmretirement)および[それが与える影響](./classic-vm-deprecation.md#how-does-this-affect-me)について確認してください。

## <a name="what-is-azure-service-manager-and-what-does-it-mean-by-classic"></a>Azure Service Manager とはどのようなもので、クラシックとはどのような意味ですか?

IaaS VM (クラシック) の "クラシック" という単語は、Azure Service Manager (ASM) によって管理される VM を指します。 Azure Service Manager (ASM) は、VM の作成、管理、削除、およびその他のコントロール プレーン操作を行う役割を担う、Azure の古いコントロール プレーンです。 

## <a name="what-is-azure-resource-manager"></a>Azure Resource Manager とは

[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) は、VM の作成、管理、削除、およびその他のコントロール プレーン操作を行う役割を担う、Azure の最新のコントロール プレーンです。 

## <a name="what-is-the-time-required-for-migration"></a>移行にはどれくらいの時間が必要ですか?

移行の計画と実行は、アーキテクチャの複雑さに大きく左右され、数か月かかることがあります。  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>IaaS VM (クラシック) での新しい顧客の定義は何ですか?

2020 年 2 月 (非推奨となった前の月) の時点でお使いのサブスクリプションに IaaS VM (クラシック) が含まれていなったお客様は、新しいお客様とみなされます。 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>IaaS 仮想マシン (クラシック) での既存の顧客の定義は何ですか?

IaaS VM (クラシック) がアクティブであるか、またはこれを停止したが、2020 年 2 月のサブスクリプションでこれを割り当てたお客様は、既存の顧客と見なされます。 これらのお客様のみが 2023 年 3 月 1 日までに、Azure Service Manager から Azure Resource Manager に VM を移行することができます。 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>"NewClassicVMCreationNotAllowedForSubscription" というエラーが発生するのはなぜですか?

提供終了プロセスの一環として、新しいお客様は IaaS VM (クラシック) を利用できなくなりました。 お客様は新しい顧客として特定されたため、お客様の操作は承認されませんでした。 [ARM を使った Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) を使用することを強くお勧めします。 ARM を使用して Azure VM を使用できない場合は、サブスクリプションを許可リストに追加するよう、サポートにお問い合わせください。

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>この移行計画は Azure 仮想マシン上で実行されている既存のサービスやアプリケーションに影響しますか? 

2023 年 3 月 1 日になるまで、IaaS VM (クラシック) には影響しません。 IaaS VM (クラシック) は、一般提供で完全にサポートされるサービスです。 引き続きこうしたリソースを利用して、Microsoft Azure のフットプリントを拡大できます。 2023 年 3 月 1 日に、これらの VM は完全に廃止され、アクティブまたは割り当て済みのすべての VM は停止され、割り当て解除されます。 Cloud Services (クラシック) やストレージ アカウント (クラシック) などの他のクラシック リソースに影響することはありません。   

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>近日中に移行する予定がない場合、VM はどうなりますか? 

2023 年 3 月 1 日に、IaaS VM (クラシック) は完全に廃止され、アクティブまたは割り当て済みのすべての VM は停止され、割り当て解除されます。 ビジネスへの影響を回避するため、移行の計画を今すぐ開始し、2023 年 3 月 1 日より前に移行を完了することを強くお勧めします。 既存のクラシック API、Cloud Services、およびリソース モデルが廃止されることはありません。 Resource Manager デプロイ モデルで使用できる高度な機能により、移行が簡単になります。 これらのリソースの Azure Resource Manager への移行計画を開始することをお勧めします。 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>既存のツールにとって、この移行計画はどのような意味がありますか? 

Resource Manager デプロイ モデルへのツールの更新は、移行計画で考慮する必要がある最も重要な変更の 1 つです。

## <a name="how-long-will-the-management-plane-downtime-be"></a>管理プレーンのダウンタイムはどれくらいですか? 

移行対象のリソースの数によって異なります。 小規模なデプロイ (VM が数十台) の場合、移行に要する時間は 1 時間未満です。 大規模なデプロイ (VM が数百台) の場合は、移行に数時間かかることがあります。

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Resource Manager で移行対象のリソースがコミットされた後で、ロールバックすることはできますか? 

リソースが準備完了状態の場合は、移行を中止できます。 リソースがコミット操作によって正常に移行されたら、ロールバックできません。

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>コミット操作が失敗した場合、移行をロールバックすることはできますか? 

コミット操作が失敗した場合は、移行を中止できません。 コミット操作を含むすべての移行操作がべき等です。 したがって、しばらくしてから操作を再試行することをお勧めします。 それでもエラーが発生する場合は、サポート チケットを作成してください。

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Resource Manager で IaaS を使用する必要がある場合、別の Express Route 回線を購入する必要はありますか? 

いいえ。 最近、 [クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線の移行](~/articles/expressroute/expressroute-move.md)が可能になりました。 ExpressRoute 回線が既にある場合は、新しいものを購入する必要はありません。

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>クラシック IaaS リソース用にロールベースの Access Control ポリシーを構成した場合はどうすればよいですか? 

移行中に、リソースはクラシックから Resource Manager に変換されます。 したがって、移行後に必要になる RBAC ポリシーの更新を計画しておくことをお勧めします。

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>クラシック VM をコンテナーにバックアップしてあります。 クラシック モードから Resource Manager モードに VM を移行して、Recovery Services コンテナーで保護することはできますか。

クラシックから Resource Manager モードに VM を移行する場合、移行前に作成されたバックアップは、新しく移行した Resource Manager VM には移行されません。 ただし、クラシック VM のバックアップを保持する場合は、移行前に次の手順に従います。 

1. Recovery Services コンテナーで、 **[保護された項目]** タブに移動して VM を選択します。 
2. [保護の停止] をクリックします。 *[関連付けられたバックアップ データを削除します]* オプションは **オフ**のままにしておきます。

> [!NOTE]
> データを保持するまでは、バックアップ インスタンス コストが課金されます。 バックアップ コピーは、リテンション期間に応じて取り除かれます。 ただし、バックアップ データを明示的に削除するまでは、最後のバックアップ コピーは常に保持されます。 仮想マシンのリテンション期間を確認し、リテンション期間が終了したら、コンテナーの保護された項目に対して "バックアップ データの削除" をトリガーすることをお勧めします。 
>
>

仮想マシンを Resource Manager モードに移行するには 

1. バックアップ/スナップショット拡張機能を VM から削除します。
2. 仮想マシンをクラシック モードから Resource Manager モードに移行します。 その仮想マシンに対応するストレージとネットワークの情報も Resource Manager モードに移行されていることを確認してください。

さらに、移行した VM をバックアップする場合は、仮想マシンの管理ブレードに移動して[バックアップを有効にします](~/articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm)。

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>サブスクリプションまたはリソースを検証し、移行が可能かどうかを確認できますか? 

はい。 プラットフォームでサポートされる移行オプションでは、移行準備の最初の手順として、リソースを移行できるかどうかを検証します。 検証操作が失敗した場合、移行を完了できないすべての理由についてメッセージを受信します。

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>IaaS リソースの移行準備中にクォータ エラーが発生した場合はどうなりますか? 

移行を中止することをお勧めします。その後、VM を移行するリージョンのクォータを増やすサポート要求を記録します。 クォータ要求が承認されたら、移行手順を再開できます。

## <a name="how-do-i-report-an-issue"></a>問題はどのようにレポートすればよいですか? 

移行の問題や質問については、ClassicIaaSMigration というキーワードを付けて、[VM に関する Microsoft Q&A ページ](https://docs.microsoft.com/answers/topics/azure-virtual-machines.html)に投稿してください。 質問はすべてのこのフォーラムに投稿することをお勧めします。 サポート契約がある場合は、サポート チケットを記録してもかまいません。

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>移行中にプラットフォームで選択されたリソース名が気に入らない場合はどうすればよいですか? 

クラシック デプロイ モデルで明示的に名前を指定したリソースはすべて、移行中は保持されます。 場合によっては、新しいリソースが作成されます。 たとえば、各 VM に対してネットワーク インターフェイスが作成されます。 現時点では、移行中に作成されるこれらの新しいリソース名を制御することはできません。 [Azure フィードバック フォーラム](https://feedback.azure.com)で、この機能の投票を記録してください。

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>リンクの承認を受けた複数のサブスクリプションによって使用されている ExpressRoute 回線は移行できますか。 

サブスクリプション間の承認リンクが使用されている ExpressRoute 回線は、ダウンタイムなしで自動的に移行することができません。 Microsoft は、手動の手順でこれらの回線を移行する方法についてのガイダンスを用意しています。 手順と詳細については、「[クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線および関連する仮想ネットワークの移行](~/articles/expressroute/expressroute-migration-classic-resource-manager.md)」を参照してください。

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>次のようなメッセージを受信しました。"*VM is reporting the overall agent status as Not Ready. (VM がエージェントの全般ステータスとして [準備未完了] を報告しています。)この VM を移行できません。VM エージェントから、エージェントの全般的な状態が準備完了として報告されるようにしてください*" または "*VM には、VM から状態が報告されない拡張機能が含まれています。Hence, this VM cannot be migrated. (そのため、この VM を移行できません。)* "

このメッセージを受信するのは、VM がインターネットに送信接続されていない場合です。 VM エージェントでは、送信接続を使用して、5 分ごとにエージェントの状態を更新するために Azure ストレージ アカウントにアクセスします。


## <a name="next-steps"></a>次のステップ

Linux の場合:

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](./linux/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](./migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](./linux/migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](./windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](./linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](./windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Review most common migration errors](./linux/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (移行の一般的なエラーを確認する)

Windows の場合:

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](./windows/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](./migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](./windows/migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](./windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](./linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](./windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Review most common migration errors](./windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (移行の一般的なエラーを確認する)