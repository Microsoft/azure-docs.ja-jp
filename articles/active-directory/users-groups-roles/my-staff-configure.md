---
title: マイ スタッフを使用してユーザーの管理を委任する (プレビュー) - Azure AD | Microsoft Docs
description: マイ スタッフと管理単位を使用して、ユーザーの管理を委任します
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 05/08/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: da358ecb126dda21d18120bbe698fec234c85e4f
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798960"
---
# <a name="manage-your-users-with-my-staff-preview"></a>マイ スタッフを使用してユーザーを管理する (プレビュー)

マイ スタッフを使用すると、ストア マネージャーやチーム リーダーなどの権限のある人に、スタッフ メンバーが Azure AD アカウントにアクセスできるようにするためのアクセス許可を委任することができます。 組織では、パスワードのリセットや電話番号の変更などの一般的なタスクを、中央のヘルプデスクに頼るのではなく、チーム マネージャーに任せることができます。 マイ スタッフを使用すると、自分のアカウントにアクセスできないユーザーは、数回のクリックでアクセスを回復することができ、ヘルプデスクや IT スタッフは必要ありません。

組織のためにマイ スタッフを構成する前に、このドキュメントと[ユーザー ドキュメント](../user-help/my-staff-team-manager.md)を確認し、この機能と、それがユーザーに及ぼす影響を、理解することをお勧めします。 ユーザーのドキュメントを利用して、新しいエクスペリエンスに備えてユーザーを訓練し、準備することで、ロールアウトを成功させることができます。

ユーザー用の SMS ベース認証は、Azure Active Directory のパブリック プレビュー機能です。 プレビューの詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください

## <a name="how-my-staff-works"></a>マイ スタッフのしくみ

マイ スタッフの基になっている管理単位 (AU) は、ロール割り当ての管理制御の範囲を制限するために使用できるリソースのコンテナーです。 マイ スタッフでは、AU を使用して、店舗や部署などの組織のユーザーのサブセットを定義します。 それにより、たとえば、スコープが 1 つまたは複数の AU であるロールにチーム マネージャーを割り当てることができます。 次の例では、ユーザーに認証管理者ロールが付与されており、そのロールのスコープは 3 つの AU です。 管理単位の詳細については、「[Azure Active Directory の管理単位の管理](directory-administrative-units.md)」を参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション

  * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* お使いのサブスクリプションに関連付けられている Azure Active Directory テナント。

  * 必要に応じて、[Azure Active Directory テナントを作成](../fundamentals/sign-up-organization.md)するか、[ご利用のアカウントに Azure サブスクリプションを関連付け](../fundamentals/active-directory-how-subscriptions-associated-directory.md)ます。
* SMS ベース認証を有効にするには、Azure AD テナントでの "*グローバル管理者*" 特権が必要です。
* テキスト メッセージ認証方法ポリシーで有効になっている各ユーザーは、その方法を使用しない場合でも、ライセンスを取得している必要があります。 有効な各ユーザーは、次の Azure AD または Microsoft 365 ライセンスのいずれかを保持している必要があります。

  * [Azure AD Premium P1 または P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 または F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise Mobility + Security (EMS) E3 または E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) または [Microsoft 365 (M365) E3 または E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>マイ スタッフを有効にする方法

Au を構成した後は、マイ スタッフにアクセスするユーザーにこのスコープを適用できます。 管理者ロールを割り当てられたユーザーだけが、マイ スタッフにアクセスできます。 マイ スタッフを有効にするには、次の手順のようにします。

1. ユーザー管理者として Azure portal にサインインします。
2. **[Azure Active Directory]**  >  **[ユーザー設定]**  >  **[ユーザー機能プレビュー]**  >  **[ユーザー機能プレビュー設定の管理]** に移動します。
3. **[管理者はマイ スタッフにアクセスできます]** では、すべてのユーザーまたは選択したユーザーにアクセスを許可したり、すべてのユーザーにアクセスを禁止したりできます。

> [!Note]
> 管理者ロールを割り当てられたユーザーだけが、マイ スタッフにアクセスできます。 管理者ロールが割り当てられていないユーザーに対してマイ スタッフを有効にすると、そのユーザーはマイ スタッフにアクセスできなくなります。

## <a name="conditional-access"></a>条件付きアクセス

Azure AD 条件付きアクセス ポリシーを使用して、マイ スタッフ ポータルを保護することができます。 これは、マイ スタッフにアクセスする前に多要素認証を要求するなどのタスクに使用します。

Microsoft では、[Azure AD 条件付きアクセス ポリシー](../conditional-access/index.yml)を使用してマイ スタッフを保護することを強くお勧めします。 マイ スタッフに条件付きアクセス ポリシーを適用するには、PowerShell を使用して、マイ スタッフのサービス プリンシパルを手動で作成する必要があります。

### <a name="apply-a-conditional-access-policy-to-my-staff"></a>マイ スタッフに条件付きアクセス ポリシーを適用する

1. [Microsoft Graph ベータ版の PowerShell コマンドレット](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/samples/0-InstallModule.ps1)をインストールします。
1. 次のコマンドを実行します。

   ```powershell
   Connect-Graph -Scopes "Directory.AccessAsUser.All"
   New-MgServicePrincipal -DisplayName "My Staff" -AppId "ba9ff945-a723-4ab5-a977-bd8c9044fe61"
   ```
1. マイ スタッフ クラウド アプリケーションに適用する条件付きアクセス ポリシーを作成します。

    ![マイ スタッフ アプリの条件付きアクセス ポリシーを作成する](media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>マイ スタッフの使用

ユーザーがマイ スタッフに移動すると、自分が管理者アクセス許可を持つ[管理単位](directory-administrative-units.md)の名前が表示されます。 [マイ スタッフのユーザー ドキュメント](../user-help/my-staff-team-manager.md)では、"場所" という用語を使用して管理単位が示されています。 管理者のアクセス許可に AU スコープがない場合、アクセス許可は組織全体に適用されます。 マイ スタッフが有効にされた後、有効にされて管理者ロールが割り当てられているユーザーは、[https://mystaff.microsoft.com](https://mystaff.microsoft.com) を使用してマイ スタッフにアクセスできます。 AU を選択してその AU 内のユーザーを表示したり、ユーザーを選択してプロファイルを開いたりすることができます。

## <a name="reset-a-users-password"></a>ユーザーのパスワードのリセット

次のロールには、ユーザーのパスワードをリセットするアクセス許可があります。

- [認証管理者](directory-assign-admin-roles.md#authentication-administrator)
- [特権認証管理者](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [グローバル管理者](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [ヘルプデスク管理者](directory-assign-admin-roles.md#helpdesk-administrator)
- [ユーザー管理者](directory-assign-admin-roles.md#user-administrator)
- [パスワード管理者](directory-assign-admin-roles.md#password-administrator)

**[マイ スタッフ]** で、ユーザーのプロファイルを開きます。 **[パスワードのリセット]** を選択します。

- ユーザーがクラウドのみの場合は、ユーザーに提供できる一時的なパスワードを確認できます。
- ユーザーがオンプレミスの Active Directory から同期されている場合は、オンプレミスの AD ポリシーを満たすパスワードを入力できます。 その後、そのパスワードをユーザーに提供できます。

    ![パスワード リセット進行状況インジケーターと成功通知](media/my-staff-configure/reset-password.png)

ユーザーは、次にサインインしたときに、パスワードの変更を要求されます。

## <a name="manage-a-phone-number"></a>電話番号を管理する

**[マイ スタッフ]** で、ユーザーのプロファイルを開きます。

- ユーザーに電話番号を追加するには、 **[電話番号の追加]** セクションを選択します
- 電話番号を変更するには、 **[電話番号の編集]** を選択します
- ユーザーの電話番号を削除するには、 **[電話番号の削除]** を選択します

設定に応じて、ユーザーは、設定された電話番号を使用して SMS でサインインし、多要素認証を実行して、セルフサービス パスワード リセットを実行できます。

ユーザーの電話番号を管理するには、次のいずれかのロールが割り当てられている必要があります。

- [認証管理者](directory-assign-admin-roles.md#authentication-administrator)
- [特権認証管理者](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [グローバル管理者](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>検索

マイ スタッフの検索バーを使用して、組織内の AU とユーザーを検索できます。 組織内のすべての AU とユーザーを検索できますが、変更できるのは、自分が管理者アクセス許可を付与されている AU 内のユーザーだけです。

AU 内のユーザーを検索することもできます。 これを行うには、ユーザー一覧の上部にある検索バーを使用します。

## <a name="audit-logs"></a>監査ログ

Azure Active Directory ポータルでは、マイ スタッフにおいて行われた操作の監査ログを見ることができます。 マイ スタッフで行われた操作によって監査ログが生成された場合、監査イベントの [ADDITIONAL DETAILS]\(追加の詳細\) の下にこのことが示されます。

## <a name="next-steps"></a>次のステップ

[マイ スタッフのユーザー ドキュメント](../user-help/my-staff-team-manager.md)
[管理単位のドキュメント](directory-administrative-units.md)