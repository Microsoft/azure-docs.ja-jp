---
title: Azure Active Directory Identity Protection の FAQ | Microsoft Docs
description: Azure AD Identity Protection についてよく寄せられる質問
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d36b513b6835088b5120072906667cd30af505d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025731"
---
# <a name="azure-active-directory-identity-protection-faq"></a>Azure Active Directory Identity Protection の FAQ

この記事では、Azure Active Directory (Azure AD) Identity Protection に関してよく寄せられる質問の回答を示します。 詳細については、「[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)」をご覧ください。 

## <a name="why-do-some-risk-detections-have-closed-system-status"></a>一部のリスク検出の状態が "クローズ (システム)" になっているのはなぜですか。

**A:** これらは、Identity Protection によって検出された後、それらのイベントの危険性が低下したと見なされたためにクローズされたリスク検出です。 これらのイベントは、ユーザーのリスク レベルには加算されません。 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>Azure Portal で Identity Protection を使用するために、全体管理者である必要はありますか。
**A:** No. セキュリティ閲覧者、セキュリティ管理者、全体管理者のいずれかになることで、Identity Protection を使用できます。

---

## <a name="how-do-i-get-identity-protection"></a>Identity Protection を入手するには、どうすればよいですか。

**A:** この質問の回答については、「[Azure Active Directory Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。

---

## <a name="how-can-i-sort-users-in-users-flagged-for-risk"></a>"リスクのフラグ付きユーザー" でユーザーを並べ替えるにはどうすればよいですか。

**A:** **[リスクのフラグ付きユーザー]** ページの上部にある **[ダウンロード]** をクリックして、リスクのフラグ付きユーザー レポートをダウンロードします。 [最終更新日時 (UTC)] など、使用可能なフィールドに基づいて、ダウンロードされたデータを並べ替えることができます。

---
