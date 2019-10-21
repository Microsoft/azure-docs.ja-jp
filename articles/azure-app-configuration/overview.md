---
title: Azure App Configuration とは | Microsoft Docs
description: Azure App Configuration サービスの概要。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 1db46e8562006e1b60e4f51ca88220aeca5fb2a8
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687038"
---
# <a name="what-is-azure-app-configuration"></a>Azure App Configuration とは

Azure App Configuration は、アプリケーション設定と機能フラグを一元的に管理するためのサービスを提供します。 近年のプログラム、特にクラウドで実行されるプログラムは、その性質上、分散されたコンポーネントが多数存在するのが一般的です。 これらのコンポーネント全体に構成設定を分散させることは、トラブルシューティングすることの難しいエラーがアプリケーションのデプロイ中に発生する原因となります。 App Configuration を使用すると、アプリケーションのすべての設定を 1 か所に格納して、そのアクセスをセキュリティで保護することができます。

現在、App Configuration はパブリック プレビュー段階にあります。 プレビュー期間中は無料で使用できます。 サインアップは [Azure portal](https://portal.azure.com) で行うことができます。

## <a name="why-use-app-configuration"></a>App Configuration を使用する理由

クラウドベースのアプリケーションは、複数リージョンにわたる複数の仮想マシンまたはコンテナーで実行され、複数の外部サービスを使用することが少なくありません。 そのような分散アプリケーションを堅牢でスケーラブルに作成することは、難しいことです。

アプリケーション構築の増大する複雑さに取り組む開発者には、さまざまなプログラミング手法が役立ちます。 たとえば、"12 ファクター アプリ" では、クラウド アプリケーションで使用される多くの実証済みのアーキテクチャ パターンとベスト プラクティスが示されています。 このガイドの主な推奨事項の 1 つは、コードから構成を切り離すことです。 この場合、アプリケーションの構成設定をその実行可能ファイルとは別の場所に置いておき、その実行環境または外部ソースから読み取るということです。

どのアプリケーションでも App Configuration を利用できますが、以下の例は、それを使用することにメリットがある種類のアプリケーションです。

* Azure Kubernetes Service、Azure Service Fabric、または 1 つ以上の地域にデプロイされるその他のコンテナー化されたアプリをベースにしたマイクロサービス
* サーバーレス アプリ (Azure Functions や、その他のイベント駆動型のステートレスなコンピューティング アプリなど)
* 継続的デプロイ パイプライン

App Configuration には次の利点があります。

* 数分で設定できるフル マネージド サービス
* 柔軟性に優れたキーによる表現とマッピング
* ラベルを使用したタグ付け
* 特定時点の設定の再生
* 専用の UI で機能フラグを管理
* 独自に定義したディメンションでの 2 つの構成群の比較
* Azure のマネージド ID で強化されたセキュリティ
* 完全なデータ暗号化 (保存データと転送中のデータ)
* 広く使われているフレームワークとのネイティブ統合

App Configuration は、アプリケーションのシークレットを格納するために使用される [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を補完します。 App Configuration を使用すると、以下のシナリオを簡単に実装できます。

* さまざまな環境や地域に対応した階層型構成データの管理と配布を一元化する
* アプリケーションを再デプロイまたは再起動することなく、アプリケーション設定を動的に変更する
* 機能の可用性をリアルタイムで制御する

## <a name="use-app-configuration"></a>App Configuration の使用

アプリ構成ストアを自分のアプリケーションに追加する最も簡単な方法は、Microsoft が提供するクライアント ライブラリを使用することです。 プログラミング言語とフレームワークに基づいて、使用できる最適な方法を次に示します。

| プログラミング言語とフレームワーク | 接続する方法 |
|---|---|
| .NET Core と ASP.NET Core | .NET Core 用 App Configuration プロバイダー |
| .NET と ASP.NET | .NET 用 App Configuration ビルダー |
| Java Spring | Spring Cloud 用 App Configuration クライアント |
| その他 | App Configuration の REST API |

## <a name="next-steps"></a>次の手順

* [ASP.NET Core のクイック スタート](./quickstart-aspnet-core-app.md)
* [.NET Core のクイック スタート](./quickstart-dotnet-core-app.md)
* [.NET Framework のクイック スタート](./quickstart-dotnet-app.md)
* [Azure 関数のクイック スタート](./quickstart-azure-function-csharp.md)
* [Java Spring のクイック スタート](./quickstart-java-spring-app.md)
* [ASP.NET Core の機能フラグのクイック スタート](./quickstart-feature-flag-aspnet-core.md)
* [Spring Boot の機能フラグのクイックスタート](./quickstart-feature-flag-spring-boot.md)
