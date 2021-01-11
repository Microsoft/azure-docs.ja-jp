---
title: 概要 - Azure Database for MySQL
description: MySQL Community Edition をベースとした、Microsoft クラウドのリレーショナル データベース サービス、Azure Database for MySQL サービスについて説明します。
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 37bc99d9f83f185a5372fd45634351987b85e20b
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2020
ms.locfileid: "88763662"
---
# <a name="what-is-azure-database-for-mysql"></a>Azure Database for MySQL とは -

Azure Database for MySQL は、Microsoft クラウドにおける、(GPLv2 ライセンスで利用可能な) [MySQL Community Edition](https://www.mysql.com/products/community/) のデータベース エンジン バージョン 5.6、5.7、および 8.0 をベースとしたリレーショナル データベース サービスです。 Azure Database for MySQL には、次の特長があります。

- 追加コストなしの組み込みの高可用性。
- 包括的な従量課金制の料金を使用した、予測可能なパフォーマンス。
- 必要に応じて数秒以内にスケーリング。
- 保存中や転送中の機密データのセキュリティ保護。
- 自動バックアップと最大 35 日間のポイントインタイム リストア。
- エンタープライズグレードのセキュリティとコンプライアンス。

これらの機能は、ほとんど管理を必要とせず、いずれも追加費用なしで利用することができます。 そのため貴重な時間とリソースを仮想マシンとインフラストラクチャの管理に奪われることなく、迅速なアプリ開発や、製品化に要する時間の短縮化に専念することができます。 加えて、オープン ソースのツールとプラットフォームを自由に選んでアプリケーションを開発し続けることができるので、新たにスキルを身に付けなくても確実に、ビジネスの現場で必要となるスピードと効率を高めることができます。

![Azure Database for MySQL の概念図](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

この記事では、パフォーマンスとスケーラビリティ、管理容易性に関連する、Azure Database for MySQL の中心概念と機能について紹介し、詳細を参照するためのリンクも提供します。 すぐに始めるには、次のクイック スタートをご覧ください。

- [Azure Portal を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-cli.md)

Azure CLI の各種サンプルについては、次のページを参照してください。

- [Azure Database for MySQL 用 Azure CLI サンプル](sample-scripts-azure-cli.md)

## <a name="automated-patching"></a>自動修正
このサービスでは、基になるハードウェア、OS、およびデータベース エンジンの自動修正が実行されます。 修正には、基になるハードウェア、OS、およびデータベース エンジンのセキュリティ更新プログラムとソフトウェア更新プログラムが含まれます。 MySQL エンジンの場合、マイナー バージョンのアップグレードは自動的に行われ、パッチ リリースの一部として含まれます。 コミュニティによってマイナー バージョンがリリースされた場合、そのマイナー バージョンは、サービスのテスト サイクルの一部として自動的に統合されます。 マイナー バージョンのテストは、MySQL の標準的なワークロードの一部で実行されます。 MySQL エンジンのマイナー バージョン リリースは、信頼性 (クラッシュしない)、可用性、セキュリティ、およびパフォーマンスについて評価されます。 すべてのマイナー バージョンがサービスの運用環境用にリリースされるわけではありませんが、バグ修正の重要度と新しい漸進的な価値に基づいて評価されます。 その目的は、新しい漸進的な価値と、システム内の変数を最小化して安定性を高めることの間で適切なバランスを取ることにあります。 パッチの適用に必要なユーザー アクションや構成設定はありません。 パッチの適用頻度は、ペイロードの重要度に基づいてサービスによって管理されます。 一般に、サービスは、継続的インテグレーションとリリースの一環として、毎月のリリース スケジュールに従います。 ユーザーは、[計画メンテナンスの通知](concepts-monitoring.md)にサブスクライブすることで、予定されているメンテナンスの通知をそのイベントの 72 時間前に受け取ることができます。

## <a name="adjust-performance-and-scale-within-seconds"></a>数秒以内でのパフォーマンスの調整とスケール
Azure Database for MySQL サービスには、次の複数のサービス レベルが用意されています。Basic、汎用、メモリ最適化。 軽量のデータベース ワークロードから重量のデータベース ワークロードに至るまでサポートできるように、レベルごとに異なるパフォーマンスと機能 を提供しています。 最初は月数ドルの小規模データベースでアプリを構築し、後から実際のソリューションのニーズに応じて、スケールを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 必要なときに必要な分のリソースにのみ課金されます。 詳しくは、 [価格レベル](concepts-service-tiers.md)に関するページをご覧ください。

## <a name="monitoring-and-alerting"></a>監視とアラート
スケールを調整するときに、適切なパフォーマンス レベルはどのようにして見極めればよいのでしょうか。 組み込みのパフォーマンス監視機能およびアラート機能と、仮想コアに基づくパフォーマンス評価とを組み合わせて使用します。 これらのツールを使用すると、現在または今後のパフォーマンスのニーズに基づいて、仮想コアのスケールアップまたはスケールダウンの影響をすばやく評価することができます。 詳細については、[アラート](howto-alert-on-metric.md)に関するページを参照してください。

## <a name="keep-your-app-and-business-running"></a>アプリケーションとビジネスの継続的な稼働
Microsoft が管理するデータセンターのグローバル ネットワークによって強化された、Azure の業界をリードする可用性 99.99% のサービス レベル アグリーメント (SLA) により、アプリケーションの 24 時間 365 日の継続的な稼働が可能になります。 すべての Azure Database for MySQL サーバーで、組み込みのセキュリティ、フォールト トレランス、データ保護を利用できます。これらは、本来なら自身で購入または設計し、構築、管理する必要があります。 Azure Database for MySQL では、ポイントインタイム リストアを使用して、サーバーを 35 日間分さかのぼって以前の状態に戻すことができます。

## <a name="secure-your-data"></a>データのセキュリティ保護
Azure データベース サービスは、アクセスの制限、保存データと移動中のデータの保護、監視アクティビティの支援を行う機能を備えた Azure Database for MySQL によって、データ セキュリティを維持してきました。 Azure のプラットフォーム セキュリティについては、[Azure セキュリティ センター](https://www.microsoft.com/trustcenter/security)をご覧ください。 Azure Database for MySQL のセキュリティ機能の詳細については、[セキュリティの概要](concepts-security.md)に関するページを参照してください。

## <a name="contacts"></a>連絡先
Azure Database for MySQL についての質問や提案は、Azure Database for MySQL チームにメール ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)) でお送りください。 このメール アドレスはテクニカル サポートのエイリアスではありません。

さらに、適切な連絡先について次の点を考慮してください。

- Azure サポートに問い合わせる場合は、[Azure portal からチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。
- フィードバックを提供したり、新しい機能を要求したりするには、[UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) でエントリを作成します。

## <a name="next-steps"></a>次のステップ
ここでは、Azure Database for MySQL の概要を紹介し、"Azure Database for MySQL とは何か" という問いに対する答えを示したので、 次のステップに進むことができます。

- コストの比較と計算については、価格のページを参照してください。 [料金](https://azure.microsoft.com/pricing/details/mysql/)
- まず、最初のサーバーを作成します。 [Azure Portal を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-portal.md)
- 次の任意の言語を使って最初のアプリを作成します。
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Go](./connect-go.md)
