---
title: Azure プライベート DNS に関する FAQ
description: Azure プライベート DNS に関してよく寄せられる質問
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: victorh
ms.openlocfilehash: cb0cc5e99cc07728d475a9f9e54c7eb6a8c7554e
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959926"
---
# <a name="azure-private-dns-faq"></a>Azure プライベート DNS に関する FAQ

Azure プライベート DNS に関してよく寄せられる質問は、次のとおりです。

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS では、プライベート ドメインはサポートされますか。

プライベート ドメインは、Azure プライベート DNS ゾーン機能を使用してサポートされます。 プライベート DNS ゾーンは、指定した仮想ネットワーク内からのみ解決可能です。 詳細については、[概要](private-dns-overview.md)に関するページを参照してください。

Azure での他の内部 DNS オプションの詳細については、「[VM とロール インスタンスの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」を参照してください。

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Azure プライベート DNS ゾーンは Azure リージョンを超えて機能しますか。

はい。 Azure リージョンを超えた仮想ネットワーク間での DNS 解決では、Private Zones がサポートされます。 Private Zones は、仮想ネットワークを明示的にピアリングしていない場合でも動作します。 すべての仮想ネットワークは、プライベート DNS ゾーンにリンクされている必要があります。

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>プライベート ゾーンでは、仮想ネットワークからインターネットに接続されている必要はありますか?

No. プライベート ゾーンは、仮想ネットワークと共に動作します。 それらを使用して、仮想ネットワーク内またはそれらの間で、仮想マシンやその他のリソース用のドメインを管理します。 名前解決にインターネットへの接続は必要ありません。

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>複数の仮想ネットワークの解決に同じプライベート ゾーンを使用できますか?

はい。 プライベート DNS ゾーンは、数千の仮想ネットワークにリンクすることができます。 詳しくは、「[Azure DNS の制限](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)」を参照してください。

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>別のサブスクリプションに属している仮想ネットワークをプライベート ゾーンにリンクできますか。

はい。 ユーザーが仮想ネットワークとプライベート DNS ゾーンでの書き込み操作のアクセス許可を持っている必要があります。 書き込みアクセス許可は、複数の RBAC ロールに付与することができます。 たとえば、従来のネットワークの共同作成者の RBAC ロールには仮想ネットワークへの書き込みアクセス許可があり、プライベート DNS ゾーンの共同作成者ロールにはプライベート DNS ゾーンに対する書き込みアクセス許可があります。 RBAC ロールについて詳しくは、[ロールベースのアクセス制御](../role-based-access-control/overview.md)に関するページをご覧ください。

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>プライベート ゾーンに自動的に登録された仮想マシンの DNS レコードは、仮想マシンを削除すると自動的に削除されますか。

はい。 リンクされた仮想ネットワークで自動登録が有効な場合、そのネットワーク内にある仮想マシンを削除すると、登録されているレコードは自動的に削除されます。

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>リンクされた仮想ネットワークからプライベート ゾーンに自動的に登録された仮想マシンのレコードは手動で削除できますか。

はい。 自動的に登録された DNS レコードを、そのゾーンに手動で作成した DNS レコードで上書きできます。 次の質問と回答がこのトピックに対応しています。

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>リンクされた仮想ネットワーク内の自動的に登録された既存の仮想マシンと同じホスト名を持つプライベート ゾーンに新しい DNS レコードを手動で作成しようとするとどうなりますか。

リンクされた仮想ネットワーク内の自動的に登録された既存の仮想マシンと同じホスト名を持つプライベート ゾーンに新しい DNS レコードを手動で作成しようとしたとします。 これを行った場合、自動的に登録された仮想マシン レコードが新しい DNS レコードで上書きされます。 この手動で作成した DNS レコードを再びゾーンから削除しようとすると、削除は成功します。 仮想マシンが存在し、プライベート IP がアタッチされている限り、自動登録が再度発生します。 DNS レコードがゾーン内に自動的に再作成されます。

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>リンクされた仮想ネットワークをプライベート ゾーンからリンク解除するとどうなりますか。 その仮想ネットワークから自動的に登録された仮想マシンのレコードもそのゾーンから削除されますか。

はい。 リンクされた仮想ネットワークをプライベート ゾーンからリンク解除するには、DNS ゾーンを更新して、関連付けられている仮想ネットワーク リンクを削除します。 このプロセスの中で、自動的に登録された仮想マシンのレコードがゾーンから削除されます。

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>プライベート ゾーンにリンクされている、リンクされた仮想ネットワークを削除するとどうなりますか。 プライベート ゾーンを手動で更新して、リンクされた仮想ネットワークとしての仮想ネットワークをそのゾーンからリンク解除する必要がありますか。

No. プライベート ゾーンとのリンクを解除せずに、リンクされた仮想ネットワークを削除しても、削除操作は成功し、DNS ゾーンへのリンクは自動的に消去されます。

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>既定の FQDN (internal.cloudapp.net) を使用している DNS 解決は、プライベート ゾーン (例: private.contoso.com) が仮想ネットワークにリンクされている場合でも機能しますか。

はい。 Private Zones によって、既定の Azure 提供の internal.cloudapp.net ゾーンは置き換えられません。 Azure 提供の internal.cloudapp.net に依存している場合でも、独自のプライベート ゾーンに依存している場合でも、解決するゾーンに対して FQDN を使用できます。

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>リンクされた仮想ネットワーク内の仮想マシンの DNS サフィックスは、そのプライベート ゾーンの DNS サフィックスに変更されますか。

No. リンクされた仮想ネットワーク内の仮想マシン上の DNS サフィックスは、Azure 提供の既定のサフィックス ("*.internal.cloudapp.net") のままです。 仮想マシン上のこの DNS サフィックスをプライベート ゾーンの DNS サフィックスに手動で変更できます。
このサフィックスを変更する方法のガイダンスについては、「[動的 DNS を使用して独自の DNS サーバーでホスト名を登録する](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)」を参照してください。

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Azure DNS プライベート ゾーンの使用制限とは何ですか。

Azure DNS プライベート ゾーンの使用制限の詳細については、「[Azure DNS の制限](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)」を参照してください。

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>既存のプライベート DNS ゾーンが新しいポータル エクスペリエンスに表示されないのはなぜですか。

既存のプライベート DNS ゾーンがプレビュー版の API を使用して作成されている場合は、これらのゾーンを新しいリソース モデルに移行する必要があります。 プレビュー版の API を使用して作成されたプライベート DNS ゾーンは、新しいポータル エクスペリエンスに表示されません。 新しいリソース モデルへの移行方法については、後述する指示を参照してください。

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>どのようにして既存のプライベート DNS ゾーンを新しいモデルに移行しますか。

できるだけ早く新しいリソース モデルに移行することを強くお勧めします。 従来のリソース モデルは今後、サポートはされますが、追加機能開発のベースになることはありません。 新しいリソース モデルを優先するために、将来的には廃止される予定です。 既存のプライベート DNS ゾーンを新しいリソース モデルに移行する方法については、[Azure DNS プライベート ゾーンの移行ガイド](private-dns-migration-guide.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure プライベート DNS の詳細](private-dns-overview.md)
