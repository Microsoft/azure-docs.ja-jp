---
title: "Azure Active Directory B2B コラボレーションのトラブルシューティング | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションの一般的な問題の対処方法"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/25/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 42229b338063634480551f26896963d8add5e071
ms.contentlocale: ja-jp
ms.lasthandoff: 05/27/2017


---

# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B コラボレーションのトラブルシューティング

以下に、Azure Active Directory (Azure AD) B2B コラボレーションの一般的な問題のいくつかの対処方法を示します。


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>外部ユーザーを追加しましたが、グローバル アドレス帳またはユーザー選択ウィンドウに表示されません

外部ユーザーが一覧に表示されない場合は、オブジェクトのレプリケートに数分かかっていることがあります。

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>B2B のゲスト ユーザーが SharePoint Online/OneDrive ユーザー選択ウィンドウに表示されません 
 
SharePoint Online (SPO) のユーザー選択ウィンドウで既存のゲスト ユーザーを検索する機能は、従来の動作と一致させるために、既定ではオフになっています。

この機能は、テナントとサイト コレクション レベルで 'ShowPeoplePickerSuggestionsForGuestUsers' 設定を使用することで有効にできます。 この機能は、Set-SPOTenant コマンドレットと Set-SPOSite コマンドレットを使用して設定できます。これにより、メンバーは、ディレクトリ内のすべての既存のゲスト ユーザーを検索することができます。 テナントのスコープの変更は、既にプロビジョニングされている SPO サイトには影響しません。

## <a name="invitations-have-been-disabled-for-directory"></a>ディレクトリに対して招待が無効になっています

ユーザーを招待するアクセス許可がないことを通知された場合は、自分のユーザー アカウントが外部ユーザーの招待を承認されていることを確認します。

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

これらの設定を変更したり、ユーザーにゲストの招待元ロールを割り当てたりしたばかりの場合は、変更が有効になるまでに 15 ～ 60 分かかることがあります。

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>招待したユーザーが招待に応じようとしたときにエラー メッセージが表示されます

一般的なエラーの理由は、次のとおりです。

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>招待されたユーザーの管理者が、メールで確認済みのユーザーをテナント内に作成することを許可していません

Azure Active Directory を使用している組織のユーザーを招待していて、その特定のユーザーのアカウントが存在しない場合 (たとえばユーザーが AAD contoso.com に存在しない場合)、 contoso.com の管理者が、ユーザーが作成されないようにするポリシーを設定している可能性があります。 ユーザーは、外部ユーザーが許可されるかどうかを管理者に確認する必要があります。 外部ユーザーの管理者は、メールで確認済みのユーザーをドメインで許可しなければならない場合があります (メールで確認済みのユーザーの許可については、[こちらの記事](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)を参照してください)。

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>外部ユーザーがフェデレーション ドメインに既に存在しません

フェデレーション認証を使用しているときに、ユーザーが Azure Active Directory に既に存在しない場合は、そのユーザーを招待することはできません。

この問題を解決するには、外部ユーザーの管理者がユーザーのアカウントを Azure Active Directory に同期する必要があります。

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>通常は無効な文字である "\#" は、どのように Azure AD と同期しますか。

"\#" は、Azure AD B2B コラボレーションまたは外部ユーザー向けの UPN 内の予約文字です。理由は、招待されたアカウント user@contoso.com が user_contoso.com#EXT@fabrikam.onmicrosoft.com になるためです。 したがって、オンプレミスから送信されるUPN 内の \# は、Azure ポータルにサインインするために使用することはできません。 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>同期済みグループに外部ユーザーを追加すると、エラー メッセージが表示されます

外部ユーザーは、"割り当て済み" または "セキュリティ" グループのみに追加できます。オンプレミスで管理されているグループには追加できません。

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>外部ユーザーが招待の電子メールを受信しませんでした

招待されるユーザーは、Invites@microsoft.com というアドレスが許可されていることを ISP またはスパム フィルターで確認する必要があります。

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>カスタム メッセージが招待メッセージに含まれないことがあります

プライバシーに関する法律を順守するため、以下に該当する場合、API は、電子メール招待状にカスタム メッセージを含めません。

- 招待元が招待元のテナントで電子メール アドレスを持っていない場合
- appservice プリンシパルが招待を送信する場合

これがお客様にとって重要なシナリオである場合は、API による招待メール送信を抑制し、選択した電子メール メカニズムを使用して送信できます。 お客様の組織の弁護士に相談して、この方法による電子メールの送信がプライバシーに関する法律を順守していることを確認してください。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [招待を使用せずに B2B コラボレーション ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

