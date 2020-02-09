---
title: 表示コントロール リファレンス
titleSuffix: Azure AD B2C
description: Azure AD B2C 表示コントロールのリファレンスです。 カスタム ポリシーで定義されているユーザー体験をカスタマイズするには、表示コントロールを使用します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5b039221f3a25bddf7953cbe8d517275f76d6f37
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474701"
---
# <a name="display-controls"></a>表示コントロール

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**表示コントロール**は、特別な機能を備え、Azure Active Directory B2C (Azure AD B2C) バックエンド サービスと対話するユーザー インターフェイス要素です。 これにより、ユーザーがバックエンドで[検証技術プロファイル](validation-technical-profile.md)を呼び出すページで操作を実行できるようになります。 ページに表示コントロールが表示され、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)によって参照されます。

次の図は、プライマリとセカンダリの電子メール アドレスを検証する 2 つの表示コントロールを含むセルフアサートされたサインアップ ページを示しています。

![レンダリングされた表示コントロールの例](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>前提条件

 [セルフアサートされた技術プロファイル](self-asserted-technical-profile.md)の[メタデータ](self-asserted-technical-profile.md#metadata) セクションで、参照先の [ContentDefinition](contentdefinitions.md) の `DataUri` をページ コントラクトのバージョン 2.0.0 以上に設定する必要があります。 次に例を示します。

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>表示コントロールの定義

**DisplayControl** 要素には、次の属性が含まれています。

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | 表示コントロールに使用される識別子です。 [参照](#referencing-display-controls)できます。 |
| UserInterfaceControlType | はい | 表示コントロールの種類。 現在サポートされているのは [VerificationControl](display-control-verification.md) です。 |

**DisplayControl** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** は、ユーザーから収集される要求の値を事前に設定するために使用されます。 |
| DisplayClaims | 0:1 | **DisplayClaims** は、ユーザーから収集される要求を表すために使用されます。 |
| OutputClaims | 0:1 | **OutputClaims** は、この **DisplayControl** のために一時的に保存される要求を表すために使用されます。 |
| Actions | 0:1 | **Actions** は、フロントエンドで発生するユーザー アクションに対して呼び出す検証技術プロファイルを一覧表示するために使用されます。 |

### <a name="input-claims"></a>入力要求

表示コントロールでは、**InputClaims** 要素を使用して、ページ上のユーザーから収集する要求の値を事前に入力できます。 **InputClaimsTransformations** は、この表示コントロールを参照するセルフアサート技術プロファイルで定義できます。

次の例では、既に存在するアドレスを使用して検証する電子メール アドレスを事前に入力します。

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>要求の表示

各種類の表示コントロールには、表示要求、[出力要求](#output-claims)、および実行される[アクション](#display-control-actions)の異なるセットが必要です。

[セルフアサートされた技術プロファイル](self-asserted-technical-profile.md#display-claims)で定義されている**表示要求**と同様に、表示要求は、表示コントロール内でユーザーから収集される要求を表します。 参照される **OutputClaims** 要素は、`TextBox` または `DropdownSingleSelect` など Azure AD B2C がサポートするユーザー入力タイプに **UserInputType** 要素を指定する必要があります。 **Action** に表示要求の値が必要な場合は、**Required** 属性を `true` に設定して、ユーザーが特定の表示要求の値を指定しなければならないようにします。

特定の種類の表示コントロールには、特定の表示要求が必要です。 たとえば、種類が **VerificationControl** の表示コントロールには、**VerificationCode** が必要です。 属性 **ControlClaimType** を使用して、その必須の要求に対して指定される DisplayClaim を指定します。 次に例を示します。

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>出力要求

表示コントロールの**出力要求**は、次のオーケストレーション ステップには送信されません。 これらは、現在の表示コントロール セッションに対してのみ一時的に保存されます。 これらの一時的な要求は、同じ表示コントロールの異なるアクション間で共有できます。

要求の出力を次のオーケストレーション ステップにバブルアップするには、この表示コントロールを参照する実際のセルフアサート技術プロファイルの **OutputClaims** を使用します。

### <a name="display-control-actions"></a>表示コントロールのアクション

表示コントロールの**アクション**は、ユーザーがクライアント側 (ブラウザー) で特定のアクションを実行したときに Azure AD B2C バックエンドで発生するプロシージャです。 たとえば、ユーザーがページのボタンを選択したときに実行する検証です。

アクションは、**検証技術プロファイル**の一覧を定義します。 これらは、表示コントロールの表示要求の一部またはすべてを検証するために使用されます。 検証技術プロファイルは、ユーザー入力を検証し、ユーザーにエラーを返すことができます。 セルフアサートされた技術プロファイルで[検証技術プロファイル](validation-technical-profile.md)に使用される方法と同様に、**ContinueOnError**、**ContinueOnSuccess**、および **Preconditions** 表示コントロールのアクションで使用することができます。

次の例では、**mfaType** 要求のユーザーの選択に基づいて電子メールまたは SMS でコードを送信します。

```XML
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>表示コントロールの参照

表示コントロールがは、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)の[表示要求](self-asserted-technical-profile.md#display-claims)で参照されます。

次に例を示します。

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```
