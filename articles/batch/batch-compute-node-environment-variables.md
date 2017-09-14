---
title: "Azure Batch コンピューティング ノードの環境変数 | Microsoft Docs"
description: "Azure 一括分析のコンピューティング ノードの環境変数に関するリファレンスです。"
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/05/2017
ms.author: tamram
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 29f642754430957e77ef68946f721f8e15dba065
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017

---

# <a name="azure-batch-compute-node-environment-variables"></a>Azure Batch コンピューティング ノードの環境変数
[Azure Batch サービス](https://azure.microsoft.com/services/batch/)は、コンピューティング ノードで以下の環境変数を設定します。 これらの環境変数は、タスク コマンドラインと、コマンド ラインにより実行されるプログラムとスクリプトで参照できます。

Batch での環境変数の使用に関する詳細については、[「タスクの環境設定」](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks)を参照してください。

## <a name="environment-variable-visibility"></a>環境変数の可視性

これらの環境変数は、**タスク ユーザー**のコンテキスト、つまりタスクが実行されるノードのユーザー アカウントだけで表示されます。 リモート デスクトップ プロトコル (RDP) や Secure Shell (SSH) を介してコンピューティング ノードに*リモート接続*して環境変数を一覧表示しようとしても、それらは "[表示されません](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes)"。 これは、リモート接続に使用されるユーザー アカウントが、タスクで使用されるアカウントと異なることが原因です。

## <a name="command-line-expansion-of-environment-variables"></a>環境変数のコマンドライン拡張

コンピューティング ノード上のタスクによって実行されるコマンドラインは、シェルの下では実行されません。 そのため、これらのコマンド ラインは、環境変数の展開 (これには `PATH` が含まれる) など、シェルの機能をネイティブに利用することはできません。 このような機能を利用するためには、コマンド ラインで**シェルを呼び出す**必要があります。 たとえば、`cmd.exe` を Windows コンピューティング ノードで起動するか、または `/bin/sh` をLinux ノードで起動します。

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>環境変数

| 変数名                     | Description                                                              | 可用性 | 例 |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | タスクが属する Batch アカウントの名前。                  | すべてのタスク。   | mybatchaccount |
| AZ_BATCH_CERTIFICATES_DIR       | [ タスク作業ディレクトリ内のディレクトリ][files_dirs]、この中に Linux コンピューティング ノードの証明書が格納される。 この環境変数は Windows コンピューティング ノードに適用されないので注意してください。                                                  | すべてのタスク。   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_JOB_ID                 | タスクが属するジョブの ID。 | 開始タスクを除くすべてのタスク。 | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | ノード上のジョブ準備[タスク ディレクトリ][files_dirs]の完全パス。 | 開始タスクおよびジョブ準備タスクを除くすべてのタスク。 ジョブがジョブ準備タスクで構成されている場合にのみ使用できます。 | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | ノード上のジョブ準備[タスク作業ディレクトリ][files_dirs]の完全パス。 | 開始タスクおよびジョブ準備タスクを除くすべてのタスク。 ジョブがジョブ準備タスクで構成されている場合にのみ使用できます。 | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_NODE_ID                | タスクが割り当てられているノードの ID。 | すべてのタスク。 | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_ROOT_DIR          | ノード上のすべての [Batch ディレクトリ][files_dirs]のルートの完全パス。 | すべてのタスク。 | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | ノード上の[共有ディレクトリ][files_dirs]の完全パス。 ノードで実行されるすべてのタスクに、このディレクトリに対する読み取り/書き込みアクセス権があります。 他のノードで実行されるタスクにはこのディレクトリに対するリモート アクセス権がありません (「共有」ネットワーク ディレクトリではありません)。 | すべてのタスク。 | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | ノード上の[開始タスク ディレクトリ][files_dirs]の完全パス。 | すべてのタスク。 | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | タスクが実行されているプールの ID。 | すべてのタスク。 | batchpool001 |
| AZ_BATCH_TASK_DIR               | ノード上の[タスク ディレクトリ][files_dirs]の完全パス。 このディレクトリには、タスクの `stdout.txt` と `stderr.txt`、および AZ_BATCH_TASK_WORKING_DIR が含まれます。 | すべてのタスク。 | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | 現在のタスクの ID。 | 開始タスクを除くすべてのタスク。 | task001 |
| AZ_BATCH_TASK_WORKING_DIR       | ノード上の[タスク作業ディレクトリ][files_dirs]の完全パス。 現在実行中のタスクにはこのディレクトリに対する読み取り/書き込みアクセス権があります。 | すべてのタスク。 | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | ノードのリストと、[マルチインスタンス タスク][multi_instance]に割り当てられているノードあたりのコア数。 ノードとコアが `numNodes<space>node1IP<space>node1Cores<space>` の形式で一覧表示されます<br/>`node2IP<space>node2Cores<space> ...`、ノードの番号の後に 1 つまたは複数のノード IP アドレスと、それぞれのコア数が続きます。 |  マルチ インスタンスのプライマリおよびサブタスク。 |`2 10.0.0.4 1 10.0.0.5 1` |
| AZ_BATCH_NODE_LIST              | [マルチ インスタンス タスク][multi_instance]に割り当てられているノードのリストを形式 `nodeIP;nodeIP` で示します。 | マルチ インスタンスのプライマリおよびサブタスク。 | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_HOST_LIST              | [マルチ インスタンス タスク][multi_instance]に割り当てられているノードのリストを形式 `nodeIP,nodeIP` で示します。 | マルチ インスタンスのプライマリおよびサブタスク。 | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_MASTER_NODE            | [マルチ インスタンス タスク][multi_instance]のプライマリ タスクを実行するコンピューティング ノードの IP アドレスとポート。 | マルチ インスタンスのプライマリおよびサブタスク。 | `10.0.0.4:6000`|
| AZ_BATCH_TASK_SHARED_DIR | プライマリ タスクと、[マルチ インスタンス タスク][multi_instance]のすべてのサブタスクで同一なディレクトリ パス。 パスは、マルチインスタンス タスクが実行されるすべてのノードで存在し、そのノードで実行されるタスク コマンド ([調整コマンド][coord_cmd]と[アプリケーション コマンド][app_cmd]の両方) に対して読み取り/書き込みアクセス可能です。 サブタスクや他のノードで実行されるプライマリ タスクにはこのディレクトリに対するリモート アクセス権がありません (「共有」ネットワーク ディレクトリではありません)。 | マルチ インスタンスのプライマリおよびサブタスク。 | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | 現在のノードが[マルチインスタンス タスク][multi_instance]のマスター ノードかどうかを指定します。 可能な値は `true` と `false` です。| マルチ インスタンスのプライマリおよびサブタスク。 | `true` |
| AZ_BATCH_NODE_IS_DEDICATED | `true` の場合、現在のノードが専用ノードです。 `false` の場合、これは[優先順位の低いノード](batch-low-pri-vms.md)です。 | すべてのタスク。 | `true` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command

