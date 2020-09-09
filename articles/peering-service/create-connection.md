---
title: 'Azure Peering Service: 作成 '
description: このチュートリアルでは、Azure Peering Service とプレフィックスを登録する方法について説明します。
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/2/2020
ms.author: derekol
Customer intent: With Azure Peering service enhancing the customer connectivity to Microsoft cloud services .
ms.openlocfilehash: f04ae0d2e541be0a7984e944298fa8d7b47fd126
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "84870576"
---
# <a name="tutorial-create-a-peering-service-connection"></a>チュートリアル:Peering Service 接続を作成する

このチュートリアルでは、Peering Service リソースを作成する方法と、Peering Service 接続を構成する方法について説明します。 

1. Peering Service 接続を登録するには、 **[リソースの作成]**  > **[Peering Service]** を選択します。

 
    ![Peering Service の登録](./media/peering-service-portal/peering-servicecreate.png)

2. **[Create a peering service connection]\(Peering Service 接続の作成\)** ページの **[基本]** タブで、次の詳細を入力します。
 
3. サブスクリプションと、サブスクリプションに関連付けられているリソース グループを選択します。

    ![Peering Service の登録、基本タブ](./media/peering-service-portal/peering-servicebasics.png)

4. Peering Service インスタンスを登録する名前を **[名前]** に入力します。

5. 次に、ページの下部にある **[次へ: 構成]** ボタンを選択します。 **[構成]** ページが表示されます。
## <a name="configure-the-peering-service-connection"></a>Peering Service 接続を構成する

1. **[構成]** ページで、 **[Peering service location]\(ピアリング サービスの場所\)** ボックスの一覧から Peering Service を有効にする場所を選択します。

1. Peering Service を取得するサービス プロバイダーを選び、 **[Peering service provider]\(ピアリング サービス プロバイダー\)** ボックスの一覧からプロバイダー名を選択します。
 
1. **[プレフィックス]** セクションの下部にある **[Create new prefix]\(新しいプレフィックスを作成する\)** を選択すると、テキスト ボックスが表示されます。 ここで、サービス プロバイダーに関連付けられているプレフィックス リソースの名前とプレフィックスを入力します。

1. **[Prefix key]\(プレフィックス キー\)** を選択し、プロバイダー (ISP または IXP) から付与されているプレフィックス キーを追加します。 MS は、このキーを使用して、IP プレフィックスとこのプレフィックスを割り当てたプロバイダーを検証できます。

    ![Peering Service の構成、構成タブ](./media/peering-service-portal/peering-serviceconfiguration.png)

1. ページの左下にある **[Review + create]\(確認と作成\)** ボタンを選択します。 **[Review + create]\(確認と作成\)** ページが表示され、Azure によって構成が検証されます。

 1. 示されているように、 **[検証に成功しました]** というメッセージが表示されたら、 **[作成]** を選択します。

> ![Peering Service の構成、構成タブ](./media/peering-service-portal/peering-service-prefix.png)

1. Peering Service 接続を登録すると、含まれているプレフィックスに対して追加の検証が実行されます。 検証の状態は、リソース名の **[プレフィックス]** セクションで確認できます。 検証が失敗した場合は、次のいずれかのエラー メッセージが表示されます。

   - Invalid Peering Service prefix, the prefix should be valid format, only Ipv4 prefix is supported. (Peering Service のプレフィックスが無効です。プレフィックスは有効な形式である必要があります。Ipv4 プレフィックスのみがサポートされています。)
   - Prefix was not received from Peering Service provider. (Peering Service プロバイダーからプレフィックスを受信できませんでした。)
   - Prefix announcement does not have a valid BGP community, please contact Peering Service provider. (プレフィックスのアナウンスに有効な BGP コミュニティが含まれていません。Peering Service プロバイダーにお問い合わせください。)
   - Backup route not found, please contact Peering Service provider. (バックアップ ルートが見つかりません。Peering Service プロバイダーにお問い合わせください。)
   - Prefix received with longer AS path, please contact Peering Service provider. (より長い AS パスを含むプレフィックスを受信しました。Peering Service プロバイダーにお問い合わせください。)
   - Prefix received with private AS in the path, please contact Peering Service provider. (パスにプライベート AS を含むプレフィックスを受信しました、Peering Service プロバイダーにお問い合わせください。)

### <a name="add-or-remove-a-prefix"></a>プレフィックスを追加または削除する

プレフィックスを追加するには、 **[プレフィックス]** ページで **[プレフィックスの追加]** を選択します。

表示されているプレフィックスの横にある省略記号 ([...]) を選択し、 **[削除]** を選択します。

### <a name="delete-a-peering-service-connection"></a>Peering Service 接続を削除する

**[すべてのリソース]** ページで、Peering Service のチェック ボックスをオンにし、ページの上部にある **[削除]** を選択します。
## <a name="next-steps"></a>次のステップ

- Peering Service 接続の詳細については、「[Peering Service 接続](connection.md)」を参照してください。
- Peering Service 接続のテレメトリの詳細については、「[Peering Service 接続のテレメトリ](connection-telemetry.md)」を参照してください。
- テレメトリを測定する方法については、[接続のテレメトリの測定](measure-connection-telemetry.md)に関するページを参照してください。
- Azure PowerShell を使用して接続を登録する方法については、[Azure PowerShell を使用した Peering Service 接続の登録](powershell.md)に関するチュートリアルを参照してください。
- Azure CLI を使用して接続を登録する方法については、[Azure CLI を使用した Peering Service 接続の登録](cli.md)に関するページを参照してください。