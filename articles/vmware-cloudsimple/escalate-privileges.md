---
title: Azure VMware Solutions (AVS) - AVS の特権をエスカレートする
description: AVS プライベート クラウドの vCenter で管理機能を実行するために AVS のアクセス許可をエスカレートする方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 90dd61fc9856978bab0b68de19d48493a8e0c5fd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025335"
---
# <a name="escalate-avs-privileges-to-perform-administrative-functions-in-avs-private-cloud-vcenter"></a>AVS プライベート クラウド vCenter で管理機能を実行するために AVS の特権をエスカレートする

AVS の特権アプローチは、通常の操作を実行するために必要な特権を vCenter ユーザーに与えるように設計されています。 場合によっては、特定のタスクを実行するために追加の特権がユーザーに必要になることがあります。 期間限定で vCenter SSO ユーザーの特権をエスカレートすることができます。

特権をエスカレートする理由には、次のようなものがあります。

* ID ソースの構成
* [ユーザー管理]
* 分散ポート グループの削除
* VCenter ソリューション (バックアップ アプリなど) のインストール
* サービス アカウントの作成

> [!WARNING]
> 特権がエスカレートされた状態でアクションを実行すると、システムに悪影響が出たり、システムが使用できなくなったりする可能性があります。 エスカレーション期間中は必要な操作のみを実行してください。

AVS ポータルから、vCenter SSO で CloudOwner ローカル ユーザーの[特権をエスカレート](escalate-private-cloud-privileges.md)します。 リモート ユーザーの特権は、追加の ID プロバイダーが VCenter で構成されている場合にのみエスカレートできます。 特権をエスカレートするには、選択したユーザーを vSphere の組み込みの Administrators グループに追加する必要があります。 エスカレートされた特権を持つことができるユーザーは 1 人だけです。 別のユーザーの特権をエスカレートする必要がある場合は、まず現在のユーザーの特権のエスカレーションを解除します。

追加の ID ソースからのユーザーを、CloudOwner グループのメンバーとして追加する必要があります。

> [!CAUTION]
> 新しいユーザーは、*Cloud-Owner-Group*、*Cloud-Global-Cluster-Admin-Group*、*Cloud-Global-Storage-Admin-Group*、*Cloud-Global-Network-Admin-Group*、または *Cloud-Global-VM-Admin-Group* にのみ追加する必要があります。  *Administrators* グループに追加されたユーザーは自動的に削除されます。  *[管理者]* グループに追加する必要があるのはサービス アカウントだけです。また、サービス アカウントを使用して vSphere Web UI にサインインすることはできません。
エスカレーション期間中、AVS では、自動監視と、それに関連付けられたアラート通知を使用して、環境に対する誤った変更を識別します。
