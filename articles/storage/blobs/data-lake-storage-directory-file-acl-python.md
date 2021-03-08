---
title: ファイルと ACL 用の Azure Data Lake Storage Gen2 Python SDK
description: Python を使用して、階層型名前空間 (HNS) が有効なストレージ アカウントでディレクトリ、ファイル、ディレクトリのアクセス制御リスト (ACL) を管理します。
author: normesta
ms.service: storage
ms.date: 01/22/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: 5036930c7bb49578582fbc1b347b11518579b53e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740620"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Python を使用して Azure Data Lake Storage Gen2 のディレクトリ、ファイル、ACL を管理する

この記事では、Python を使用して、階層型名前空間 (HNS) が有効なストレージ アカウントでディレクトリ、ファイル、アクセス許可を作成および管理する方法について説明します。 

[パッケージ (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/) | [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API リファレンス](/python/api/azure-storage-file-datalake/azure.storage.filedatalake) | [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [フィードバックを送る](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
> * 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](../common/storage-account-create.md)の手順に従います。

## <a name="set-up-your-project"></a>プロジェクトの設定

Python 向けの Azure Data Lake Storage クライアント ライブラリを、[pip](https://pypi.org/project/pip/) を使用してインストールします。

```
pip install azure-storage-file-datalake
```

次の import ステートメントを、コード ファイルの先頭に追加します。

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>アカウントに接続する

この記事のスニペットを使用するには、ストレージ アカウントを表す **DataLakeServiceClient** インスタンスを作成する必要があります。 

### <a name="connect-by-using-an-account-key"></a>アカウント キーを使用して接続する

これはアカウントに接続する最も簡単な方法です。 

この例では、アカウント キーを使用して **DataLakeServiceClient** インスタンスを作成します。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- `storage_account_name` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

- `storage_account_key` プレースホルダーの値は、実際のストレージ アカウントのアクセス キーに置き換えます。

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD) を使用して接続する

[Python 用 Azure ID クライアント ライブラリ](https://pypi.org/project/azure-identity/)を使用して、Azure AD でアプリケーションを認証できます。

この例では、クライアント ID、クライアント シークレット、およびテナント ID を使用して **DataLakeServiceClient** インスタンスを作成します。  これらの値を取得するには、「[クライアント アプリケーションからの要求を承認するために Azure AD からトークンを取得する](../common/storage-auth-aad-app.md)」を参照してください。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> その他の例については、[Python 用 Azure ID クライアント ライブラリ](https://pypi.org/project/azure-identity/)のドキュメントを参照してください。

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーは、ファイルのファイル システムとして機能します。 **FileSystemDataLakeServiceClient.create_file_system** メソッドを呼び出すことによって作成できます。

この例では、`my-file-system` という名前のコンテナーを作成します。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>ディレクトリを作成する

**FileSystemClient.create_directory** メソッドを呼び出して、ディレクトリ参照を作成します。

この例では、`my-directory` という名前のディレクトリをコンテナーに追加します。 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>ディレクトリの名前変更または移動

**DataLakeDirectoryClient.rename_directory** メソッドを呼び出して、ディレクトリの名前を変更するか、ディレクトリを移動します。 目的のディレクトリのパスをパラメーターに渡します。 

この例では、サブディレクトリの名前を `my-subdirectory-renamed` に変更します。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>ディレクトリを削除する

**DataLakeDirectoryClient.delete_directory** メソッドを呼び出して、ディレクトリを削除します。

この例では、`my-directory` という名前のディレクトリを削除します。  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>ファイルをディレクトリにアップロードする 

まず、**DataLakeFileClient** クラスのインスタンスを作成して、ターゲット ディレクトリにファイル参照を作成します。 **DataLakeFileClient.append_data** メソッドを呼び出して、ファイルをアップロードします。 **DataLakeFileClient.flush_data** メソッドを呼び出して、アップロードの完了を確認してください。

この例では、`my-directory` という名前のディレクトリにテキスト ファイルをアップロードします。   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> ファイル サイズが大きい場合は、コードで **DataLakeFileClient.append_data** メソッドを複数回呼び出す必要があります。 代わりに **DataLakeFileClient.upload_data** メソッドを使用することを検討してください。 この方法により、1 回の呼び出しでファイル全体をアップロードできます。 

## <a name="upload-a-large-file-to-a-directory"></a>大きなファイルをディレクトリにアップロードする

**DataLakeFileClient.upload_data** メソッドを使用して大きなファイルをアップロードします。**DataLakeFileClient.append_data** メソッドを複数回呼び出す必要はありません。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>ディレクトリからダウンロードする 

書き込み用にローカル ファイルを開きます。 次に、ダウンロードするファイルを表す **DataLakeFileClient** インスタンスを作成します。 **DataLakeFileClient.read_file** を呼び出して、ファイルからバイトを読み取り、そのバイトをローカル ファイルに書き込みます。 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>ディレクトリの内容を一覧表示する

**FileSystemClient.get_paths** メソッドを呼び出してディレクトリの内容を一覧表示し、結果を列挙します。

この例では、`my-directory` という名前のディレクトリにある各サブディレクトリとファイルのパスを出力します。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a>アクセス制御リスト (ACL) を管理する

ディレクトリとファイルのアクセス許可を取得、設定、更新できます。

> [!NOTE]
> Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、セキュリティ プリンシパルに [Storage BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)が割り当てられていることを確認してください。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](./data-lake-storage-access-control.md)」を参照してください。

### <a name="manage-directory-acls"></a>ディレクトリ ACL を管理する

**DataLakeDirectoryClient.get_access_control** メソッドを呼び出してディレクトリのアクセス制御リスト (ACL) を取得し、**DataLakeDirectoryClient.set_access_control** メソッドを呼び出して ACL を設定します。

> [!NOTE]
> アプリケーションが Azure Active Directory (Azure AD) を使用してアクセスを承認する場合は、アプリケーションがアクセスを承認するために使用するセキュリティ プリンシパルが、[ストレージ BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)に割り当てられていることを確認します。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](./data-lake-storage-access-control.md)」を参照してください。

この例では、`my-directory` という名前のディレクトリの ACL を取得して設定します。 `rwxr-xrw-` 文字列により、所有ユーザーには読み取り、書き込み、および実行のアクセス許可が、所有グループには読み取りと実行のアクセス許可のみが、他のすべてのユーザーに読み取りと書き込みのアクセス許可が付与されます。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

また、コンテナーのルート ディレクトリの ACL を取得して設定することもできます。 ルート ディレクトリを取得するには、**FileSystemClient._get_root_directory_client** メソッドを呼び出します。

### <a name="manage-file-permissions"></a>ファイルのアクセス許可を管理する

**DataLakeFileClient.get_access_control** メソッドを呼び出してファイルのアクセス制御リスト (ACL) を取得し、**DataLakeFileClient.set_access_control** メソッドを呼び出して ACL を設定します。

> [!NOTE]
> アプリケーションが Azure Active Directory (Azure AD) を使用してアクセスを承認する場合は、アプリケーションがアクセスを承認するために使用するセキュリティ プリンシパルが、[ストレージ BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)に割り当てられていることを確認します。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](./data-lake-storage-access-control.md)」を参照してください。

この例では、`my-file.txt` という名前のファイルの ACL を取得して設定します。 `rwxr-xrw-` 文字列により、所有ユーザーには読み取り、書き込み、および実行のアクセス許可が、所有グループには読み取りと実行のアクセス許可のみが、他のすべてのユーザーに読み取りと書き込みのアクセス許可が付与されます。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

### <a name="set-an-acl-recursively"></a>ACL を再帰的に設定する

また、親ディレクトリの既存の子項目に対して ACL を再帰的に追加、更新、および削除することができます。それぞれの子項目に対してこれらの変更を個別に行う必要はありません。 詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御リスト (ACL) を再帰的に設定する](recursive-access-control-lists.md)」を参照してください。

## <a name="see-also"></a>関連項目

* [API リファレンス ドキュメント](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
* [パッケージ (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/)
* [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 から Gen2 へのマッピング](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [フィードバックを送る](https://github.com/Azure/azure-sdk-for-python/issues)