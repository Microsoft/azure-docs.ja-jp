---
title: Azure Automation の Runbook の種類
description: この記事では、Azure Automation で使用できる Runbook の種類と、使用する種類を決定する際の考慮事項について説明します。
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 24d0123eecc56b56573e94d831283d8d360cd16e
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185927"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation の Runbook の種類

Azure Automation のプロセス オートメーション機能では、次の表で定義されているように、いくつかの種類の Runbook がサポートされています。 プロセス オートメーション環境の詳細については、「[Azure Automation での Runbook の実行](automation-runbook-execution.md)」を参照してください。

| Type | 説明 |
|:--- |:--- |
| [グラフィカル](#graphical-runbooks)|Windows PowerShell に基づき、Azure portal のグラフィカル エディターで完全に作成および編集される グラフィカル Runbook です。 |
| [グラフィカル PowerShell ワークフロー](#graphical-runbooks)|Windows PowerShell ワークフローに基づき、Azure portal のグラフィカル エディターで完全に作成および編集される グラフィカル Runbook です。 |
| [PowerShell](#powershell-runbooks) |Windows PowerShell スクリプトに基づくテキスト Runbook です。 |
| [PowerShell ワークフロー](#powershell-workflow-runbooks)|Windows PowerShell ワークフロー スクリプトに基づくテキスト Runbook です。 |
| [Python](#python-runbooks) |Python スクリプトに基づくテキスト Runbook です。 |

特定の Runbook に使用する種類を決定するときは、次のことを考慮します。

* グラフィカル Runbook からテキスト Runbook への変換、およびこの逆の変換を行うことはできません。
* 異なる種類の Runbook を子 Runbook として使用する場合は制限があります。 詳しくは、「[Azure Automation での子 Runbook](automation-child-runbooks.md)」をご覧ください。

## <a name="graphical-runbooks"></a>グラフィック Runbook

Azure portal のグラフィカル エディターを使用して、グラフィカルおよびグラフィカル PowerShell ワークフロー Runbook を作成および編集できます。 ただし、別のツールを使用してこの種類の Runbook を作成または編集することはできません。 グラフィカル Runbook の主な機能を次に示します。

* Automation アカウントのファイルにエクスポートし、別の Automation アカウントにインポートすることができます。 
* PowerShell コードを生成します。 
* インポート時にグラフィカル PowerShell ワークフロー Runbook との間で変換できます。 

### <a name="advantages"></a>長所

* 視覚的な insert-link-configure 作成モデルを使用できます。
* プロセスを介したデータの流れに専念できます。
* 管理プロセスが視覚的に表現されます。
* 他の Runbook を子 Runbook として含めることで、高度なワークフローを作成できます。
* モジュール型プログラミングを促進できます。

### <a name="limitations"></a>制限事項

* Azure portal の外部で作成または編集することができません。
* 複雑なロジックを実行するために、PowerShell ワークフローのコードを含むコード アクティビティが必要になる場合があります。
* [テキスト形式](automation-runbook-types.md)のいずれかに変換することはできません。また、テキスト Runbook をグラフィカル形式に変換することもできません。 
* グラフィカル ワークフローによって作成される PowerShell コードを表示したり、直接編集したりすることができません。 コード アクティビティで作成したコードは表示できます。
* Linux Hybrid Runbook Worker で Runbook を実行することができません。 「[Hybrid Runbook Worker を使用してデータ センターまたはクラウドのリソースを自動化する](automation-hybrid-runbook-worker.md)」を参照してください。

## <a name="powershell-runbooks"></a>PowerShell Runbook

PowerShell Runbook は、Windows PowerShell に基づきます。 Azure ポータルのテキスト エディターを使用して、Runbook のコードを直接編集します。  また、任意のオフライン テキスト エディターを使用したり、Azure Automation に [Runbook をインポート](manage-runbooks.md) したりすることもできます。

### <a name="advantages"></a>長所

* すべての複雑なロジックを PowerShell コードで実装でき、PowerShell ワークフローに関する複雑さが加わることはありません。
* 実行前にコンパイルする必要がないため、PowerShell ワークフロー Runbook よりも速く起動できます。
* Windows と Linux の両方の Azure と Hybrid Runbook Worker で動作します。

### <a name="limitations"></a>制限事項

* PowerShell スクリプトについて理解している必要があります。
* Runbook に[並列処理](automation-powershell-workflow.md#use-parallel-processing)を使用し、複数のアクションを並列して実行することはできません。
* Runbook に[チェックポイント](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)を使用し、エラーが発生した場合に Runbook を再開することはできません。
* PowerShell ワークフロー Runbook とグラフィカル Runbook を子 Runbook として組み込むには、新しいジョブを作成する [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) コマンドレットを使用する必要があります。

### <a name="known-issues"></a>既知の問題

PowerShell Runbook に関する現在の既知の問題は次のとおりです。

* PowerShell Runbook では、null 値を含む暗号化されていない[変数資産](./shared-resources/variables.md)は取得できません。
* PowerShell Runbook では、名前に `*~*` が含まれる変数資産は取得できません。
* PowerShell Runbook のループ内の [Get-Process](/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) 操作は、約 80 回の反復後にクラッシュする可能性があります。
* 一度に大量のデータを出力ストリームに書き込もうとした場合、PowerShell Runbook が失敗する可能性があります。 通常、大きなオブジェクトの処理に必要な情報のみを Runbook から出力することで、この問題を回避できます。 たとえば、制限なしで `Get-Process` を使用するのではなく、`Get-Process | Select ProcessName, CPU` のように必要なパラメーターのみをコマンドレットから出力させることができます。

## <a name="powershell-workflow-runbooks"></a>PowerShell ワークフロー Runbook

PowerShell ワークフロー Runbook は、 [Windows PowerShell ワークフロー](automation-powershell-workflow.md)に基づくテキスト Runbook です。 Azure ポータルのテキスト エディターを使用して、Runbook のコードを直接編集します。 また、任意のオフライン テキスト エディターを使用したり、Azure Automation に [Runbook をインポート](manage-runbooks.md) したりすることもできます。

### <a name="advantages"></a>長所

* すべての複雑なロジックを PowerShell ワークフローのコードで実装できます。
* エラーが発生した場合は、[チェックポイント](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)を使用して操作を再開できます。
* [並列処理](automation-powershell-workflow.md#use-parallel-processing)を使用して、複数のアクションを並行して実行できます。
* 高度なワークフローを作成するために、他のグラフィカル Runbook や PowerShell ワークフロー Runbook を子 Runbook として組み込むことができます。

### <a name="limitations"></a>制限事項

* PowerShell ワークフローについて理解している必要があります。
* [逆シリアル化されたオブジェクト](automation-powershell-workflow.md#deserialized-objects)など、PowerShell ワークフローに関する別の複雑さに対応する必要があります。
* Runbook は、実行前にコンパイルする必要があるため、PowerShell Runbook よりも起動に時間がかかります。
* PowerShell Runbook を子 Runbook として含めるには、`Start-AzAutomationRunbook` コマンドレットを使用する必要があります。
* Linux Hybrid Runbook Worker では Runbook を実行できません。

## <a name="python-runbooks"></a>Python Runbook

Python Runbook は Python 2 でコンパイルします。 Azure portal のテキスト エディターを使用して、Runbook のコードを直接編集することができます。 また、任意のオフライン テキスト エディターを使用したり、Azure Automation に [Runbook をインポート](manage-runbooks.md)したりすることもできます。

### <a name="advantages"></a>長所

* 堅牢な Python ライブラリを使用できます。
* Azure または Linux Hybrid Runbook Worker で実行できます。 Windows Hybrid Runbook Worker がサポートされるには、[Python2.7](https://www.python.org/downloads/release/latest/python2) がインストールされている必要があります。

### <a name="limitations"></a>制限事項

* Python スクリプトについて理解している必要があります。
* 現在、Python 2 のみがサポートされています。 Python 3 固有の関数は失敗します。
* サードパーティ製ライブラリを使用するには、Automation アカウントに[パッケージをインポートする](python-packages.md)必要があります。

## <a name="next-steps"></a>次のステップ

* PowerShell Runbook の詳細については、「[チュートリアル: PowerShell Runbook を作成する](learn/automation-tutorial-runbook-textual-powershell.md)」を参照してください。
* PowerShell Workflow Runbook の詳細については、「[チュートリアル: PowerShell Workflow Runbook を作成する](learn/automation-tutorial-runbook-textual.md)」を参照してください。
* グラフィカル Runbook の詳細については、「[チュートリアル: グラフィカル Runbook を作成する](learn/automation-tutorial-runbook-graphical.md)」を参照してください。
* Python Runbook の詳細については、「[チュートリアル: Python Runbook を作成する」](learn/automation-tutorial-runbook-textual-python2.md)を参照してください。
