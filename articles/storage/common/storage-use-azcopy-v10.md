---
title: AzCopy v10 を使用して Azure Storage にデータをコピーまたは移動する | Microsoft Docs
description: AzCopy は、ストレージ アカウント間のデータ コピーに利用できるコマンドライン ユーティリティです。 この記事は、AzCopy をダウンロードし、ストレージ アカウントに接続し、ファイルを転送する際に役立ちます。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: b43b20023aa0d96dcaa3ce79b626d61ec6f6752d
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423833"
---
# <a name="get-started-with-azcopy"></a>AzCopy を使ってみる

AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 この記事は、AzCopy をダウンロードし、ストレージ アカウントに接続し、ファイルを転送する際に役立ちます。

> [!NOTE]
> AzCopy **V10** が現在サポートされているバージョンの AzCopy です。
>
> 以前のバージョンの AzCopy を使用する必要がある場合は、この記事の「[以前のバージョンの AzCopy の使用](#previous-version)」セクションを参照してください。

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>AzCopy をダウンロードする

まず、お使いのコンピューター上の任意のディレクトリに AzCopy V10 実行可能ファイルをダウンロードします。 AzCopy V10 は単に実行可能ファイルなので、インストールするものはありません。

- [Windows 64 ビット](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32 ビット](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

これらのファイルは、zip ファイル (Windows および Mac) または tar ファイル (Linux) として圧縮されます。 Linux 上で tar ファイルをダウンロードして圧縮を解除するには、お使いの Linux ディストリビューションのドキュメントを参照してください。

> [!NOTE]
> [Azure Table Storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) サービスとの間でデータをコピーする場合、[AzCopy バージョン 7.3](https://aka.ms/downloadazcopynet) をインストールしてください。


## <a name="run-azcopy"></a>AzCopy を実行する

利便性のため、AzCopy 実行可能ファイルのディレクトリの場所をご自分のシステム パスに追加して使いやすくすることを検討してください。 そうすると、ご使用のシステム上にある任意のディレクトリから「`azcopy`」を入力できます。

AzCopy ディレクトリをご自分のパスに追加しないことを選択した場合、実際の AzCopy 実行可能ファイルの場所にディレクトリを変更し、Windows PowerShell コマンド プロンプトで「`azcopy`」または「`.\azcopy`」と入力する必要があります。

コマンドの一覧を表示するには、「`azcopy -h`」と入力し、ENTER キーを押します。

特定のコマンドの情報を知るには、単にコマンドの名前を含めてください (例: `azcopy list -h`)。

![インライン ヘルプ](media/storage-use-azcopy-v10/azcopy-inline-help.png)

各コマンドとコマンド パラメーターの詳細なリファレンス ドキュメントについては、「[azcopy](storage-ref-azcopy.md)」を参照してください

> [!NOTE] 
> ご自分の Azure Storage アカウントの所有者であっても、データへのアクセス許可が自動的に割り当てられるわけではありません。 AzCopy を使用して意味のある動作を行う前に、ストレージ サービスに認証資格情報を提供する方法を決定する必要があります。 

## <a name="choose-how-youll-provide-authorization-credentials"></a>認証資格情報の提供方法を選択する

認証資格情報は、Azure Active Directory (AD) または Shared Access Signature (SAS) トークンを使用して提供できます。

次の表をガイドとして使用してください。

| ストレージの種類 | 現在サポートされている認証方法 |
|--|--|
|**Blob Storage** | Azure AD および SAS |
|**BLOB ストレージ (階層型名前空間)** | Azure AD および SAS |
|**File Storage** | SAS のみ |

### <a name="option-1-use-azure-active-directory"></a>オプション 1: Azure Active Directory を使用する

Azure Active Directory を使用すると、各コマンドに SAS トークンを追加する代わりに、資格情報を 1 回入力するだけで済みます。  

> [!NOTE]
> 現在のリリースでは、ストレージ アカウント間で BLOB をコピーする場合は、各ソース URL に SAS トークンを追加する必要があります。 コピー先 URL からのみ、SAS トークンを省略できます。 例については、「[ストレージ アカウント間で BLOB をコピーする](storage-use-azcopy-blobs.md)」をご覧ください。

必要な認証レベルは、ファイルをアップロードする予定か、ファイルをダウンロードするだけなのかによって異なります。

ファイルをダウンロードするだけの場合は、ユーザー ID、マネージド ID、またはサービス プリンシパルに[ストレージ BLOB データ閲覧者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)が割り当てられていることを確認します。

> ユーザー ID、マネージド ID、およびサービス プリンシパルはそれぞれ "*セキュリティ プリンシパル*" の種類です。そのため、この記事の残りの部分では、"*セキュリティ プリンシパル*" という用語を使います。

ファイルをアップロードする場合は、これらのロールのいずれかがご自分のセキュリティ プリンシパルに割り当てられていることを確認します。

- [ストレージ BLOB データ共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)
- [ストレージ BLOB データ所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

これらのロールは、以下のいずれかの範囲でセキュリティ プリンシパルに割り当てることができます。

- コンテナー (ファイル システム)
- ストレージ アカウント
- Resource group
- サブスクリプション

ロールを確認し、割り当てる方法については、「[Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

> [!NOTE]
> Azure ロールの割り当ての反映には最大で 5 分かかる場合があることに留意してください。

ターゲット コンテナーまたはディレクトリのアクセス制御リスト (ACL) にご自分のセキュリティ プリンシパルが追加されている場合は、これらのロールのいずれかがご自分のセキュリティ プリンシパルに割り当てられている必要はありません。 ACL では、ご自分のセキュリティ プリンシパルには、ターゲット ディレクトリの書き込みアクセス許可と、コンテナーおよび各親ディレクトリの実行アクセス許可が必要になります。

詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)に関するページを参照してください。

#### <a name="authenticate-a-user-identity"></a>ユーザー ID の認証

ご自分のユーザー ID に必要な認証レベルが与えられていることを確認したら、コマンド プロンプトを開き、次のコマンドを入力し、Enter キーを押します。

```azcopy
azcopy login
```

エラーが発生する場合は、ストレージ アカウントが属する組織のテナント ID を含めてみてください。

```azcopy
azcopy login --tenant-id=<tenant-id>
```

`<tenant-id>` プレースホルダーを、ストレージ アカウントが属する組織のテナント ID に置き換えます。 テナント ID を確認するには、Azure portal 内で **[Azure Active Directory] > [プロパティ] > [ディレクトリ ID]** の順に選択します。

このコマンドによって、認証コードと Web サイトの URL が返されます。 Web サイトを開き、コードを指定し、 **[次へ]** ボタンを選択します。

![コンテナーを作成する](media/storage-use-azcopy-v10/azcopy-login.png)

サインイン ウィンドウが表示されます。 そのウィンドウで、Azure アカウント資格情報を使用して、Azure アカウントにサインインします。 正常にサインインしたら、ブラウザー ウィンドウを閉じ、AzCopy の使用を開始できます。

<a id="service-principal"></a>

#### <a name="authenticate-a-service-principal"></a>サービス プリンシパルの認証

ユーザーの介入なしで実行されるスクリプト内で AzCopy を使用する予定の場合、これは優れたオプションです (特にオンプレミスで実行するとき)。 Azure で実行されている VM で AzCopy を実行する場合、マネージド サービス ID を管理しやすくなります。 詳しくは、この記事の「[マネージド ID を認証する](#managed-identity)」セクションを参照してください。

そのスクリプトを実行する前に、少なくとも 1 回対話的にサインインする必要があります。そうすることで、ご自身のサービス プリンシパルの資格情報を AzCopy に渡すことができます。  それらの資格情報は暗号化された安全なファイルに格納されます。そのため、実際のスクリプトでその機密情報を渡す必要がありません。

クライアント シークレットを使用して、またはご自分のサービス プリンシパルのアプリ登録に関連付けられている証明書のパスワードを使用して、ご自分のアカウントにサインインできます。

サービス プリンシパルの作成の詳細については、「[方法:リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)」のガイダンスに従って、サービス プリンシパルを作成します。

サービス プリンシパル全般の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)」を参照してください。

##### <a name="using-a-client-secret"></a>クライアント シークレットの使用

まず、`AZCOPY_SPA_CLIENT_SECRET` 環境変数を、ご自分のサービス プリンシパルのアプリ登録のクライアント シークレットに設定します。

> [!NOTE]
> この値は、必ず、ご使用のオペレーティング システムの環境変数の設定ではなく、ご使用のコマンド プロンプトから設定します。 そうすることで、この値を現在のセッションでのみ使用できるようになります。

この例では、これを PowerShell で実行する方法を示しています。

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> この例で示すように、プロンプトを使用することを検討してください。 そうすると、ご自分のパスワードがご使用のコンソールのコマンド履歴に表示されません。  

次に、以下のコマンドを入力し、Enter キーを押します。

```azcopy
azcopy login --service-principal --certificate-path path-to-certificate-file --application-id application-id --tenant-id=tenant-id
```

`<application-id>` プレースホルダーを、ご自分のサービス プリンシパルのアプリ登録のアプリケーション ID に置き換えます。 `<tenant-id>` プレースホルダーを、ストレージ アカウントが属する組織のテナント ID に置き換えます。 テナント ID を確認するには、Azure portal 内で **[Azure Active Directory] > [プロパティ] > [ディレクトリ ID]** の順に選択します。 

##### <a name="using-a-certificate"></a>証明書の使用

承認にご自分の資格情報を使用する場合は、ご自分のアプリ登録に証明書をアップロードした後、その証明書を使用してログインできます。

ご自分のアプリ登録にご使用の証明書をアップロードするだけでなく、AzCopy が実行されるマシンまたは VM 上に証明書のコピーを保存する必要もあります。 この証明書のコピーは、.PFX または .PEM 形式で、秘密キーが含まれている必要があります。 秘密キーはパスワードで保護する必要があります。 Windows を使用していて、ご使用の証明書が証明書ストア内にのみ存在する場合は、必ず、その証明書 (秘密キーを含む) を PFX ファイルにエクスポートしてください。 ガイダンスについては、「[Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)」を参照してください。

次に、`AZCOPY_SPA_CERT_PASSWORD` 環境変数を、証明書のパスワードに設定します。

> [!NOTE]
> この値は、必ず、ご使用のオペレーティング システムの環境変数の設定ではなく、ご使用のコマンド プロンプトから設定します。 そうすることで、この値を現在のセッションでのみ使用できるようになります。

この例では、このタスクを PowerShell で実行する方法を示しています。

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

次に、以下のコマンドを入力し、Enter キーを押します。

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

`<path-to-certificate-file>` プレースホルダーを、証明書ファイルの相対または完全修飾パスに置き換えます。 AzCopy は、この証明書のパスを保存しますが、証明書のコピーは保存しません。そのため、必ず所定の場所にその証明書を保持してください。 `<tenant-id>` プレースホルダーを、ストレージ アカウントが属する組織のテナント ID に置き換えます。 テナント ID を確認するには、Azure portal 内で **[Azure Active Directory] > [プロパティ] > [ディレクトリ ID]** の順に選択します。

> [!NOTE]
> この例で示すように、プロンプトを使用することを検討してください。 そうすると、ご自分のパスワードがご使用のコンソールのコマンド履歴に表示されません。 

<a id="managed-identity"></a>

#### <a name="authenticate-a-managed-identity"></a>マネージド ID を認証する

ユーザーの介入なしで実行されるスクリプト内で AzCopy を使用し、スクリプトが Azure 仮想マシン (VM) から実行される場合、これは優れたオプションです。 このオプションを使用する場合、資格情報を VM に保存する必要はありません。

VM で有効にしたシステム全体のマネージド ID を使用して、または VM に割り当てたユーザー割り当てのマネージド ID のクライアント ID、オブジェクト ID、またはリソース ID を使用して、アカウントにサインインできます。

システム全体のマネージド ID を有効にする方法、またはユーザー割り当てのマネージド ID を作成したりする方法について詳しくは、「[Azure portal を使用して Azure VM で Azure リソースのマネージド ID を構成する](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)」をご覧ください。

##### <a name="using-a-system-wide-managed-identity"></a>システム全体のマネージド ID の使用

まず、VM でシステム全体のマネージド ID が有効になっていることを確認します。 「[システム割り当てマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)」をご覧ください。

次に、コマンド コンソールで、次のコマンドを入力して Enter キーを押します。

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID の使用

まず、VM でユーザー割り当てマネージド ID が有効になっていることを確認します。 「[ユーザー割り当てマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity)」をご覧ください。

次に、コマンド コンソールで、次のいずれかのコマンドを入力して Enter キーを押します。

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

`<client-id>` プレースホルダーは、ユーザー割り当てのマネージド ID のクライアント ID に置き換えます。

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

`<object-id>` プレースホルダーは、ユーザー割り当てのマネージド ID のオブジェクト ID に置き換えます。

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

`<resource-id>` プレースホルダーは、ユーザー割り当てのマネージド ID のリソース ID に置き換えます。

### <a name="option-2-use-a-sas-token"></a>オプション 2:SAS トークンを使用する

AzCopy コマンドで使用する各コピー元または各コピー先の URL に SAS トークンを追加できます。

この例のコマンドでは、ローカル ディレクトリから BLOB コンテナーにデータが繰り返しコピーされます。 架空の SAS トークンがコンテナー URL の末尾に追加されます。

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS トークンの詳細とその取得方法については、「[Shared Access Signatures (SAS) の使用](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)」を参照してください。

## <a name="transfer-files"></a>ファイルの転送

ID を認証し、SAS トークンを取得したら、ファイルの転送を開始できます。

サンプル コマンドは次の記事のいずれかをご覧ください。

- [AzCopy と Blob Storage でデータを転送する](storage-use-azcopy-blobs.md)

- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)

- [AzCopy と Amazon S3 バケットでデータを転送する](storage-use-azcopy-s3.md)

- [AzCopy と Azure Stack ストレージを使用してデータを転送する](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>スクリプト内で AzCopy を使用する

### <a name="obtain-a-static-download-link"></a>静的なダウンロード リンクを取得する

時間と共に、AzCopy の[ダウンロード リンク](#download-and-install-azcopy)は AzCopy の新しいバージョンを指します。 実際のスクリプトで AzCopy をダウンロードする場合、実際のスクリプトで使用する機能が新しいバージョンの AzCopy で変更されていると、スクリプトの動作が停止する可能性があります。

こうした問題を回避するには、AzCopy の現在のバージョンの静的 (変更されない) リンクを取得します。 そうすることで、実際のスクリプトを実行するたびに、まったく同じバージョンの AzCopy がダウンロードされます。

そのリンクを取得するには、このコマンドを実行します。

| オペレーティング システム  | コマンド |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Linux の場合、`tar` コマンドの `--strip-components=1` では、バージョン名を含む最上位フォルダーが削除され、代わりに現在のフォルダーに直接バイナリが抽出されます。 これにより、`wget` URL を更新するだけで、新しいバージョンの `azcopy` でスクリプトを更新できます。

この URL はこのコマンドの出力に表示されます。 その後、実際のスクリプトでその URL を使用して AzCopy をダウンロードできます。

| オペレーティング システム  | コマンド |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>SAS トークンの特殊文字をエスケープする

拡張子が `.cmd` のバッチ ファイルでは、SAS トークンに出現する `%` 文字をエスケープする必要があります。 これを行うには、SAS トークン文字列の既存の `%` 文字の横に、`%` の文字を追加します。

### <a name="run-scripts-by-using-jenkins"></a>Jenkins を使用してスクリプトを実行する

[Jenkins](https://jenkins.io/) を使用してスクリプトを実行する場合は、必ずスクリプトの先頭に次のコマンドを配置してください。

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Azure Storage Explorer で AzCopy を使用する

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) は、AzCopy を使用してすべてのデータ転送操作を実行します。 AzCopy のパフォーマンス上の利点を活用する場合は、[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用できますが、ファイルの操作にはコマンド ラインではなくグラフィカル ユーザー インターフェイスを使用することをお勧めします。

Storage Explorer では、ご自分のアカウント キーを使用して、操作を実行します。そのため、Storage Explorer にサインインした後は、追加の承認資格情報を提供する必要はありません。

<a id="previous-version"></a>

## <a name="use-the-previous-version-of-azcopy"></a>以前のバージョンの AzCopy の使用

以前のバージョンの AzCopy を使用する必要がある場合は、次のいずれかのリンクを参照してください。

- [Windows での AzCopy (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [Linux での AzCopy (v7)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy の構成、最適化、トラブルシューティング

「[AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

ご質問、問題、一般的なフィードバックは、[GitHub](https://github.com/Azure/azure-storage-azcopy) ページからお送りください。
