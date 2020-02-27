---
title: Azure Active Directory B2C での言語のカスタマイズ
description: ユーザー フローの言語のエクスペリエンスのカスタマイズについて説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ab34c1c34c2d2a67b5e3fdd6a5e84f0663455e20
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76850762"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C での言語のカスタマイズ

Azure Active Directory B2C (Azure AD B2C) の言語のカスタマイズを使うと、顧客のニーズに応じて、ユーザー フローをさまざまな言語に対応させることができます。 Microsoft では [36 の言語](#supported-languages)の翻訳が提供されていますが、どの言語についてもユーザーが自分で翻訳を提供できます。 1 つの言語でのみページが提供されている場合でも、ページ上の任意のテキストをカスタマイズできます。

## <a name="how-language-customization-works"></a>言語のカスタマイズのしくみ

ユーザー フローで利用できる言語を選ぶには、言語のカスタマイズを使います。 この機能を有効にした後、アプリケーションからクエリ文字列パラメーター `ui_locales` を指定できます。 Azure AD B2C を呼び出すと、ページが指定したロケールに翻訳されます。 この種類の構成では、ユーザー フローの言語を完全に制御し、顧客のブラウザーの言語設定を無視できます。

顧客に表示される言語をそこまで制御する必要がない場合もあります。 `ui_locales` パラメーターを指定しない場合、顧客のエクスペリエンスはブラウザーの設定によって決まります。 その場合でも、ユーザー フローの翻訳先言語を、サポートされる言語として追加することで制御できます。 サポートしない言語を表示するように顧客のブラウザーが設定されている場合は、サポートされているカルチャで既定として選択した言語が代わりに表示されます。

* **ui-locales で指定した言語**:言語のカスタマイズを有効にすると、ユーザー フローはここで指定した言語に翻訳されます。
* **ブラウザーで要求されている言語**:`ui_locales` パラメーターを指定しないと、ユーザー フローはブラウザーで要求されている言語に翻訳されます ("*言語がサポートされている場合*")。
* **ポリシーの既定の言語**:ブラウザーで言語が指定されていない場合、またはサポートされていない言語が指定されている場合は、ユーザー フローはユーザー フローの既定の言語に翻訳されます。

> [!NOTE]
> カスタム ユーザー属性を使っている場合は、独自の翻訳を提供する必要があります。 詳しくは、「[文字列のカスタマイズ](#customize-your-strings)」をご覧ください。

## <a name="support-requested-languages-for-ui_locales"></a>ui_locales で要求された言語のサポート

言語のカスタマイズの一般公開前に作成されたポリシーについては、最初に、この機能を有効にする必要があります。 一般公開後に作成されたポリシーとユーザー フローでは、言語のカスタマイズは既定で有効になっています。

ユーザー フローで言語のカスタマイズを有効にした後は、`ui_locales` パラメーターを追加することにより、ユーザー フローの言語を制御できます。

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. 翻訳を有効にするユーザー フローをクリックします。
1. **[言語]** を選択します。
1. **[言語のカスタマイズを有効化]** を選びます。

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>ユーザー フローで有効にする言語を選択する

`ui_locales` パラメーターが指定されていないブラウザーで要求された場合に、ユーザー フローで言語セットが翻訳されるように、言語セットを有効にします。

1. 前の手順で、ユーザー フローの言語のカスタマイズを有効にしていることを確認します。
1. ユーザー フローの **[言語]** ページで、サポートする言語を選択します。
1. プロパティ ウィンドウで、 **[有効]** を **[はい]** に変更します。
1. プロパティ ウィンドウの上部にある **[保存]** を選びます。

>[!NOTE]
>`ui_locales` パラメーターが指定されていない場合、ページは顧客のブラウザーの言語に翻訳されます (有効になっている場合のみ)。
>

## <a name="customize-your-strings"></a>文字列のカスタマイズ

言語のカスタマイズを使うと、ユーザー フローの文字列をカスタマイズできます。

1. 前の手順で、ユーザー フローの言語のカスタマイズを有効にしていることを確認します。
1. ユーザー フローの **[言語]** ページで、カスタマイズする言語を選択します。
1. **[Page-level-resources files]\(ページ レベル リソース ファイル\)** で、編集するページを選択します。
1. **[既定値のダウンロード]** (この言語を前に編集した場合は **[オーバーライドのダウンロード]** ) を選びます。

上記の手順により、文字列の編集を開始する際に使用できる JSON ファイルを入手できます。

### <a name="change-any-string-on-the-page"></a>ページ上の文字列を変更する

1. 前の手順でダウンロードした JSON ファイルを JSON エディターで開きます。
1. 変更する要素を探します。 探している文字列の `StringId` を見つけるか、変更する `Value` 属性を見つけます。
1. `Value` 属性を表示する文字列に更新します。
1. 変更するすべての文字列について、`Override` を `true` に変更します。
1. ファイルを保存し、変更内容をアップロードします。 (アップロード コントロールは、JSON ファイルをダウンロードした場所にあります)

> [!IMPORTANT]
> 文字列をオーバーライドする必要がある場合は、必ず `Override` の値を `true` に設定してください。 値が変更されていない場合、そのエントリは無視されます。

### <a name="change-extension-attributes"></a>拡張属性を変更する

カスタム ユーザー属性の文字列を変更する場合や、JSON に追加する場合は、次の形式を使います。

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

`<ExtensionAttribute>` をカスタム ユーザー属性の名前に置き換えます。

`<ExtensionAttributeValue>` を表示する新しい文字列に置き換えます。

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>LocalizedCollections を使って値の一覧を提供する

応答の値セットの一覧を指定する場合は、`LocalizedCollections` 属性を作成する必要があります。 `LocalizedCollections` は、`Name` と `Value` のペアの配列です。 項目の順序は、表示される順序になります。 `LocalizedCollections` を追加するには、次の形式を使います。

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` は、この `LocalizedCollections` 属性が応答になるユーザー属性です。
* `Name` は、ユーザーに表示される値です。
* `Value` は、このオプションが選択された場合に要求で返される値です。

### <a name="upload-your-changes"></a>変更のアップロード

1. JSON ファイルの変更が完了したら、B2C テナントに戻ります。
1. **[ユーザー フロー]** を選択し、翻訳を有効にするユーザー フローをクリックします。
1. **[言語]** を選択します。
1. 翻訳する言語を選びます。
1. 翻訳を提供するページを選びます。
1. フォルダー アイコンを選び、アップロードする JSON ファイルを選びます。

変更がユーザー フローに自動的に保存されます。

## <a name="customize-the-page-ui-by-using-language-customization"></a>言語のカスタマイズを使用してページの UI をカスタマイズする

HTML コンテンツをローカライズする方法は 2 つあります。 方法の 1 つは、[言語のカスタマイズ](user-flow-language-customization.md)を有効にすることです。 この機能を有効にすると、Azure AD B2C で OpenID Connect パラメーター `ui-locales` をエンドポイントに転送できるようになります。 コンテンツ サーバーは、このパラメーターを使用して、言語固有のカスタマイズされた HTML ページを提供できます。

使われているロケールに基づいて、さまざまな場所からコンテンツをプルすることもできます。 CORS 対応エンドポイントでは、特定の言語のコンテンツをホストするようにフォルダー構造を設定できます。 ワイルドカード値 `{Culture:RFC5646}` を使うと、適切な言語が呼び出されます。 たとえば、次のようなカスタム ページの URI があるものとします。

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

このページを `fr` で読み込むことができます。 ページは、HTML および CSS のコンテンツを取得とき、次の場所から取得します。

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>カスタム言語の追加

現在 Microsoft が翻訳を提供していない言語を追加することもできます。 ユーザー フロー内のすべての文字列に対して翻訳を提供する必要があります。 言語およびロケールのコードは、ISO 639-1 標準にものに限られます。

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
2. カスタム言語を追加するユーザー フローをクリックし、 **[言語]** をクリックします。
3. ページの上部から **[カスタム言語の追加]** を選択します。
4. 表示されたコンテキスト ウィンドウで、有効なロケール コードを入力して、翻訳を提供する言語を指定します。
5. ページごとに英語の一連のオーバーライドをダウンロードし、翻訳作業を行うことができます。
6. 完了した JSON ファイルは、ページごとにアップロードできます。
7. **[有効]** を選択すると、ユーザー フローでユーザーに対してこの言語を表示できるようになります。
8. 言語を保存します。

>[!IMPORTANT]
>カスタム言語を有効にするか、保存する前のオーバーライドをアップロードする必要があります。

## <a name="additional-information"></a>関連情報

### <a name="page-ui-customization-labels-as-overrides"></a>オーバーライドとしてのページ UI カスタマイズ ラベル

言語のカスタマイズを有効にすると、ページ UI のカスタマイズを使った、ラベルの以前の編集内容は、英語 (en) の JSON ファイルに保存されます。 引き続き、言語のカスタマイズで言語リソースをアップロードして、ラベルなどの文字列を変更できます。

### <a name="up-to-date-translations"></a>翻訳を更新する

Microsoft は、最新の翻訳を提供するよう努めています。 継続的に翻訳を改善し、コンプライアンスを維持します。 バグやグローバルな用語の変更を特定し、ユーザー フローにシームレスに適用される更新を行います。

### <a name="support-for-right-to-left-languages"></a>右から左へ記述する言語のサポート

現在、右から左に書く言語はサポートしていません。 これには、カスタム ロケールと CSS を使用して、文字列の表示方法を変更することで対応できます。 この機能が必要な場合は、[Azure フィードバック](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)で投票してください。

### <a name="social-identity-provider-translations"></a>ソーシャル ID プロバイダーの翻訳

Microsoft は、`ui_locales` OIDC パラメーターをソーシャル ログインに提供しています。 ただし、Facebook や Google などの一部のソーシャル ID プロバイダーは、それを利用していません。

### <a name="browser-behavior"></a>ブラウザーの動作

Chrome と Firefox はどちらも、それぞれに設定されている言語を要求します。 サポートされている言語の場合は、既定値の前に表示されます。 Microsoft Edge は現在、言語を要求せず、既定の言語が表示されます。

## <a name="supported-languages"></a>サポートされている言語

Azure AD B2C では、以下の言語がサポートされています。 ユーザー フローの言語は、Azure AD B2C によって提供されます。 Multi-Factor Authentication (MFA) 通知の言語は、[Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md) によって提供されます。

| Language              | 言語コード | ユーザー フロー         | MFA 通知  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| アラビア語                | ar            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Bulgarian             | bg            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| ベンガル語                | bn            | ![はい](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| カタロニア語               | ca            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Czech                 | cs            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Danish                | da            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| German                | de            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Greek                 | el            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| English               | en            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Spanish               | es            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Estonian              | et            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| バスク語                | eu            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Finnish               | fi            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| French                | fr            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| ガリシア語              | gl            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| グジャラート語              | gu            | ![はい](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| ヘブライ語                | he            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| ヒンディー語                 | hi            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Croatian              | hr            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Hungarian             | hu            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| インドネシア語            | id            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Italian               | it            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Japanese              | ja            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| カザフ語                | kk            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| カンナダ語               | kn            | ![はい](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| Korean                | ko            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Lithuanian            | lt            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Latvian               | lv            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| マラヤーラム語             | ml            | ![はい](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| マラーティー語               | mr            | ![はい](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| マレー語                 | ms            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| ノルウェー語 - ブークモール      | nb            | ![はい](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| Dutch                 | nl            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| ノルウェー語             | no            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| パンジャーブ語               | pa            | ![はい](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| Polish                | pl            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| ポルトガル語 - ブラジル   | pt-br         | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| ポルトガル語 - ポルトガル | pt-pt         | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Romanian              | ro            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Russian               | ru            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Slovak                | sk            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Slovenian             | sl            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| セルビア語 - キリル    | sr-cryl-cs    | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| セルビア語 - ラテン       | sr-latn-cs    | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Swedish               | sv            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| タミル語                 | ta            | ![はい](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| テルグ語                | te            | ![はい](./media/user-flow-language-customization/yes.png) | ![no](./media/user-flow-language-customization/no.png) |
| Thai                  | th            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| Turkish               | tr            | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| ウクライナ語             | uk            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| ベトナム語            | vi            | ![no](./media/user-flow-language-customization/no.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| 中国語 - 簡体字  | zh-hans       | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
| 中国語 - 繁体字 | zh-hant       | ![はい](./media/user-flow-language-customization/yes.png) | ![はい](./media/user-flow-language-customization/yes.png) |
