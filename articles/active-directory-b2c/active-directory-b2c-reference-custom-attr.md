---
title: Azure Active Directory B2C でカスタム属性を定義する | Microsoft Docs
description: 顧客に関する情報を収集するために、Azure Active Directory B2C でアプリケーションのカスタム属性を定義します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8b32a1eccfc90731d4c3458a0696a8e5900691aa
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727819"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム属性を定義する

 すべての顧客向けアプリケーションには、収集する必要のある情報についての固有の要件があります。 Azure Active Directory (Azure AD) B2C テナントには、名、姓、市区町村、郵便番号などの属性に格納された組み込みの一連の情報が用意されています。 Azure AD B2C では、各顧客アカウントに関して格納されている一連の属性を拡張できます。 
 
 [Azure portal](https://portal.azure.com/) でカスタム属性を作成し、それらをサインアップ ユーザー フロー、サインアップまたはサインイン ユーザー フロー、プロファイル編集ユーザー フローなどで使用できます。 また、 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)を使用してこれらの属性を読み書きすることもできます。 Azure AD B2C のカスタム属性では、[Azure AD Graph API ディレクトリ スキーマ拡張機能](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions)が使用されます。

## <a name="create-a-custom-attribute"></a>カスタム属性を作成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Portal の右上隅でディレクトリを切り替えて、Azure AD B2C テナントが含まれるディレクトリを使用していることを確認してください。 サブスクリプション情報を選択し、**[ディレクトリの切り替え]** を選択します。 

    ![Azure AD B2C テナントに切り替え](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    テナントが含まれるディレクトリを選択します。

    ![新しいディレクトリを選択する](./media/active-directory-b2c-reference-custom-attr/select-directory.png)

3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ユーザー属性]**、**[追加]** の順に選択します。
5. カスタム属性の **[名前]** ("ShoeSize" など) を指定します。
6. **[データ型]** を選択します。 **[文字列]**、**[ブール値]**、および **[Int] (整数)** のみを使用できます。
7. オプションで、情報目的のために **[説明]** を入力します。 
8. **Create** をクリックしてください。

これで、このカスタム属性が **[ユーザー属性]** の一覧やユーザー フローで使用できるようになります。 カスタム属性が作成されるのは、いずれかのユーザー フローで初めて使用されるときだけであり、**[ユーザー属性]** の一覧に追加されるときではありません。

## <a name="use-a-custom-attribute-in-your-user-flow"></a>ユーザー フローでカスタム属性を使用する

1. Azure AD B2C テナントで、**[ユーザー フロー]** を選択します。
2. ポリシー ("B2C_1_SignupSignin" など) を選択して開きます。 
4. **[ユーザー属性]**、カスタム属性 ("ShoeSize" など) の順に選択します。 **[Save]** をクリックします。
5. **[アプリケーションの要求]**、カスタム属性の順に選択します。 
6. **[Save]** をクリックします。

ユーザー フローに対して **[ユーザー フローを実行します]** 機能を使用して、カスタマー エクスペリエンスを確認できます。 これで、**ShoeSize** がサインアップ中に収集される属性の一覧や、アプリケーションに送り返されるトークンに表示されるようになります。

