---
title: アプリケーションの同意の管理と同意要求の評価 - Azure AD
description: ユーザーの同意が無効または制限されている場合に同意要求を管理する方法、およびアプリケーションに対するテナント全体の管理者の同意要求を評価する方法について説明します。
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0451fe18629a572c9b49f14924bfa50293f42a2b
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367839"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>アプリケーションの同意の管理と同意要求の評価

Microsoft は、エンドユーザーによるアプリケーションの同意を無効にすることを[お勧めします](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations)。 これにより、組織のセキュリティ チームと ID 管理者チームの意思決定プロセスが一元化されます。

エンドユーザーの同意が無効または制限された後でも、ビジネス クリティカルなアプリケーションの使用を許可しながら、組織のセキュリティを確保するための重要な考慮事項がいくつかあります。 これらの手順は、サードパーティ製のアプリケーションにおける管理されていないアカウントの使用を制限しながら、組織のサポート チームと IT 管理者への影響を最小限に抑えるために不可欠です。

## <a name="process-changes-and-education"></a>変更と教育を処理する

 1. ユーザーが同意画面から直接管理者の承認を要求できるようにするには、[管理者の同意ワークフロー (プレビュー)](configure-admin-consent-workflow.md) を有効にすることを検討してください。

 2. すべての管理者が[アクセス許可と同意フレームワーク](../develop/consent-framework.md)、[同意プロンプト](../develop/application-consent-experience.md)のしくみ、および[テナント全体の管理者の同意要求を評価する](#evaluating-a-request-for-tenant-wide-admin-consent)方法を理解していることを確認します。
 3. ユーザーがアプリケーションの管理者の承認を要求する方法について、組織の既存のプロセスを確認し、必要に応じて更新します。 プロセスが変更された場合:
    * 関連するドキュメント、監視、自動化などを更新します。
    * 影響を受けるすべてのユーザー、開発者、サポート チーム、および IT 管理者にプロセスの変更を伝達します。

## <a name="auditing-and-monitoring"></a>監査と監視

1. 組織の[アプリと許可されたアクセス許可を監査](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions)し、認められていない、または疑わしいアプリケーションが以前にデータへのアクセスを許可されていないことを確認します。

2. OAuth 同意を要求する疑わしいアプリケーションに対する追加のベスト プラクティスと保護手段については、[Office 365 での不正な同意の許可の検出と修復](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)に関するページを確認します。

3. 組織が適切なライセンスを持っている場合:

    * [Microsoft Cloud App Security で OAuth アプリケーション監査機能](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth)を追加で使用します。
    * [Azure Monitor Workbooks を使用して、アクセス許可および同意](../reports-monitoring/howto-use-azure-monitor-workbooks.md)に関連するアクティビティを監視します。 *Consent Insights* ワークブックには、失敗した同意要求の数を示すアプリの一覧が表示されます。 これは、管理者の同意を許可するかどうかを管理者が確認および判断するため、アプリケーションの優先順位を決定するのに役立ちます。

### <a name="additional-considerations-for-reducing-friction"></a>摩擦の軽減に関するその他の考慮事項

既に使用されている、信頼されたビジネス クリティカルなアプリケーションへの影響を最小限に抑えるには、許可するユーザーの同意が多いアプリケーションに管理者の同意を事前に許可することを検討してください。

1. サインイン ログまたは同意許可アクティビティに基づいて、使用率が高い組織に既に追加されているアプリのインベントリを取得します。 PowerShell [スクリプト](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09)を使用すると、多数のユーザーの同意が許可されたアプリケーションをすばやく簡単に検出できます。

2. 管理者の同意がまだ許可されていない上位のアプリケーションを評価します。

   > [!IMPORTANT]
   > アプリケーションは、組織内の多くのユーザーが既に各自で同意している場合でも、テナント全体の管理者の同意を許可する前に慎重に評価してください。

3. 承認された各アプリケーションについて、以下に記載されているいずれかの方法を使用して、テナント全体の管理者の同意を許可します。

4. 承認されたアプリケーションごとに、[ユーザー アクセスを制限すること](configure-user-consent.md)を検討してください。

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>テナント全体の管理者の同意要求を評価する

テナント全体の管理者の同意を許可する際は、慎重に行う必要があります。  アクセス許可は、組織全体に代わって許可され、高度な権限を必要とする操作を試行するためのアクセス許可が含まれることがあります。 たとえば、ロール管理、すべてのメールボックスまたはすべてのサイトへのフルアクセス、完全なユーザー偽装などです。

テナント全体の管理者の同意を許可する前に、許可するアクセス レベルに対して、アプリケーションとアプリケーションの発行元を信頼していることを確認する必要があります。 アプリケーションを制御しているユーザーと、アプリケーションがなぜアクセス許可を要求しているのか確信を持てない場合は、*同意を許可しないでください*。

次の一覧に、管理者の同意を求める要求を評価する際に考慮すべき推奨事項を示します。

* **Microsoft ID プラットフォームでの[アクセス許可と同意フレームワーク](../develop/consent-framework.md)について理解する。**

* **[委任されたアクセス許可とアプリケーションのアクセス許可の違い](../develop/v2-permissions-and-consent.md#permission-types)について理解する。**

   アプリケーションのアクセス許可を許可すると、アプリケーションはユーザーの介入を必要とせずに組織全体のデータにアクセスできます。 委任されたアクセス許可を使用すると、ある時点でアプリケーションにサインインしたユーザーの代わりにアプリケーションを操作できます。

* **要求されているアクセス許可を理解します。**

   アプリケーションによって要求されたアクセス許可は、[同意プロンプト](../develop/application-consent-experience.md)に一覧表示されます。 アクセス許可のタイトルを展開すると、アクセス許可の説明が表示されます。 アプリケーションのアクセス許可の説明は、通常 "サインインしたユーザーなし" で終わります。 委任されたアクセス許可の説明は、通常 "サインインしたユーザーの代わりに" で終わります。 Microsoft Graph API のアクセス許可については、[Microsoft Graph のアクセス許可のリファレンス] で説明されています。他の API が公開するアクセス許可については、ドキュメントを参照してください。

   要求されているアクセス許可を把握していない場合は、*同意を許可しないでください*。

* **アクセス許可を要求しているアプリケーションとアプリケーションの発行元を把握します。**

   他のアプリケーションに偽装している悪意のあるアプリケーションに注意してください。

   アプリケーションまたはその発行元の正当性が不明な場合は、*同意を許可しないでください*。 代わりに、追加の確認 (たとえば、アプリケーションの発行元から直接) を取ります。

* **要求されたアクセス許可が、アプリケーションに期待される機能と一致していることを確認します。**

   たとえば、SharePoint サイト管理を提供するアプリケーションでは、すべてのサイト コレクションの読み取りに委任アクセスを求める場合がありますが、すべてのメールボックスへのフルアクセス、またはディレクトリ内の完全な借用権限は必要ありません。

   アプリケーションが必要以上のアクセス許可を要求していると思われる場合は、*同意を許可しないでください*。 詳細については、アプリケーションの発行元にお問い合わせください。

## <a name="granting-consent-as-an-administrator"></a>管理者として同意を許可する

### <a name="granting-tenant-wide-admin-consent"></a>テナント全体の管理者の同意を許可する

テナント全体の管理者の同意を Azure portal、Azure AD PowerShell、または同意プロンプト自体から許可する手順については、「[アプリケーションに対してテナント全体の管理者の同意を許可する](grant-admin-consent.md)」を参照してください。

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>特定のユーザーに代わって同意を許可する

管理者は、組織全体に同意を許可するのではなく、[Microsft Graph API](https://docs.microsoft.com/graph/use-the-api) を使用して、1 人のユーザーに代わって委任されたアクセス許可に同意を許可することもできます。 詳細については、「[ユーザーの代わりにアクセスを取得](https://docs.microsoft.com/graph/auth-v2-user)」を参照してください。

## <a name="limiting-user-access-to-applications"></a>アプリケーションへのユーザー アクセスを制限する

テナント全体の管理者の同意が許可されている場合でも、ユーザーのアプリケーションへのアクセスは制限される可能性があります。 アプリケーションへのユーザー割り当てを要求する方法の詳細については、[ユーザーとグループを割り当てる方法](methods-for-assigning-users-and-groups.md)に関する記事を参照してください。

その他の複雑なシナリオの処理方法などについての詳細な概要については、[Azure AD を使用したアプリケーション アクセス管理](what-is-access-management.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

[ID インフラストラクチャをセキュリティ保護する 5 つのステップ](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[管理者の同意ワークフローの構成](configure-admin-consent-workflow.md)

[エンド ユーザーがアプリケーションに同意する方法の構成](configure-user-consent.md)

[Microsoft ID プラットフォームでのアクセス許可と同意](../develop/active-directory-v2-scopes.md)

[StackOverflow での Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)