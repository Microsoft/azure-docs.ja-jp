---
title: Azure AD の接続済みサービスでエラーを診断する (Visual Studio)
description: Active Directory の接続済みサービスで、サポートされていない認証の種類が検出された
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 7b437e3117540719c8c0adc5701ac1a5e934340b
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88114474"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Azure Active Directory の接続済みサービスでエラーを診断する

Azure Active Director 接続済みサービスを使って以前の認証コードが検出されたときに、互換性のない認証の種類が検出されました。

プロジェクトで以前の認証コードを正しく検出するには、プロジェクトを再構築する必要があります。 このエラーが発生したときに、以前の認証コードがない場合は、再構築して、もう一度やり直してください。

## <a name="project-types"></a>プロジェクトの種類

接続済みサービスは、プロジェクトに適切な認証ロジックを挿入できるように、開発しているプロジェクトの種類を確認します。 プロジェクトに `ApiController` から派生したコントローラーが含まれている場合、プロジェクトは WebAPI プロジェクトと見なされます。 プロジェクトに `MVC.Controller` から派生したコントローラーしか含まれていない場合は、MVC プロジェクトと見なされます。 接続済みサービスでは他の種類のプロジェクトはサポートされません。

## <a name="compatible-authentication-code"></a>互換性のある認証コード

接続済みサービスは、以前にサービスで構成されたか、サービスと互換性のある認証設定が存在するかどうかも確認します。 すべての設定が存在する場合は、再入可能なケースと見なされ、接続済みサービスが開き、設定が表示されます。  一部の設定のみが存在する場合は、エラー ケースと見なされます。

MVC プロジェクトの場合、接続済みサービスは、以前にサービスを使用して構成された次の設定のいずれかが存在するかどうかを確認します。

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:AADInstance" value="" />
<add key="ida:PostLogoutRedirectUri" value="" />
```

さらに、以前に接続済みサービスを使用して構成された、Web API プロジェクトの次の設定のいずれかが存在するかどうかを確認します。

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:Audience" value="" />
```

## <a name="incompatible-authentication-code"></a>互換性のない認証コード

最後に、接続済みサービスは、Visual Studio の以前のバージョンで構成された認証コードのバージョンを検出しようと試みます。 このエラーが発生した場合、プロジェクトで互換性のない認証の種類が検出されたことを表します。 接続済みサービスは、Visual Studio の以前のバージョンに対して次の種類の認証を検出します。

* Windows 認証
* 個々のユーザー アカウント
* 組織アカウント

MVC プロジェクトで Windows 認証を検出するために、接続済みサービスは `web.config` ファイルで `authentication` 要素を探します。

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Web API プロジェクトで Windows 認証を検出するために、接続済みサービスはプロジェクトの `.csproj` ファイルで `IISExpressWindowsAuthentication` 要素を探します。

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

個々のユーザー アカウント認証を検出するために、接続済みサービスは `packages.config` ファイルで package 要素を探します。

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

組織アカウント認証の古い形式を検出するために、接続済みサービスは `web.config`ファイルで次の要素を探します:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

認証の種類を変更するには、互換性のない認証の種類を削除して、再度接続済みサービスの追加を試行します。

詳細については、「 [Azure AD の認証シナリオ](./authentication-vs-authorization.md)」を参照してください。