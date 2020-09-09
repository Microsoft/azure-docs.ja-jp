---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でカスタム ポリシーの TechnicalProfiles 要素を指定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a788134f64066b0469d34fbfbacacd8c45438bde
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85203148"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**TechnicalProfiles** 要素には、クレーム プロバイダーによってサポートされる一連の技術プロファイルが含まれています。 どのクレーム プロバイダーも、そのクレーム プロバイダーと通信するために必要なエンドポイントとプロトコルを特定する技術プロファイルを 1 つ以上持っている必要があります。 1 つのクレーム プロバイダーが複数の技術プロファイルを持つことができます。

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**TechnicalProfile** 要素には、次の属性が含まれています。

| 属性 | Required | 説明 |
|---------|---------|---------|
| Id | はい | 技術プロファイルの一意の識別子。 ポリシー ファイル内の他の要素からこの識別子を使用して、技術プロファイルを参照することができます。 例として、**OrchestrationSteps** や **ValidationTechnicalProfile** が挙げられます。 |

**TechnicalProfile** には、次の属性が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| Domain | 0:1 | 技術プロファイルのドメイン名。 たとえば、技術プロファイルで Facebook ID プロバイダーが指定されている場合、ドメイン名は Facebook.com です。 |
| DisplayName | 1:1 | ユーザーに表示できる技術プロファイルの名前。 |
| Description | 0:1 | ユーザーに表示できる技術プロファイルの説明。 |
| Protocol | 0:1 | 他の利用者との通信に使用されるプロトコル。 |
| Metadata | 0:1 | トランザクション中にエンドポイントと通信するためにプロトコルによって利用されるキー/値ペアのコレクション。 |
| InputTokenFormat | 0:1 | 入力トークンの形式。 指定できる値: `JSON`、`JWT`、`SAML11`、または `SAML2`。 `JWT` 値は、IETF 仕様に従って JSON Web トークンを表します。 `SAML11` 値は、OASIS 仕様に従って SAML 1.1 セキュリティ トークンを表します。  `SAML2` 値は、OASIS 仕様に従って SAML 2.0 セキュリティ トークンを表します。 |
| OutputTokenFormat | 0:1 | 出力トークンの形式。 指定できる値: `JSON`、`JWT`、`SAML11`、または `SAML2`。 |
| CryptographicKeys | 0:1 | 技術プロファイルで使用される暗号化キーの一覧。 |
| InputClaimsTransformations | 0:1 | 要求がクレーム プロバイダーまたは証明書利用者に送信される前に実行する必要がある、要求変換に対する定義済みの参照の一覧。 |
| InputClaims | 0:1 | 技術プロファイルで入力として取得される、要求の種類に対する定義済みの参照の一覧。 |
| PersistedClaims | 0:1 | 技術プロファイルに関連するクレーム プロバイダーによって保持される、要求の種類に対する定義済みの参照の一覧。 |
| DisplayClaims | 0:1 | [セルフアサ―ト技術プロファイル](self-asserted-technical-profile.md)に関連するクレーム プロバイダーによって提供される、要求の種類に対する定義済みの参照の一覧。 現在、DisplayClaims 機能は**プレビュー**段階です。 |
| OutputClaims | 0:1 | 技術プロファイルで出力として取得される、要求の種類に対する定義済みの参照の一覧。 |
| OutputClaimsTransformations | 0:1 | クレーム プロバイダーから要求を受け取った後に実行する必要がある、要求変換に対する定義済みの参照の一覧。 |
| ValidationTechnicalProfiles | 0:n | 技術プロファイルが検証目的で使用する、他の技術プロファイルへの参照の一覧。 詳細については、「[検証技術プロファイル](validation-technical-profile.md)」を参照してください。|
| SubjectNamingInfo | 0:1 | サブジェクト名が要求とは別に指定されているトークンのサブジェクト名の生成を制御します。 たとえば、OAuth または SAML です。  |
| IncludeInSso | 0:1 |  この技術プロファイルを使用する場合、セッションにシングル サインオン (SSO) 動作を適用するか、明示的な対話が必要です。 この要素は、検証技術プロファイル内で使用される SelfAsserted プロファイルでのみ有効です。 指定できる値は `true`(既定値) または`false`です。 |
| IncludeClaimsFromTechnicalProfile | 0:1 | この技術プロファイルに対するすべての入力要求と出力要求の追加元となる、技術プロファイルの識別子。 参照先の技術プロファイルは、同じポリシー ファイルで定義する必要があります。 |
| IncludeTechnicalProfile |0:1 | この技術プロファイルに対するすべてのデータの追加元となる、技術プロファイルの識別子。 |
| UseTechnicalProfileForSessionManagement | 0:1 | セッション管理に使用される別の技術プロファイル。 |
|EnabledForUserJourneys| 0:1 |ユーザー体験で技術プロファイルを実行するかどうかを制御します。  |

## <a name="protocol"></a>Protocol

**Protocol** 要素には、次の属性が含まれています。

| 属性 | Required | Description |
| --------- | -------- | ----------- |
| 名前 | はい | 技術プロファイルの一部として使用される Azure AD B2C によってサポートされている有効なプロトコルの名前。 指定できる値: `OAuth1`、`OAuth2`、`SAML2`、`OpenIdConnect`、`Proprietary` または `None`。 |
| Handler | いいえ | プロトコル名が `Proprietary` に設定されている場合は、Azure AD B2C がプロトコル ハンドラーを決定するために使用するアセンブリの完全修飾名を指定します。 |

## <a name="metadata"></a>Metadata

**Metadata** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| Item | 0:n | 技術プロファイルに関連するメタデータ。 技術プロファイルの種類ごとに、異なるメタデータ項目のセットがあります。 詳細については、技術プロファイルの種類に関するセクションを参照してください。 |

### <a name="item"></a>Item

**Metadata** 要素の **Item** 要素には、次の属性が含まれています。

| 属性 | Required | Description |
| --------- | -------- | ----------- |
| Key | はい | メタデータ キー。 メタデータ項目の一覧については、各技術プロファイルの種類を参照してください。 |

## <a name="cryptographickeys"></a>CryptographicKeys

**CryptographicKeys** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| Key | 1:n | この技術プロファイルで使用される暗号化キー。 |

### <a name="key"></a>Key

**Key** 要素には、次の属性が含まれています。

| 属性 | Required | 説明 |
| --------- | -------- | ----------- |
| Id | いいえ | ポリシー ファイル内の他の要素から参照される特定のキー ペアの一意の識別子。 |
| StorageReferenceId | はい | ポリシー ファイル内の他の要素から参照されるストレージ キー コンテナーの識別子。 |

## <a name="inputclaimstransformations"></a>InputClaimsTransformations

**InputClaimsTransformations** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | 要求がクレーム プロバイダーまたは証明書利用者に送信される前に実行する必要がある、要求変換の識別子。 要求変換は、既存の ClaimsSchema の要求を変更したり、新しい要求を生成するために使用できます。 |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

**InputClaimsTransformation** 要素には、次の属性が含まれています。

| 属性 | Required | Description |
| --------- | -------- | ----------- |
| ReferenceId | はい | ポリシー ファイルまたは親ポリシー ファイルで定義済みの要求変換の識別子。 |

## <a name="inputclaims"></a>InputClaims

**InputClaims** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | 予想される入力要求の種類。 |

### <a name="inputclaim"></a>InputClaim

**InputClaim** 要素には、次の属性が含まれています。

| 属性 | Required | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | はい | ポリシーファイルまたは親ポリシーファイルの　ClaimsSchema セクションで定義済みの要求の種類の識別子。 |
| DefaultValue | いいえ | ClaimTypeReferenceId で示された要求が存在しない場合に、要求を作成するために使用する既定値。これにより、作成された要求を技術プロファイルで InputClaim として使用できます。 |
| PartnerClaimType | いいえ | 指定されたポリシーの要求の種類のマップ先となる外部パートナーの要求の種類の識別子。 PartnerClaimType 属性が指定されていない場合は、指定されたポリシーの要求の種類が、同じ名前のパートナーの要求の種類にマップされます。 このプロパティは、要求の種類の名前が他の利用者と異なる場合に使用します。 たとえば、最初の要求の名前が 'givenName' で、パートナーが 'first_name' という要求を使用する場合です。 |

## <a name="displayclaims"></a>DisplayClaims

**DisplayClaims** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | 予想される入力要求の種類。 |

現在、DislayClaims 機能は**プレビュー**段階です。

### <a name="displayclaim"></a>DisplayClaim

**DisplayClaim** 要素には、次の属性が含まれています。

| 属性 | Required | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | いいえ | ポリシーファイルまたは親ポリシーファイルの　ClaimsSchema セクションで定義済みの要求の種類の識別子。 |
| DisplayControlReferenceId | いいえ | ポリシー ファイルまたは親ポリシー ファイルの ClaimsSchema セクションで定義されている[表示コントロール](display-controls.md)の識別子。 |
| Required | いいえ | 表示要求が必須かどうかを示します。 |

**DisplayClaim** では、`ClaimTypeReferenceId` と `DisplayControlReferenceId` のいずれかを指定する必要があります。

### <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | 保持する要求の種類。 |

### <a name="persistedclaim"></a>PersistedClaim

**PersistedClaim** 要素には、次の属性が含まれています。

| 属性 | Required | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | はい | ポリシーファイルまたは親ポリシーファイルの　ClaimsSchema セクションで定義済みの要求の種類の識別子。 |
| DefaultValue | いいえ | ClaimTypeReferenceId で示された要求が存在しない場合に、要求を作成するために使用する既定値。これにより、作成された要求を技術プロファイルで InputClaim として使用できます。 |
| PartnerClaimType | いいえ | 指定されたポリシーの要求の種類のマップ先となる外部パートナーの要求の種類の識別子。 PartnerClaimType 属性が指定されていない場合は、指定されたポリシーの要求の種類が、同じ名前のパートナーの要求の種類にマップされます。 このプロパティは、要求の種類の名前が他の利用者と異なる場合に使用します。 たとえば、最初の要求の名前が 'givenName' で、パートナーが 'first_name' という要求を使用する場合です。 |

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | 予想される出力要求の種類。 |

### <a name="outputclaim"></a>OutputClaim

**OutputClaim** 要素には、次の属性が含まれています。

| 属性 | Required | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | はい | ポリシーファイルまたは親ポリシーファイルの　ClaimsSchema セクションで定義済みの要求の種類の識別子。 |
| DefaultValue | いいえ | ClaimTypeReferenceId で示された要求が存在しない場合に、要求を作成するために使用する既定値。これにより、作成された要求を技術プロファイルで InputClaim として使用できます。 |
|AlwaysUseDefaultValue |いいえ |既定値の使用を強制します。  |
| PartnerClaimType | いいえ | 指定されたポリシーの要求の種類のマップ先となる外部パートナーの要求の種類の識別子。 PartnerClaimType 属性が指定されていない場合は、指定されたポリシーの要求の種類が、同じ名前のパートナーの要求の種類にマップされます。 このプロパティは、要求の種類の名前が他の利用者と異なる場合に使用します。 たとえば、最初の要求の名前が 'givenName' で、パートナーが 'first_name' という要求を使用する場合です。 |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformations

**OutputClaimsTransformations** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | 要求がクレーム プロバイダーまたは証明書利用者に送信される前に実行する必要がある、要求変換の識別子。 要求変換は、既存の ClaimsSchema の要求を変更したり、新しい要求を生成するために使用できます。 |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

**OutputClaimsTransformation** 要素には、次の属性が含まれています。

| 属性 | Required | Description |
| --------- | -------- | ----------- |
| ReferenceId | はい | ポリシー ファイルまたは親ポリシー ファイルで定義済みの要求変換の識別子。 |

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

**ValidationTechnicalProfiles** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 参照元の技術プロファイルの出力要求の一部またはすべてを検証するために使用される技術プロファイルの識別子。 参照先の技術プロファイルのすべての入力要求は、参照元の技術プロファイルの出力要求に表示する必要があります。 |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

**ValidationTechnicalProfile** 要素には、次の属性が含まれています。

| 属性 | Required | Description |
| --------- | -------- | ----------- |
| ReferenceId | はい | ポリシー ファイルまたは親ポリシー ファイルで定義済みの技術プロファイルの識別子。 |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** には、次の属性が含まれています。

| 属性 | Required | Description |
| --------- | -------- | ----------- |
| ClaimType | はい | ポリシー ファイルの ClaimsSchema セクションで定義済みの要求の種類の識別子。 |

## <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

**IncludeTechnicalProfile** 要素には、次の属性が含まれています。

| 属性 | Required | Description |
| --------- | -------- | ----------- |
| ReferenceId | はい | ポリシー ファイルまたは親ポリシー ファイルで既に定義済みの技術プロファイルの識別子。 |

## <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

**UseTechnicalProfileForSessionManagement** 要素には、次の属性が含まれています。

| 属性 | Required | Description |
| --------- | -------- | ----------- |
| ReferenceId | はい | ポリシー ファイルまたは親ポリシー ファイルで定義済みの技術プロファイルの識別子。 |

## <a name="enabledforuserjourneys"></a>EnabledForUserJourneys

ユーザー体験の **ClaimsProviderSelections** は、クレーム プロバイダー選択オプションの一覧とその順序を定義します。 **EnabledForUserJourneys** 要素を使用して、ユーザーが利用できるクレーム プロバイダーをフィルタリングします。 **EnabledForUserJourneys** 要素には、次の値のいずれかが含まれています。

- **Always**、技術プロファイルを実行します。
- **Never**、技術プロファイルをスキップします。
- **OnClaimsExistence**、技術プロファイルで指定された特定の要求が存在する場合にのみ実行します。
- **OnItemExistenceInStringCollectionClaim**、 文字列コレクション要求に項目が存在する場合にのみ実行します。
- **OnItemAbsenceInStringCollectionClaim**、文字列コレクション要求に項目が存在しない場合にのみ実行します。

**OnClaimsExistence**、**OnItemExistenceInStringCollectionClaim** または **OnItemAbsenceInStringCollectionClaim** を使用して、以下のメタデータを指定する必要があります。**ClaimTypeOnWhichToEnable** は評価される要求の種類を指定します、**ClaimValueOnWhichToEnable** は比較される値を指定します。

次の技術プロファイルは、**identityProviders** 文字列コレクションに `facebook.com` という値が含まれている場合にのみ実行されます。

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
