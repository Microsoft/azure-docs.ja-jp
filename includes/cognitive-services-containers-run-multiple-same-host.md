---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704239"
---
### <a name="run-multiple-containers-on-the-same-host"></a>同じホスト上で複数のコンテナーを実行する

公開されているポートを使って複数のコンテナーを実行する予定の場合、必ず各コンテナーを別の公開されているポートで実行してください。 たとえば、最初のコンテナーをポート 5000 上で、2 番目のコンテナーを 5001 上で実行します。

このコンテナーと、別の Azure Cognitive Services コンテナーを HOST 上で同時に実行することができます。 同じ Cognitive Services コンテナーの複数のコンテナーを実行することもできます。
