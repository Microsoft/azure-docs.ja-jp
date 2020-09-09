---
title: カスタム ポリシーを通じてアクセス トークンをご自身のアプリに渡す
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C のカスタム ポリシーを通じて、OAuth 2.0 ID プロバイダーのアクセス トークンを要求としてご自身のアプリケーションへ渡す方法について学習します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c434ad6a724ba513caf7923916997600097b43f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387866"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のカスタム ポリシーを通じてアクセス トークンをご自身のアプリケーションに渡す

Azure Active Directory B2C (Azure AD B2C) の[カスタム ポリシー](custom-policy-get-started.md)は、アプリケーションのユーザーに、ID プロバイダーを使用してサインアップまたはサインインする機会を提供します。 これが発生すると、Azure AD B2C は ID プロバイダーから[アクセス トークン](tokens-overview.md)を受け取ります。 Azure AD B2C はそのトークンを使用して、そのユーザーに関する情報を取得します。 要求の種類を追加し、要求をカスタム ポリシーに出力して、トークンを Azure AD B2C に登録するアプリケーションに渡します。

Azure AD B2C では、[OAuth 2.0](authorization-code-flow.md) および [OpenID Connect](openid-connect.md) ID プロバイダーのアクセス トークンを渡すことがサポートされています。 その他すべての ID プロバイダーについては、要求が空で返されます。

## <a name="prerequisites"></a>前提条件

* 対象のカスタム ポリシーが OAuth 2.0 または OpenID Connect の ID プロバイダーを使用して構成されている。

## <a name="add-the-claim-elements"></a>要求の要素を追加する

1. ご自身の *TrustframeworkExtensions.xml* ファイルを開き、識別子が `identityProviderAccessToken` の次の **ClaimType** 要素を、**ClaimsSchema** 要素に追加します。

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. アクセス トークンを必要とする OAuth 2.0 ID プロバイダーごとに、**OutputClaim** 要素を **TechnicalProfile** 要素に追加します。 次の例は、Facebook の技術プロファイルに追加された要素を示します。

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. *TrustframeworkExtensions.xml* ファイルを保存します。
4. ご自身の証明書利用者のポリシー ファイル (*SignUpOrSignIn.xml* など) を開き、**TechnicalProfile** に **OutputClaim** 要素を追加します。

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. ポリシー ファイルを保存します。

## <a name="test-your-policy"></a>ポリシーのテスト

Azure AD B2C でアプリケーションをテスト中に、Azure AD B2C トークンを `https://jwt.ms` に返して、その中の要求を見直すことができると便利なことがあります。

### <a name="upload-the-files"></a>ファイルのアップロード

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認します。そのためには、上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターをクリックし、ご利用のテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[Identity Experience Framework]** を選択します。
5. [カスタム ポリシー] ページで、 **[ポリシーのアップロード]** をクリックします。
6. **[ポリシーが存在する場合は上書きする]** を選択し、*TrustframeworkExtensions.xml* ファイルを検索して選択します。
7. **[アップロード]** を選択します。
8. その証明書利用者ファイル (*SignUpOrSignIn.xml* など) で、手順 5 から 7 を繰り返します。

### <a name="run-the-policy"></a>ポリシーを実行する

1. 変更したポリシーを開きます。 たとえば、*B2C_1A_signup_signin* などです。
2. **[アプリケーション]** には、前に登録したアプリケーションを選択します。 以下の例でトークンを表示するには、 **[応答 URL]** に `https://jwt.ms` が表示される必要があります。
3. **[今すぐ実行]** を選択します。

    次の例のようなコードが表示されます。

    ![Idp_access_token block が強調表示されている jwt.ms 内のデコードされたトークン](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>次のステップ

[Azure Active Directory B2C のトークン リファレンス](tokens-overview.md)でトークンの詳細について学習します。
