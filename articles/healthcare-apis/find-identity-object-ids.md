---
title: 認証のための ID オブジェクト ID を見つける - Azure API for FHIR
description: この記事では、Azure API for FHIR の認証を構成するために必要な ID オブジェクト ID を見つける方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 5b42d61d59a3c816c3b664297470cfbf91f17439
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851768"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>認証構成のための ID オブジェクト ID を見つける

この記事では、データ プレーンに[外部またはセカンダリ Active Directory テナントを使用する](configure-local-rbac.md)ように Azure API for FHIR を構成するときに必要な ID オブジェクト ID を見つける方法について説明します。

## <a name="find-user-object-id"></a>ユーザーのオブジェクト ID を見つける

ユーザー名 `myuser@consoso.com` を持つユーザーがいる場合は、次の PowerShell コマンドを使用してユーザー `ObjectId` を見つけることができます。

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

または、次の Azure CLI を使用できます。

```azurecli-interactive
az ad user show --id myuser@consoso.com --query objectId --out tsv
```

## <a name="find-service-principal-object-id"></a>サービス プリンシパル オブジェクト ID を見つける

[サービス クライアント アプリ](register-service-azure-ad-client-app.md)を登録しており、このサービス クライアントでの Azure API for FHIR へのアクセスを許可する場合は、次の PowerShell コマンドを使用してクライアント サービス プリンシパルのオブジェクト ID を見つけることができます。

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

ここで、`XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` はサービス クライアント アプリケーション ID です。 あるいは、そのサービス クライアントの `DisplayName` を使用できます。

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Azure CLI を使用している場合は、次を使用できます。

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX --query objectId --out tsv
```

## <a name="find-a-security-group-object-id"></a>セキュリティ グループ オブジェクト ID を見つける

セキュリティ グループのオブジェクト ID を探す場合は、次の PowerShell コマンドを使用できます。

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
ここで `mygroup` は、関心のあるグループの名前です。

Azure CLI を使用している場合は、次を使用できます。

```azurecli-interactive
az ad group show --group "mygroup" --query objectId --out tsv
```

## <a name="next-steps"></a>次のステップ

この記事では、外部またはセカンダリ Active Directory テナントを使用するように Azure API for FHIR を構成するときに必要な ID オブジェクト ID を見つける方法について学習しました。 次に、オブジェクト ID を使用してローカル RBAC 設定を構成する方法について説明します。
 
>[!div class="nextstepaction"]
>[ローカル RBAC 設定を構成する](configure-local-rbac.md)
