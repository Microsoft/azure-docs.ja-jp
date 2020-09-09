---
title: Azure シリアル コンソール | Microsoft Docs
description: Azure シリアルコンソールを使用すると、SSH または RDP を利用できない場合に VM に接続できます。
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 28c5a3085d84b25deb7c5ee09a9c9cc4d7a06819
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374067"
---
# <a name="azure-serial-console"></a>Azure シリアル コンソール

Azure portal のシリアル コンソールでは、Linux または Windows を実行している仮想マシン (VM) および仮想マシン スケール セット インスタンス用のテキスト ベースのコンソールへのアクセスが提供されます。 このシリアル接続は、ネットワークやオペレーティング システムの状態には関係なく、VM または仮想マシン スケール セット インスタンスの ttyS0 または COM1 シリアル ポートに接続してアクセスを提供します。 このシリアル コンソールは Azure portal を使用してのみアクセスでき、VM または仮想マシン スケール セットへの共同作成者以上のアクセス ロールを持つユーザーに対してのみ許可されます。

シリアル コンソールは、VM と仮想マシン スケール セット インスタンスに対して同じ方法で動作します。 このドキュメントでは、特に記載のない限り、VM という記述にはすべて仮想マシン スケール セット インスタンスが暗黙的に含まれます。

シリアル コンソールは、グローバル Azure リージョンで一般公開されており、Azure Government ではパブリック プレビュー段階にあります。 Azure China Cloud ではまだ利用できません。

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Azure シリアル コンソールにアクセスするための前提条件
VM または仮想マシン スケール セット インスタンスのシリアル コンソールにアクセスするには、次のものが必要です。

- VM に対してブート診断を有効にする必要があります
- パスワード認証を使用するユーザー アカウントが VM 内に存在する必要があります。 パスワード ベースのユーザーは、VM アクセス拡張機能の[パスワードのリセット](../extensions/vmaccess.md#reset-password)機能を使用して作成できます。 **[サポート + トラブルシューティング]** セクションの **[パスワードのリセット]** を選択します。
- シリアル コンソールにアクセスする Azure アカウントには、VM および[ブート診断](boot-diagnostics.md)ストレージ アカウントの両方に、[仮想マシン共同作成者ロール](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)が必要です
- クラシック デプロイはサポートされていません。 VM または仮想マシン スケール セット インスタンスは、Azure Resource Manager デプロイ モデルを使用する必要があります。

> [!NOTE]
> シリアル コンソールは、現在マネージド ブート診断ストレージ アカウントとの完全な互換性がありません。 シリアル コンソールを使用するには、カスタム ストレージ アカウントを使用していることを確認してください。

## <a name="get-started-with-the-serial-console"></a>シリアル コンソールの概要
VM および仮想マシン スケール セット用のシリアル コンソールには、Azure portal を使用してのみアクセスできます。

### <a name="serial-console-for-virtual-machines"></a>仮想マシン用のシリアル コンソール
VM 用のシリアル コンソールには、Azure portal で **[サポート + トラブルシューティング]** セクション内の **[シリアル コンソール]** をクリックするだけでアクセスできます。
  1. [Azure Portal](https://portal.azure.com)を開きます。

  1. **[すべてのリソース]** に移動し、仮想マシンを選択します。 VM の概要ページが開きます。

  1. 下へスクロールして **[サポート + トラブルシューティング]** セクションを表示し、 **[シリアル コンソール]** を選択します。 シリアル コンソールで新しいウィンドウが開き、接続が開始されます。

     ![Linux シリアル コンソール ウィンドウ](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>仮想マシン スケール セット用のシリアル コンソール
シリアル コンソールは、仮想マシン スケール セットで使用でき、スケール セット内の各インスタンスでアクセスできます。 **[シリアル コンソール]** ボタンが表示されるようにするには、仮想マシン スケール セットの個々のインスタンスに移動する必要があります。 仮想マシン スケール セットでブート診断が有効になっていない場合は、ブート診断を有効にするように仮想マシン スケール セット モデルを更新したことを確認してから、シリアル コンソールにアクセスするためにすべてのインスタンスを新しいモデルにアップグレードします。
  1. [Azure Portal](https://portal.azure.com)を開きます。

  1. **[すべてのリソース]** に移動し、仮想マシン スケール セットを選択します。 仮想マシン スケール セットの概要ページが開きます。

  1. **[インスタンス]** に移動します

  1. 仮想マシン スケール セット インスタンスを選択します

  1. **[サポート + トラブルシューティング]** セクションから、 **[シリアル コンソール]** を選択します。 シリアル コンソールで新しいウィンドウが開き、接続が開始されます。

     ![Linux 仮想マシン スケール セットのシリアル コンソール](./media/virtual-machines-serial-console/vmss-start-console.gif)


### <a name="tls-12-in-serial-console"></a>シリアル コンソールでの TLS 1.2
シリアル コンソールでは、TLS 1.2 をエンドツーエンドで使用して、サービス内のすべての通信をセキュリティで保護します。 シリアル コンソールはユーザーによって管理される起動診断ストレージ アカウントに依存しており、TLS 1.2 をストレージ アカウント用に別個に構成する必要があります。 そのための手順については、[こちら](../../storage/common/transport-layer-security-configure-minimum-version.md)を参照してください。

## <a name="advanced-uses-for-serial-console"></a>シリアル コンソールの高度な使用方法
VM へのコンソール アクセス以外に、Azure シリアル コンソールを使用して次のことを行うこともできます。
* [VM へのシステム要求コマンド](./serial-console-nmi-sysrq.md)の送信
* [VM へのマスク不可能割り込み](./serial-console-nmi-sysrq.md)の送信
* [VM の正常な再起動または強制的な電源切り替え](./serial-console-power-options.md)


## <a name="next-steps"></a>次のステップ
シリアル コンソールの追加ドキュメントはサイドバーから入手できます。
- [Linux VM 用シリアル コンソール](./serial-console-linux.md)についての詳細情報を入手できます。
- [Windows VM 用シリアル コンソール](./serial-console-windows.md)についての詳細情報を入手できます。
