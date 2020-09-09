---
title: Microsoft Azure Data Box の自己管理型の発送 |Microsoft Docs in data
description: Azure Data Box デバイスの自己管理型の発送ワークフローについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: cfa2dee41f07e273d933da356f74063453cb1775
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142538"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Azure portal で Azure Data Box の自己管理型の発送を使用する

この記事では、Azure Data Box デバイスの注文、受け取り、および返却を行う、自己管理型の発送タスクについて説明します。 Data Box デバイスは、Azure portal を使用して管理できます。

## <a name="prerequisites"></a>前提条件

自己管理型の発送は、[Azure Data Box の注文](data-box-deploy-ordered.md)時にオプションとして利用できます。 自己管理型の発送は、次のリージョンでのみご利用いただけます。

* 米国政府
* 西ヨーロッパ
* 日本
* シンガポール
* 韓国
* インド
* 南アフリカ

## <a name="use-self-managed-shipping"></a>自己管理の出荷の使用

Data Box を注文するときに、自己管理型の発送オプションを選択できます。

1. Azure Data Box の注文の **[連絡先の詳細]** で、 **[+ 配送先住所の追加]** を選択します。
   ![自己管理型の発送](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 発送の種類を選択する時に、 **[Self-managed shipping]\(自己管理型の発送\)** オプションを選択します。 このオプションは、「前提条件」で説明されているように、サポートされているリージョンでのみ使用できます。

3. 発送先住所を入力したら、確認して注文を完了する必要があります。
   ![自己管理型の発送](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. デバイスの準備が完了し、その電子メール通知を受け取ったら、受け取りをスケジュールします。

   Azure Data Box の注文の **[概要]** に移動し、 **[Schedule pickup]\(受け取りのスケジュールを設定\)** を選択します。

   ![Data Box デバイスの受け取りを注文する](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. **[Schedule pickup for Azure]\(Azure の受け取りのスケジュール\)** の説明に従ってください。

   認証コードを取得する前に、[adbops@microsoft.com](mailto:adbops@microsoft.com) にメールを送信して、リージョンのデータセンターからデバイスを受け取りのスケジュールを設定する必要があります。

   ![集荷のスケジュール](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. デバイスの受け取りをスケジュールすると、 **[Schedule pickup for Azure]\(Azure の受け取りのスケジュール\)** ウィンドウにデバイスの認証コードが表示されます。

   ![認証コードの表示](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   この**認証コード**をメモしておきます。 セキュリティ要件に従って、受け取りをスケジュールするときに、受け取りを行う人物の名前を指定する必要があります。

   また、データセンターに受け取りに行くユーザーの詳細を指定する必要もあります。 お客様または連絡担当者は、データセンターで検証される政府発行の写真付きの ID を携帯する必要があります。

   さらに、デバイスを受け取りに行くユーザーにも、**認証コード**が必要です。 認証コードは、データセンターでの受け取り時に検証されます。

7. デバイスをデータセンターから受け取ると、注文は自動的に **[Picked up]\(受け取り済み\)** 状態になります。

    ![集荷されました](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. デバイスを受け取ったら、サイトのデータを Data Box にコピーします。 データのコピーが完了したら、Data Box の発送の準備をします。 詳細については、「[配送の準備](data-box-deploy-picked-up.md#prepare-to-ship)」を参照してください。

   **発送準備**の手順は、重大なエラーがないよう完了する必要があります。エラーがあった場合、必要な修正を行った後で、この手順をもう一度実行する必要があります。 発送準備が正常に完了したら、デバイスのローカル ユーザーインターフェイスで、返却用の認証コードを確認します。

   > [!NOTE]
   > この認証コードは、メールで共有しないでください。 これは、データセンターでの返却時にのみ検証されます。

9. 返却の予定を受け取ると、Azure portal に表示されるその注文の状態が **[Ready to receive at Azure datacenter]\(Azure データセンターで受け取る準備が完了しました\)** になります。 **[Schedule drop-off]\(返却のスケジュール\)** の指示に従って、デバイスを返却します。

   ![認証コードの表示](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. ID と認証コードが検証され、データセンターにデバイスを返却すると、注文ステータスが **[Received]\(返却済み\)** になります。

    ![返却完了](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. デバイスを返却すると、データのコピーが続行されます。 コピーが完了すると、注文が完了します。

## <a name="next-steps"></a>次のステップ

* [Azure Data Box を使ってみる](data-box-quickstart-portal.md)
