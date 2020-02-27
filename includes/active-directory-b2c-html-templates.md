---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: marsma
ms.openlocfilehash: 264604bfdf0c514e6464854f431addbc9d2dcdef
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373117"
---
## <a name="sample-templates"></a>サンプル テンプレート
ここでは、UI カスタマイズのサンプル テンプレートを示します。

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

このプロジェクトには、次のテンプレートが含まれています。
- [オーシャン ブルー](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [スレート グレー](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

サンプルを使用するには、次のようにします。

1. ローカル コンピューターにリポジトリを複製します。 テンプレート フォルダーの `/ocean_blue` または `/slate_gray` を選択します。
1. 前のセクションの説明に従い、テンプレート フォルダーと `/assets` フォルダーの下にあるすべてのファイルを Blob Storage にアップロードします。
1. 次に、`/ocean_blue` または `/slate_gray` のルートにある各 `\*.html` ファイルを開き、相対 URL のすべての出現箇所を、手順 2 でアップロードした css、images、および fonts ファイルの URL に置き換えます。 次に例を示します。
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    ターゲット 
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. `\*.html` ファイルを保存し、それらを Blob Storage にアップロードします。
1. ここで、前述のように、HTML ファイルを指すようにポリシーを変更します。
1. 欠落しているフォント、イメージ、または CSS がある場合は、拡張ポリシーおよび \*.html ファイルでお使いの参照を確認してください。
