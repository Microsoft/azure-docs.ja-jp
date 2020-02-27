---
title: ポリシー割り当て構造の詳細
description: ポリシーの定義とパラメーターを評価のためにリソースに関連付けるために Azure Policy によって使用されるポリシー割り当ての定義について説明します。
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: f03c654dfc4c8dfdf2bdc5103a5961b4d8ce1e64
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747205"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy の割り当ての構造

ポリシーの割り当ては、どのポリシーまたはイニシアティブにどのリソースを割り当てるか定義するために Azure Policy によって使用されます。 ポリシーの割り当てでは、割り当て時にそのリソース グループのパラメーターの値を決定でき、ポリシー定義を再利用して、同じリソースのプロパティのコンプライアンス ニーズが異なる場合に対応できます。

ポリシーの割り当てを作成するには、JSON を使用します。 ポリシーの割り当てには、次に対する要素が含まれています。

- 表示名
- description
- metadata
- 適用モード
- ポリシーの定義
- parameters

たとえば、次の JSON では、動的パラメーターでの _DoNotEnforce_ モードのポリシー割り当てを示します。

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Azure Policy のサンプルはすべて「[Azure Policy のサンプル](../samples/index.md)」にあります。

## <a name="display-name-and-description"></a>表示名と説明

**displayName** と **description** は、ポリシーの割り当てを識別し、リソースの特定のセットで使用するためのコンテキストを提供するために使用します。 **displayName** の最大長は _128_ 文字で、**description** の最大長は _512_ 文字です。

## <a name="enforcement-mode"></a>適用モード

**enforcementMode** プロパティを使用すると、ユーザーは、ポリシーの適用を開始したり、[Azure Activity ログ](../../../azure-monitor/platform/platform-logs-overview.md)のエントリをトリガーしたりすることなく、既存のリソースに対するポリシーの結果をテスできます。 このシナリオは、一般に "What If" と呼ばれ、安全な展開のプラクティスに沿っています。 **enforcementMode** は、[無効](./effects.md#disabled)の効果とは異なります。なぜなら、その効果は、リソースの評価がまったく行われないようにするからです。

このプロパティの値は次のとおりです。

|モード |JSON 値 |種類 |手動で修復 |アクティビティ ログ エントリ |説明 |
|-|-|-|-|-|-|
|有効 |Default |string |はい |はい |ポリシーの効果は、リソースの作成時または更新時に適用されます。 |
|無効 |DoNotEnforce |string |はい |いいえ | ポリシーの効果は、リソースの作成時または更新時に適用されません。 |

ポリシーまたはイニシアティブの定義で **enforcementMode** を指定しないと、値 _Default_ が使用されます。 **enforcementMode** が _DoNotEnforce_ に設定されている場合でも、[deployIfNotExists](./effects.md#deployifnotexists) のポリシーに対して[修復タスク](../how-to/remediate-resources.md)を開始できます。

## <a name="policy-definition-id"></a>ポリシー定義 ID

このフィールドでは、ポリシー定義またはイニシアティブ定義の完全なパス名を指定する必要があります。
`policyDefinitionId` は文字列であり、配列ではありません。 複数のポリシーを一緒に割り当てることが多い場合は、代わりに[イニシアティブ](./definition-structure.md#initiatives)を使用することをお勧めします。

## <a name="parameters"></a>パラメーター

ポリシー割り当てのこのセグメントでは、[ポリシー定義またはイニシアティブ定義](./definition-structure.md#parameters)で定義されているパラメーターに対する値を指定します。
この設計により、異なるリソースでポリシーまたはイニシアティブの定義を再利用しながら、異なるビジネス値や結果を確認することができます。

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

この例では、ポリシー定義で以前に定義されているパラメーターは、`prefix` および `suffix` です。 この特定のポリシー割り当てでは、`prefix` を **DeptA** に、`suffix` を **-LC** に設定します。 同じポリシー定義を異なる部門の異なるパラメーター セットで再利用できるため、柔軟性を提供しながら、ポリシー定義の重複と複雑さを軽減できます。

## <a name="next-steps"></a>次のステップ

- [ポリシー定義の構造](./definition-structure.md)についてさらに学習します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/get-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。