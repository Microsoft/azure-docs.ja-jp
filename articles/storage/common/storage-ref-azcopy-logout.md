---
title: azcopy logout | Microsoft Docs
description: この記事では、azcopy logout コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 162946ca2de9e613f0ad92297944a9f5390ab378
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285256"
---
# <a name="azcopy-logout"></a>azcopy logout

ユーザーをログアウトし、Azure Storage リソースへのアクセスを終了します。

## <a name="synopsis"></a>概要

このコマンドは、現在のユーザーのキャッシュされているすべてのログイン情報を削除します。

```azcopy
azcopy logout [flags]
```

## <a name="related-conceptual-articles"></a>関連する概念に関する記事

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [AzCopy と Blob Storage でデータを転送する](storage-use-azcopy-blobs.md)
- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)
- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|-h, --help|logout コマンドのヘルプ コンテンツを表示します。|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps float|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|
|--trusted-microsoft-suffixes string   |Azure Active Directory ログイン トークンを送信できる追加のドメイン サフィックスを指定します。  既定値は " *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net" です。 ここに記載されているすべてが既定値に追加されます。 セキュリティのために、Microsoft Azure のドメインのみをここに入力してください。 複数のエンティティは、セミコロンで区切ります。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
