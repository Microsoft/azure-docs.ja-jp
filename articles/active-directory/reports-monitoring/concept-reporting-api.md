---
title: Azure AD レポート API の概要 | Microsoft Docs
description: Azure Active Directory Reporting API の概要について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf2e3f98bd96c09fe741a24a1332443c876e2e61
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89225912"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory レポート API の概要

Azure Active Directory にはさまざまな[レポート](overview-reports.md)が用意されています。レポートには、SIEM システム、監査、ビジネス インテリジェンス ツールなどのアプリケーションに役立つ情報が掲載されています。 

Azure AD レポートに Microsoft Graph API を使用すると、一連の REST ベースの API を介してプログラムからデータにアクセスできます。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。

この記事では、アクセス方法など、レポート API の概要について説明します。

問題が発生した場合は、「[Azure Active Directory のサポートを受ける方法](../fundamentals/active-directory-troubleshooting-support-howto.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

ユーザー操作の有無にかかわらず、レポート API にアクセスするには以下が必要です。

1. ロールを割り当てる (セキュリティ閲覧者、セキュリティ管理者、全体管理者)
2. アプリケーションを登録する
3. [アクセス許可の付与]
4. 構成設定を収集する

詳しい説明については、[Azure Active Directory レポート API にアクセスするための前提条件](howto-configure-prerequisites-for-reporting-api.md)に関するページをご覧ください。 

## <a name="api-endpoints"></a>API エンドポイント 

監査ログ用の Microsoft Graph API エンドポイントは `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits` であり、サインイン用の Microsoft Graph API エンドポイントは `https://graph.microsoft.com/v1.0/auditLogs/signIns` です。 詳細については、[監査 API リファレンス](/graph/api/resources/directoryaudit?view=graph-rest-1.0)と[サインイン API リファレンス](/graph/api/resources/signIn?view=graph-rest-1.0)のページを参照してください。

また、[Identity Protection リスク検出 API](/graph/api/resources/identityriskevent?view=graph-rest-beta) を使用すると、Microsoft Graph を使ってプログラムからセキュリティの検出にアクセスすることもできます。 詳細については、「[Azure Active Directory Identity Protection と Microsoft Graph の基本](../identity-protection/howto-identity-protection-graph-api.md)」を参照してください。 
  
## <a name="apis-with-microsoft-graph-explorer"></a>Microsoft Graph Explorer を使用する API

[Microsoft Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer)を使用してサインインを確認し、API データを監査できます。 Graph エクスプローラーの UI で、両方のサインイン ボタンを使用してアカウントにサインインし、次に示すようにテナントの **AuditLog.Read.All** および **Directory.ReadAll** のアクセス許可を設定します。   

![Graph エクスプローラー](./media/concept-reporting-api/graph-explorer.png)

![アクセス許可の変更 UI](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>証明書を使用して Azure AD レポート API にアクセスする 

ユーザーによる操作を必要としないでレポート データを取得することを計画している場合は、Azure AD レポート API と共に証明書を使ってください。

詳しくは、「[Azure Active Directory Reporting API と証明書を使用したデータの取得](tutorial-access-api-with-certificates.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

 * [Reporting API にアクセスするための前提条件](howto-configure-prerequisites-for-reporting-api.md) 
 * [Azure AD Reporting API と証明書を使ってデータを取得する](tutorial-access-api-with-certificates.md)
 * [Azure Active Directory Reporting API でのエラーのトラブルシューティング](troubleshoot-graph-api.md)