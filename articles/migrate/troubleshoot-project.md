---
title: Azure Migrate プロジェクトのトラブルシューティング
description: Azure Migrate プロジェクトの作成と管理に関する問題のトラブルシューティングに役立ちます。
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: 3b8c2f6ec33965317d2aaa23a36b6becff11a54a
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722128"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Azure Migrate プロジェクトのトラブルシューティング

この記事は、[Azure Migrate](migrate-services-overview.md) プロジェクトを作成および管理するときの問題のトラブルシューティングに役立ちます。

## <a name="how-to-add-new-project"></a>新しいプロジェクトを追加するには

1 つのサブスクリプションに複数の Azure Migrate プロジェクトを含めることができます。 初めてプロジェクトを作成する方法、またはプロジェクトを[さらに追加する](create-manage-projects.md#create-additional-projects)方法について[説明します](how-to-add-tool-first-time.md)。

## <a name="what-azure-permissions-are-needed"></a>必要な Azure アクセス許可とは

Azure Migrate プロジェクトを作成するには、サブスクリプションに共同作成者または所有者アクセス許可が必要です。

## <a name="cant-find-a-project"></a>プロジェクトが見つからない

既存の Azure Migrate プロジェクトの検索方法は、Azure Migrate の現在のバージョンを使用しているか、以前のバージョンを使用しているかによって異なります。 [こちらに従ってください](create-manage-projects.md#find-a-project)。


## <a name="cant-find-a-geography"></a>地域が見つからない

Azure Migrate プロジェクトは、[サポートされている地域](migrate-support-matrix.md#supported-geographies)で作成できます。 プロジェクトの地域は、検出されたマシン メタデータを格納するために使用されます。 他の場所のマシンも評価または移行できます。

## <a name="what-are-vm-limits"></a>VM の制限とは

1 つのプロジェクトで最大 35,000 の VMware VM または最大 35,000 の Hyper-V VM を評価できます。 評価の上限に達するまでは、1 つのプロジェクトに VMware VM と Hyper-V VM の両方を含めることができます。

## <a name="can-i-upgrade-old-project"></a>古いプロジェクトをアップグレードできますか

以前のバージョンの Azure Migrate のプロジェクトは更新できません。 [新しいプロジェクトを作成して](how-to-add-tool-first-time.md)、ツールを追加する必要があります。

## <a name="cant-create-a-project"></a>プロジェクトを作成できない

プロジェクトを作成しようとして、デプロイ エラーが発生する場合は、次の手順に従います。

- 一時的なエラーの場合は、プロジェクトの再作成をお試しください。 再試行するには、 **[Deployments]\(デプロイ\)** で **[Re-deploy]\(再デプロイ\)** をクリックします。
- サブスクリプションの共同作成者または所有者のアクセス許可があることを確認します。
- 新しく追加された地域でデプロイする場合は、しばらく待ってから再試行してください。
- エラー "要求にはユーザー ID ヘッダーが含まれていなければなりません" が発生した場合、組織の Azure Active Directory (Azure AD) テナントへのアクセス権がないことが示されている可能性があります。 この場合、次のようになります。
    - ユーザーは、Azure AD テナントに初めて追加されるときに、テナントへの参加を求める招待メールを受け取ります。
    - テナントに追加されるには、招待を承認します。
    - メールが表示されない場合は、テナントへのアクセス権を持つユーザーに連絡し、[招待を再送信する](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)ように依頼してください。
    - 招待メールを受け取った後、メールを開き、リンクを選択して招待を承諾します。 その後、Azure portal からサインアウトし、改めてサインインします。 (ブラウザーの更新は機能しません。)これで、移行プロジェクトの作成を始めることができます。

## <a name="how-do-i-delete-a-project"></a>プロジェクトを削除する方法を教えてください

[こちらの手順に従って](create-manage-projects.md#delete-a-project)、プロジェクトを削除します。 プロジェクトを削除すると、プロジェクトと、そのプロジェクト内の検出されたマシンに関するメタデータが両方とも削除されます。

## <a name="added-tools-dont-show"></a>追加したツールが表示されない

適切なプロジェクトが選択されていることを確認します。 Azure Migrate ハブ > **[サーバー]** または **[データベース]** で、画面の右上隅にある **[Migrate project (Change)]\(移行プロジェクト (変更)\)** の横にある **[変更]** をクリックします。 正しいサブスクリプションとプロジェクト名を選択し、 **[OK]** をクリックします。 選択したプロジェクトの追加したツールでページが更新されるはずです。

## <a name="next-steps"></a>次のステップ

[評価](how-to-assess.md)または[移行](how-to-migrate.md)ツールを Azure Migrate プロジェクトに追加します。