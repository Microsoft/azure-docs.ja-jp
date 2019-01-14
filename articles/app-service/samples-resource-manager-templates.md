---
title: Azure Resource Manager テンプレートのサンプル - App Service | Microsoft Docs
description: App Service 用 Azure Resource Manager テンプレートのサンプル
services: app-service
documentationcenter: app-service
author: tfitzmac
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 10/15/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: d2b7e7a29838fd14292e3498c8db3a496d7d9434
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717808"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>App Service 用 Azure Resource Manager テンプレート

次の表は、Azure App Service 用の Azure Resource Manager テンプレートのリンク一覧です。 アプリのテンプレートを作成するときに多く発生するエラーを回避するために推奨される事柄については、[Azure Resource Manager テンプレートを使ったアプリのデプロイに関するガイダンス](deploy-resource-manager-template.md)のページを参照してください。

| | |
|-|-|
|**アプリのデプロイ**||
| [App Service プランと基本的な Linux アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Linux 用に構成された App Service アプリをデプロイします。 |
| [App Service プランと基本的な Windows アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Windows 用に構成された App Service アプリをデプロイします。 |
| [GitHub リポジトリにリンクされたアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| GitHub からコードをプルする App Service アプリをデプロイします。 |
| [カスタム デプロイ スロットを使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| カスタム デプロイ スロット/環境を使って App Service アプリをデプロイします。 |
|**アプリの構成**||
| [Key Vault からのアプリの証明書](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| App Service アプリの証明書を Azure Key Vault シークレットからデプロイして SSL バインディングに使用します。 |
| [カスタム ドメインを使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| カスタム ホスト名を使って App Service アプリをデプロイします。 |
| [カスタム ドメインと SSL を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| カスタム ホスト名を使って App Service アプリをデプロイし、アプリの証明書を Key Vault から取得して SSL バインディングに使用します。 |
| [GoLang 拡張機能を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Golang サイト拡張機能を使って App Service アプリをデプロイします。 Golang で開発した Web アプリケーションを Azure で実行することができます。 |
| [Java 8 と Tomcat 8 を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Java 8 と Tomcat 8 に対応した App Service アプリをデプロイします。 Java アプリケーションを Azure で実行することができます。 |
|**接続リソースを使った Linux アプリ**||
| [MySQL を使った Linux 上のアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Azure Database for MySQL を使って Linux 上に App Service アプリをデプロイします。 |
| [PostgreSQL を使った Linux 上のアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Azure Database for PostgreSQL を使って Linux 上に App Service アプリをデプロイします。 |
|**接続リソースを使ったアプリ**||
| [MySQL を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Azure Database for MySQL を使って Windows 上に App Service アプリをデプロイします。 |
| [PostgreSQL を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Azure Database for PostgreSQL を使って Windows 上に App Service アプリをデプロイします。 |
| [SQL データベースを使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| App Service アプリと SQL データベースを Basic サービス レベルでデプロイします。 |
| [Blob Storage 接続を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Azure Blob Storage の接続文字列を使って App Service アプリをデプロイします。 その後、そのアプリから BLOB ストレージを使用することができます。 |
| [Azure Cache for Redis を使ったアプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Azure Cache for Redis を使用して App Service アプリをデプロイします。 |
|**PowerApps の App Service Environment**||
| [App Service Environment v2 の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | App Service Environment v2 を仮想ネットワークに作成します。 |
| [ILB アドレスを使った App Service Environment v2 の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | プライベート内部ロード バランサー アドレスを使って仮想ネットワークに App Service Environment v2 を作成します。 |
| [ILB App Service Environment または ILB App Service Environment v2 に使用する既定の SSL 証明書の構成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | ILB App Service Environment または ILB App Service Environment v2 に使用する既定の SSL 証明書を構成します。 |
| | |
