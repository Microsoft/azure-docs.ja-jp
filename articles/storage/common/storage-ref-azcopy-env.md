---
title: azcopy env | Microsoft Docs
description: この記事では、azcopy env コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e7ceff4df320aa1fbc3aa6e601c61f6407fd762e
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514740"
---
# <a name="azcopy-env"></a>azcopy env

AzCopy の動作を構成できる環境変数を示します。

## <a name="synopsis"></a>概要

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> コマンド ラインを使用して環境変数を設定した場合、その変数はコマンド ラインの履歴で読み取ることができます。 資格情報を含む変数をコマンド ラインの履歴から消去することを検討してください。 変数が履歴に表示されないようにするために、ユーザーに資格情報の入力を要求し、環境変数を設定するためのスクリプトを使用できます。

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|-h, --help|env コマンドのヘルプ コンテンツを表示します。 |
|--show-sensitive|機密/シークレット環境変数を表示します。|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
