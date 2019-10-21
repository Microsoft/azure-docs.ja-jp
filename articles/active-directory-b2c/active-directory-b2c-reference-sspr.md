---
title: Azure Active Directory B2C でのセルフサービスによるパスワードのリセット | Microsoft Docs
description: Azure Active Directory B2C で顧客向けにセルフサービスのパスワード リセットをセットアップする方法を示します
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7903ec669772c3a3858a9c1d514ab3e6de6a2bd4
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936827"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>顧客向けにセルフサービス パスワードのリセットをセットアップする

セルフサービスのパスワード リセット機能を使用すると、ローカル アカウントにサインアップした顧客はパスワードを自分でリセットできます。 これによりサポート スタッフの負担を大幅に軽減でき、アプリケーションを定期的に使用する顧客が何百万人もいるような場合に特に効果的です。 現在、回復の手段としては、検証済みの電子メール アドレスの使用のみがサポートされています。

> [!NOTE]
> この記事は、ID プロバイダーとして**ローカル アカウント サインイン**を使用する、V1 **サインイン** ユーザー フローのコンテキスト内で使用されるセルフ サービスのパスワード リセットに適用されます。 ご利用のアプリから呼び出される完全にカスタマイズ可能なパスワードのリセット ユーザー フローが必要な場合は、[こちらの記事](active-directory-b2c-reference-policies.md)を参照してください。
> 
> 

既定のディレクトリでは、セルフサービスによるパスワードのリセットは無効になっています。 この機能を有効にするには次のようにします。

1. サブスクリプション管理者として [Azure Portal](https://portal.azure.com/) にサインインします。 これは、ディレクトリを作成するときに使用したものと同じ職場/学校アカウント、または同じ Microsoft アカウントです。
2. (左側にあるナビゲーション バーで) **Azure Active Directory** を開きます。
3. オプション ブレードで下にスクロールし、 **[パスワードのリセット]** を選択します。
4. **[セルフ サービスによるパスワードのリセットが有効]** を **[すべて]** に設定します。 
5. ページの上部にある **[保存]** をクリックします。 以上で終わりです。

テストするには、任意のサインイン ユーザー フロー (ID プロバイダーがローカル アカウントのもの) で [今すぐ実行] 機能を使用します。 ローカル アカウントのサインイン ページ (電子メール アドレスとパスワード、またはユーザー名とパスワードを入力する場所) で、 **[アカウントにアクセスできない場合]** をクリックしてカスタマー エクスペリエンスを確認します。

> [!NOTE]
> [セルフサービスによるパスワードのリセット] ページは、 [会社のブランド機能](../active-directory/fundamentals/customize-branding.md)を使用してカスタマイズできます。
> 
> 

