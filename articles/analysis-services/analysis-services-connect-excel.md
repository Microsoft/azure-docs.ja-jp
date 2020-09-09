---
title: Excel で Azure Analysis Services に接続する | Microsoft Docs
description: Azure Analysis Services サーバーに Excel を使って接続する方法を説明します。 接続されたら、ユーザーはピボットテーブルを作成してデータを調査できます。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 123e271ae1b83603d599b9ef0381e25b3c963def
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361559"
---
# <a name="connect-with-excel"></a>Excel を使用した接続

サーバーを作成し、それに表形式モデルをデプロイしたら、クライアントは接続してデータの調査を開始できます。 

## <a name="before-you-begin"></a>開始する前に

サインインに使用するアカウントは、少なくとも、読み取り許可を持つ model データベースのロールに属していなければなりません。 詳細については、「[認証とユーザーのアクセス許可](analysis-services-manage-users.md)」を参照してください。 

## <a name="connect-in-excel"></a>Excel での接続

Excel でのサーバーへの接続は、Excel 2016 以降のデータの取得を使用してサポートされます。 Power Pivot での [テーブルのインポート] ウィザードを使用した接続はサポートされません。 

1. Excel では、 **[データ]** リボンで **[外部データの取り込み]**  >  **[その他のデータ ソース]**  >  **[Analysis Services]** の順にクリックします。

2. データ接続ウィザードで、 **[サーバー名]** にプロトコルや URI などのサーバー名を入力します。 たとえば、「asazure://westcentralus.asazure.windows.net/advworks」のように入力します。 そして **[ログオン資格情報]** で **[以下のユーザー名とパスワードを使用する]** を選択し、組織でのユーザー名 (たとえば nancy@adventureworks.com) とパスワードを入力します。

    > [!IMPORTANT]
    > Microsoft アカウント、Live ID、Yahoo、Gmail などでサインインする場合や、多要素認証を使用してサインインする必要がある場合は、パスワード フィールドを空白のままにしておきます。 [次へ] をクリックすると、パスワードの入力を求められます。 

    ![Excel ログオンから接続する](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. **[データベースとテーブルの選択]** で、データベースとモデルまたはパースペクティブを選択し、 **[完了]** をクリックします。
   
    ![Excel 選択モデルから接続する](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>参照

[クライアント ライブラリ](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)   
[サーバーの管理](analysis-services-manage.md)     


