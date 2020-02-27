---
title: Azure Storage リソース プロバイダーを使用して管理リソースにアクセスする
description: Azure Storage リソース プロバイダーは、Azure Storage の管理リソースへのアクセスを提供するサービスです。 Azure Storage リソース プロバイダーを使用すると、ストレージ アカウント、プライベート エンドポイント、アカウント アクセス キーなどのリソースを作成、更新、管理、および削除できます。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5d42a6a0567d3949bc4b0fb1947450a9c957f18
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75972345"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Azure Storage リソース プロバイダーを使用して管理リソースにアクセスする

Azure Resource Manager は、Azure のデプロイおよび管理サービスです。 Azure Storage リソース プロバイダーは、Azure Resource Manager に基づき、Azure Storage の管理リソースへのアクセスを提供するサービスです。 Azure Storage リソース プロバイダーを使用すると、ストレージ アカウント、プライベート エンドポイント、アカウント アクセス キーなどのリソースを作成、更新、管理、および削除できます。 Azure Resource Manager の詳細については、「[Azure Resource Manager の概要](/azure/azure-resource-manager/resource-group-overview)」を参照してください。

Azure Storage リソース プロバイダーを使用すると、ストレージ アカウントの作成または削除、サブスクリプション内のストレージ アカウント一覧の取得などのアクションを実行できます。 Azure Storage リソース プロバイダーに対する要求を承認するには、Azure Active Directory (Azure AD) を使用します。 この記事では、管理リソースにアクセス許可を割り当てる方法について説明し、Azure Storage リソース プロバイダーに対して要求を行う方法を示す例を紹介します。

## <a name="management-resources-versus-data-resources"></a>管理リソースとデータ リソース

Microsoft からは、Azure Storage リソースを操作する 2 つの REST API が用意されています。 これらの API は、Azure Storage に対して実行できるすべてのアクションの基礎を形成します。 Azure Storage REST API を使用すると、ストレージ アカウントのデータ (BLOB、キュー、ファイル、テーブル データなど) を操作できます。 Azure Storage リソース プロバイダー REST API を使用すると、ストレージ アカウントと関連リソースを操作できます。

BLOB データを読み取りまたは書き込みを行う要求には、管理操作を実行する要求とは異なるアクセス許可が必要です。 RBAC を使うと、両方の種類のリソースへのアクセス許可をきめ細かく制御できます。 RBAC ロールをセキュリティ プリンシパルに割り当てる場合は、必ずそのプリンシパルに付与されるアクセス許可を理解してください。 各組み込み RBAC ロールに関連付けられているアクションが説明された詳細なリファレンスについては、「[Azure リソースの組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

Azure Storage は、Azure AD を使用して Blob および Queue ストレージに対する要求を承認する処理をサポートしています。 BLOB およびキュー データ操作の RBAC ロールの詳細については、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](storage-auth-aad.md)」を参照してください。

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>ロールベースのアクセス制御 (RBAC) を使用して管理アクセス許可を割り当てる

すべての Azure サブスクリプションには、ユーザー、グループ、アプリケーションを管理する Azure Active Directory が関連付けられています。 ユーザー、グループ、アプリケーションは、[Microsoft ID プラットフォーム](/azure/active-directory/develop/)のコンテキストではセキュリティ プリンシパルと呼ばれることもあります。 ロールベースのアクセス制御 (RBAC) を使用し、Active Directory で定義されているセキュリティ プリンシパルに対して、サブスクリプションのリソースへのアクセス権を付与できます。

RBAC ロールをセキュリティ プリンシパルに割り当てるときに、ロールによって付与されたアクセス許可を有効にするスコープも指定します。 管理操作の場合、サブスクリプション、リソース グループ、またはストレージ アカウントのレベルでロールを割り当てることができます。 セキュリティ プリンシパルに RBAC ロールを割り当てるには、[Azure portal](https://portal.azure.com/)、[Azure CLI ツール](../../cli-install-nodejs.md)、[PowerShell](/powershell/azureps-cmdlets-docs)、または [Azure Storage リソース プロバイダー REST API](/rest/api/storagerp) を使用します。

RBAC の詳細については、「[Azure リソースのロールベースのアクセス制御 (RBAC) の概要](../../role-based-access-control/overview.md)」と「[従来のサブスクリプション管理者ロール、Azure RBAC ロール、および Azure AD 管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。

### <a name="built-in-roles-for-management-operations"></a>管理操作のための組み込みロール

Azure には、管理操作を呼び出すアクセス許可を付与する組み込みロールが用意されています。 Azure Storage には、Azure Storage リソース プロバイダー専用の組み込みロールも用意されています。

ストレージ管理操作を呼び出すアクセス許可を付与する組み込みのロールには、次の表で説明するロールが含まれています。

|    RBAC ロール    |    説明    |    アカウント キーへのアクセスは含まれるか?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **[所有者]** | すべてのストレージ リソースを管理し、リソースにアクセスできます。  | はい、ストレージ アカウント キーを表示および再生成するアクセス許可が付与されます。 |
| **Contributor**  | すべてのストレージ リソースを管理できますが、リソースへの割り当てを管理することはできません。 | はい、ストレージ アカウント キーを表示および再生成するアクセス許可が付与されます。 |
| **Reader** | ストレージ アカウントに関する情報は表示できますが、アカウント キーを表示することはできません。 | いいえ。 |
| **Storage Account Contributor** | ストレージ アカウントの管理、サブスクリプションのリソース グループとリソースに関する情報の取得、サブスクリプションのリソース グループのデプロイの作成と管理を行うことができます。 | はい、ストレージ アカウント キーを表示および再生成するアクセス許可が付与されます。 |
| **User Access Administrator** | ストレージ アカウントへのアクセスを管理できます。   | はい。セキュリティ プリンシパルに対して、自分や他のユーザーにアクセス許可を割り当てることを許可します。 |
| **Virtual Machine Contributor** | 仮想マシンを管理できますが、その接続先のストレージ アカウントは管理できません。   | はい、ストレージ アカウント キーを表示および再生成するアクセス許可が付与されます。 |

表の 3 列目は、組み込みロールが **Microsoft.Storage/storageAccounts/listkeys/action** をサポートしているかどうかを示します。 このアクションにより、ストレージ アカウント キーの読み取りと再生成を行うアクセス許可が付与されます。 Azure Storage 管理リソースにアクセスするアクセス許可には、データにアクセスするアクセス許可は含まれていません。 ただし、ユーザーがアカウント キーにアクセスできる場合は、そのアカウント キーを使用して、共有キー認証を介して Azure Storage データにアクセスできます。

### <a name="custom-roles-for-management-operations"></a>管理操作のカスタム ロール

Azure は、管理リソースにアクセスするためのカスタム RBAC ロールの定義もサポートしています。 カスタム ロールの詳細については、「[Azure リソースのカスタム ロール](../../role-based-access-control/custom-roles.md)」を参照してください。

## <a name="code-samples"></a>コード サンプル

Azure Storage 管理ライブラリから管理操作を承認し、呼び出す方法を示すコード例については、次のサンプルを参照してください。

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager とクラシック デプロイ

Resource Manager デプロイ モデルとクラシック デプロイ モデルは、Azure ソリューションのデプロイと管理における 2 種類の異なる方法です。 新しいストレージ アカウントを作成するときは、Azure Resource Manager デプロイ モデルを使用することをお勧めします。 可能であれば、Resource Manager モデルを使用して既存のクラシック ストレージ アカウントを再作成することもお勧めします。 クラシック デプロイ モデルを使用してストレージ アカウントを作成することはできますが、クラシック モデルは柔軟性が低く、最終的に非推奨になる予定です。

Azure デプロイ モデルの詳細については、「[Resource Manager デプロイとクラシック デプロイ](../../azure-resource-manager/management/deployment-models.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure リソース マネージャーの概要](/azure/azure-resource-manager/resource-group-overview)
- [Azure リソースのロールベースのアクセス制御 (RBAC) の概要](../../role-based-access-control/overview.md)
- [Azure Storage リソース プロバイダーのスケーラビリティ ターゲット](scalability-targets-resource-provider.md)
