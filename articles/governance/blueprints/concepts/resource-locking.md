---
title: リソース ロックについて
description: ブループリントを割り当てるときにリソースを保護するロック オプションについて説明します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 5c62fdb698dddf293d339904fd0c854052d636eb
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981043"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Azure Blueprint でのリソース ロックについて

一貫性のある環境を大規模に作成することが本当に価値があるのは、一貫性を維持するためのメカニズムがある場合だけです。 この記事では、Azure Blueprint でのリソース ロックのしくみについて説明します。 リソース ロックと "_拒否割り当て_" の適用の例については、[新しいリソースの保護](../tutorials/protect-new-resources.md)に関するチュートリアルを参照してください。

## <a name="locking-modes-and-states"></a>ロック モードと状態

ブループリント割り当てに適用されるロック モードには、次に示す 3 つのオプションがあります。**ロックしない**、**読み取り専用**、**削除しない**。 ロック モードは、ブループリント割り当て中のアーティファクトのデプロイにおいて構成されます。 別のロック モードを設定するには、ブループリント割り当てを更新します。
ただし、ブループリントの外部でロック モードを変更することはできません。

ブループリント割り当て内のアーティファクトによって作成されたリソースは、次の 4 つのいずれかの状態になります。**ロックなし**、**読み取り専用**、**編集/削除できません**、または**削除不可**。 各種のアーティファクトは、**ロックなし**の状態にすることができます。 次の表を使用することで、リソースの状態を特定することができます。

|Mode|アーティファクトのリソースの種類|State|説明|
|-|-|-|-|
|ロックしない|*|ロックなし|リソースはブループリントによって保護されません。 この状態は、ブループリント割り当ての外部から**読み取り専用**または**削除しない**のリソース グループ アーティファクトに追加されるリソースに対しても使用されます。|
|読み取り専用|Resource group|編集/削除できません|リソース グループは読み取り専用であり、リソース グループ上のタグを変更することはできません。 このリソース グループに対しては、**ロックなし**リソースの追加、移動、変更、削除を行うことができます。|
|読み取り専用|非リソース グループ|読み取り専用|いかなる方法でもリソースを変更することはできません。変更することも削除することもできません。|
|削除しない|*|削除不可|リソースを変更することはできますが、削除することはできません。 このリソース グループに対しては、**ロックなし**リソースの追加、移動、変更、削除を行うことができます。|

## <a name="overriding-locking-states"></a>ロック状態をオーバーライドする

通常、サブスクリプションに対する適切な[ロールベースのアクセス制御](../../../role-based-access-control/overview.md) (RBAC) を持つユーザー (所有者ロールのユーザーなど) が、リソースの変更や削除を許可されることはありえます。 ただし、デプロイされた割り当ての一部として Blueprint がロックを適用している場合には、このアクセスは許可されません。 **読み取り専用**オプションまたは**削除しない**オプションを使用して割り当てが設定されている場合、サブスクリプション所有者であっても、ブロックされたアクションを保護されたリソースに対して実行することはできません。

このセキュリティ対策により、定義済みのブループリントの一貫性と作成目的で設計された環境が、偶発的またはプログラムによる削除や変更から保護されます。

## <a name="removing-locking-states"></a>ロック状態を削除する

割り当てによって保護されたリソースを変更または削除する必要が生じた場合、2 つの方法でそれを行うことができます。

- ブループリント割り当てを更新して、ロック モードを**ロックなし**にする
- ブループリント割り当てを削除する

割り当てを削除すると、Blueprint によって作成されたロックが削除されます。 ただし、リソース自体は維持されるので、通常の方法を使用して削除する必要があります。

## <a name="how-blueprint-locks-work"></a>ブループリントのロックのしくみ

ブループリントの割り当て時には、**読み取り専用**オプションまたは**削除しない**オプションがその割り当てによって選択された場合、RBAC [拒否割り当て](../../../role-based-access-control/deny-assignments.md)の拒否アクションがアーティファクト リソースに適用されます。 この拒否アクションは、ブループリント割り当てのマネージド ID によって追加され、アーティファクト リソースから削除するには、同じマネージド ID を使用する必要があります。 このセキュリティ対策により、ロック メカニズムを強制して、Blueprint 外からブループリントのロックを解除できないようにします。

![リソース グループに対するブループリント拒否割り当て](../media/resource-locking/blueprint-deny-assignment.png)

各モードの[拒否割り当てプロパティ](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties)は、次のようになります。

|Mode |Permissions.Actions |Permissions.NotActions |Principals[i].Type |ExcludePrincipals[i].Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|読み取り専用 |**\*** |**\*/read** |SystemDefined (Everyone) |**excludedPrincipals** におけるブループリント割り当てとユーザー定義 |リソース グループ - _true_;リソース - _false_ |
|削除しない |**\*/delete** | |SystemDefined (Everyone) |**excludedPrincipals** におけるブループリント割り当てとユーザー定義 |リソース グループ - _true_;リソース - _false_ |

> [!IMPORTANT]
> Azure Resource Manager では、ロール割り当ての詳細が最大 30 分間キャッシュされます。 そのため、ブループリント リソースに対する拒否割り当ての拒否アクションは、すぐには完全に反映されない場合があります。 その間は、ブルー プリントのロックで保護しようとしたリソースが削除される可能性もあります。

## <a name="exclude-a-principal-from-a-deny-assignment"></a>拒否割り当てからプリンシパルを除外する

一部のデザインまたはセキュリティ シナリオでは、ブルー プリント割り当てによって作成される[割り当て拒否](../../../role-based-access-control/deny-assignments.md)からのプリンシパルの除外が必要な場合があります。 これは、[割り当てを作成する](/rest/api/blueprints/assignments/createorupdate)ときに、REST API 内で **locks** プロパティの **excludedPrincipals** 配列に最大 5 つの値を追加することで実行されます。
これは、**excludedPrincipals** を含む要求本文の例です。

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="next-steps"></a>次の手順

- [新しいリソースの保護](../tutorials/protect-new-resources.md)に関するチュートリアルに従います。
- [ブループリントのライフサイクル](lifecycle.md)を参照する。
- [静的および動的パラメーター](parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](sequencing-order.md)のカスタマイズを参照する。
- [既存の割り当ての更新](../how-to/update-existing-assignments.md)方法を参照する。
- ブループリントの割り当て時の問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する。