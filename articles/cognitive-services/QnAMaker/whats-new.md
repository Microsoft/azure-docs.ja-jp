---
title: QnA Maker サービスの新機能
titleSuffix: Azure Cognitive Services
description: この記事には、QnA Maker に関するニュースが含まれています。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 07/16/2020
ms.openlocfilehash: 73044f35b90fd3e5996899fd7c3b0a925056f8ed
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836877"
---
# <a name="whats-new-in-qna-maker"></a>QnA Maker の新機能

サービス内の新機能について説明します。 以下の項目には、リリース ノート、ビデオ、ブログの投稿、およびその他の種類の情報が含まれる可能性があります。 このページをブックマークして、常にサービスの最新情報を確認してください。

## <a name="release-notes"></a>リリース ノート

QnA Maker の新機能について説明します。

### <a name="july-2020"></a>2020 年 7 月

* [メタデータ: `OR` による複数のメタデータ ペアの論理の組み合わせ](how-to/metadata-generateanswer-usage.md#logical-or-using-strictfilterscompoundoperationtype-property)
* Cognitive Search エンドポイントをプライベートにしたうえで引き続き QnA Maker にアクセスできるように構成する[手順](how-to/set-up-qnamaker-service-azure.md#configuring-cognitive-search-as-a-private-endpoint-inside-a-vnet)。
* Free レベルの Cognitive Search リソースは、[90 日間非アクティブな期間](how-to/set-up-qnamaker-service-azure.md#inactivity-policy-for-free-search-resources)が経過すると削除されます。

### <a name="june-2020"></a>2020 年 6 月

* すばやく簡単に手順に取り組めるよう、[Power Virtual Agent](tutorials/integrate-with-power-virtual-assistant-fallback-topic.md) のチュートリアルを更新

### <a name="may-2020"></a>2020 年 5 月

* [Azure ロールベースのアクセス制御 (Azure RBAC)](concepts/role-based-access-control.md)
* 回答の[リッチテキスト編集](how-to/edit-knowledge-base.md#rich-text-editing-for-answer)

### <a name="march-2020"></a>2020 年 3 月

* TLS 1.2 は現在、このサービスへのすべての HTTP 要求に適用されるようになりました。 詳細については、[Azure Cognitive Services のセキュリティ](../cognitive-services-security.md)に関するページを参照してください。

### <a name="february-2020"></a>2020 年 2 月

* GenerateAnswer API を使用した [NPM パッケージ](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)

### <a name="november-2019"></a>2019 年 11 月

* QnA Maker に対する[政府クラウドのサポート](https://docs.microsoft.com/azure/azure-government/documentation-government-services-aiandcognitiveservices#qna-maker)
* [複数ターン](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/multiturn-conversation)機能が一般提供になりました
* レベル 1 の言語で[おしゃべりのサポート](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/chit-chat-knowledge-base#language-support)が利用可能になりました

### <a name="october-2019"></a>2019 年 10 月

* QnA Maker サービスにおけるすべてのナレッジ ベースの[言語を明示的に設定](./how-to/language-knowledge-base.md#select-language-when-creating-first-knowledge-base)

### <a name="september-2019"></a>2019 年 9 月

* [XLS ファイル形式](concepts/content-types.md)を使用したインポートとエクスポート

### <a name="june-2019"></a>2019 年 6 月

* フランス語、イタリア語、ドイツ語、スペイン語、ポルトガル語向けの[ランカー モデル](concepts/query-knowledge-base.md#ranker-process)の改善

### <a name="april-2019"></a>2019 年 4 月

* Web サイト コンテンツ抽出のサポート
* 認証済みのアクセスからの [SharePoint ドキュメント](how-to/add-sharepoint-datasources.md)のサポート

### <a name="march-2019"></a>2019 年 3 月

* [アクティブ ラーニング](how-to/improve-knowledge-base.md)による、実際のユーザーの質問に基づく新しい質問の候補の提供
* 英語向けの自然言語処理 (NLP) [ランカー](concepts/query-knowledge-base.md#ranker-process) モデルの改善

> [!div class="nextstepaction"]
> [QnA Maker サービスを作成する](how-to/set-up-qnamaker-service-azure.md)

## <a name="cognitive-service-updates"></a>Cognitive Services の更新プログラム

[Cognitive Services に対する Azure 更新プログラムのお知らせ](https://azure.microsoft.com/updates/?product=cognitive-services)
