---
title: Microsoft Cloud App Security によるアプリの可視性と制御
description: アプリのリスク レベルを識別し、侵害と漏洩をリアルタイムで阻止し、アプリ コネクタを使用して、可視性とガバナンスのためのプロバイダー API を活用する方法について説明します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62a77c1b21a6d602a2d54f56a2ed294fe800ac38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763654"
---
# <a name="cloud-app-visibility-and-control"></a>クラウド アプリの可視性と制御

クラウド アプリとサービスの利点を最大限に活用するには、IT チームは、重要なデータを保護するためのコントロールを維持しながら、アクセスをサポートするための適切なバランスを見つける必要があります。 Microsoft Cloud App Security は、Microsoft およびサードパーティのクラウド サービス全体にわたるサイバー攻撃の脅威を特定し、対処するために、豊富な表示機能、データ送受信の制御、高度な分析を備えています。

## <a name="discover-and-manage-shadow-it-in-your-network"></a>ネットワーク内のシャドウ IT の検出と管理

従業員が使用しているクラウド アプリがいくつあると思うかと IT 管理者に尋ねたところ、平均で 30 または 40 と答えましたが、実際に組織内の従業員が使用しているアプリの数は平均で 1,000 を超えていました。 シャドウ IT は、どのアプリが使用されているか、およびどのようなリスク レベルであるかを把握し特定するのに役立ちます。 誰も確認しておらず、セキュリティとコンプライアンス ポリシーに準拠していない未承認のアプリを従業員の 80% が使用しています。 また、従業員は企業ネットワークの外部からリソースやアプリにアクセスできるため、ファイアウォールでルールやポリシーを設定するだけではもはや不十分です。

Microsoft Cloud App Discovery (Azure Active Directory Premium P1 機能) を使用して、使用されているアプリを検出し、それらのアプリのリスクを調査し、リスクの高い新しいアプリを特定するためのポリシーを構成し、これらのアプリを、プロキシまたはファイアウォール アプライアンスを使用してネイティブにブロックするために、承認しないようにします。

- シャドウ IT の検出と識別
- 評価と分析
- アプリの管理
- 高度なシャドウ IT 検出レポート
- 承認されたアプリの制御
 
### <a name="learn-more"></a>詳細情報

- [ネットワーク内のシャドウ IT の検出と管理](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Cloud App Security で検出されたアプリ](https://docs.microsoft.com/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>ユーザー セッションの可視性と制御 

今日の職場では、多くの場合、クラウド環境で何が起こっているかを事後的に知るだけでは十分ではありません。 従業員が意図的に、あるいは不注意でデータと組織を危険にさらす前に、侵害と漏洩をリアルタイムで阻止する必要があります。 Microsoft Cloud App Security は Azure Active Directory (Azure AD) とともに、アプリの条件付きアクセス制御を使用して、包括的で統合されたエクスペリエンスでこれらの機能を提供します。 

セッション制御はリバース プロキシ アーキテクチャを使用し、Azure AD 条件付きアクセスと一意に統合されます。 Azure AD 条件付きアクセスによって、組織のアプリに対するアクセス制御を一定の条件に基づいて適用できます。 この条件では、条件付きアクセス ポリシーの適用先が、誰であるか (ユーザーまたはユーザーのグループ)、何であるか (どのクラウド アプリか)、どこであるか (どの場所およびネットワークか) が定義されます。 条件を決定したら、データをリアルタイムで保護できる Cloud App Security にユーザーをルーティングできます。  

このコントロールを使用すると、次のことができます。  
- ファイルのダウンロードを制御する
- B2B シナリオを監視する  
- ファイルへのアクセスを制御する  
- ダウンロード時にドキュメントを保護する  
 
### <a name="learn-more"></a>詳細情報

- [Cloud App Security でセッション制御を使用してアプリを保護する ](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>高度なアプリの可視性と制御 

アプリのコネクタは、アプリ プロバイダーの API を使用して、接続するアプリに対する Microsoft Cloud App Security による可視性と制御を強化します。 Cloud App Security は、クラウド プロバイダーによって提供される API を活用します。 各サービスには、調整、API の制限、動的なタイムシフト API ウィンドウなど、独自のフレームワークと API の制限があります。 Cloud App Security 製品チームはこれらのサービスを使用して、API の使用を最適化し、最高のパフォーマンスを提供します。 サービスによって API に適用されるさまざまな制限事項を考慮しながら、Cloud App Security エンジンは最大許容容量を使用します。 テナント内のすべてのファイルをスキャンするなど、一部の操作では多数の API 呼び出しが必要になるため、長期間に及びます。 ポリシーによっては数時間あるいは数日にわたって実行されることを想定してください。 
 
### <a name="learn-more"></a>詳細情報  

- [Cloud App Security でアプリに接続する](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>次のステップ

- [ネットワーク内のシャドウ IT の検出と管理](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Cloud App Security で検出されたアプリ](https://docs.microsoft.com/cloud-app-security/discovered-apps)
- [Cloud App Security でセッション制御を使用してアプリを保護する ](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Cloud App Security でアプリに接続する](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)
