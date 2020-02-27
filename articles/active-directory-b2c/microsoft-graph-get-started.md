---
title: Microsoft Graph を使用してリソースを管理する
titleSuffix: Azure AD B2C
description: 必要な Graph API アクセス許可が付与されたアプリケーションを登録することにより、Microsoft Graph を使用した Azure AD B2C リソースの管理について準備します。
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bcddcf051e73381af35ca40e22c443c7b9ae30a7
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492364"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Microsoft Graph を使用して Azure AD B2C を管理する

[Microsoft Graph][ms-graph] を使用すると、顧客のユーザー アカウントやカスタム ポリシーなど、Azure AD B2C テナント内の多くのリソースを管理できます。 [Microsoft Graph API][ms-graph-api] を呼び出すスクリプトまたはアプリケーションを作成することによって、次のようなテナント管理タスクを自動化できます。

* 既存のユーザー ストアを Azure AD B2C テナントに移行する
* Azure DevOps で Azure Pipelines を使用してカスタム ポリシーをデプロイし、カスタム ポリシー キーを管理する
* 自分のページでユーザー登録をホストし、バックグラウンドで Azure AD B2C ディレクトリにユーザー アカウントを作成する
* アプリケーションの登録を自動化する
* 監査ログを取得する

次のセクションでは、Microsoft Graph API を使用して Azure AD B2C ディレクトリ内のリソースの管理を自動化するための準備について説明します。

## <a name="microsoft-graph-api-interaction-modes"></a>Microsoft Graph API の相互作用モード

Microsoft Graph API を使用して Azure AD B2C テナントのリソースを管理する際は、次の 2 つの通信モードを使用できます。

* **対話型** - 一度だけ実行されるタスクに適切です。B2C テナントの管理者アカウントを使用して管理タスクを実行します。 このモードでは、Microsoft Graph API を呼び出す前に、管理者は自分の資格情報を使用してサインインする必要があります。

* **自動** - スケジュールされた、または継続的に実行されるタスクの場合、この方法では、管理タスクを実行するために必要なアクセス許可で構成したサービス アカウントが使用されます。 Azure AD B2C に "サービスアカウント" を作成するには、アプリケーションとスクリプトが*アプリケーション (クライアント) ID* と OAuth 2.0 のクライアント資格情報付与を使用した認証に使用するアプリケーションを登録します。 この場合、アプリケーションは、前に説明した対話型の方法のように管理者ユーザーとしてではなく、それ自体として Microsoft Graph API を呼び出します。

**自動**の相互作用シナリオを有効にするには、次のセクションで示すアプリケーション登録を作成します。

## <a name="register-management-application"></a>管理アプリケーションを登録する

スクリプトとアプリケーションが [Microsoft Graph API][ms-graph-api] と対話して Azure AD B2C リソースを管理できるようにするには、必要な API アクセス許可を付与するアプリケーション登録を Azure AD B2C テナントに作成する必要があります。

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>API アクセスの許可

次に、Microsoft Graph API の呼び出しによってテナント リソースを操作するための登録済みアプリケーションのアクセス許可を付与します。

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>クライアント シークレットを作成する

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

これで、Azure AD B2C テナントでユーザーの "*作成*"、"*読み取り*"、"*更新*"、および "*削除*" を実行するアクセス許可を持つアプリケーションが用意されました。 次のセクションに進み、"*パスワードの更新*" アクセス許可を追加します。

## <a name="enable-user-delete-and-password-update"></a>ユーザーの削除とパスワードの更新を有効にする

*[ディレクトリ データの読み取りと書き込み]* アクセス許可には、ユーザーを削除したり、ユーザー アカウントのパスワードを更新したりする権限は含まれて**いません**。

アプリケーションまたはスクリプトでユーザーを削除したりパスワードを更新したりする必要がある場合は、"*ユーザー管理者*" のロールをアプリケーションに割り当ててください。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[ディレクトリ + サブスクリプション]** フィルターを使用して Azure AD B2C テナントに切り替えます。
1. **Azure AD B2C** を検索して選択します。
1. **[管理]** で **[ロールと管理者]** を選択します。
1. **[ユーザー管理者]** ロールを選択します。
1. **[割り当ての追加]** を選択します。
1. **[選択]** テキスト ボックスに、前に登録したアプリケーションの名前 (たとえば、*managementapp1*) を入力します。 検索結果に表示されたらアプリケーションを選択します。
1. **[追加]** を選択します。 アクセス許可が完全に反映されるまでに数分かかる場合があります。

## <a name="next-steps"></a>次のステップ

管理アプリケーションを登録し、必要なアクセス許可を付与したので、お使いのアプリケーションとサービス (たとえば、Azure Pipelines) はその資格情報とアクセス許可を使用して、Microsoft Graph API と対話することができます。

* [Microsoft Graph でサポートされている B2C 操作](microsoft-graph-operations.md)
* [Microsoft Graph を使用して Azure AD B2C ユーザー アカウントを管理する](manage-user-accounts-graph-api.md)
* [Azure AD Reporting API を使って監査ログを取得する](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
