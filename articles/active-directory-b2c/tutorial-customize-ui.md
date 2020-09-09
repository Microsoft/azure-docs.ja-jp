---
title: チュートリアル:ユーザー インターフェイスをカスタマイズする
titleSuffix: Azure AD B2C
description: このチュートリアルでは、Azure portal を使用し、Azure Active Directory B2C でアプリケーションのユーザー インターフェイス (UI) をカスタマイズする方法について説明します。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9ca6d91be95bfb1a47f85b20f3775a57518ffcf
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922134"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C でユーザー エクスペリエンスのインターフェースをカスタマイズする

サインアップ、サインイン、プロファイル編集など、より一般的なユーザー エクスペリエンスについては、Azure Active Directory B2C (Azure AD B2C) の[ユーザー フロー](user-flow-overview.md)を使用できます。 このチュートリアルの情報は、独自の HTML ファイルや CSS ファイルを使用してそのようなエクスペリエンスの[ユーザー インターフェイス (UI) をカスタマイズする](customize-ui-overview.md)方法を理解する上で役立ちます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * UI カスタマイズ ファイルを作成する
> * ファイルを使用するユーザー フローを更新する
> * カスタマイズした UI をテストする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

[ユーザー フローを作成](tutorial-create-user-flows.md)して、ユーザーがアプリケーションにサインアップおよびサインインできるようにします。

## <a name="create-customization-files"></a>カスタマイズ ファイルを作成する

Azure のストレージ アカウントとコンテナーを作成し、基本の HTML ファイルと CSS ファイルをコンテナーに置きます。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

ファイルはさまざまな方法で保存できますが、このチュートリアルでは、[Azure BLOB ストレージ](../storage/blobs/storage-blobs-introduction.md)に保存します。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. Azure サブスクリプションが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のサブスクリプションが含まれるディレクトリを選択します。 このディレクトリは、Azure B2C テナントが含まれるディレクトリとは異なります。
3. Azure portal の左上隅の [すべてのサービス] を選択し、 **[ストレージ アカウント]** を検索して選択します。
4. **[追加]** を選択します。
5. **[リソース グループ]** の下で **[新規作成]** を選択し、新しいリソース グループの名前を入力し、 **[OK]** をクリックします。
6. ストレージ アカウントの名前を入力します。 選択する名前は、Azure 全体で一意であり、長さは 3 ～ 24 文字でなければならず、数字と小文字のみを使用できます。
7. ストレージ アカウントの場所を選択するか、既定の場所をそのまま使用します。
8. その他の既定値をそのまま使用し、 **[確認および作成]** を選択し、 **[作成]** をクリックします。
9. ストレージ アカウントの作成後、 **[リソースに移動]** を選択します。

### <a name="create-a-container"></a>コンテナーを作成する

1. ストレージ アカウントの [概要] ページで **[BLOB]** を選択します。
2. **[コンテナー]** を選択し、コンテナーの名前を入力し、 **[BLOB (BLOB 専用の匿名読み取りアクセス)]** を選択し、 **[OK]** をクリックします。

### <a name="enable-cors"></a>CORS を有効にする

 ブラウザーの Azure AD B2C コードでは、最新かつ標準の手法を利用し、ユーザー フローに指定された URL からカスタム コンテンツを読み込みます。 クロスオリジン リソース共有 (CORS) によって、Web ページ上の制限付きリソースを他のドメインから要求することが許可されます。

1. メニューで **[CORS]** を選択します。
2. **[許可されるオリジン]** には、`https://your-tenant-name.b2clogin.com` を入力します。 `your-tenant-name`を Azure AD B2C テナントの名前に置き換えます。 たとえば、「 `https://fabrikam.b2clogin.com` 」のように入力します。 テナント名を入力するときは、すべて小文字を使用する必要があります。
3. **[許可されたメソッド]** に、`GET`、`PUT`、`OPTIONS` を選択します。
4. **[許可されたヘッダー]** に、アスタリスク (*) を入力します。
5. **[公開されるヘッダー]** に、アスタリスク (*) を入力します。
6. **[最長有効期間]** には「200」と入力します。

    ![Azure portal の Azure Blob Storage の CORS 構成ページ](./media/tutorial-customize-ui/enable-cors.png)

5. **[保存]** をクリックします。

### <a name="create-the-customization-files"></a>カスタマイズ ファイルを作成する

サインアップ エクスペリエンスの UI をカスタマイズするには、まず簡単な HTML ファイルと CSS ファイルを作成することから始めます。 HTML は自由に構成できますが、識別子が `api` の **div** 要素を含める必要があります。 たとえば、「 `<div id="api"></div>` 」のように入力します。 ページが表示されるとき、Azure AD B2C によって `api` コンテナーに要素が挿入されます。

1. ローカル フォルダーで次のファイルを作成したら、必ずストレージ アカウントの名前を `your-storage-account` に変更し、作成したコンテナーの名前を `your-container` に変更します。 たとえば、「 `https://store1.blob.core.windows.net/b2c/style.css` 」のように入力します。

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    ページは自由にデザインできますが、作成した HTML カスタマイズ ファイルには **api** div 要素が必須です。

3. *custom-ui.html* としてファイルを保存します。
4. Azure AD B2C によって挿入される要素など、サインアップまたはサインイン ページのすべての要素を中心とする次のような簡単な CSS を作成します。

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. *style.css* としてファイルを保存します。

### <a name="upload-the-customization-files"></a>カスタマイズ ファイルをアップロードする

このチュートリアルでは、Azure AD B2C でアクセスできるように、ストレージ アカウントで作成したファイルを保存します。

1. Azure portal の左上隅の **[すべてのサービス]** を選択し、 **[ストレージ アカウント]** を検索して選択します。
2. 作成したストレージ アカウントを選択し、 **[BLOB]** を選択し、作成したコンテナーを選択します。
3. **[アップロード]** を選択し、*custom-ui.html* ファイルに移動してそれを選択し、 **[アップロード]** をクリックします。

    ![[アップロード] ボタンと [ファイル] が強調表示されているポータルの [BLOB のアップロード] ページ](./media/tutorial-customize-ui/upload-blob.png)

4. チュートリアルの後半で使用するため、アップロードしたファイルの URL をコピーしておきます。
5. *style.css* ファイルに手順 3 と 4 を繰り返します。

## <a name="update-the-user-flow"></a>ユーザー フローを更新する

1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
2. **[ユーザー フロー]** を選択し、*B2C_1_signupsignin1* ユーザー フローを選択します。
3. **[ページ レイアウト]** を選択し、 **[統合されたサインアップまたはサインイン ページ]** の下で、 **[カスタム コンテンツの使用]** に対して **[はい]** を選択します。
4. **[カスタム ページ URI]** に、先に記録しておいた *custom-ui.html* ファイルの URL を入力します。
5. ページの最上部で **[保存]** を選択します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択し、*B2C_1_signupsignin1* ユーザー フローを選択します。
2. ページの上部にある **[ユーザー フローを実行します]** をクリックします。
3. **[ユーザー フローを実行します]** ボタンをクリックします。

    ![サインアップまたはサインイン ユーザー フローの [ユーザー フローを実行します] ページ](./media/tutorial-customize-ui/run-user-flow.png)

    次の例のような、作成した CSS ファイルに基づいて要素が配置されているページが表示されるはずです。

    ![サインアップまたはサインイン ページとカスタム UI 要素を示している Web ブラウザー](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>次のステップ

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * UI カスタマイズ ファイルを作成する
> * ファイルを使用するユーザー フローを更新する
> * カスタマイズした UI をテストする

> [!div class="nextstepaction"]
> [Azure Active Directory B2C 内のユーザー インターフェイスをカスタマイズする](customize-ui-overview.md)
