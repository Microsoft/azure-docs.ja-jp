---
title: Azure Key Vault 証明書での SSL 終端
description: HTTPS 対応リスナーにアタッチされているサーバー証明書の Key Vault と Azure Application Gateway を統合する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 5633dd7b72f4de22cd34b7d093e8ec4d9cb411f1
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137699"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Key Vault 証明書での SSL 終端

[Azure Key Vault](../key-vault/key-vault-overview.md) はプラットフォームマネージド シークレット ストアです。シークレット、キー、SSL 証明書を保護するために使用できます。 Azure Application Gateway では、HTTPS 対応リスナーにアタッチされているサーバー証明書用の Key Vault との統合をサポートします。 このサポートは、Application Gateway の v2 SKU に制限されます。

Key Vault 統合では、SSL 終端に次の 2 つのモデルが提供されています。

- リスナーにアタッチされている SSL 証明書を明示的に提供できます。 このモデルは、SSL 終端のために SSL 証明書を Application Gateway に渡す従来の方法を示しています。
- HTTPS 対応リスナーの作成時に、必要に応じて、既存の Key Vault 証明書またはシークレットへの参照を指定できます。

Application Gateway と Key Vault の統合には、次のようにさまざまな利点があります。

- セキュリティの強化 (SSL 証明書がアプリケーション開発チームによって直接処理されないため)。 統合により、別のセキュリティ チームが以下のこと実行できます。
  * アプリケーション ゲートウェイを設定する。
  * アプリケーション ゲートウェイのライフ サイクルを制御する。
  * ご使用のキー コンテナーに格納されている証明書にアクセスする権限を特定のアプリケーション ゲートウェイに付与する。
- ご使用のキー コンテナーに既存の証明書をインポートできます。 また、任意の信頼できる Key Vault パートナーと共に Key Vault API を使用して、新しい証明書を作成したり管理したりできます。
- ご使用のキー コンテナーに格納されている証明書を自動更新できます。

Application Gateway で現在サポートされているのは、ソフトウェアで検証された証明書のみです。 ハードウェア セキュリティ モジュール (HSM) で検証された証明書はサポートされません。 Key Vault 証明書を使用するように Application Gateway を構成すると、そのインスタンスは Key Vault から証明書を取得し、SSL 終了のためにその証明書をローカルにインストールします。 また、インスタンスは Key Vault を 24 時間間隔でポーリングし、証明書の更新バージョン (存在する場合) を取得します。 更新された証明書が検出されると、HTTPS リスナーに現在関連付けられている SSL 証明書が自動的にローテーションされます。

> [!NOTE]
> Azure portal では、シークレットではなく KeyVault 証明書のみがサポートされます。 Application Gateway は引き続き KeyVault からのシークレットの参照をサポートしていますが、PowerShell、CLI、API、ARM テンプレートなどの非ポータル リソースを通じてのみとなります。 

## <a name="how-integration-works"></a>統合のしくみ

Application Gateway と Key Vault との統合には、3 つの手順の構成プロセスが必要です。

1. **ユーザー割り当てマネージド ID を作成する**

   Application Gateway がユーザーに代わって Key Vault から証明書を取得するために使用する、ユーザー割り当てマネージド ID を作成するか、既存の ID を使用します。 詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。 この手順では、Azure Active Directory テナントに新しい ID を作成します。 この ID は、ID を作成するために使用されたサブスクリプションによって信頼されます。

1. **キー コンテナーの構成**

   次に、既存の証明書をインポートするか、キー コンテナーで新しい証明書を作成します。 証明書は、アプリケーション ゲートウェイを介して実行されるアプリケーションによって使用されます。 この手順では、パスワードのない Base 64 でエンコードされた PFX ファイルとして保存されているキー コンテナー シークレットを使用することもできます。 キー コンテナーでは証明書型のオブジェクトに自動更新機能を使用できるため、証明書型を使用することをお勧めします。 証明書またはシークレットを作成したら、キー コンテナーでアクセス ポリシーを定義し、その ID にシークレットの "*取得*" アクセス権が付与されるようにする必要があります。

1. **アプリケーション ゲートウェイの構成**

   前の 2 つの手順を完了したら、ユーザー割り当てマネージド ID を使用するようにプリケーション ゲートウェイを設定するか、既存のアプリケーション ゲートウェイを変更することができます。 また、キー コンテナーの証明書またはシークレットの ID の完全な URI をポイントするように、HTTP リスナーの SSL 証明書を構成します。

   ![キー コンテナー証明書](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>次のステップ

[Azure PowerShell で Key Vault 証明書を使用して SSL 終了を構成する](configure-keyvault-ps.md)
