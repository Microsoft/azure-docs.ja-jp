---
title: Azure portal を使用して Exchange ピアリングを作成または変更する
titleSuffix: Azure
description: Azure portal を使用して Exchange ピアリングを作成または変更する
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/2/2020
ms.author: derekol
ms.openlocfilehash: 69201c97882846fb929b3b6f9a90be6647603bcc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700482"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Azure portal を使用して Exchange ピアリングを作成または変更する

この記事では、Azure portal を使用して Microsoft Exchange ピアリングを作成する方法について説明します。 また、この記事では、リソースの状態確認、更新、または削除およびプロビジョニング解除の方法も示します。

必要に応じて、[PowerShell](howto-exchange-powershell.md) を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)と [Exchange ピアリングのチュートリアル](walkthrough-exchange-all.md)を確認します。
* Microsoft との Exchange ピアリングが既にあり、Azure リソースに変換されていない場合は、「[ポータルを使用してレガシの Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-portal.md)」を参照してください。

## <a name="create-and-provision-an-exchange-peering"></a>Exchange ピアリングを作成・プロビジョニングする

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>ポータルにサインインしてサブスクリプションを選択する
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Exchange ピアリングを作成する


インターネット交換プロバイダーは、[ピアリングを作成]( https://go.microsoft.com/fwlink/?linkid=2129593)することにより、ピアリング要求を作成できます。

1. **[ピアリングの作成]** ページの **[基本]** タブで、次のようにボックスに入力します。

    > [!div class="mx-imgBorder"] 
    > ![Peering Service の登録](./media/setup-basics-tab.png)

*    お使いの Azure サブスクリプションを選択します。

* [リソース グループ] では、ボックスの一覧から既存のリソース グループを選択するか、[新規作成] を選択して新しいグループを作成します。 この例では、新しいリソース グループを作成します。

* [名前] は、リソース名に対応し、任意の名前を選択できます。

* 既存のリソース グループを選択した場合、[リージョン] は自動的に選択されます。 新しいリソース グループの作成を選択した場合は、リソースを配置する Azure リージョンも選択する必要があります。

>[!NOTE]
>リソース グループを配置するリージョンは、Microsoft とのピアリングを作成する場所とは関係ありません。 ただし、最も近い Azure リージョンにリソース グループを配置して、使用するピアリング リソースをそこで整理することをお勧めします。 たとえば、Ashburn でのピアリングの場合、米国東部または米国東部 2 にリソース グループを作成できます。

* **[PeerASN]** ボックスで、使用する ASN を選択します。

>[!IMPORTANT] 
>選択できるのは、ピアリング要求を送信する前に ValidationState が "Approved" になっている ASN だけです。 PeerAsn 要求を送信した直後であれば、ASN の関連付けが承認されるまで 12 時間程度かかります。 選択した ASN が検証待ちの場合、エラー メッセージが表示されます。 選択する必要がある ASN が表示されない場合は、適切なサブスクリプションを選択しているかどうかを確認します。 選択している場合は、「 **[ピア ASN を Azure サブスクリプションに関連付ける](https://go.microsoft.com/fwlink/?linkid=2129592)** 」を参照して、PeerAsn を既に作成したかどうかを確認します。

* **[次へ: 構成]** を選択して続行します。

#### <a name="configure-connections-and-submit"></a>接続を構成して送信する
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Exchange ピアリングを確認する
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Exchange ピアリングを変更する
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Exchange ピアリングをプロビジョニング解除する
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>次のステップ

* [ポータルを使用して Direct ピアリングを作成または変更する](howto-direct-portal.md)
* [ポータルを使用してレガシの Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>その他のリソース

詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください。
