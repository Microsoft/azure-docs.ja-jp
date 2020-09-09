---
title: Microsoft 365 Government における Azure AD の最新情報 | Microsoft Docs
description: Microsoft 365 Government クラウド インスタンスの Azure Active Directory (Azure AD) に対して行われたいくつかの変更は、ユーザーへの影響が考えられます。それらの変更について取り上げます。
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0efc4bc8f89b0fbefbba171d80a3f8a1ed5e7f6
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318932"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Microsoft 365 Government における Azure Active Directory の最新情報

Microsoft 365 Government クラウド インスタンスの Azure Active Directory (Azure AD) には、いくつかの変更が加えられています。次のサービスをご利用のお客様に関係します。

- Microsoft Azure Government

- Microsoft 365 Government – GCC High

- Microsoft 365 Government – DoD

この記事の内容は、Microsoft 365 Government - GCC のユーザーには該当しません。

## <a name="changes-to-the-initial-domain-name"></a>初期ドメイン名に対する変更

Microsoft 365 Government オンライン サービスへの初回サインアップ時に、貴社のドメイン名 (`<your-domain-name>.onmicrosoft.com`) を選択するように求められます。 既に .com というサフィックスのドメイン名がある場合は、何も変わりません。

一方、新しい Microsoft 365 Government サービスにサインアップする場合は、`.us` サフィックスを使用してドメイン名を選択するように求められます。 つまり、`<your-domain-name>.onmicrosoft.us` のようになります。

>[!Note]
>クラウド サービス プロバイダー (CSP) によって管理されるお客様には、この変更は関係しません。

## <a name="changes-to-portal-access"></a>ポータル アクセスへの変更

Microsoft Azure Government、Microsoft 365 Government - GCC High、Microsoft 365 Government - DoD に関して、ポータルのエンドポイントを更新しました ([エンドポイント マッピングの表](#endpoint-mapping)を参照)。

従来は、ワールドワイドの Azure (portal.azure.com) ポータルおよび Office 365 (portal.office.com) ポータルを使用してサインインすることができました。 この更新により、今後は、Microsoft Azure Government、Microsoft 365 Government - GCC High、Microsoft 365 Government - DoD の特定のポータルを使用してサインインしていただく必要があります。

## <a name="endpoint-mapping"></a>エンドポイント マッピング

次の表に、すべてのお客様のエンドポイントを示します。

| 名前 | エンドポイントの詳細 |
|------|------------------|
| ポータル |Microsoft Azure Government: https://portal.azure.us<p>Microsoft 365 Government – GCC High: https://portal.office365.us<p>Microsoft 365 Government – DoD: https://portal.apps.mil |
| Azure Active Directory オーソリティ エンドポイント | https://login.microsoftonline.us |
| Microsoft Graph API for Microsoft 365 Government - GCC High | https://graph.microsoft.us |
| Microsoft Graph API for Microsoft 365 Government - DoD | https://dod-graph.microsoft.us |
| Azure Government サービス エンドポイント | 詳細については、「[Azure Government developer guide (Azure Government 開発者ガイド)](../../azure-government/documentation-government-developer-guide.md)」を参照してください。 |
| Microsoft 365 Government - GCC High エンドポイント | 詳細については、「[Office 365 U.S. Government GCC High エンドポイント](/office365/enterprise/office-365-u-s-government-gcc-high-endpoints)」を参照してください。 |
| Microsoft 365 Government - DoD | 詳細については、「[Office 365 U.S. Government DoD エンドポイント](/office365/enterprise/office-365-u-s-government-dod-endpoints)」を参照してください。 |

## <a name="next-steps"></a>次のステップ

詳細と例については、次の記事をご覧ください。

- [Azure Government とは](../../azure-government/documentation-government-welcome.md)

- [Azure Government AAD オーソリティ エンドポイントの更新](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [US Government クラウドの Microsoft Graph エンドポイント](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 US Government GCC High および DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)