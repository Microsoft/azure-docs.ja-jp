---
title: "Azure MFA とサード パーティ VPN の高度なシナリオ"
description: "Azure MFA と Cisco、Citrix、および Juniper を統合するためのステップ バイ ステップの構成ガイドです。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 1f94a214-d6f6-48a8-8a12-006b5896ae45
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: kgremban
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 61cf29acac22d546e1b75604f799993ad6ff9042
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---

# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Azure Multi-Factor Authentication とサード パーティ VPN の高度なシナリオ ソリューション
Azure Multi-Factor Authentication を使用して、さまざまなサード パーティ VPN ソリューションにシームレスに接続できます。 この記事では、Cisco® ASA VPN アプライアンス、Citrix NetScaler SSL VPN アプライアンス、および Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN アプライアンスに重点を置いて説明します。 これら 3 つの一般的なアプライアンスを対象とした構成ガイドを作成しました。 Multi-Factor Authentication Server は、RADIUS、LDAP、IIS、または AD FS に対して要求ベースの認証を使用するその他の大半のシステムと統合できます。 詳細については、[MFA Server の構成](multi-factor-authentication-get-started-server.md#next-steps)に関するトピックをご覧ください。

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Cisco ASA VPN アプライアンスと Azure Multi-Factor Authentication
Azure Multi-Factor Authentication は Cisco® ASA VPN アプライアンスと統合し、Cisco AnyConnect® VPN のログインとポータル アクセスのセキュリティを強化します。  LDAP または RADIUS プロトコルのいずれかを使用できます。  次のいずれかを選択し、詳しい段階的構成ガイドをダウンロードしてください。

| 構成ガイド | Description |
| --- | --- |
| [Anyconnect VPN / Azure MFA 構成の Cisco ASA (LDAP の場合)](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Cisco ASA VPN アプライアンスと Azure MFA を LDAP で統合します |
| [Anyconnect VPN / Azure MFA 構成の Cisco ASA (RADIUS の場合)](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Cisco ASA VPN アプライアンスと Azure MFA を RADIUS で統合します |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN と Azure Multi-Factor Authentication
Azure Multi-Factor Authentication は Citrix NetScaler SSL VPN アプライアンスと統合し、Citrix NetScaler SSL VPN のログインとポータル アクセスのセキュリティを強化します。  LDAP または RADIUS プロトコルのいずれかを使用できます。  次のいずれかを選択し、詳しい段階的構成ガイドをダウンロードしてください。

| 構成ガイド | Description |
| --- | --- |
| [Citrix NetScaler SSL VPN / Azure MFA 構成 (LDAP の場合)](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Citrix NetScaler SSL VPN と Azure MFA アプライアンスを LDAP で統合します |
| [Citrix NetScaler SSL VPN / Azure MFA 構成 (RADIUS の場合)](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Citrix NetScaler SSL VPN アプライアンスと Azure MFA を RADIUS で統合します |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Juniper/Pulse Secure SSL VPN アプライアンスと Azure Multi-Factor Authentication
Azure Multi-Factor Authentication は Juniper/Pulse Secure SSL VPN アプライアンスと統合し、Juniper/Pulse Secure SSL VPN のログインとポータル アクセスのセキュリティを強化します。  LDAP または RADIUS プロトコルのいずれかを使用できます。  次のいずれかを選択し、詳しい段階的構成ガイドをダウンロードしてください。

| 構成ガイド | Description |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN と Azure MFA の構成 (LDAP の場合)](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Juniper/Pulse Secure SSL VPN と Azure MFA アプライアンスを LDAP で統合します |
| [Juniper/Pulse Secure SSL VPN と Azure MFA の構成 (RADIUS の場合)](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Juniper/Pulse Secure SSL VPN アプライアンスと Azure MFA を RADIUS で統合します |

## <a name="next-steps"></a>次のステップ

- [Azure Multi-Factor Authentication の NPS 拡張機能を使用して既存の認証インフラストラクチャを強化する](multi-factor-authentication-nps-extension.md)

- [Azure Multi-Factor Authentication 設定の構成](multi-factor-authentication-whats-next.md)
