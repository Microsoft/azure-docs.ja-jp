---
title: "Azure Security Center で OS バージョンを更新する | Microsoft Docs"
description: "この記事では、&quot;**OS バージョンの更新**&quot; という Azure Security Center の推奨事項を実装する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ce0d178914907750e5da59f223a4b1e04b9bb6fb


---
# <a name="update-os-version-in-azure-security-center"></a>Azure Security Center で OS バージョンを更新する
Azure Security Center では、クラウド サービス内の仮想マシン (VM) で使用しているオペレーティング システム (OS) の最新バージョンが提供されている場合、そのバージョンに更新するよう推奨されます。  監視されるのは、運用スロットで実行されているクラウド サービスの Web ロールと worker ロールだけです。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。  ステップ バイ ステップ ガイドではありません。
> 
> 

## <a name="implement-the-recommendation"></a>推奨事項の実装
1. **[推奨事項]** ブレードで、**[OS バージョンの更新]** を選択します。
   ![OS バージョンの更新][1]
2. これにより、 **[Update OS version (OS バージョンの更新)]**ブレードが開きます。 このブレードの手順に従い、OS バージョンを更新します。

## <a name="see-also"></a>関連項目
この記事では、"OS バージョンの更新" という Security Center の推奨事項を実装する方法について説明しました。 クラウド サービス、およびクラウド サービスの OS バージョンの更新について詳しくは、以下の記事を参照してください。

* [Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)
* [クラウド サービスの更新方法](../cloud-services/cloud-services-update-azure-service.md)
* [Cloud Services の構成方法](../cloud-services/cloud-services-how-to-configure-portal.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」-- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png



<!--HONumber=Nov16_HO3-->


