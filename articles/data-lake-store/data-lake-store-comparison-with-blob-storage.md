---
title: Azure Data Lake Storage Gen1 と Blob Storage の比較
description: Azure Data Lake Storage Gen1 と Azure Blob Storage の違いの概要を示します。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: f9a03b5636af4a60c4abf563e073e22c970b8a02
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87921779"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Azure Data Lake Storage Gen1 と Azure Blob Storage の比較

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

この記事の表には、ビッグ データ処理の重要な側面に沿った Azure Data Lake Storage Gen1 と Azure Blob Storage の違いがまとめられています。 Azure BLOB ストレージは、さまざまなストレージ シナリオ向けに設計されたスケーラブルな汎用オブジェクト ストアです。 Azure Data Lake Storage Gen1 は、ビッグ データ分析ワークロードに最適化されたハイパースケール リポジトリです。

| カテゴリ | Azure Data Lake Storage Gen1 | Azure Blob Storage |
| -------- | ---------------------------- | ------------------ |
| 目的 |ビッグ データ分析ワークロードに最適化されたストレージ |さまざまなストレージ シナリオ (ビッグ データ分析を含む) のための汎用オブジェクト ストア |
| 例 |バッチ、対話型、ストリーミング分析、および機械学習データ (ログ ファイル、IoT データ、クリック ストリーム、大規模なデータセットなど) |あらゆる種類のテキストまたはバイナリ データ (アプリケーション バックエンド、バックアップ データ、ストリーミング用のメディア ストレージ、汎用データなど)。 さらに、分析ワークロードに対するフル サポート。バッチ、対話型、ストリーミング分析、および機械学習データ (ログ ファイル、IoT データ、クリック ストリーム、大規模なデータセットなど) |
| 主要概念 |Data Lake Storage Gen1 アカウントにはフォルダーが含まれ、フォルダーにはファイルとして保存されたデータが含まれます。 |ストレージ アカウントにはコンテナーが含まれ、コンテナーには BLOB の形でデータが含まれます。 |
| 構造体 |階層型ファイル システム |フラットな名前空間を使用するオブジェクト ストア |
| API |HTTPS 経由の REST API |HTTP/HTTPS 経由の REST API |
| サーバー側 API |[WebHDFS 互換の REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Azure BLOB ストレージ REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Hadoop ファイル システム クライアント |はい |はい |
| データ操作 - 認証 |[Azure Active Directory ID](../active-directory/develop/authentication-scenarios.md) |共有シークレット ([アカウント アクセス キー](../storage/common/storage-account-keys-manage.md)と [Shared Access Signature](../storage/common/storage-dotnet-shared-access-signature-part-1.md) キー) に基づきます。 |
| データ操作 - 認証プロトコル |OAuth 2.0。 呼び出しには、Azure Active Directory によって発行された有効な JWT (JSON Web トークン) が含まれている必要があります。 |ハッシュベース メッセージ認証コード (HMAC)。 呼び出しには、HTTP 要求の一部に対する Base64 でエンコードされた SHA-256 ハッシュが含まれている必要があります。 |
| データ操作 - 承認 |POSIX アクセス制御リスト (ACL)。  Azure Active Directory ID に基づく ACL は、ファイルおよびフォルダー レベルで設定できます。 |アカウントレベルの承認には、[アカウント アクセス キー](../storage/common/storage-account-keys-manage.md)を使用します<br>アカウント、コンテナー、または BLOB の承認には、[Shared Access Signature キー](../storage/common/storage-dotnet-shared-access-signature-part-1.md)を使用します |
| データ操作 - 監査 |使用可能。 詳細については、 [こちら](data-lake-store-diagnostic-logs.md) をご覧ください。 |利用可能 |
| 保存データの暗号化 |<ul><li>透過的、サーバー側</li> <ul><li>サービスによって管理されるキーを使用</li><li>ユーザーによって Azure KeyVault で管理されるキーを使用</li></ul></ul> |<ul><li>透過的、サーバー側</li> <ul><li>サービスによって管理されるキーを使用</li><li>ユーザーによって Azure KeyVault で管理されるキーを使用 (プレビュー)</li></ul><li>クライアント側暗号化</li></ul> |
| 管理操作 (アカウントの作成など) |アカウント管理用の [Azure ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) |アカウント管理用の [Azure ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) |
| Developer SDK |.NET、Java、Python、Node.js |.NET、Java、Python、Node.js、C++、Ruby、PHP、Go、Android、iOS |
| 分析ワークロードのパフォーマンス |並列分析ワークロードに最適化されたパフォーマンス。 高スループットおよび高 IOPS。 |並列分析ワークロードに最適化されたパフォーマンス。 |
| サイズ制限 |アカウント サイズ、ファイル サイズ、ファイル数に制限はありません。 |特定の制限事項については、[Standard ストレージ アカウントのスケーラビリティ ターゲット](../storage/common/scalability-targets-standard-account.md)に関する記事と「[BLOB ストレージのスケーラビリティとパフォーマンス ターゲット](../storage/blobs/scalability-targets.md)」を参照してください。 より大きなアカウント制限については [Azure サポート](https://azure.microsoft.com/support/faq/)への問い合わせで入手可能 |
| geo 冗長 |ローカル冗長 (1 つの Azure リージョンにデータの複数のコピー) |ローカル冗長 (LRS)、ゾーン冗長 (ZRS)、geo 冗長 (GRS)、読み取りアクセス geo 冗長 (RA-GRS)。 詳細については、 [こちら](../storage/common/storage-redundancy.md) をご覧ください。 |
| サービスの状態 |一般公開 |一般公開 |
| リージョン別の提供状況 |詳細については、 [こちら](https://azure.microsoft.com/regions/#services) |すべての Azure リージョンで使用可能 |
| Price |詳細については、 [価格](https://azure.microsoft.com/pricing/details/data-lake-store/) |詳細については、 [価格](https://azure.microsoft.com/pricing/details/storage/) |
