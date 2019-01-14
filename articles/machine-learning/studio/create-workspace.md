---
title: ワークスペースの作成 - Azure Machine Learning Studio | Microsoft Docs
description: Azure Machine Learning Studio を使用するには、Machine Learning Studio ワークスペースが必要です。 このワークスペースには、実験を管理および公開するのに必要なツールが用意されています。
services: machine-learning
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: e6835afd66d054a79c422a35839d64ae89031de5
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268259"
---
# <a name="create-and-share-an-azure-machine-learning-studio-workspace"></a>Azure Machine Learning Studio ワークスペースの作成と共有

Azure Machine Learning Studio を使用するには、Machine Learning Studio ワークスペースが必要です。 このワークスペースには、実験を管理および公開するのに必要なツールが用意されています。



### <a name="to-create-a-workspace"></a>ワークスペースを作成するには
1.  [Azure ポータル](https://portal.azure.com/)

    > [!NOTE]
    > サインインしてワークスペースを作成するには、Azure サブスクリプションの管理者である必要があります。 
    >
    > 

2. **[+新規]** をクリックします。

3. 検索ボックスに「**Machine Learning Studio ワークスペース**」と入力し、一致する項目を選びます。 次に、ページ下部にある **[作成]** をクリックします。

4. ワークスペースの情報を入力します。

    - *[ワークスペース名]* に使用できる最大文字数は 260 文字です。末尾に空白文字は使用しないでください。 また、`< > * % & : \ ? + /` の各文字は使用できません。
    - このワークスペースから Web サービスをデプロイした場合、選択 (または作成) した "*Web サービス プラン*" とそれに関連して選択した "*価格レベル*" が使用されます。

    ![新しいワークスペースを作成する](./media/create-workspace/create-new-workspace.png)

5. **Create** をクリックしてください。

デプロイしたワークスペースは、Machine Learning Studio で開くことができます。

1. [https://studio.azureml.net/](https://studio.azureml.net/) で Machine Learning Studio を参照します。

2. 右上隅で該当するワークスペースを選択します。

    ![ワークスペースを選択](./media/create-workspace/open-workspace.png)

3. **[my experiments (実験)]** をクリックします。

    ![実験を開く](./media/create-workspace/my-experiments.png)

ワークスペースの管理方法の詳細については、「 [Azure Machine Learning ワークスペースの管理](manage-workspace.md)」をご覧ください。
ワークスペースの作成で問題が発生した場合は、「[トラブルシューティング ガイド: Machine Learning ワークスペースの作成と接続](troubleshooting-creating-ml-workspace.md)」をご覧ください。


## <a name="sharing-an-azure-machine-learning-workspace"></a>Azure Machine Learning ワークスペースの共有
Machine Learning ワークスペースを作成した後は、ユーザーをワークスペースに招待し、ワークスペースとそのすべての実験、データセット、ノートブックへのアクセス権を共有することができます。ユーザーは、次のいずれかの役割で追加することができます。

* **ユーザー** - ワークスペース内の実験やデータセットの作成、表示、変更、削除を行うことができます。
* **所有者** - ワークスペース ユーザーの招待と削除、ユーザーが実行できる操作の登録を行うことができます。

> [!NOTE]
> ワークスペースを作成した管理者アカウントは、ワークスペースの所有者としてワークスペースに自動的に追加されます。 ただしそのサブスクリプションに含まれる他の管理者またはユーザーについては、ワークスペースへのアクセス権が自動的には付与されません。明示的に招待する必要があります。
> 
> 

### <a name="to-share-a-workspace"></a>ワークスペースを共有するには

1. [https://studio.azureml.net/Home](https://studio.azureml.net/Home) で Machine Learning Studio にサインインします。

2. 左側のパネルの **[設定]** をクリックします。

3. **[ユーザー]** タブをクリックします。

4. ページ下部の **[その他のユーザーの招待]** をクリックします。

    ![Studio の設定](./media/create-workspace/settings.png)

5. 電子メール アドレスを少なくとも 1 つ入力します。 ユーザーには、有効な Microsoft アカウントまたは (Azure Active Directory の) 組織アカウントが必要です。

6. ユーザーを所有者として追加するか、ユーザーとして追加するかを選択します。

7. チェックマークが表示された **[OK]** ボタンをクリックします。

追加した各ユーザーには、共有ワークスペースへのサインイン方法が記載された電子メールが送信されます。

> [!NOTE]
> このワークスペースの Web サービスをデプロイしたり管理したりするユーザーは、Azure サブスクリプションの共同作成者または管理者である必要があります。 



