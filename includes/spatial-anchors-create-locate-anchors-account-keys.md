---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 7210b05566f5cd6f3c56792bce0904b3c9b645da
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993079"
---
## <a name="set-up-authentication"></a>認証の設定

サービスにアクセスするには、アカウント キー、アクセス トークン、または Azure Active Directory 認証トークンを提供する必要があります。 この詳細については、[認証の概念に関するページ](../articles/spatial-anchors/concepts/authentication.md)も参照してください。

### <a name="account-keys"></a>アカウント キー

アカウント キーは、アプリケーションが Azure Spatial Anchors サービスで認証できるようにするための資格情報です。 アカウント キーの使用目的は、すぐに開始できるようにサポートすることです。 特にアプリケーションの Azure Spatial Anchors との統合の開発フェーズ時などがこれに該当します。 そのようなものとして、開発中にクライアント アプリケーションにアカウント キーを埋め込んで使用できます。 開発の先のフェーズに進むときには、実稼働レベルであるか、アクセス トークンによりサポートされるか、または Azure Active Directory ベースのユーザー認証である認証メカニズムに移行することを強く推奨します。 開発のためにアカウント キーを取得するには、Azure Spatial Anchors アカウントにアクセスし、[キー] タブに移動します。