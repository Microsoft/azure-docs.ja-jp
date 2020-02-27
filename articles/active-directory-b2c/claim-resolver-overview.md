---
title: カスタム ポリシーでの要求リゾルバー
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 内のカスタム ポリシーで要求リゾルバーを使用する方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/17/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4434c877f69391f5dc5926c6aed07049ba46b7b7
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425648"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C カスタム ポリシーでの要求リゾルバーについて

Azure Active Directory B2C (Azure AD B2C) [カスタム ポリシー](custom-policy-overview.md)での要求リゾルバーは、ポリシー名、要求の相関 ID、ユーザー インターフェイス言語など、承認要求に関するコンテキスト情報を提供します。

入力要求または出力要求で要求リゾルバーを使用するには、[ClaimsSchema](claimsschema.md) 要素の下で文字列 **ClaimType** を定義した後、入力または出力の要求要素で **DefaultValue** を要求リゾルバーに設定します。 Azure AD B2C によって要求リゾルバーの値が読み取られて、技術プロファイルで使用されます。

次の例では、`correlationId` という名前の要求の種類が、`string` の **DataType** で定義されています。

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

技術プロファイルでは、要求リゾルバーが要求の種類にマップされます。 Azure AD B2C では、要求リゾルバー `{Context:CorrelationId}` の値が要求 `correlationId` に設定されて、技術プロファイルに要求が送信されます。

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>要求リゾルバーの種類

以下のセクションでは、使用できる要求リゾルバーの一覧を示します。

### <a name="culture"></a>カルチャ

| 要求 | 説明 | 例 |
| ----- | ----------- | --------|
| {Culture:LanguageName} | 言語に対する 2 文字の ISO コード。 | en |
| {Culture:LCID}   | 言語コードの LCID。 | 1033 |
| {Culture:RegionName} | リージョンに対する 2 文字の ISO コード。 | US |
| {Culture:RFC5646} | RFC5646 言語コード。 | ja-JP |

### <a name="policy"></a>ポリシー

| 要求 | 説明 | 例 |
| ----- | ----------- | --------|
| {Policy:PolicyId} | 証明書利用者ポリシーの名前。 | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | 証明書利用者ポリシーのテナント ID。 | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | 証明書利用者ポリシーのテナント オブジェクト ID。 | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | 信頼フレームワークのテナント ID。 | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| 要求 | 説明 | 例 |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |`acr_values` クエリ文字列パラメーター。 | 該当なし |
| {OIDC:ClientId} |`client_id` クエリ文字列パラメーター。 | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |`domain_hint` クエリ文字列パラメーター。 | facebook.com |
| {OIDC:LoginHint} |  `login_hint` クエリ文字列パラメーター。 | someone@contoso.com |
| {OIDC:MaxAge} | `max_age`。 | 該当なし |
| {OIDC:Nonce} |`Nonce` クエリ文字列パラメーター。 | defaultNonce |
| {OIDC:Prompt} | `prompt` クエリ文字列パラメーター。 | ログイン (login) |
| {OIDC:Resource} |`resource` クエリ文字列パラメーター。 | 該当なし |
| {OIDC:scope} |`scope` クエリ文字列パラメーター。 | openid |

### <a name="context"></a>Context

| 要求 | 説明 | 例 |
| ----- | ----------- | --------|
| {Context:BuildNumber} | Identity Experience Framework のバージョン (ビルド番号)。  | 1.0.507.0 |
| {Context:CorrelationId} | 関連付け ID。  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |UTC での日時。  | 10/10/2018 12:00:00 PM |
| {Context:DeploymentMode} |ポリシーの展開モード。  | Production |
| {Context:IPAddress} | ユーザーの IP アドレス。 | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>プロトコル以外のパラメーター

OIDC 要求または OAuth2 要求の一部に含まれているすべてのパラメーター名は、ユーザー体験の要求にマップできます。 たとえば、アプリケーションからの要求には、`app_session` の名前、`loyalty_number`、またはカスタム クエリ 文字列が指定されたクエリ文字列パラメーターが含まれる場合があります。

| 要求 | 説明 | 例 |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | クエリ文字列パラメーター。 | hawaii |
| {OAUTH-KV:app_session} | クエリ文字列パラメーター。 | A3C5R |
| {OAUTH-KV:loyalty_number} | クエリ文字列パラメーター。 | 1234 |
| {OAUTH-KV:任意のカスタム クエリ文字列} | クエリ文字列パラメーター。 | 該当なし |

### <a name="oauth2"></a>OAuth2

| 要求 | 説明 | 例 |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | アクセス トークン。 | 該当なし |


### <a name="saml"></a>SAML

| 要求 | 説明 | 例 |
| ----- | ----------- | --------|
| {SAML:AuthnContextClassReferences} | SAML 要求からの `AuthnContextClassRef` 要素の値。 | urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport |
| {SAML:NameIdPolicyFormat} | SAML 要求の `NameIDPolicy` 要素からの `Format` 属性。 | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| {SAML:Issuer} |  SAML 要求の SAML `Issuer` 要素の値。| https://contoso.com |
| {SAML:AllowCreate} | SAML 要求の `NameIDPolicy` 要素からの `AllowCreate` 属性値。 | True |
| {SAML:ForceAuthn} | SAML 要求の `AuthnRequest` 要素からの `ForceAuthN` 属性値。 | True |
| {SAML:ProviderName} | SAML 要求の `AuthnRequest` 要素からの `ProviderName` 属性値。| Contoso.com |

## <a name="using-claim-resolvers"></a>要求リゾルバーの使用 

次の要素を使用して、要求リゾルバーを使用できます。 

| Item | 要素 | 設定 |
| ----- | ----------------------- | --------|
|Application Insights の技術プロファイル |`InputClaim` | |
|[Azure Active Directory](active-directory-technical-profile.md) の技術プロファイル| `InputClaim`, `OutputClaim`| 1、2|
|[OAuth2](oauth2-technical-profile.md) の技術プロファイル| `InputClaim`, `OutputClaim`| 1、2|
|[OpenID Connect](openid-connect-technical-profile.md) の技術プロファイル| `InputClaim`, `OutputClaim`| 1、2|
|[要求変換](claims-transformation-technical-profile.md) の技術プロファイル| `InputClaim`, `OutputClaim`| 1、2|
|[RESTful プロバイダー](restful-technical-profile.md) の技術プロファイル| `InputClaim`| 1、2|
|[SAML2](saml-technical-profile.md) の技術プロファイル| `OutputClaim`| 1、2|
|[セルフアサート](self-asserted-technical-profile.md) の技術プロファイル| `InputClaim`, `OutputClaim`| 1、2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[RelyingParty](relyingparty.md#technicalprofile) の技術プロファイル| `OutputClaim`| 2 |

以下のように設定します。 
1. `IncludeClaimResolvingInClaimsHandling` メタデータを `true` に設定する必要があります。
1. 入力要求または出力要求の属性 `AlwaysUseDefaultValue` は、`true`に設定する必要があります。

## <a name="claim-resolvers-samples"></a>要求リゾルバーのサンプル

### <a name="restful-technical-profile"></a>RESTful 技術プロファイル

[RESTful](restful-technical-profile.md) 技術プロファイルでは、ユーザーの言語、ポリシー名、スコープ、クライアント ID を送信したいことがあります これらの要求に基づいて、REST API はカスタム ビジネス ロジックを実行し、必要な場合は、ローカライズされたエラー メッセージを発生させることができます。

このシナリオを使用する RESTful 技術プロファイルの例を次に示します。

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>直接サインイン

要求リゾルバーを使用すると、サインイン名を事前入力したり、Facebook、LinkedIn、Microsoft アカウントなどの特定のソーシャル ID プロバイダーに直接サインインしたりできます。 詳しくは、「[Azure Active Directory B2C を使用した直接サインインの設定](direct-signin.md)」をご覧ください。

### <a name="dynamic-ui-customization"></a>動的 UI のカスタマイズ

Azure AD B2C を使用すると、HTML コンテンツ定義エンドポイントにクエリ文字列パラメーターを渡して、ページの内容を動的にレンダリングできます。 たとえば、これを利用すると Web またはモバイル アプリケーションから渡すカスタム パラメーターに基づいて、Azure AD B2C サインアップまたはサインイン ページの背景イメージを変更できます。 詳しくは、[Azure Active Directory B2C でのカスタム ポリシーを使用した UI の動的な構成](custom-policy-ui-customization.md)に関するページをご覧ください。 言語パラメーターに基づいて HTML ページをローカライズしたり、クライアント ID に基づいて内容を変更したりすることもできます。

次の例では、名前が **campaignId** で値が `hawaii` のクエリ文字列パラメーター、**language** コード `en-US`、およびクライアント ID を表す **app** を渡しています。

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

結果として、Azure AD B2C によって HTML コンテンツ ページに上記のパラメーターが送信されます。

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>コンテンツ定義

[ContentDefinition](contentdefinitions.md) `LoadUri`では、使用されるパラメーターに基づいて、さまざまな場所からコンテンツをプルする要求リゾルバーを送信できます。 

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Application Insights の技術プロファイル

Azure Application Insights と要求リゾルバーを使用すると、ユーザーの動作に関する分析情報を取得できます。 Application Insights の技術プロファイルでは、Azure Application Insights に保持される入力要求を送信します。 詳しくは、「[Application Insights を使用した Azure AD B2C 体験でのユーザー動作の追跡](analytics-with-application-insights.md)」をご覧ください。 次の例では、ポリシー ID、相関 ID、言語、クライアント ID を Azure Application Insights に送信しています。

```XML
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>証明書利用者ポリシー

[証明書利用者](relyingparty.md) ポリシー技術プロファイルでは、テナント ID または関連付け ID を JWT 内の証明書利用者アプリケーションに送信することができます。 

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
