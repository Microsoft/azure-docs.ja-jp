---
title: Windows Virtual Desktop における委任されたアクセス - Azure
description: Windows Virtual Desktop のデプロイで管理機能を委任する方法 (例を含む)。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 849a3b1eb1a4648c2eaae7dd564f14b9c511fdcf
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367354"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Windows Virtual Desktop における委任されたアクセス

Windows Virtual Desktop には委任されたアクセス モデルが用意され、これにより特定のユーザーにロールを割り当てることで、それらのユーザーが持つことのできるアクセス数を定義できます。 ロールの割り当てには、セキュリティ プリンシパル、ロールの定義、スコープの 3 つのコンポーネントがあります。 Windows Virtual Desktop の委任されたアクセス モデルは、Azure RBAC モデルに基づいています。 特定のロールの割り当てとそのコンポーネントの詳細については、「[Azure ロールベースのアクセス制御の概要](../role-based-access-control/built-in-roles.md)」を参照してください。

Windows Virtual Desktop の委任されたアクセスでは、ロール割り当ての要素ごとに次の値がサポートされています。

* セキュリティ プリンシパル
    * ユーザー
    * サービス プリンシパル
* ロール定義
    * 組み込みのロール
* Scope
    * テナント グループ
    * テナント
    * ホスト プール
    * アプリ グループ

## <a name="built-in-roles"></a>組み込みのロール

Windows Virtual Desktop の委任されたアクセスには、ユーザーおよびサービス プリンシパルに割り当てることのできるいくつかの組み込みロールの定義があります。

* RDS 所有者は、リソースへのアクセスを含め、すべてを管理できます。
* RDS 共同作業者は、リソースへのアクセス以外のすべてを管理できます。
* RDS 閲覧者は、すべてを閲覧できますが、変更を加えることはできません。
* RDS オペレーターは、診断アクティビティを表示できます。

## <a name="powershell-cmdlets-for-role-assignments"></a>ロール割り当てを目的とした PowerShell のコマンドレット

次のコマンドレットを実行して、ロールの割り当てを作成、表示、削除できます。

* **Get-RdsRoleAssignment** はロール割り当ての一覧を表示します。
* **New-RdsRoleAssignment** は新しいロール割り当てを作成します。
* **Remove-RdsRoleAssignment** はロールの割り当てを削除します。

### <a name="accepted-parameters"></a>使用できるパラメーター

基本的な 3 つのコマンドレットを次のパラメーターで変更できます。

* **AadTenantId**: サービス プリンシパルがメンバーとなる Azure Active Directory テナント ID を指定します。
* **AppGroupName**: リモート デスクトップのアプリ グループの名前です。
* **Diagnostics**: 診断のスコープを示します (**Infrastructure** または **Tenant** パラメーターのいずれかと組み合わせる必要があります)。
* **HostPoolName**: リモート デスクトップのホスト プールの名前です。
* **Infrastructure**: インフラストラクチャのスコープを示します。
* **RoleDefinitionName**: ユーザー、グループ、またはアプリに割り当てられているリモート デスクトップ サービスのロールベース アクセス制御のロールの名前 (リモート デスクトップ サービスの所有者、リモート デスクトップ サービスの閲覧者など)。
* **ServerPrincipleName**: Azure Active Directory アプリケーションの名前。
* **SignInName**: ユーザーの電子メール アドレスまたはユーザー プリンシパル名。
* **TenantName**: リモート デスクトップ テナントの名前。

## <a name="next-steps"></a>次のステップ

各ロールで使用できる PowerShell コマンドレットのより詳細な一覧については、「[PowerShell リファレンス](/powershell/windows-virtual-desktop/overview)」を参照してください。

Windows Virtual Desktop 環境を設定する方法のガイドについては、「[Windows Virtual Desktop 環境](environment-setup.md)」を参照してください。
