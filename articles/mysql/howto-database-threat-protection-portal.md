---
title: Advanced Threat Protection - Azure Database for MySQL
description: データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出する Advanced Threat Protection を構成する方法について説明します。
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: fe058433cfe4c6c9f89f14d2c74e33ff1b8372ac
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970497"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Azure Database for MySQL の Advanced Threat Protection

Azure Database for MySQL 用の Advanced Threat Protection では、データベースにアクセスしたりデータベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。

Advanced Threat Protection は、高度なセキュリティ機能の統合パッケージである Advanced Data Security オファリングの一部です。 Advanced Threat Protection は [Azure portal](https://portal.azure.com) を使ってアクセスおよび管理でき、現在はプレビュー状態です。

> [!NOTE]
> Advanced Threat Protection 機能は、次の Azure 政府およびソブリン クラウド リージョンでは使用**できません**。US Gov テキサス、US Gov アリゾナ、US Gov アイオワ、US Gov バージニア、US DoD 東部、US DoD 中部、ドイツ中部、ドイツ北部、中国東部、中国東部 2。 一般的な製品の可用性については[リージョン毎の製品の可用性](https://azure.microsoft.com/global-infrastructure/services/)を確認してください。
>

> [!NOTE]
> この機能は、Azure Database for MySQL が汎用サーバーとメモリ最適化サーバー用にデプロイされている Azure のすべてのリージョンで利用できます。

## <a name="set-up-threat-detection"></a>脅威検出の設定
1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) を開きます。
2. 保護する Azure Database for MySQL サーバーの構成ページに移動します。 セキュリティの設定で、 **[Advanced Threat Protection] (プレビュー)** を選びます。
3. **[Advanced Threat Protection] (プレビュー)** の構成ページで次のようにします。

   - サーバーでの Advanced Threat Protection を有効にします
   - **[Advanced Threat Protection 設定]** の **[アラートの送信先]** テキスト ボックスで、異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取るメール アドレスの一覧を指定します。
  
   ![脅威検出の設定](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>異常なデータベース アクティビティの調査

異常なデータベース アクティビティが検出されると、電子メールで通知を受け取ります。 電子メールでは、異常なアクティビティの特徴、データベース名、サーバー名、アプリケーション名、イベントの時刻など、疑わしいセキュリティ イベントについての情報が提供されます。 さらに、データベースへの潜在的な脅威の考えられる原因と調査や緩和のための推奨されるアクションについての情報も提供されます。
 
1. 電子メールの **[View recent alerts]\(最近のアラートの表示\)** リンクをクリックして Microsoft Azure portal を起動し、Azure Security Center のアラート ページを表示します。このページには、SQL データベースで検出されたアクティブな脅威の概要が示されます。
    
    ![異常アクティビティ レポート](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    アクティブな脅威を表示する:

    ![アクティブな脅威](./media/howto-database-threat-protection-portal/active-threats.png)

2. 特定のアラートをクリックすると、さらに詳細な情報と、この脅威を調査し、今後の脅威に対処するためのアクションが表示されます。
    
    ![特定のアラート](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>脅威検出アラートを調査する

SQL Database の脅威の検出では、アラートが [Azure Security Center](https://azure.microsoft.com/services/security-center/) と統合されています。 ライブ SQL 脅威検出タイルは、Azure portal でデータベースおよび SQL ATP ページ内のアクティブな脅威の状態を追跡します。

**[脅威検出アラート]** をクリックすると、Azure Security Center のアラート ページが起動され、データベースに対して検出されたアクティブな SQL 脅威の概要が表示されます。

   ![脅威検出アラート](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>次の手順

* [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について参照してください
* 価格の詳細については、[Azure Database for MySQL の価格ページ](https://azure.microsoft.com/pricing/details/mysql/)をご覧ください  
