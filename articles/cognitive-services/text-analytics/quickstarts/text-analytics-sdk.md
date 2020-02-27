---
title: クイック スタート:Text Analytics クライアント ライブラリ v3 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: このクイックスタートを使用して、アプリケーションを Azure Cognitive Services の Text Analytics API に接続してみましょう。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/24/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: c6883b9b79adfc4f91af354147d45ea27d806d78
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614487"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>クイック スタート:Text Analytics クライアント ライブラリを使用する

Text Analytics クライアント ライブラリの概要を紹介します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。

Text Analytics クライアント ライブラリを使って次のことを実行します。

* センチメント分析
* 言語検出
* エンティティの認識
* キー フレーズの抽出

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Text Analytics API の最新のプレビュー バージョンは `3.0-preview` です。これには、強化された[感情分析](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)および[名前付きエンティティの認識 (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) のパブリック プレビューが含まれています。 最新の安定バージョンは `2.1` です。
>    * 必ず、使用するバージョンの手順にのみ従ってください。
> * この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 運用環境のシナリオでは、パフォーマンスとスケーラビリティを確保するために、バッチ処理された非同期メソッドを使用することをお勧めします。 以下のリファレンス ドキュメントを参照してください。

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * このクイックスタートは、Text Analytics クライアント ライブラリのバージョン `3.0-preview` 専用です。これには、強化された[感情分析](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)および[名前付きエンティティの認識 (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) のパブリック プレビューが含まれています。
>    * 必ず、使用するバージョンの手順にのみ従ってください。
> * この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 運用環境のシナリオでは、パフォーマンスとスケーラビリティを確保するために、バッチ処理された非同期メソッドを使用することをお勧めします。 以下のリファレンス ドキュメントを参照してください。

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Text Analytics API の最新のプレビュー バージョンは `3.0-preview` です。これには、強化された[感情分析](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)および[名前付きエンティティの認識 (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) のパブリック プレビューが含まれています。 最新の安定バージョンは `2.1` です。
>    * 必ず、使用するバージョンの手順にのみ従ってください。
> * この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 運用環境のシナリオでは、パフォーマンスとスケーラビリティを確保するために、バッチ処理された非同期メソッドを使用することをお勧めします。 以下のリファレンス ドキュメントを参照してください。

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Text Analytics API の最新のプレビュー バージョンは `3.0-preview` です。これには、強化された[感情分析](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)および[名前付きエンティティの認識 (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) のパブリック プレビューが含まれています。 最新の安定バージョンは `2.1` です。
>    * 必ず、使用するバージョンの手順にのみ従ってください。
> * この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 運用環境のシナリオでは、パフォーマンスとスケーラビリティを確保するために、バッチ処理された非同期メソッドを使用することをお勧めします。 以下のリファレンス ドキュメントを参照してください。 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>言語サポートの追加

このタブをクリックした場合、お気に入りのプログラミング言語でクイックスタートが表示されないことがあります。 別途クイックスタートを用意していますので、ご安心ください。 この表を使用して、お使いのプログラミング言語に適したサンプルを見つけます。

| Language | 利用可能なバージョン | 
|----------|------------------------|
| Ruby     | [バージョン 2.1](ruby-sdk.md) | 
| Go       | [バージョン 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ソリューションを探す](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Text Analytics の概要](../overview.md)
* [感情分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [エンティティの認識](../how-tos/text-analytics-how-to-entity-linking.md)
* [言語を検出する](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [言語の認識](../how-tos/text-analytics-how-to-language-detection.md)
