---
title: Azure Security Center でのアラートの検証 (EICAR テスト ファイル) | Microsoft Docs
description: このドキュメントは、Azure Security Center でセキュリティ アラートを検証する場合に役立ちます。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 10ea15168d61d5e73aff976ef641e07b6327dbca
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604569"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Azure Security Center でのアラートの検証 (EICAR テスト ファイル)
このドキュメントは、Azure Security Center のアラートに対してシステムが適切に構成されているかどうかを確認する方法を学ぶのに役立ちます。

## <a name="what-are-security-alerts"></a>セキュリティの警告とは何か
アラートは、Security Center がリソース上の脅威を検出したときに生成する通知です。 アラートは優先順位を付けられ、問題の迅速な調査に必要な情報と共に一覧表示されます。 Security Center は、攻撃をどのように修復できるかに関する推奨事項も提供します。
詳細については、[Security Center のセキュリティ アラート](security-center-alerts-overview.md)および[セキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md)に関する記事をご覧ください

## <a name="alert-validation"></a>アラートの検証

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## Windows VM 上でのアラートの検証 <a name="validate-windows"></a>

Security Center エージェントをコンピューターにインストールした後、アラートの攻撃対象リソースとして使用するコンピューターから次の手順を実行します。

1. 実行可能ファイル (**calc.exe** など) をコンピューターのデスクトップまたは他の適切なディレクトリにコピーし、名前を **ASC_AlertTest_662jfi039N.exe** に変更します。
1. コマンド プロンプトを開き、引数 (単なるダミーの引数名) を指定してこのファイルを実行します。例: ```ASC_AlertTest_662jfi039N.exe -foo```
1. 5 ～ 10 分待って、Security Center のアラートを開きます。 以下の[例](#alert-validate)のようなアラートが表示されます。

> [!NOTE]
> Windows のこのテスト アラートを確認する場合は、 **[Arguments Auditing Enabled]\(引数の監査が有効\)** フィールドが **[true]** であることを確認してください。 それが **[false]** である場合は、コマンド ライン引数の監査を有効にする必要があります。 これを有効にするには、次のコマンドを使用します。
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Linux VM 上でのアラートの検証 <a name="validate-linux"></a>

Security Center エージェントをコンピューターにインストールした後、アラートの攻撃対象リソースとして使用するコンピューターから次の手順を実行します。
1. 実行可能ファイルを任意の場所にコピーし、名前を **./asc_alerttest_662jfi039n** に変更します。例:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. コマンド プロンプトを開き、このファイルを実行します。

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 5 ～ 10 分待って、Security Center のアラートを開きます。 以下の[例](#alert-validate)のようなアラートが表示されます。

### アラートの例 <a name="alert-validate"></a>

![アラートの検証の例](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## Kubernetes 上でのアラートの検証 <a name="validate-kubernetes"></a>

Azure Kubernetes Service を統合する Security Center のプレビュー機能を使用している場合は、次の kubectl コマンドを実行して、アラートが動作していることをテストします。

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Azure Kubernetes Service と Azure Security Center の統合について詳しくは、[こちらの記事](azure-kubernetes-service-integration.md)をご覧ください。

## <a name="next-steps"></a>次のステップ
この記事では、アラートの検証プロセスについて説明しました。 この検証について理解できたら、次の記事をお読みください。

* [Azure Security Center でのセキュリティ アラートの管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) - Security Center で、アラートの管理とセキュリティ インシデントへの対応を行う方法について説明しています。
* 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」 - Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) - さまざまな種類のセキュリティ アラートについて説明しています。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide) - Security Center の一般的な問題をトラブルシューティングする方法について説明しています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/) - Azure のセキュリティとコンプライアンスに関するブログ記事を確認できます。
