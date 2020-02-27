---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474183"
---
Azure Functions で発生するエラーは、次のいずれかが元になっています。

- Azure Functions の組み込みの[トリガーとバインド](..\articles\azure-functions\functions-triggers-bindings.md)の使用
- 基になっている Azure サービスの API の呼び出し
- REST エンドポイントの呼び出し
- クライアント ライブラリ、パッケージ、またはサードパーティ API の呼び出し

データやメッセージが失われないようにするには、次の確実なエラー処理手法が重要です。 推奨されるエラー処理方法には、次のアクションが含まれます。

- [Application Insights を有効にする](../articles/azure-functions/functions-monitoring.md)
- [構造化エラー処理を使用する](#use-structured-error-handling)
- [べき等に設計する](../articles/azure-functions/functions-idempotent.md)
- [再試行ポリシーを実装する](../articles/azure-functions/functions-reliable-event-processing.md) (該当する場合)

### <a name="use-structured-error-handling"></a>構造化エラー処理を使用する

アプリケーションの正常性を監視するには、エラーをキャプチャして発行することが重要です。 関数コードの最上位レベルに、try/catch ブロックを含める必要があります。 catch ブロックでは、エラーをキャプチャして発行できます。

### <a name="retry-support"></a>再試行のサポート

次のトリガーには、組み込みの再試行サポートがあります。

* [Azure BLOB Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (キュー/トピック)](../articles/azure-functions/functions-bindings-service-bus.md)

既定では、これらのトリガーにより要求が最大 5 回再試行されます。 5 回目の再試行後に、Azure Queue storage と Azure Service Bus の両方のトリガーによって[有害キュー](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages)にメッセージが書き込まれます。

他のトリガーまたはバインドの種類については、再試行ポリシーを手動で実装する必要があります。 手動の実装には、[有害メッセージ キュー](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs)へのエラー情報の書き込みが含まれる場合があります。 有害キューに書き込むことによって、後で操作を再試行する機会ができます。 この方法は、Blob Storage トリガーによって使用されるものと同じです。
