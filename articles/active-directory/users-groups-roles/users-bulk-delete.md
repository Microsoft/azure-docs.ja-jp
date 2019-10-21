---
title: Azure Active Directory ポータルでユーザーを一括削除する (プレビュー) | Microsoft Docs
description: Azure Active Directory の Azure 管理センターでユーザーを一括削除する
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7c47887c12c8bf9be7a0c5b11dfb3f099965cb7
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174367"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Azure Active Directory でユーザーを一括削除する (プレビュー)

Azure Active Directory (Azure AD) ポータルでは、コンマ区切り値 (CSV) ファイルを使用してユーザーを一括削除することで、グループの多数のメンバーを削除できます。

## <a name="to-bulk-delete-users"></a>ユーザーを一括削除するには

1. 組織のユーザー管理者アカウントで、[ご自身の Azure AD 組織にサインイン](https://aad.portal.azure.com)します。
1. Azure AD で、 **[ユーザー]**  >  **[一括削除]** の順に選択します。
1. **[ユーザーの一括削除]** ページで **[ダウンロード]** を選択して、ユーザー プロパティの有効な CSV ファイルを受信します。

   ![削除するユーザーをリストするローカル CSV ファイルを選択する](./media/users-bulk-delete/bulk-delete.png)

1. この CSV ファイルを開いて、削除するユーザーごとに 1 行を追加します。 唯一の必須値は、**ユーザー プリンシパル名**です。 そのうえでファイルを保存します。

   ![CSV ファイルには、削除するユーザーの名前と ID が含まれています。](./media/users-bulk-delete/delete-csv-file.png)

1. **[ユーザーの一括削除 (プレビュー)]** ページの **[csv ファイルをアップロードします]** で、そのファイルを参照します。 ファイルを選択して [送信] をクリックすると、CSV ファイルの検証が開始されます。
1. ファイルの内容が検証されると、"**ファイルが正常にアップロードされました**" と表示されます。 エラーが存在する場合は、ジョブを送信する前にそれらを修正する必要があります。
1. ファイルの検証に合格したら、 **[送信]** を選択して、ユーザーを削除する Azure 一括操作を開始します。
1. 削除操作が完了すると、一括操作が成功したという通知が表示されます。

エラーが発生した場合は、 **[一括操作の結果]** ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

## <a name="check-status"></a>状態の確認

**[一括操作の結果 (プレビュー)]** ページでは、保留中のすべての一括要求の状態を確認できます。

   ![[一括操作の結果] ページでアップロードの状態を確認する](./media/users-bulk-delete/bulk-center.png)

次に、削除したユーザーが Azure AD 組織に存在するかどうかを、Azure portal または PowerShell を使用して確認できます。

## <a name="verify-deleted-users-in-the-azure-portal"></a>Azure portal で削除済みのユーザーを確認する

1. 組織のユーザー管理者アカウントで、Azure portal にサインインします。
1. ナビゲーション ペインで、 **[Azure Active Directory]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[表示]** で **[すべてのユーザー]** のみを選択し、削除したユーザーがリストされなくなったことを確認します。

### <a name="verify-deleted-users-with-powershell"></a>PowerShell で削除済みユーザーを確認する

次のコマンドを実行します。

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

削除したユーザーがリストされなくなったことを確認します。

## <a name="next-steps"></a>次の手順

- [ユーザーの一括追加](users-bulk-add.md)
- [ユーザーの一覧のダウンロード](users-bulk-download.md)
- [ユーザーの一括復元](users-bulk-restore.md)
