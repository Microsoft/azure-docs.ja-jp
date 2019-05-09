---
title: Azure Cosmos DB での競合解決の種類と複数の書き込みリージョンを使用した解決ポリシー
description: この記事では、Azure Cosmos DB の競合カテゴリと競合解決ポリシーについて説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 24201cfd657d4f23eb962b7407ed20262d780cf7
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407421"
---
# <a name="conflict-types-and-resolution-policies"></a>競合のタイプと解決ポリシー

競合と競合解決ポリシーは、複数の書き込みリージョンを使用して Azure Cosmos DB アカウントが構成されている場合に該当します。

複数の書き込みリージョンで構成された Azure Cosmos アカウントの場合は、ライターが複数のリージョン内の同じ項目を同時に更新するときに更新の競合が発生する可能性があります。 更新の競合は、次の 3 種類に分類できます。

* **挿入の競合**:これらの競合は、アプリケーションが 2 つ以上のリージョン内の同じ一意のインデックスを持つ 2 つ以上の項目を同時に挿入するときに発生する可能性があります。 たとえば、この競合は ID プロパティで発生することがあります。

* **置換の競合:**:これらの競合は、アプリケーションが 2 つ以上のリージョン内の同じ項目を同時に更新するときに発生する可能性があります。

* **削除の競合:**:これらの競合は、アプリケーションがあるリージョン内の項目の削除と、別のリージョン内のその項目の更新を同時に行うときに発生する可能性があります。

## <a name="conflict-resolution-policies"></a>競合解決ポリシー

Azure Cosmos DB は、書き込みの競合を解決するための、柔軟なポリシー主導のメカニズムを提供します。 Azure Cosmos コンテナーでは、次の 2 つの競合解決ポリシーから選択できます。

- **最後の書き込みが有効 (LWW)**:この解決ポリシーでは、システム定義のタイムスタンプ プロパティが既定で使用されます。 時刻同期クロック プロパティに基づきます。 SQL API を使用する場合は、競合解決に使用される他の任意のカスタム数値プロパティ (たとえば、タイムスタンプの独自の概念) を指定できます。 カスタム数値プロパティは、*競合解決パス*とも呼ばれます。 

  2 つ以上の項目が挿入または置換操作で競合する場合、競合解決パスで最も高い値を持つ項目が勝者になります。 複数の項目の競合解決パスの数値が同じ場合、勝者はシステムによって決定されます。 すべてのリージョンは単一の勝者に収束し、結果として同じバージョンのコミット済み項目になることが保証されます。 削除の競合がある場合、削除されるバージョンは挿入または置換の競合より常に優先されます。 この結果は、競合解決パスの値には関係なく発生します。

  > [!NOTE]
  > "最後の書き込みが有効" が、既定の競合解決ポリシーです。 これは、SQL、MongoDB、Cassandra、Gremlin、Table の各 API で使用できます。

  詳しくは、[LWW 競合解決ポリシーを使用する例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)に関する記事をご覧ください。

- **カスタム**:この解決ポリシーは、競合の調整のためのアプリケーション定義のセマンティクス用に設計されています。 Azure Cosmos コンテナーでこのポリシーを設定する場合は、*マージ ストアド プロシージャ*も登録する必要があります。 このプロシージャは、サーバー上のデータベース トランザクションで競合が検出されたときに自動的に呼び出されます。 システムにより、コミットメント プロトコルの一部としてマージ プロシージャの実行が 1 回だけとなることが保証されます。  

  カスタム解決オプションを使用してコンテナーを構成しているとき、コンテナーでマージ プロシージャの登録に失敗するか、またはマージ プロシージャが実行時に例外をスローした場合は、それらの競合が*競合フィード*に書き込まれます。 このときは、アプリケーションにおいて競合フィード内の競合を手動で解決する必要があります。 詳しくは、[カスタム解決ポリシーの使用と競合フィードを使用する方法の例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)に関する記事をご覧ください。

  > [!NOTE]
  > カスタム競合解決ポリシーは、SQL API アカウントでのみ使用できます。

## <a name="next-steps"></a>次の手順

競合解決ポリシーを構成する方法を学習します。

* [アプリケーションでマルチマスターを構成する方法](how-to-multi-master.md)
* [LWW 競合解決ポリシーを使用する方法](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [カスタム競合解決ポリシーを使用する方法](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [競合フィードから読み取る方法](how-to-manage-conflicts.md#read-from-conflict-feed)
