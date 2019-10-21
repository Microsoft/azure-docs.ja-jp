---
title: azcopy jobs resume | Microsoft Docs
description: この記事では、azcopy jobs resume コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9e8dbbd40259c7a71f252d0d6e93dd6f135973de
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513543"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

指定されたジョブ ID を持つ既存のジョブを再開します。

## <a name="synopsis"></a>概要

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|--destination-sas string|指定されたジョブ ID の宛先の宛先 SAS。|
|--exclude string|フィルター:ジョブの再開時に、これらの失敗した転送を除外します。 ファイルは ";" で区切る必要があります。|
|-h, --help|resume コマンドのヘルプ コンテンツを表示します。|
|--include string|フィルター: ジョブの再開時に、これらの失敗した転送のみを含めます。 ファイルは ";" で区切る必要があります。|
|--source-sas string |指定されたジョブ ID のソースのソース SAS。|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|

## <a name="see-also"></a>関連項目

- [azcopy jobs](storage-ref-azcopy-jobs.md)
