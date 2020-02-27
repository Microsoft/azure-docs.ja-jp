---
title: VPN ユーザー用の MFA を有効にする：Azure AD 認証
description: VPN ユーザー用の多要素認証を有効にします。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: 13f012af95bb2b6098317e59e5293fb72804a6a6
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471544"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>VPN ユーザーの Azure 多要素認証 (MFA)を有効にします。

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enableauth"></a>認証を有効にする

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="enablesign"></a> サインインの設定を構成する

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="peruser"></a>オプション 1 - ユーザーアクセスごと

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="conditional"></a>オプション 2 - 条件付きアクセス

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>次のステップ

仮想ネットワークに接続するには、VPN クライアント プロファイルを作成し、構成する必要があります。 「[Azure へのポイント対サイト接続に Azure AD 認証を構成する](virtual-wan-point-to-site-azure-ad.md)」を参照してください。
