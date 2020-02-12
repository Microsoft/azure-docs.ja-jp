---
title: 既存の Service Fabric クラスターでマネージド ID のサポートを構成する
description: 既存の Azure Service Fabric クラスターでマネージド ID のサポートを有効にする方法を次に示します
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: cb6e4ab00afd80cba41881e46296f7046a905919
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934945"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>既存の Service Fabric クラスターでマネージド ID のサポートを構成する (プレビュー)

Service Fabric アプリケーションで [Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用するには、まずクラスターで "*マネージド ID トークン サービス*" を有効にします。 このサービスは、マネージド ID を使用して Service Fabric アプリケーションの認証を実行し、アクセス トークンを代理で取得します。 サービスが有効になると、Service Fabric Explorer の左側のウィンドウの **[システム]** セクションに表示され、**fabric:/System/ManagedIdentityTokenService** という名前で実行されます。

> [!NOTE]
> **マネージド ID トークン サービス**を有効にするには、Service Fabric ランタイム バージョン 6.5.658.9590 以降が必要です。  
>
> クラスターの Service Fabric バージョンを調べるには、Azure portal からクラスター リソースを開き、 **[Essentials]\(基本\)** セクションで **[Service Fabric バージョン]** プロパティを確認します。
>
> クラスターが **[手動]** アップグレード モードの場合、最初に 6.5.658.9590 以降にアップグレードする必要があります。

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>既存のクラスターで "*マネージド ID トークン サービス*" を有効にする

既存のクラスターでマネージド ID トークン サービスを有効にするには、2 つの変更を指定してクラスターのアップグレードを開始する必要があります。(1) マネージド ID トークン サービスを有効化し、(2) 各ノードの再起動を要求します。 まず、クラスターの Azure Resource Manager テンプレートに次のスニペットを追加します。

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

また、変更を有効にするには、アップグレード ポリシーを変更し、クラスターでアップグレードが進行するのに合わせて各ノードで Service Fabric ランタイムを強制的に再起動するよう指定する必要があります。 この再起動により、新たに有効になったシステム サービスが各ノードで確実に開始および実行されます。 次のスニペットでは、`forceRestart` が必須の設定です。残りの設定には既存の値を使用します。  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> アップグレードが正常に完了したら、`forceRestart` の設定をロールバックすることを忘れないでください。これにより、以降のアップグレードの影響を最小限に抑えることができます。 

## <a name="errors-and-troubleshooting"></a>エラーとトラブルシューティング

デプロイが失敗し、次のメッセージが表示された場合は、十分に新しいバージョンの Service Fabric がクラスターで実行されていないことを意味します。

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>次のステップ
* [システム割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [ユーザー割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [サービス コードから Service Fabric アプリケーションのマネージド ID を活用する](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric アプリケーションに他の Azure リソースへのアクセス権を付与する](./how-to-grant-access-other-resources.md)