---
title: カスタム イメージ、複数コンテナー、または組み込みのイメージをデプロイする - Azure App Service | Microsoft Docs
description: App Service on Linux のカスタム Docker コンテナーのデプロイか、複数コンテナーか、組み込みのアプリケーション フレームワークかを判断する方法
keywords: Azure App Service, Web アプリ, Linux, OSS
services: app-service
documentationCenter: ''
authors: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 6ebcbc5b716f20cbc1d826899769e5629559675b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543106"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>カスタム イメージか、複数コンテナーか、組み込みのプラットフォーム イメージか

[App Service on Linux](app-service-linux-intro.md) では、アプリケーションを Web に公開する 3 つの異なる方法が用意されています。

- **カスタム イメージのデプロイ**:実行準備完了パッケージにあるすべてのファイルと依存関係を含む Docker イメージにアプリを "Docker 化" します。
- **複数コンテナーのデプロイ**:Docker Compose または Kubernetes 構成ファイルを使用して複数コンテナーにわたってアプリを"Docker 化" します。 詳細については、[複数コンテナー アプリ](#multi-container-apps-supportability)に関するページをご覧ください。
- **組み込みのプラットフォーム イメージを使ったアプリのデプロイ**:組み込みのプラットフォーム イメージには、共通の Web アプリ ランタイムとノードや PHP などの依存関係が含まれています。 いずれかの [Azure App Service のデプロイ方法](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)を使って、お使いの Web アプリのストレージにアプリをデプロイし、組み込みのプラットフォーム イメージを使用してアプリを実行します。

## <a name="which-method-is-right-for-your-app"></a>お使いのアプリにとって最適な方法は? 

考慮すべき主な要素は次のとおりです。

- **開発ワークフローにおける Docker の可用性**:カスタム イメージの開発には、Docker 開発ワークフローの基本的な知識が必要です。 Web アプリにカスタム イメージをデプロイするには、Docker Hub などのリポジトリ ホストにカスタム イメージを発行する必要があります。 Docker をよく理解していて、ビルド ワークフローに Docker タスクを追加できる場合、または、既に Docker イメージとしてアプリを発行している場合は、カスタム イメージがほぼ確実に最適な選択です。
- **多層アーキテクチャ**:複数コンテナーを使用することで、Web アプリケーション レイヤー、API レイヤーなど、複数のコンテナーをデプロイして機能を分離します。 
- **アプリケーションのパフォーマンス**:Redis などのキャッシュ レイヤーを使用して、お使いの複数コンテナー アプリのパフォーマンスを向上させます。 これを実現するには、複数コンテナーを選択します。
- **一意のランタイム要件**:組み込みのプラットフォーム イメージは、ほとんどの Web アプリのニーズに合うように設計されていますが、カスタマイズ性は制限されます。 アプリが一意の依存関係や、組み込みのイメージでは対応しきれない他のランタイム要件を備えている場合もあります。
- **ビルドの要件**:[継続的配置](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)を利用すると、ソース コードから直接アプリを起動して Azure 上で実行できます。 外部のビルドや発行プロセスは必要ありません。 ただし、[Kudu](https://github.com/projectkudu/kudu/wiki) デプロイ エンジン内の組み込みツールのカスタマイズ性と可用性には制限があります。 お使いのアプリが、カスタム ビルド ロジックの依存関係や要件において向上すると、それに伴って Kudu の機能を拡張できます。
- **ディスク読み取り/書き込み要件**:すべての Web アプリは Web コンテンツのストレージ ボリュームに割り当てられます。 Azure Storage でサポートされるこのボリュームは、アプリのファイルシステムの `/home` にマウントされています。 コンテナー ファイルシステム内のファイルとは異なり、コンテンツ ボリューム内のファイルは、アプリのスケール インスタンス全体にアクセスでき、アプリを再起動しても変更は保持されます。 ただし、コンテンツ ボリュームのディスクの待ち時間は、ローカル コンテナー ファイルシステムの待ち時間よりも長く、より変化が大きくなります。また、アクセスは、プラットフォームのアップグレード、計画外のダウンタイム、およびネットワーク接続の問題の影響を受ける場合があります。 コンテンツ ファイルに対して高負荷の読み取り専用アクセスが必要になるアプリでは、コンテンツ ボリュームではなく、イメージ ファイルシステムにファイルを配置するカスタム イメージのデプロイが有益な場合があります。
- **ビルド リソースの利用状況**:アプリがソースからデプロイされた場合、Kudu から実行されるデプロイ スクリプトでは、実行中のアプリと同じ App Service プランのコンピューティングとストレージ リソースを使用します。 大規模なアプリのデプロイには、予定よりも多くのリソースや時間を費やすことがあります。 特に、デプロイ ワークフローの数が多くなると、アプリのコンテンツ ボリュームではディスクの動作が高負荷になります。 カスタム イメージでは、アプリのすべてのファイルと依存関係を単一のパッケージで Azure に配信し、追加のファイル転送やデプロイの動作は必要ありません。
- **迅速な反復処理の必要性**:アプリの Docker 化には、追加のビルド ステップが必要になります。 変更を有効にするには、各更新プログラムを使って新しいイメージをリポジトリにプッシュする必要があります。 これらの更新プログラムは、Azure 環境にプルされます。 組み込みのコンテナーのいずれかがアプリのニーズを満たしている場合、ソースからのデプロイによって、より迅速なデプロイ ワークフローを提供できます。

## <a name="multi-container-apps-supportability"></a>複数コンテナー アプリのサポート

### <a name="supported-docker-compose-configuration-options"></a>サポートされている Docker Compose 構成オプション
- コマンド
- entrypoint
- 環境
- image
- ports
- restart
- services
- volumes

### <a name="unsupported-docker-compose-configuration-options"></a>サポートされていない Docker Compose 構成オプション
- build (禁止)
- depends_on (無視)
- networks (無視)
- secrets (無視)
- ports (80 および 8080 以外) (無視)

> [!NOTE]
> パブリック プレビューでは、ここで明示的に示されていないその他のオプションも無視されます。

### <a name="supported-kubernetes-configuration-options"></a>サポートされている Kubernetes 構成オプション
- args
- コマンド
- containers
- image
- name
- ports
- spec

> [!NOTE]
>ここで明示的に示されていないその他の Kubernetes オプションは、パブリック プレビューではサポートされません。
>
