---
title: "Azure IoT Suite と Azure Active Directory | Microsoft Docs"
description: "Azure IoT Suite が Azure Active Directory を使用してアクセス許可を管理する方法を説明します。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/09/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 518e6a481ab6385b03dd3ddc2e155fb724e677fe
ms.contentlocale: ja-jp
ms.lasthandoff: 06/09/2017


---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>azureiotsuite.com サイトでのアクセス許可

## <a name="what-happens-when-you-sign-in"></a>サインイン時に行われること

ユーザーが [azureiotsuite.com][lnk-azureiotsuite] に初めてサインインすると、選択されている Azure Active Directory (AAD) テナントと Azure サブスクリプションに基づいて、ユーザーのアクセス許可レベルが決定されます。

1. サイトは最初に、ログイン ユーザー名の隣に表示されるテナントの一覧を設定するため、Azure からユーザーが属している AAD テナントを取得します。 現時点では、サイトは一度に 1 つのテナントのユーザー トークンしか取得できません。 このため、ユーザーが右上隅のドロップダウンを使用してテナントを切り替えると、サイトはユーザーをそのテナントにログインさせて、そのテナントのトークンを取得します。

2. 次に、サイトは、ユーザーが選択されているテナントと関連付けているサブスクリプションを Azure から検索します。 ユーザーが新しい構成済みソリューションを作成するときに、使用できるサブスクリプションが表示されます。

3. 最後に、サイトはサブスクリプション内および構成済みソリューションというタグが付いているリソース グループ内のすべてのリソースを取得して、ホーム ページのタイトルを設定します。

以下のセクションでは、構成済みソリューションへのアクセスを制御するロールについて説明します。

## <a name="aad-roles"></a>AAD のロール

AAD のロールは、構成済みソリューションをプロビジョニングし、構成済みソリューション内のユーザーを管理する権限を制御します。

ADD の管理者ロールの詳細については、「[Azure AD での管理者ロールの割り当て][lnk-aad-admin]」を参照してください。 この記事では、主に構成済みソリューションで使用される**グローバル管理者**ロールと**ユーザー** ディレクトリ ロールに重点を置いて説明しています。

### <a name="global-administrator"></a>グローバル管理者

AAD テナントごとに多数のグローバル管理者がいてもかまいません。

* AAD テナントを作成したユーザーは、既定でそのテナントのグローバル管理者になります。
* グローバル管理者は、構成済みソリューションをプロビジョニングでき、AAD テナントの内部にあるアプリケーションに対する **Admin** ロールを割り当てられます。
* 同じ AAD テナントの別のユーザーがアプリケーションを作成した場合は、グローバル管理者に付与される既定のロールは **ReadOnly** です。
* グローバル管理者は、[Azure Portal][lnk-portal] を使用して、アプリケーションに対するロールをユーザーに割り当てることができます。

### <a name="domain-user"></a>ドメイン ユーザー

AAD テナントごとに多数のドメイン ユーザーがいてもかまいません。

* ドメイン ユーザーは [azureiotsuite.com][lnk-azureiotsuite] サイトを使用して構成済みソリューションをプロビジョニングできます。 プロビジョニングされたアプリケーションでドメイン ユーザーに対して付与される既定のロールは **Admin** です。
* ドメイン ユーザーは、[azure-iot-remote-monitoring][lnk-rm-github-repo]、[azure-iot-predictive-maintenance][lnk-pm-github-repo]、または [azure-iot-connected-factory][lnk-cf-github-repo] リポジトリの build.cmd スクリプトを使用してアプリケーションを作成できますが、 ロールを割り当てるアクセス許可はドメイン ユーザーにはないので、ドメイン ユーザーに対して既定で付与されるロールは **ReadOnly** となります。
* AAD テナントの別のユーザーが作成したアプリケーションに関して、ドメイン ユーザーに既定で割り当てられるロールは **ReadOnly** です。
* ドメイン ユーザーがアプリケーションのロールを割り当てることはできません。そのため、自分でプロビジョニングしたアプリケーションであっても、ドメイン ユーザーは、ユーザーまたはユーザーのロールを追加することはできません。

### <a name="guest-user"></a>ゲスト ユーザー

AAD テナントごとに多数のゲスト ユーザーがいてもかまいません。 ゲスト ユーザーの AAD テナントでの権限セットは制限されています。 したがって、ゲスト ユーザーは AAD テナントで構成済みソリューションをプロビジョニングできません。

AAD におけるユーザーとロールの詳細については、次のリソースを参照してください。

* [Azure AD でユーザーを作成する][lnk-create-edit-users]
* [ユーザーをアプリに割り当てる][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Azure サブスクリプション管理者ロール

Azure 管理者ロールは、Azure サブスクリプションを AD テナントにマップする機能を制御します。

Azure 管理者ロールの詳細については、[Azure 共同管理者、サービス管理者、アカウント管理者を追加または変更する方法][lnk-admin-roles]に関する記事を参照してください。

## <a name="application-roles"></a>アプリケーション ロール

アプリケーション ロールは、構成済みソリューション内のデバイスへのアクセスを制御します。

プロビジョニングしたアプリケーションには、2 つの定義済みロールと 1 つの暗黙ロールが定義されます。

* **Admin:** デバイスの追加、管理、削除、および設定の変更をすべて実行できます。
* **ReadOnly:** デバイス、ルール、アクション、ジョブ、およびテレメトリを表示できます。

[RolePermissions.cs][lnk-resource-cs] ソース ファイルで、各ロールに割り当てられるアクセス許可を確認できます。

### <a name="changing-application-roles-for-a-user"></a>ユーザーのアプリケーション ロールの変更

次の手順を使用して、Active Directory のユーザーを構成済みソリューションの管理者にできます。

ユーザーのロールを変更できるのは、AAD グローバル管理者だけです。

1. [Azure Portal][lnk-portal] にアクセスします。
2. **[Azure Active Directory]** を選択します。
3. ソリューションをプロビジョニングしたときに azureiotsuite.com で選択したディレクトリを必ず使用します。 サブスクリプションに複数のディレクトリが関連付けられている場合は、ポータルの右上にあるアカウント名をクリックすることで、ディレクトリを切り替えることができます。
4. **[エンタープライズ アプリケーション]**、**[すべてのアプリケーション]** の順にクリックします。
4. **任意の**状態の**すべてのアプリケーション**を表示します。 構成済みのソリューションの名前でアプリケーションを検索します。
5. 構成済みソリューション名と一致するアプリケーションの名前をクリックします。
6. **[ユーザーとグループ]** をクリックします。
7. ロールを切り替えるユーザーを選択します。
8. **[割り当て]** をクリックし、ユーザーに割り当てるロール (**[管理者]** など) を選択して、チェック マークをクリックします。

## <a name="faq"></a>FAQ

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>サービス管理者が自分のサブスクリプションと特定の AAD テナントの間のディレクトリ マッピングを変更する必要がある場合は、 どうすればよいですか

1. [Azure クラシック ポータル][lnk-classic-portal]に移動し、左側のサービスの一覧で **[設定]** をクリックします。
2. ディレクトリ マッピングを変更するサブスクリプションを選択します。
3. **[ディレクトリの編集]** をクリックします。
4. ドロップダウン リストで使用する **ディレクトリ** を選択します。 右向きの矢印をクリックします。
5. ディレクトリのマッピングと影響を受ける共同管理者を確認します。 別のディレクトリから移動する場合、元のディレクトリのすべての共同管理者が削除されます。

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>AAD テナントのドメイン ユーザー/メンバーが構成済みソリューションを作成した場合、 アプリケーションに対するロールを割り当ててもらうにはどうすればよいですか

グローバル管理者に依頼して AAD テナントでのグローバル管理者を割り当ててもらい、自分でユーザーにロールを割り当てるか、 またはグローバル管理者に直接ロールの割り当てを依頼します。 構成済みソリューションをデプロイした AAD テナントを変更したい場合は、次の質問を参照してください。

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>リモート監視構成済みソリューションおよびアプリケーションが割り当てられている AAD テナントを切り替えるにはどうすればよいですか

<https://github.com/Azure/azure-iot-remote-monitoring> からクラウドのデプロイを実行し、新しく作成された AAD テナントで再デプロイできます。 新しい AAD テナントを作成したユーザーは既定でグローバル管理者になるので、ユーザーを追加し、ユーザーにロールを割り当てることができます。

1. [Azure ポータル][lnk-portal]で AAD ディレクトリを作成します。
2. <https://github.com/Azure/azure-iot-remote-monitoring> に移動します。
3. `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` を実行します (例: `build.cmd cloud debug myRMSolution`)
4. プロンプトが表示されたら、古いテナントの代わりに新しく作成したテナントの **tenantid** を設定します。

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>組織アカウントでログインするときにサービス管理者または共同管理者を変更するにはどうすればよいですか

「[組織アカウントを利用してログインしたときのサービス管理者および共同管理者の変更][lnk-service-admins]」を参照してください。

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>次のエラーが表示されるのはなぜですか。 「お使いのアカウントにはソリューションを作成する適切なアクセス許可がありません。 アカウント管理者に確認するか、別のアカウントを使用してください。」

次の図を見てみましょう。

![][img-flowchart]

> [!NOTE]
> 自分が AAD テナントの全体管理者になっていることとサブスクリプションの共同管理者になっていることを確認した後もエラーが表示される場合は、ユーザーを削除してから必要なアクセス許可を再度割り当てるよう、アカウント管理者に依頼してください。 その場合はまず、ユーザーを全体管理者として追加したうえで、Azure サブスクリプションの共同管理者としてそのユーザーを追加することになります。 問題が解決しない場合は、[ヘルプとサポート][lnk-help-support]にお問い合わせください。

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Azure サブスクリプションがあるのに次のエラーが表示されるのはなぜですか。 「構成済みソリューションを作成するには Azure サブスクリプションが必要です。 数分で無料試用版のアカウントを作成することができます。」

Azure サブスクリプションが確かにある場合は、サブスクリプションのテナント マッピングを調べ、ドロップダウンで正しいテナントが選択されていることを確認してください。 目的のテナントが正しいことを確認できた場合は、上の図に従って、サブスクリプションとこの AAD テナントのマッピングを確認してください。

## <a name="next-steps"></a>次のステップ
IoT Suite について引き続き学習するには、[構成済みソリューションをカスタマイズする][lnk-customize]方法を参照してください。

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md

