---
title: "Azure AD Connect: プレビュー段階の機能 |Microsoft Docs"
description: "このトピックでは、Azure AD Connect のプレビュー段階の機能について詳しく説明します。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: b110b2e3041cf7839d1bf1c4d384b28b243efbd8
ms.openlocfilehash: c1653c769a6b42d18ffb0da71220ce06c6556587
ms.lasthandoff: 02/07/2017


---
# <a name="more-details-about-features-in-preview"></a>プレビュー段階の機能の詳細
このトピックでは、現在プレビュー段階の機能を使用する方法について説明します。

## <a name="group-writeback"></a>グループの書き戻し
オプション機能のグループの書き戻し用のオプションでは、**Office 365 グループ**を Exchange がインストールされているフォレストに書き戻すことができます。 これは、クラウドで常に管理されるグループです。 オンプレミスの Exchange をご利用の場合、これらのグループをオンプレミスに書き戻すことができます。オンプレミスの Exchange メールボックスを所有するユーザーは、それらのグループから電子メールを送受信することができます。

Office 365 グループの詳細とその使い方については、 [こちら](http://aka.ms/O365g)をご覧ください。

Office 365 グループは、オンプレミスの AD DS では配布グループとして表現されます。 この新しいグループの種類が表示されるようにするには、オンプレミスの Exchange サーバーを Exchange 2013 累積更新プログラム 8 (2015 年 3 月リリース) または Exchange 2016 で更新する必要があります。

**プレビュー期間中の注意事項**

* 現在、プレビュー版ではアドレス帳の属性は設定されません。 この属性がないと、GAL にグループが表示されません。 この属性は、Exchange PowerShell コマンドレット `update-recipient`を使って設定するのが最も簡単です。
* Exchange のスキーマを持つフォレストのみを、グループの有効なターゲットとすることができます。 Exchange が見つからない場合は、グループの書き戻しを有効にすることはできません。
* 現在サポートされているのは、単一フォレストから成る Exchange 組織のデプロイだけです。 複数の Exchange 組織がオンプレミスに存在する場合、他のフォレストにグループを反映するには、オンプレミスの GALSync ソリューションが必要となります。
* グループの書き戻し機能では、セキュリティ グループまたは配布グループは扱えません。

> [!NOTE]
> グループの書き戻しには、Azure AD Premium のサブスクリプションが必要です。
> 
>

## <a name="user-writeback"></a>ユーザーの書き戻し
> [!IMPORTANT]
> ユーザーの書き戻しプレビュー機能は、Azure AD Connect の 2015 年 8 月の更新時に削除されました。 この機能を有効にしていた場合は、無効にする必要があります。
>
>

## <a name="next-steps"></a>次のステップ
「[Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)」に進んでください。

「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

