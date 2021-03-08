---
author: baanders
description: Visual Studio から Azure 関数を発行するプロセス用のインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 63b393f519ad29baa05fef046ee1e8ba9e5330d8
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701163"
---
プロジェクトを Azure の関数アプリに発行するには、"*ソリューション エクスプローラー*" でプロジェクトを右クリックし、 **[発行]** を選択します。

> [!IMPORTANT] 
> Azure の関数アプリに発行すると、Azure Digital Twins とは関係なく、サブスクリプションに追加料金が発生します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="右クリックのソリューション メニューを示す Visual Studio のスクリーンショット。メニューで [発行] が強調表示されています。":::

表示された *[発行]* ページで、既定のターゲット (**Azure**) を選択したままにし、 *[次へ]* をクリックします。 

特定のターゲットで **[Azure Function App (Windows)]** を選択し、 *[次へ]* をクリックします。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Azure 関数の [発行] ダイアログの Visual Studio のスクリーンショット。[特定のターゲット] ページで [Azure Function App (Windows)] が選択されています。":::

*[Functions インスタンス]* ページで、該当するサブスクリプションを選択します。 これで、サブスクリプション内の "*リソース グループ*" がボックスに反映されます。

インスタンスのリソース グループを選択し、 *+* をクリックして新しい Azure 関数を作成します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Azure 関数の [発行] ダイアログの Visual Studio のスクリーンショット。[Functions インスタンス] ページで、新しい関数を作成するための [+] ボタンが強調表示されています。":::

*[Function App (Windows) - 新規作成]* ウィンドウの各フィールドに次のように入力します。
* **[名前]** は、Azure Functions アプリをホストするために Azure で使用される従量課金プランの名前です。 実際の関数を収容する関数アプリの名前としても使用されます。 一意の値を独自に選択するか、候補として表示された既定値をそのまま使用します。
* **[サブスクリプション]** は、使用するサブスクリプションと一致させます。 
* **[リソース グループ]** は、使用するリソース グループと合わせます。
* **[プランの種類]** は "*従量課金プラン*" のままにします。
* リソース グループの場所と一致する **[場所]** を選択します。
* *[新規]* リンクを使用して、新しい **Azure Storage** リソースを作成します。 場所の設定はリソース グループに合わせてください。それ以外は既定値のままにし、[OK] をクリックします。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Azure 関数の [発行] ダイアログの Visual Studio のスクリーンショット。名前、サブスクリプション、リソース グループ、プランの種類、場所、Azure Storage など、新しい関数アプリの詳細が入力されています。":::

そのうえで **[Create]\(作成\)** を選択します。

*[Functions インスタンス]* ページが再び表示されます。今度は、リソース グループの下に新しい関数アプリが確認できます。 *[終了]* をクリックします。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Visual Studio で Azure 関数を発行する: Functions インスタンス (関数アプリの後)":::

Visual Studio のメイン ウィンドウに再度 *[発行]* ペインが表示されるので、そこですべての情報が正しいことを確認し、 **[発行]** を選択します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Azure 関数の [発行] ダイアログの Visual Studio のスクリーンショット。新しい関数アプリが関数アプリの一覧に表示され、[終了] ボタンが表示されます。":::

> [!NOTE]
> 次のようなポップアップが表示される場合は、以下のようにします。:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="発行資格情報と呼ばれる Visual Studio ポップアップ ウィンドウのスクリーンショット。これには、ユーザー名とパスワードのフィールドと、[Attempt to retrieve credentials from Azure]\(Azure から資格情報の取得を試みる\) ためのボタンが含まれています。" border="false":::
> **[Attempt to retrieve credentials from Azure]\(Azure から資格情報の取得を試みる\)** 、 **[保存]** の順に選択します。
>
> "*Azure で関数のバージョンをアップグレード*" するようにという警告や、"*関数ランタイムのバージョンが Azure で実行されているバージョンと一致しない*" という警告が表示される場合は、次のようにしてください。
>
> 画面の指示に従って、最新の Azure Functions ランタイム バージョンにアップグレードします。 この問題は、古いバージョンの Visual Studio を使用している場合に発生する可能性があります。

関数アプリから Azure Digital Twins にアクセスできるようにするには、システムマネージド ID を割り当て、Azure Digital Twins インスタンスにアクセスできるアクセス許可を付与する必要があります。 次はその設定をします。
