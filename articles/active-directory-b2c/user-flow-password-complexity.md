---
title: パスワードの複雑さの要件を構成する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でコンシューマーによって指定されるパスワードの複雑さの要件を構成する方法。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 59ac2f74d4753c1f647bedfa2c402e729e25ee62
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76850730"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でパスワードの複雑さの要件を構成する

Azure Active Directory B2C (Azure AD B2C) では、アカウントの作成時にエンド ユーザーが指定するパスワードの複雑さの要件の変更がサポートされます。 既定では、Azure AD B2C では `Strong` パスワードを使います。 Azure AD B2C では、顧客が使用できるパスワードの複雑さを制御する構成オプションもサポートしています。

## <a name="password-rule-enforcement"></a>パスワード ルールの適用

サインアップ時またはパスワードのリセット時に、エンド ユーザーは、複雑さのルールを満たすパスワードを指定する必要があります。 パスワードの複雑さのルールはユーザー フローごとに適用されます。 あるユーザー フローがサインアップ時に 4 桁の PIN を要求し、別のユーザー フローがサインアップ時に 8 文字の文字列を要求することができます。 たとえば、大人と子供とで異なるパスワードの複雑さを持つユーザー フローを使用できます。

パスワードの複雑さは、サインイン時には適用されません。 ユーザーは、現在の複雑さの要件を満たしていないためにサインイン時にパスワードの変更を求められることはありません。

パスワードの複雑さは次の種類のユーザー フローで構成できます。

- サインアップまたはサインインのユーザー フロー
- パスワード リセットのユーザー フロー

カスタム ポリシーを使用している場合、([カスタム ポリシーでパスワードの複雑さを構成する](custom-policy-password-complexity.md)) ことができます。

## <a name="configure-password-complexity"></a>パスワードの複雑さの構成

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ポータル ツールバーの **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントが含まれているディレクトリを選択します。
3. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
4. **[ユーザー フロー (ポリシー)]** を選択します。
2. ユーザー フローを選択し、 **[プロパティ]** をクリックします。
3. **[パスワードの複雑さ]** で、このユーザー フローのパスワードの複雑さを **[シンプル]** 、 **[強]** 、 **[カスタム]** のいずれかに変更します。

### <a name="comparison-chart"></a>比較チャート

| 複雑さ | 説明 |
| --- | --- |
| シンプル | 少なくとも 8 ～ 64 文字のパスワード。 |
| Strong | 少なくとも 8 ～ 64 文字のパスワード。 小文字、大文字、数字、記号の 4 種類のうち 3 種が必要です。 |
| Custom | このオプションでは、パスワードの複雑さのルールを最も細かく制御できます。  カスタムの長さを構成できます。  数字のみのパスワード (PIN) を受け入れることもできます。 |

## <a name="custom-options"></a>カスタム オプション

### <a name="character-set"></a>文字セット

数字のみ (PIN) または完全な文字セットを受け入れることができます。

- **[数字のみ]** では、パスワードを入力するときに数字 (0 ～ 9) のみを使用できます。
- **[すべて]** では、任意の文字、数字、記号を使用できます。

### <a name="length"></a>長さ

パスワードの長さの要件を制御できます。

- **[最短]** は、少なくとも 4 にする必要があります。
- **[最大長]** は最小の長さ以上で、最大 64 文字にすることができます。

### <a name="character-classes"></a>文字クラス

パスワードで使われる異なる文字の種類を制御できます。

- **[2 of 4:Lowercase character, Uppercase character, Number (0-9), Symbol]\(3/4: 小文字、大文字、数字 (0 ～ 9)、記号\)** では、パスワードに少なくとも 3 種類の文字が含まれることを保証します。 たとえば、数値と小文字です。
- **3 of 4:Lowercase character, Uppercase character, Number (0-9), Symbol\(3/4: 小文字、大文字、数字 (0 ～ 9)、記号\)** では、パスワードに少なくとも 3 種類の文字が含まれることを保証します。 たとえば、数値、小文字、大文字です。
- **4 of 4:Lowercase character, Uppercase character, Number (0-9), Symbol\(4/4: 小文字、大文字、数字 (0 ～ 9)、記号\)** では、パスワードにすべての文字種が含まれることを保証します。

    > [!NOTE]
    > **4/4** を要求すると、エンドユーザーが不満を感じます。 いくつかの調査では、この要件にによってパスワードのエントロピは向上しないことが示されています。 [NIST パスワード ガイドライン](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)をご覧ください
