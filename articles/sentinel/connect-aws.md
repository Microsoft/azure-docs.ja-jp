---
title: AWS CloudTrail を Azure Sentinel に接続する | Microsoft Docs
description: Azure Sentinel に AWS CloudTrail のデータを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 61bee1618092788da33549ec322dc4ab7130de16
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244692"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Azure Sentinel を AWS CloudTrail に接続する

AWS コネクタを使用してすべての AWS CloudTrail イベントを Azure Sentinel にストリーミングします。 この接続プロセスにより、Azure Sentinel へのアクセスが AWS リソース ログに委任され、AWS CloudTrail とAzure Sentinel の間の信頼関係が確立されます。 これを行うには、Azure Sentinel にアクセス許可を付与するロールを AWS で作成し、AWS ログにアクセスできるようにします。

## <a name="prerequisites"></a>前提条件

Azure Sentinel ワークスペースへの書き込みアクセス許可が必要です。

> [!NOTE]
> Azure Sentinel は、すべてのリージョンから CloudTrail イベントを収集します。 あるリージョンから別のリージョンにイベントをストリーミングしないことをお勧めします。

## <a name="connect-aws"></a>AWS の接続 


1. Azure Sentinel で、 **[Data connectors]\(データ コネクタ\)** を選択し、テーブルで **[Amazon Web Services]** 行を選択し、右側の [AWS] ウィンドウで **[Open connector page]\(コネクタ ページを開く\)** をクリックします。

1. 次の手順を使用して、 **[Configuration]\(構成\)** の下の指示に従います。
 
1.  アマゾン ウェブ サービス コンソールの **[Security, Identity & Compliance]\(セキュリティ、ID、およびコンプライアンス\)** で **[IAM]** を選択します。

    ![AWS1](./media/connect-aws/aws-1.png)

1.  **Roles\(ロール\)** を選択し、**Create role\(ロールの作成\)** を選択します。

    ![AWS2](./media/connect-aws/aws-2.png)

1.  **[別の AWS アカウント]** を選択します。 **[アカウント ID]** フィールドに、**Microsoft アカウント ID** (**123412341234**) を入力します。このID は、Azure Sentinel ポータルの AWS コネクタ ページで確認できます。

    ![AWS3](./media/connect-aws/aws-3.png)

1.  必ず **[外部 ID が必要]** を選択し、外部 ID (ワークスペース ID) を入力します。この ID は、Azure Sentinel ポータルの AWS コネクタ ページで確認できます。

    ![AWS4](./media/connect-aws/aws-4.png)

1.  **[Attach permissions policy]\(アクセス許可ポリシーのアタッチ\)** で **[AWSCloudTrailReadOnlyAccess]** を選択します。

    ![AWS5](./media/connect-aws/aws-5.png)

1.  (省略可能) タグを入力します。

    ![AWS6](./media/connect-aws/aws-6.png)

1.  次に、**ロール名**を入力し、 **[ロールの作成]** を選択します。

    ![AWS7](./media/connect-aws/aws-7.png)

1.  ロールの一覧で、作成したロールを選択します。

    ![AWS8](./media/connect-aws/aws-8.png)

1.  **Role ARN** をコピーします。 Azure Sentinel ポータルのアマゾン ウェブ サービス コネクタ画面の **[Role to add]\(追加するロール\)** フィールドにこれを貼り付け、 **[Add]\(追加\)** をクリックします。

    ![AWS9](./media/connect-aws/aws-9.png)

1. Log Analytics で AWS イベントに関連するスキーマを使用するには、**AWSCloudTrail** を検索します。



## <a name="next-steps"></a>次の手順
このドキュメントでは、AWS CloudTrail を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

