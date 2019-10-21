---
title: 効果のしくみを理解する
description: Azure Policy の定義には、コンプライアンスが管理および報告される方法を決定するさまざまな効果があります。
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 991cfb54dc511c284c5f5d0cf1807d5dd42b34ea
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978071"
---
# <a name="understand-azure-policy-effects"></a>Azure Policy の効果について

Azure Policy 内の各ポリシー定義には単一の効果があります。 その効果によって、ポリシー規則が一致すると評価されたときの動作が決まります。 効果の動作は、対象 (新しいリソース、更新されたリソース、または既存のリソース) によって異なります。

現在、ポリシー定義では次の効果がサポートされています。

- [Append](#append)
- [監査](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Deny](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled)
- [EnforceRegoPolicy](#enforceregopolicy) (プレビュー)
- [Modify](#modify)

## <a name="order-of-evaluation"></a>評価の順序

Azure Resource Manager を通したリソースの作成または更新の要求は、まず Azure Policy によって評価されます。 Azure Policy では、リソースに適用されるすべての割り当ての一覧が作成された後、各定義に対してリソースが評価されます。 Azure Policy では、適切なリソース プロバイダーに要求が渡される前に、さまざまな効果が処理されます。 それによって、リソースが意図された Azure Policy のガバナンス コントロールを満たさない場合に、リソース プロバイダーによって不要な処理が行われるのを防止します。

- **Disabled** は、ポリシー規則を評価する必要があるかどうかを判断するために、最初に確認されます。
- **Append** と **Modify** は、その後で評価されます。 これらいずれかによって要求が変更される可能性があるため、その変更によって、Audit または Deny の効果がトリガーされるのが止められる場合があります。
- 次に、**Deny** が評価されます。 Audit の前に Deny を評価することによって、不要なリソースの二重のログ記録が回避されます。
- 次に、要求がリソース プロバイダーに移動する前に **Audit** が評価されます。

リソース プロバイダーによって成功コードが返された後、**AuditIfNotExists** と **DeployIfNotExists** が評価され、追加のコンプライアンスのログ記録またはアクションが必要かどうかが判断されます。

現在のところ、**EnforceRegoPolicy** 効果の評価順序はありません。

## <a name="disabled"></a>無効

この効果は、状況をテストする場合や、効果がポリシー定義によってパラメーター化されている場合に役立ちます。 この柔軟性により、ポリシーのすべての割り当てを無効にするのではなく、単一の割り当てを無効にすることができます。

## <a name="append"></a>Append

Append は、作成中または更新中に要求されたリソースにフィールドを追加するために使用します。 一般的な例としては、ストレージ リソースに対して許可される IP の指定が挙げられます。

> [!IMPORTANT]
> Append は、タグ以外のプロパティで使用することを目的としています。 Append では、作成要求または更新要求時にリソースにタグを追加することができますが、タグに対しては [Modify](#modify) 効果を使用することをお勧めします。

### <a name="append-evaluation"></a>Append の評価

リソースを作成中または更新中に、リソース プロバイダーによって要求が処理される前に Append による評価が行われます。 Append では、ポリシー規則の **if** 条件が満たされた場合、リソースにフィールドが追加されます。 Append 効果によって元の要求の値が別の値でオーバーライドされる場合、Append は Deny 効果として機能し、要求は拒否されます。 新しい値を既存の配列に追加するには、 **[\*]** バージョンの別名を使用します。

Append 効果を使用するポリシー定義が評価サイクルの一部として実行される場合、既存のリソースに対する変更は行われません。 代わりに、**if** 条件を満たすリソースが非準拠とマークされます。

### <a name="append-properties"></a>Append のプロパティ

Append 効果には必須の **details** 配列が 1 つだけあります。 **details** は配列なので、1 つまたは複数の **field/value** のペアをとることができます。 許容可能なフィールドの一覧については、[定義の構造](definition-structure.md#fields)を参照してください。

### <a name="append-examples"></a>Append の例

例 1:非 **[\*]** [別名](definition-structure.md#aliases)と配列 **value** を使用してストレージ アカウントに IP 規則を設定する単一の **field/value** のペア。 非 **[\*]** 別名が配列の場合、この効果により、**value** が配列全体として追加されます。 配列が既に存在する場合は、競合から拒否イベントが発生します。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

例 2: **[\*]** [別名](definition-structure.md#aliases)と配列 **value** を使用してストレージ アカウントに IP 規則を設定する単一の **field/value** のペア。 **[\*]** 別名を使用することで、この効果により、**value** が事前に存在している可能性のある配列に追加されます。 配列まだ存在しない場合は、配列が作成されます。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="modify"></a>[変更]

Modify は、作成時または更新時にリソースのタグを追加、更新、または削除するために使用されます。 一般的な例としては、コスト センターなどのリソースでタグを更新することが挙げられます。 Modify ポリシーでは、常に `mode` が _Indexed_ に設定されている必要があります。 準拠していない既存のリソースは、[修復タスク](../how-to/remediate-resources.md)で修復できます。
1 つの Modify 規則には、任意の数の操作を含めることができます。

> [!IMPORTANT]
> Modify は現在、タグでのみ使用されます。 タグを管理している場合は、Append ではなく Modify を使用することをお勧めします。Modify では、追加の操作タイプが使用でき、既存のリソースを修復する機能が提供されます。 ただし、マネージド ID を作成できない場合は、Append を追加することをお勧めします。

### <a name="modify-evaluation"></a>Modify の評価

リソースを作成中または更新中に、リソース プロバイダーによって要求が処理される前に Modify による評価が行われます。 Modify では、ポリシー規則の **if** 条件が満たされた場合、リソースのフィールドが追加または更新されます。

Modify 効果を使用するポリシー定義が評価サイクルの一部として実行される場合、既存のリソースに対する変更は行われません。 代わりに、**if** 条件を満たすリソースが非準拠とマークされます。

### <a name="modify-properties"></a>Modify のプロパティ

Modify 効果の **details** プロパティには、修復に必要なアクセス許可を定義するすべてのサブプロパティと、タグ値の追加、更新、または削除に使用する **operations** が含まれます。

- **roleDefinitionIds** [必須]
  - このプロパティには、サブスクリプションでアクセス可能なロールベースのアクセス制御ロール ID と一致する文字列の配列を含める必要があります。 詳細については、[修復 - ポリシー定義を構成する](../how-to/remediate-resources.md#configure-policy-definition)を参照してください。
  - 定義されたロールには、[Contributor](../../../role-based-access-control/built-in-roles.md#contributor) ロールに与えられているすべての操作が含まれている必要があります。
- **operations** [必須]
  - 一致するリソースで完了されるすべてのタグ操作の配列です。
  - プロパティ:
    - **operation** [必須]
      - 一致するリソースに対して実行するアクションを定義します。 オプションは、_addOrReplace_、_Add_、_Remove_ です。 _Add_ は、[Append](#append) 効果に似た動作をします。
    - **field** [必須]
      - 追加、置換、または削除するタグです。 タグ名は、他の [fields](./definition-structure.md#fields) と同じ名前付け規則に従う必要があります。
    - **value** (オプション)
      - タグに設定する値です。
      - このプロパティは、**operation** が _addOrReplace_ または _Add_ の場合に必要です。

### <a name="modify-operations"></a>Modify の操作

**operations** プロパティ配列を使用すると、1 つのポリシー定義から複数のタグを異なる方法で変更できます。 各操作は **operation**、**field**、および **value** の各プロパティで構成されます。 operation では、修復タスクがタグに対して行う処理を決定し、field では、どのタグを変更するかを決定し、value では、そのタグの新しい設定を定義します。 下記の例では、以下のタグ変更が実行されます。

- `environment` タグを "Test" に設定する (異なる値で既に存在している場合でも)。
- タグ `TempResource` を削除する。
- `Dept` タグを、ポリシーの割り当てで構成されたポリシー パラメーター _DeptName_ に設定する。

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "field": "[parameters('DeptName')]"
        }
    ]
}
```

**operation** プロパティには、次のオプションが用意されています。

|Operation |説明 |
|-|-|
|addOrReplace |定義済みのタグと値をリソースに追加します (タグに別の値が既に存在する場合でも)。 |
|Add |定義済みのタグと値をリソースに追加します。 |
|Remove |定義済みのタグをリソースから削除します。 |

### <a name="modify-examples"></a>Modify の例

例 1:`environment` タグを追加し、既存の `environment` タグを "Test" に置き換えます。

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

例 2:`env` タグを削除し、`environment` タグを追加するか、既存の `environment` タグをパラメーター化された値に置き換えます。

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

## <a name="deny"></a>拒否

Deny は、ポリシーを通して定義された基準に一致していないために失敗するリソース要求を防ぐために使用されます。

### <a name="deny-evaluation"></a>Deny の評価

照合されたリソースを作成または更新する場合、Deny は、リソース プロバイダーに要求が送信されないようにします。 要求は `403 (Forbidden)` として返されます。 ポータルでは、ポリシーの割り当てによって阻止されたデプロイの状態として、この Forbidden を表示できます。

既存のリソースの評価では、Deny ポリシー定義と一致するリソースは、非準拠としてマークされます。

### <a name="deny-properties"></a>Deny のプロパティ

Deny 効果には、ポリシー定義の **then** 条件で使用するためのその他のプロパティはありません。

### <a name="deny-example"></a>Deny の例

例:Deny 効果を使用します。

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Audit

非準拠のリソースが評価された場合、Audit を使用してアクティビティ ログに警告イベントが作成されますが、要求は停止されません。

### <a name="audit-evaluation"></a>Audit の評価

Audit は、リソースの作成中または更新中に Azure Policy によって確認される最後の効果です。 その後、リソースが Azure Policy によってリソース プロバイダーに送信されます。 Audit は、リソース要求でも評価サイクルでも同じように動作します。 Azure Policy によって `Microsoft.Authorization/policies/audit/action` 操作がアクティビティ ログに追加され、リソースが非準拠としてマークされます。

### <a name="audit-properties"></a>Audit のプロパティ

Audit 効果には、ポリシー定義の **then** 条件で使用するためのその他のプロパティはありません。

### <a name="audit-example"></a>Audit の例

例:Audit 効果を使用します。

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists は **if** 条件に一致するリソースの監査を有効にしますが、**then** 条件の **details** に指定されるコンポーネントはありません。

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists の評価

AuditIfNotExists は、リソース プロバイダーでリソースの作成または更新要求が処理され、成功を示す状態コードが返された後で実行されます。 関連するリソースがない場合、または **ExistenceCondition** によって定義されたリソースが true と評価されない場合、監査が発生します。 Audit 効果と同じ方法で、Azure Policy によって `Microsoft.Authorization/policies/audit/action` 操作がアクティビティ ログに追加されます。 トリガーされた場合、**if** 条件を満たしているリソースは、非準拠としてマークされているリソースです。

### <a name="auditifnotexists-properties"></a>AuditIfNotExists のプロパティ

AuditIfNotExists 効果の **details** プロパティは、照合する関連リソースを定義するすべてのサブプロパティを含みます。

- **Type** [必須]
  - 照合する関連リソースの型を指定します。
  - **details.type** が **if** 条件リソース下にあるリソースの型である場合、この **type** のリソースが、ポリシーによって評価対象リソースのスコープ内から照会されます。 それ以外の場合は、評価対象リソースと同じリソース グループ内から照会されます。
- **Name** (省略可能)
  - 照合するリソースの正確な名前を指定して、指定した型のすべてのリソースではなく 1 つの特定のリソースを取得します。
  - **if.field.type** と **then.details.type** の条件値が一致する場合、**Name** は "_必須_" になり、`[field('name')]` であることが必要です。 ただし、代わりに [audit](#audit) の影響を考慮する必要があります。
- **ResourceGroupName** (省略可能)
  - 別のリソース グループに由来する関連リソースを照合できるようにします。
  - **type** が **if** 条件リソースの下にあるリソースである場合は適用されません。
  - 既定値は、**if** 条件リソースのリソース グループです。
- **ExistenceScope** (省略可能)
  - 使用できる値は _Subscription_ と _ResourceGroup_ です。
  - 照合する関連リソースを取得する範囲を設定します。
  - **type** が **if** 条件リソースの下にあるリソースである場合は適用されません。
  - _ResourceGroup_ の場合は、**if** 条件リソースのリソース グループまたは **ResourceGroupName** で指定されたリソース グループに制限されます。
  - _Subscription_ の場合は、関連リソースのサブスクリプション全体を検索します。
  - 既定値は _ResourceGroup_ です。
- **ExistenceCondition** (省略可能)
  - 指定されない場合、**type** の関連リソースは効果を満たしているため、監査はトリガーされません。
  - **if** 条件のポリシー規則と同じ言語が使用されますが、それぞれの関連リソースに対して個別に評価されます。
  - 照合する関連リソースのいずれかが true と評価された場合、効果は条件を満たしているため、監査はトリガーされません。
  - [field()] を使用して、**if** 条件の値と等しいことを確認できます。
  - たとえば、(**if** 条件内の) 親リソースが照合する関連リソースと同じリソースの場所にあることを検証できます。

### <a name="auditifnotexists-example"></a>AuditIfNotExists の例

例:仮想マシンを評価してマルウェア対策の拡張機能が存在するかどうかを判定し、ない場合に監査します。

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

AuditIfNotExists と同様に、DeployIfNotExists ポリシー定義は条件が満たされたときにテンプレートのデプロイを実行します。

> [!NOTE]
> **deployIfNotExists** で [入れ子になったテンプレート](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template)がサポートされていますが、[リンク済みテンプレート](../../../azure-resource-manager/resource-group-linked-templates.md)は現在サポートされていません。

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists の評価

DeployIfNotExists は、リソース プロバイダーでリソースの作成または更新要求が処理され、成功を示す状態コードが返された後で実行されます。 関連するリソースがない場合、または **ExistenceCondition** によって定義されたリソースが true と評価されない場合、テンプレートのデプロイが発生します。

評価サイクル中は、リソースを照合する DeployIfNotExists 効果があるポリシー定義は非準拠としてマークされ、リソースに対するアクションは実行されません。

### <a name="deployifnotexists-properties"></a>DeployIfNotExists のプロパティ

DeployIfNotExists 効果の **details** プロパティは、照合する関連リソースおよび実行するテンプレートのデプロイを定義するすべてのサブプロパティを含みます。

- **Type** [必須]
  - 照合する関連リソースの型を指定します。
  - 最初に **if** 条件リソースの下にあるリソースを取得しようとし、次に **if** 条件リソースと同じリソース グループ内を検索します。
- **Name** (省略可能)
  - 照合するリソースの正確な名前を指定して、指定した型のすべてのリソースではなく 1 つの特定のリソースを取得します。
  - **if.field.type** と **then.details.type** の条件値が一致する場合、**Name** は "_必須_" になり、`[field('name')]` であることが必要です。
- **ResourceGroupName** (省略可能)
  - 別のリソース グループに由来する関連リソースを照合できるようにします。
  - **type** が **if** 条件リソースの下にあるリソースである場合は適用されません。
  - 既定値は、**if** 条件リソースのリソース グループです。
  - テンプレートのデプロイが実行される場合は、この値のリソース グループにデプロイされます。
- **ExistenceScope** (省略可能)
  - 使用できる値は _Subscription_ と _ResourceGroup_ です。
  - 照合する関連リソースを取得する範囲を設定します。
  - **type** が **if** 条件リソースの下にあるリソースである場合は適用されません。
  - _ResourceGroup_ の場合は、**if** 条件リソースのリソース グループまたは **ResourceGroupName** で指定されたリソース グループに制限されます。
  - _Subscription_ の場合は、関連リソースのサブスクリプション全体を検索します。
  - 既定値は _ResourceGroup_ です。
- **ExistenceCondition** (省略可能)
  - 指定されない場合、**type** の関連リソースは効果を満たすため、デプロイはトリガーされません。
  - **if** 条件のポリシー規則と同じ言語が使用されますが、それぞれの関連リソースに対して個別に評価されます。
  - 照合する関連リソースのいずれかが true と評価された場合、効果は条件を満たしているため、デプロイはトリガーされません。
  - [field()] を使用して、**if** 条件の値と等しいことを確認できます。
  - たとえば、(**if** 条件内の) 親リソースが照合する関連リソースと同じリソースの場所にあることを検証できます。
- **roleDefinitionIds** [必須]
  - このプロパティには、サブスクリプションでアクセス可能なロールベースのアクセス制御ロール ID と一致する文字列の配列を含める必要があります。 詳細については、[修復 - ポリシー定義を構成する](../how-to/remediate-resources.md#configure-policy-definition)を参照してください。
- **DeploymentScope** (省略可能)
  - 使用できる値は _Subscription_ と _ResourceGroup_ です。
  - トリガーされるデプロイの種類を設定します。 _Subscription_ は[サブスクリプション レベルでのデプロイ](../../../azure-resource-manager/deploy-to-subscription.md)を示し、_ResourceGroup_ はリソース グループへのデプロイを示します。
  - サブスクリプション レベルのデプロイを使用する場合は、_Deployment_ で _location_ プロパティを指定する必要があります。
  - 既定値は _ResourceGroup_ です。
- **Deployment** [必須]
  - このプロパティは `Microsoft.Resources/deployments` PUT API に渡されるため、完全なテンプレートのデプロイを含める必要があります。 詳細については、[Deployments REST API](/rest/api/resources/deployments) をご覧ください。

  > [!NOTE]
  > **Deployment** プロパティ内のすべての関数が、ポリシーではなくテンプレートのコンポーネントとして評価されます。 例外は、ポリシーからテンプレートに値を渡す **parameters** プロパティです。 このセクションのテンプレート パラメーター名の **value** は、この値渡しを実行するために使用されます (DeployIfNotExists の例の _fullDbName_ を参照)。

### <a name="deployifnotexists-example"></a>DeployIfNotExists の例

例:SQL Server データベースを評価して、transparentDataEncryption が有効になっているかどうかを判断します。 有効になっていない場合は、有効にするためのデプロイが実行されます。

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

この効果は、`Microsoft.ContainerService.Data` のポリシー定義*モード*で使用されます。 これは、[Rego](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego) で定義されている受付制御規則を [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) 上の [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) に渡すために使用されます。

> [!NOTE]
> [Kubernetes 用の Azure Policy](rego-for-aks.md) はパブリック プレビューで、組み込みのポリシー定義のみをサポートします。

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy の評価

Open Policy Agent アドミッション コントローラーは、クラスター上の新しい要求をリアルタイムで評価します。
5 分ごとにクラスターのフル スキャンが完了し、結果が Azure Policy に報告されます。

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy のプロパティ

EnforceRegoPolicy 効果の **details** プロパティには、Rego 受付制御規則を記述するサブプロパティがあります。

- **policyId** [必須]
  - Rego 受付制御規則にパラメーターとして渡される一意の名前。
- **policy** [必須]
  - Rego 受付制御規則の URI を指定します。
- **policyParameters** [省略可能]
  - rego ポリシーに渡すパラメーターと値を定義します。

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy の例

例:AKS で指定されたコンテナー イメージのみを許可する Rego 受付制御規則。

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="layering-policies"></a>階層化ポリシー

リソースは複数の割り当ての影響を受ける可能性があります。 これらの割り当てのスコープは、同じ場合も異なっている場合もあります。 これらの各割り当てにもさまざまな効果が定義されている可能性があります。 各ポリシーの条件と効果は個別に評価されます。 例:

- ポリシー 1
  - リソースの場所を 'westus' に制限する
  - サブスクリプション A に割り当てる
  - Deny 効果
- ポリシー 2
  - リソースの場所を 'eastus' に制限する
  - サブスクリプション A のリソース グループ B に割り当てる
  - Audit 効果
  
この設定の結果は次のようになります。

- リソース グループ B の既存のリソースで、場所が 'eastus' のリソースは、ポリシー 2 に準拠しているが、ポリシー 1 には準拠していない
- リソース グループ B に既に存在するが、場所が 'eastus' でない既存のリソースは、ポリシー 2 に準拠せず、場所が 'westus' でない場合はポリシー 1 にも準拠していない
- サブスクリプション A の新しいリソースで、場所が 'westus' でないリソースは、ポリシー 1 によって拒否される
- サブスクリプション A のリソース グループ B の新しいリソースで、場所が 'westus' であるリソースは作成されるが、ポリシー 2 には準拠していない

ポリシー 1 とポリシー 2 の両方に Deny 効果がある場合、状況は次のように変化します。

- リソース グループ B に既に存在するが、場所が 'eastus' でないリソースは、ポリシー 2 に準拠していない
- リソース グループ B に既に存在するが、場所が 'westus' でないリソースは、ポリシー 1 に準拠していない
- サブスクリプション A の新しいリソースで、場所が 'westus' でないリソースは、ポリシー 1 によって拒否される
- サブスクリプション A のリソース グループ B の新しいリソースは、すべて拒否される

各割り当ては個別に評価されます。 そのため、スコープの違いによって発生する隙間をリソースがすり抜けるチャンスはありません。 ポリシーの階層化またはポリシーの重複による最終的な結果は、**累積的に最も制限が厳しい**と考えられます。 たとえば、ポリシー 1 とポリシー 2 の両方に拒否効果が設定されている場合、重複するポリシーと競合するポリシーによって、リソースがブロックされます。 リソースを対象のスコープ内に必ず作成する必要がある場合は、それぞれの割り当ての除外を見直して、適切なポリシーが適切なスコープに影響を与えていることを確認してください。

## <a name="next-steps"></a>次の手順

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](definition-structure.md)」を確認します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/getting-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。