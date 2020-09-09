---
title: Windows 認証と Azure MFA Server - Azure Active Directory
description: Windows 認証と Azure Multi-Factor Authentication Server のデプロイ。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b945b984210dc3fc3338da8a5670a7813198e40
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919473"
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Windows 認証と Azure Multi-Factor Authentication Server

Azure Multi-Factor Authentication Server の [Windows 認証] セクションを使用して、アプリケーションの Windows 認証を有効にして構成します。 Windows 認証を設定する前に、次の点に留意してください。

* 設定したら、Azure Multi-Factor Authentication を再起動して、ターミナル サービスを有効にします。
* [Multi-Factor Authentication のユーザー照合が必要] がオンになっており、ユーザーがユーザー一覧にいない場合、再起動後にマシンにログインできません。
* 信頼できる IP は、アプリケーションが認証付きのクライアント IP を提供できるかどうかに依存します。 現在はターミナル サービスのみがサポートされます。  

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft では新しいデプロイに対して MFA Server が提供されなくなりました。 サインイン イベント時に多要素認証が必要な新しいお客様は、クラウドベースの Azure Multi-Factor Authentication (MFA) を使用していただく必要があります。
>
> クラウドベースの MFA の使用を開始するには、「[チュートリアル: Azure Multi-Factor Authentication を使用してユーザーのサインイン イベントのセキュリティを確保する](tutorial-enable-azure-mfa.md)」を参照してください。
>
> 2019 年 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

> [!NOTE]
> この機能は Windows Server 2012 R2 上のターミナル サービスのセキュリティを保護しません。

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Windows 認証を使用してアプリケーションを保護するには、次の手順に従います

1. Azure Multi-Factor Authentication Server で、[Windows 認証] アイコンをクリックします。
   ![MFA サーバーでの Windows 認証](./media/howto-mfaserver-windows/windowsauth.png)
2. **[Windows 認証を有効にする]** チェック ボックスをオンにします。 既定では、このボックスはオフになっています。
3. [アプリケーション] タブでは、管理者が Windows 認証を使用する 1 つ以上のアプリケーションを構成できます。
4. サーバーまたはアプリケーションを選択し、サーバーまたはアプリケーションが有効になっているかどうかを指定します。 **[OK]** をクリックします。
5. **[追加]** をクリックします。
6. [信頼される IP] タブでは、特定の IP から送信された Windows セッションの Azure Multi-Factor Authentication をスキップすることができます。 たとえば、従業員がオフィスと自宅の両方でアプリケーションを使用する場合、オフィスにいる間は Azure Multi-Factor Authentication の電話を鳴らさないように設定できます。 それには、社内のサブネットを信頼される IP エントリとして指定します。
7. **[追加]** をクリックします。
8. 単一の IP アドレスをスキップする場合は、 **[単一 IP を追加する]** を選択します。
9. IP 範囲全体をスキップする場合は、 **[IP 範囲を指定して追加する]** を選択します (例: 10.63.193.1-10.63.193.100)。
10. サブネット表記を使用して IP の範囲を指定する場合は、 **[サブネット]** を選択します。 サブネットの開始 IP を入力し、ドロップダウン リストから適切なネットマスクを選択します。
11. **[OK]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [Azure MFA Server のサード パーティ VPN アプライアンスを構成する](howto-mfaserver-nps-vpn.md)

- [Azure MFA の NPS 拡張機能で既存の認証インフラストラクチャを強化する](howto-mfa-nps-extension.md)
