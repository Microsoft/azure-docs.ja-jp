---
title: 条件付きアクセス ポリシーの許可コントロール - Azure Active Directory
description: Azure AD 条件付きアクセス ポリシーの許可コントロールとは
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89063cc8131c28f20153c6fe9b4c71b58794e609
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192044"
---
# <a name="conditional-access-grant"></a>条件付きアクセス:Grant

条件付きアクセス ポリシー内では、管理者はアクセス コントロールを使用して、リソースへのアクセスを許可またはブロックすることができます。

![多要素認証を必要とする許可コントロールを使用した条件付きアクセス ポリシー](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>アクセスのブロック

ブロックは、割り当てを考慮して、条件付きアクセス ポリシーの構成に基づいてアクセスを禁止します。

ブロックは強力なコントロールであるため、適切な知識を習得したうえで扱う必要があります。 管理者は、有効化する前に[レポート専用モード](concept-conditional-access-report-only.md)を使用してそれをテストする必要があります。

## <a name="grant-access"></a>アクセス権の付与

管理者は、アクセス権を付与するときに 1 つ以上のコントロールを適用することを選択できます。 これらのコントロールには、次のオプションがあります。 

- [多要素認証 (Azure Multi-Factor Authentication) を要求する](../authentication/concept-mfa-howitworks.md)
- [デバイスが準拠としてマーク済みであることを必要とする (Microsoft Intune)](https://docs.microsoft.com/intune/protect/device-compliance-get-started)
- [ハイブリッド Azure AD 参加済みのデバイスを必要とする](../devices/concept-azure-ad-join-hybrid.md)
- [承認済みクライアント アプリを必須にする](app-based-conditional-access.md)
- [アプリの保護ポリシーを必須にする](app-protection-based-conditional-access.md)

これらのオプションを組み合わせることを選択する場合、管理者は次の方法を選択できます。

- 選択したコントロールすべてが必要 (コントロール**と**コントロール)
- 選択したコントロールのいずれかが必要 (コントロール**または**コントロール)

既定では、条件付きアクセスでは、選択したすべてのコントロールが必要です。

### <a name="require-multi-factor-authentication"></a>多要素認証が必要です

このチェックボックスをオンにすると、ユーザーは Azure Multi-Factor Authentication を実行する必要があります。 Azure Multi-factor Authentication のデプロイの詳細については、「[クラウド ベースの Azure Multi-Factor Authentication のデプロイの計画](../authentication/howto-mfa-getstarted.md)」の記事を参照してください。

### <a name="require-device-to-be-marked-as-compliant"></a>デバイスは準拠としてマーク済みである必要がある

Microsoft Intune をデプロイしている組織では、デバイスから返された情報を使用して、特定の準拠要件を満たすデバイスを識別することができます。 このポリシー準拠情報は Intune から Azure AD に転送され、条件付きアクセスはそこで、リソースへのアクセスを許可するかブロックするかを決定できます。 準拠ポリシーの詳細については、「[Intune を使用して組織内のリソースへのアクセスを許可するように、デバイス上でルールを設定する](https://docs.microsoft.com/intune/protect/device-compliance-get-started)」という記事を参照してください。

### <a name="require-hybrid-azure-ad-joined-device"></a>ハイブリッド Azure AD 参加済みのデバイスを必要とする

組織は、条件付きアクセス ポリシーの一部としてデバイス ID を使用することを選択できます。 組織は、このチェックボックスを使用して、デバイスがハイブリッド Azure AD 参加済みであることを必須にすることができます。 デバイス ID の詳細については、「[デバイス ID とは](../devices/overview.md)」の記事を参照してください。

### <a name="require-approved-client-app"></a>承認済みクライアント アプリを必須にする

組織は、選択したクラウド アプリへのアクセス試行を、承認されたクライアント アプリから行うように要求することができます。

この設定は、以下のクライアント アプリに適用されます。

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**解説**

- 承認されたクライアント アプリは、Intune モバイル アプリケーション管理機能をサポートしています。
- **[承認されたクライアント アプリが必要です]** 要件:
   - デバイス プラットフォームの条件に関しては、iOS と Android のみがサポートされます。
- 条件付きアクセスでは、InPrivate モードの Microsoft Edge を承認されたクライアント アプリと見なすことはできません。

### <a name="require-app-protection-policy"></a>アプリの保護ポリシーを必須にする

条件付きアクセス ポリシー内で、選択したクラウド アプリがアクセスできるようにする前に、クライアント アプリにアプリの保護ポリシーが存在することを要求できます。 

![アプリの保護ポリシーによるアクセスの制御](./media/technical-reference/22.png)

この設定は、以下のクライアント アプリに適用されます。

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**解説**

- アプリ保護ポリシーのアプリは、ポリシー保護を含む Intune モバイル アプリケーション管理機能をサポートしています。
- **アプリの保護ポリシーが必要**の要件:
    - デバイス プラットフォームの条件に関しては、iOS と Android のみがサポートされます。

## <a name="next-steps"></a>次のステップ

- [条件付きアクセス: セッション コントロール](concept-conditional-access-session.md)

- [Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

- [レポート専用モード](concept-conditional-access-report-only.md)
