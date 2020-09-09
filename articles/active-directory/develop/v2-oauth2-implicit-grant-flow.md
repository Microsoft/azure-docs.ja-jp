---
title: OAuth 2.0 暗黙的な許可のフロー - Microsoft ID プラットフォーム | Azure
description: Microsoft ID プラットフォームの暗黙的なフローを使用してシングルページ アプリをセキュリティで保護します。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: fbe74b62352babf7a1fdd93bf19a6e1475e3f032
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85553572"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft ID プラットフォームと暗黙的な許可のフロー

Microsoft ID プラットフォーム エンドポイントを使ったシングル ページ アプリでは、ユーザーは、Microsoft の個人アカウントと職場/学校アカウントのどちらでもサインインできます。 シングル ページ アプリなどの主にブラウザーで実行される JavaScript アプリには、認証に関して重要な課題があります。

* これらのアプリのセキュリティ特性は、従来のサーバーベースの Web アプリケーションとは大きく異なります。
* 多くの承認サーバーや ID プロバイダーでは、CORS 要求をサポートしていません。
* アプリからブラウザーにフル ページがリダイレクトされると、ユーザー エクスペリエンスに大きな悪影響が及びます。

これらのアプリケーション (Angular、Ember.js、React.js など) の場合、Microsoft ID プラットフォームでは OAuth 2.0 の暗黙的な許可のフローをサポートしています。 この暗黙的フローは、[OAuth 2.0 仕様](https://tools.ietf.org/html/rfc6749#section-4.2)で規定されています。 主な利点は、バックエンド サーバーとの資格情報交換を実行しなくても、アプリが Microsoft ID プラットフォームからトークンを取得できることです。 これにより、アプリは、ユーザーのサインイン、セッションの維持、他の Web API へのトークンの取得をすべてクライアント JavaScript コード内で実行できます。 暗黙的フローを使用する際に考慮が必要なセキュリティに関する重要事項がいくつかあります。具体的には、[クライアント](https://tools.ietf.org/html/rfc6749#section-10.3)と[ユーザーの偽装](https://tools.ietf.org/html/rfc6749#section-10.3)に関する事項です。

この記事では、アプリケーションでプロトコルに対して直接プログラミングする方法について説明します。  可能な場合は、[トークンを取得してセキュリティで保護された Web API を呼び出す](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)代わりに、サポートされている Microsoft 認証ライブラリ (MSAL) を使用することをお勧めします。  また、[MSAL を使用するサンプル アプリ](sample-v2-code.md)も参照してください。

シングル ページ アプリケーションでライブラリを使用せずに、自分でプロトコル メッセージを送信する場合は、次の一般的な手順を実行してください。

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>OAuth2 の暗黙的な許可の適切なシナリオ

OAuth2 の仕様で明らかにされているとおり、暗黙的な許可はユーザー エージェント アプリケーション (つまり、ブラウザー内で実行される JavaScript アプリケーション) を実現にするために考案されました。 このようなアプリケーションに特徴的なのは、サーバー リソース (通常は Web API) にアクセスするため、そしてアプリケーション ユーザー エクスペリエンスを適宜更新するために、JavaScript コードが使用されるという点です。 Gmail や Outlook Web Access のようなアプリケーションを考えてみてください。受信トレイからメッセージを選択したときに、メッセージ視覚化パネルのみが変更されて新しい選択内容が表示され、ページの残りの部分は変更されません。 この特性は、ユーザー操作ごとにページ全体がポストバックされ、新しいサーバー応答によりページ全体がレンダリングされる、従来のリダイレクト ベースの Web アプリとは対照的です。

JavaScript ベースのアプローチを最大限に活用するアプリケーションは、シングルページ アプリケーション (SPA) と呼ばれます。 これらのアプリケーションは最初の HTML ページと関連する JavaScript のみを提供し、後続のすべてのやりとりは JavaScript を介して実行される Web API 呼び出しによって行われるという考えです。 ただし、ハイブリッド アプローチも珍しくはありません。このアプローチでは、アプリケーションはほとんどの場合ポストバックに基づきますが、JS 呼び出しを実行することもあります。暗黙的フローの使用方法に関する議論は、ハイブリッド アプローチにも当てはまります。

リダイレクト ベースのアプリケーションでは通常、Cookie を使用して要求をセキュリティ保護します。ただし、このアプローチは JavaScript アプリケーションではうまくいきません。 Cookie はその生成元のドメインに対してのみ機能しますが、JavaScript 呼び出しは他のドメインにリダイレクトされる可能性があるためです。 これは実際、多くの場合に発生します。Microsoft Graph API、Office API、Azure API を呼び出すアプリケーションを考えてみてください。これらの API はいずれもアプリケーションが動作するドメインの外部にあります。 JavaScript アプリケーションでは、バックエンドを一切持たず、サード パーティの Web API に 100% 依存してビジネス機能を実装する傾向が強まっています。

現時点で推奨される Web API 呼び出しの保護方法は、OAuth2 べアラー トークンのアプローチを使用する方法です。このアプローチでは、すべての呼び出しに OAuth2 のアクセス トークンが付随します。 Web API は受信したアクセス トークンを調べ、アクセス トークン内に必要なスコープが見つかった場合に、要求された操作へのアクセス許可を与えます。 暗黙的フローは、JavaScript アプリケーションが Web API 用のアクセス トークンを取得するための便利なメカニズムを提供するものであり、Cookie に関してさまざまなメリットがあります。

* クロス オリジン呼び出しをしなくても、トークンを確実に取得できます。トークンが返されるリダイレクト URI の登録が必須であるため、トークンが置換されないことが保証されます。
* JavaScript アプリケーションは、ドメインの制限なしに、対象とする Web API の数だけ、必要な数のアクセス トークンを取得できます。
* セッションやローカル ストレージなどの HTML5 機能ではトークンのキャッシュと有効期間管理にフル コントロールを許可しますが、Cookie の管理はアプリにとって非透過的です。
* アクセス トークンはクロスサイト リクエスト フォージェリ (CSRF) 攻撃をあまり受けません

暗黙的な許可フローでは、主にセキュリティ上の理由から、更新トークンを発行しません。 更新トークンはアクセス トークンほどスコープが狭義ではないため、多くの権限を付与すると、リークされた場合のダメージが大きくなります。暗黙的フローでは、トークンは URL の形式で配信されるため、傍受されるリスクが認証コード付与よりも高くなります。

ただし、JavaScript アプリケーションには、アクセス トークンを更新するために廃棄する際、ユーザーに資格情報を繰り返し求めない別のメカニズムがあります。 アプリケーションは非表示の iframe を使用して、Azure AD の承認エンドポイントに対して新しいトークン要求を実行します。ブラウザーが Azure AD ドメインに対してアクティブなセッション (読み取り: セッション Cookie あり) を持っている限り、この認証要求は正常に行われ、ユーザーの操作は必要ありません。

このモデルにより、JavaScript アプリケーションは独立してアクセス トークンを更新できるようになるほか、(ユーザーが事前に同意している場合は) 新しい API の新しいアクセス トークンを取得することさえ可能になります。 これにより、更新トークンなどの高価値のアーティファクトを取得、管理、保護する負担を増やさずに済みます。 サイレント更新を可能にするアーティファクト、Azure AD のセッション Cookie は、アプリケーションの外部で管理されます。 このアプローチのもう 1 つの利点は、いずれかのブラウザー タブで実行されている、Azure AD にサインインしているいずれかのアプリケーションを使用して、ユーザーが Azure AD からサインアウトできることです。 サインアウトすると、Azure AD のセッション Cookie が削除され、JavaScript アプリケーションは自動的に、サインアウトしたユーザーのトークンを更新できなくなります。

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>暗黙的な許可に適切なアプリ

暗黙的な付与は他の付与よりリスクが大きくなります。また、注意を払うべき領域が詳しく記録されます (たとえば、「[Misuse of Access Token to Impersonate Resource Owner in Implicit Flow (暗黙的フローでの偽装リソース所有者に対するアクセス トークンの誤用)][OAuth2-Spec-Implicit-Misuse]」や「[OAuth 2.0 Threat Model and Security Considerations (OAuth 2.0 の脅威モデルとセキュリティの考慮事項)][OAuth2-Threat-Model-And-Security-Implications]」)。 ただし、よりリスクが高いプロファイルは、多くの場合、リモート リソースによってブラウザーに対して処理されるアクティブ コードを実行するアプリケーションを有効にしなければならないという事実に起因します。 SPA アーキテクチャを計画していて、バックエンド コンポーネントがない場合、または JavaScript を使用して Web API を呼び出そうとしている場合は、トークンの取得に暗黙的フローを使用することをお勧めします。

アプリケーションがネイティブ クライアントの場合は、暗黙的フローはあまり向いていません。 ネイティブ クライアントのコンテキストには Azure AD のセッション Cookie がないので、アプリケーションには存続期間の長いセッションを維持する手段がありません。 つまり、アプリケーションは新しいリソースのアクセス トークンを取得する場合、繰り返しユーザーに求めることになります。

バックエンドを含む Web アプリケーションを開発しており、そのバックエンド コードから API を使用する場合も、暗黙的フローはあまり向いていません。 他の方法の方がはるかに便利です。 たとえば、OAuth2 クライアント資格情報付与では、ユーザー委任とは対照的に、アプリケーション自体に割り当てられているアクセス許可を反映したトークンを取得できます。 これは、ユーザーがセッションにアクティブに関与していない場合などでも、クライアントがプログラムによるリソース アクセスを維持できることを意味します。 メリットはそれだけにとどまりません。このような付与では、セキュリティ保証が強化されます。 たとえば、アクセス トークンがユーザーのブラウザーを通過せず、ブラウザーの履歴に保存されるなどのリスクがありません。 また、クライアント アプリケーションは、トークンの要求時に強力な認証を実行できます。

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>プロトコルのダイアグラム

次の図は、暗黙的なサインイン フローの全体像を示しています。各手順については、この後のセクションで詳しく説明します。

![暗黙的なサインイン フローを示す図](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>サインイン要求を送信する

最初にユーザーをアプリにサインインするために、[OpenID Connect](v2-protocols-oidc.md) 認証要求を送信し、Microsoft ID プラットフォーム エンドポイントから `id_token` を取得します。

> [!IMPORTANT]
> ID トークンおよびアクセス トークンを正しく要求するには、[Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページのアプリ登録で、 **[暗黙の付与]** セクションの **[ID トークン]** および **[アクセス トークン]** を選択して、対応する暗黙的な許可フローを有効にする必要があります。 それが有効でない場合は、`unsupported_response` エラー **The provided value for the input parameter 'response_type' is not allowed for this client.Expected value is 'code' (入力パラメーター 'response_type' に入力された値はこのクライアントで許可されません。入力できる値は 'code' です。)** が返されます

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> 暗黙的フローを使用したサインインをテストするには、<a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. をクリックします。</a>サインイン後、ブラウザーは `https://localhost/myapp/` にリダイレクトされ、アドレス バーに `id_token` が含まれた状態になります。
>

| パラメーター | Type | 説明 |
| --- | --- | --- |
| `tenant` | required |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。 |
| `client_id` | required | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページでアプリに割り当てられたアプリケーション (クライアント) ID。 |
| `response_type` | required |OpenID Connect サインインでは、 `id_token` を指定する必要があります。 response_type `token` が含まれる場合もあります。 ここで `token` を使用すると、アプリでは承認エンドポイントへ 2 度目の要求を行うことなく、承認エンドポイントからアクセス トークンをすぐに受け取ることができます。 `token` response_type を使用する場合、`scope` パラメーターには、トークンを発行するリソースを示すスコープを含める必要があります (たとえば、Microsoft Graph では user.read)。  |
| `redirect_uri` | 推奨 |アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| `scope` | required |[スコープ](v2-permissions-and-consent.md)のスペース区切りリスト。 OpenID Connect (id_tokens) では、スコープとして `openid` を指定する必要があります。このスコープは、承認 UI で "サインイン" アクセス許可に変換されます。 必要に応じて、その他のユーザー データにアクセスするために `email` および `profile` スコープを含めることも可能です。 アクセス トークンを要求する場合、さまざまなリソースに対する同意を求めるこの要求に、他のスコープが含まれていてもかまいません。 |
| `response_mode` | 省略可能 |結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。 既定値は、アクセス トークンだけのクエリ (ただし、要求に id_token が含まれている場合はフラグメント) です。 |
| `state` | 推奨 |要求に含まれ、かつトークンの応答として返される値。 任意の文字列を指定することができます。 [クロスサイト リクエスト フォージェリ攻撃を防ぐ](https://tools.ietf.org/html/rfc6749#section-10.12)ために通常、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| `nonce` | required |要求に追加する (アプリによって生成された) 値。この値が、最終的な id_token に要求として追加されます。 アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。 通常この値はランダム化された一意の文字列になっており、要求の送信元を特定する際に使用できます。 Id_token が要求された場合のみ必須です。 |
| `prompt` | 省略可能 |ユーザーとの必要な対話の種類を指定します。 現時点で有効な値は 'login'、'none'、'select_account'、'consent' だけです。 `prompt=login` は、その要求でユーザーに資格情報の入力を強制させ、シングル サインオンを無効にします。 `prompt=none` はその反対であり、ユーザーにどのような対話型プロンプトも表示されないようにします。 シングル サインオンで確認なしで要求を完了できない場合は、Microsoft ID プラットフォーム エンドポイントからエラーが返されます。 `prompt=select_account` は、ユーザーを、セッションで記憶されているすべてのアカウントが表示されるアカウント ピッカーに送ります。 `prompt=consent` では、ユーザーがサインインした後で OAuth 同意ダイアログが表示され、アプリへのアクセス許可の付与をユーザーに求めます。 |
| `login_hint`  |省略可能 |ユーザー名が事前にわかっている場合、ユーザーに代わって事前に、サインイン ページのユーザー名/電子メール アドレス フィールドに入力ができます。 アプリはしばしば前回のサインインから `preferred_username` 要求を抽出して再認証時にこのパラメーターを使用します。|
| `domain_hint` | 省略可能 |これが含まれていると、ユーザーがサインイン ページで実行する電子メール ベースの検出プロセスがスキップされ、多少効率化されたユーザー エクスペリエンスが提供されます。 これは、1 つのテナントで動作する基幹業務アプリで一般的に使用され、アプリでは特定のテナント内のドメイン名が提供されます。  これにより、ユーザーはそのテナントのフェデレーション プロバイダーに転送されます。  ゲストはこのアプリケーションにサインインできなくなることに注意してください。  |

現時点では、ユーザーに資格情報の入力と認証が求められます。 Microsoft ID プラットフォーム エンドポイントではまた、ユーザーが `scope` クエリ パラメーターに示されたアクセス許可に同意していることも確認されます。 ユーザーが同意したアクセス許可がこれらの中に**ない**場合、必要なアクセス許可に同意するようユーザーに求めます。 詳細については、[アクセス許可、同意、およびマルチ テナント アプリ](v2-permissions-and-consent.md)に関するページを参照してください。

ユーザー本人であることを証明書し、同意の許可を与えると、Microsoft ID プラットフォーム エンドポイントは `response_mode` パラメーターで指定された方法を使用して、指定された `redirect_uri` でアプリに応答を返します。

#### <a name="successful-response"></a>成功応答

`response_mode=fragment` と `response_type=id_token+token` を使用した成功応答は、次のようになります (読みやすいように改行してあります)。

```HTTP
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| パラメーター | 説明 |
| --- | --- |
| `access_token` |`response_type` に `token` が含まれる場合に含まれます。 アプリが要求したアクセス トークン。 アクセス トークンはデコードしないようにする必要があります。そうしないと、検証した場合に不透明な文字列として扱われます。 |
| `token_type` |`response_type` に `token` が含まれる場合に含まれます。 常に `Bearer` になります。 |
| `expires_in`|`response_type` に `token` が含まれる場合に含まれます。 キャッシュ用に有効なトークンの秒数を示します。 |
| `scope` |`response_type` に `token` が含まれる場合に含まれます。 access_token が有効なスコープを示します。 要求されたスコープをユーザーに適用できなかった場合 (ログインのために個人アカウントが使用されているときに Azure AD のみのスコープが要求された場合)、必ずしもすべてのスコープが含まれないことがあります。 |
| `id_token` | 署名付き JSON Web トークン (JWT)。 アプリは、このトークンのセグメントをデコードすることによって、サインインしたユーザーに関する情報を要求することができます。 この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。 id_token の詳細については、[`id_token reference`](id-tokens.md)を参照してください。 <br> **注:** `openid` スコープが要求された場合のみ提供されます。 |
| `state` |要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

#### <a name="error-response"></a>エラー応答

アプリ側でエラーを適切に処理できるよう、 `redirect_uri` にはエラー応答も送信されます。

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| パラメーター | 説明 |
| --- | --- |
| `error` |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| `error_description` |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

## <a name="getting-access-tokens-silently-in-the-background"></a>バックグラウンドでサイレントにアクセス トークンを取得する

これでユーザーをシングルページ アプリにサインインさせたので、[Microsoft Graph](https://developer.microsoft.com/graph) などの Microsoft ID プラットフォームによってセキュリティ保護された Web API を呼び出すためのアクセス トークンをサイレントに取得できます。 このメソッドを使用すると、`token` response_type を使用してトークンを既に取得している場合でも、再度サインインするためにユーザーをリダイレクトする必要なく、その他のリソースのトークンを取得できます。

通常の OpenID Connect/OAuth フローでは、これは Microsoft ID プラットフォームの `/token` エンドポイントに要求を発行することによって行います。 ただし、Microsoft ID プラットフォーム エンドポイントは CORS 要求をサポートしていないため、AJAX 呼び出しによってトークンを取得または更新することは不可能です。 代わりに、非表示の iframe で暗黙的フローを使用して、他の Web API 用の新しいトークンを取得できます。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

URL のクエリ パラメーターの詳細については、「[サインイン要求を送信する](#send-the-sign-in-request)」を参照してください。

> [!TIP]
> 以下の要求をコピーしてブラウザー タブに貼り付けてみてください (`login_hint` の値をユーザーの正しい値に置き換えてください)。
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

`prompt=none` パラメーターに応じて、要求はすぐに成功または失敗し、アプリケーションに戻ります。 成功すると、`response_mode` パラメーターで指定された方法を使用して、指定された `redirect_uri` でアプリに応答が送信されます。

#### <a name="successful-response"></a>成功応答

`response_mode=fragment` を使用した場合の正常な応答は次のようになります。

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| パラメーター | 説明 |
| --- | --- |
| `access_token` |`response_type` に `token` が含まれる場合に含まれます。 この場合は、Microsoft Graph 用にアプリケーションが要求したアクセス トークンです。 アクセス トークンはデコードしないようにする必要があります。そうしないと、検証した場合に不透明な文字列として扱われます。 |
| `token_type` | 常に `Bearer` になります。 |
| `expires_in` | キャッシュ用に有効なトークンの秒数を示します。 |
| `scope` | access_token が有効なスコープを示します。 要求されたスコープをユーザーに適用できなかった場合 (ログインのために個人アカウントが使用されているときに Azure AD のみのスコープが要求された場合)、必ずしもすべてのスコープが含まれないことがあります。 |
| `id_token` | 署名付き JSON Web トークン (JWT)。 `response_type` に `id_token` が含まれる場合に含まれます。 アプリは、このトークンのセグメントをデコードすることによって、サインインしたユーザーに関する情報を要求することができます。 この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。 id_token の詳細については、[`id_token` のリファレンス](id-tokens.md)を参照してください。 <br> **注:** `openid` スコープが要求された場合のみ提供されます。 |
| `state` |要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

#### <a name="error-response"></a>エラー応答

アプリ側で適切に処理できるように、 `redirect_uri` にエラーの応答が送信される場合もあります。 `prompt=none`の場合、予期されるエラーは次のようになります。

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| パラメーター | 説明 |
| --- | --- |
| `error` |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| `error_description` |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

Iframe 要求でこのエラーを受信した場合、ユーザーは対話形式でもう一度サインインして新しいトークンを取得する必要があります。 この場合は、アプリケーションに適した任意の方法で処理できます。

## <a name="refreshing-tokens"></a>トークンを更新する

暗黙的な付与では、更新トークンが与えられません。 `id_token` と `access_token` はどちらも短時間で期限切れになるため、トークンを定期的に更新するようにアプリを準備しておく必要があります。 どちらの種類のトークンを更新する場合も、ID プラットフォームの動作を制御するための `prompt=none` パラメーターを使用して、上と同じ非表示の iframe 要求を実行できます。 新しい `id_token` を受け取りたい場合は、`response_type` の `id_token` と `scope=openid` に加えて、`nonce` パラメーターも使用してください。

## <a name="send-a-sign-out-request"></a>サインアウト要求を送信する

OpenID Connect `end_session_endpoint` を使用すると、アプリから Microsoft ID プラットフォーム エンドポイントへの要求をエンド ユーザーのセッションに送信し、Microsoft ID プラットフォーム エンドポイントによって設定された Cookie をクリアできます。 ユーザーが Web アプリケーションから完全にサインアウトするには、アプリがユーザーとのセッションを終了し (通常、トークン キャッシュをクリアするか Cookie を切断する)、ブラウザーを以下にリダイレクトする必要があります。

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| パラメーター | Type | 説明 |
| --- | --- | --- |
| `tenant` |required |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。 |
| `post_logout_redirect_uri` | 推奨 | ログアウト完了後にユーザーが戻る URL。 この値は、アプリケーションに登録されているリダイレクト URI のいずれかと一致する必要があります。 一致しない場合、Microsoft ID プラットフォーム エンドポイントにより汎用メッセージが表示されます。 |

## <a name="next-steps"></a>次のステップ

* [MSAL JS のサンプル](sample-v2-code.md)を見直して、コーディング作業を開始します。

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
