---
author: baanders
description: Azure Digital Twins モデルの検証方法を説明するファイルを含める
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 32c02750f99f1aa6733d9c6dd673ffe4964978f4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303781"
---
> [!TIP]
> モデルの作成後、モデルは、Azure Digital Twins インスタンスにアップロードする前に、オフラインで検証することが推奨されます。

モデル ドキュメントを検証してインスタンスにアップロードする前に DTDL が正しいことを確認するための、言語に依存しないサンプルがあります。 これはこちらにあります。[**DTDL 検証ツール サンプル**](/samples/azure-samples/dtdl-validator/dtdl-validator)

DTDL 検証ツールのサンプルは、NuGet でクライアント側ライブラリとして提供されている .NET DTDL パーサー ライブラリに基づいて構築されています。[**Microsoft.Azure.DigitalTwins.Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). また、このライブラリを直接使用し、自分独自の検証ソリューションを設計することもできます。 パーサー ライブラリを使用する場合は、Azure Digital Twins が実行されているバージョンと互換性のあるバージョンを使用するようにしてください。 現時点では、このバージョンは *3.12.4* です。

使用例を含む検証ツールのサンプルとパーサー ライブラリの詳細については、"[*モデルを解析および検証する方法*](../articles/digital-twins/how-to-parse-models.md)" に関するページを参照してください。