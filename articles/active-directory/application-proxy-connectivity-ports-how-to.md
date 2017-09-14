---
title: "アプリケーション プロキシ アプリケーションに必要なファイアウォール ポートを開く方法 | Microsoft Docs"
description: "Azure AD アプリケーション プロキシを正しく動作させるために開くポートを確認します"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 4cbffd6627bccc96400ec6de103d4d7fc5d77950
ms.contentlocale: ja-jp
ms.lasthandoff: 04/18/2017

---

# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>アプリケーション プロキシ アプリケーションに必要なファイアウォール ポートを開く方法

必要なポートと各ポートの機能を網羅したリストについては、[アプリケーション プロキシに関するドキュメント](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable)の前提条件のセクションを参照してください。 アプリケーション プロキシでは送信ポートのみを使用する点に注意してください。

オンプレミスのネットワークから[コネクタ ポート テスト ツール](https://aadap-portcheck.connectorporttest.msappproxy.net/)を開き、必要なすべてのポートが開いているかどうかを確認することもできます。 緑色のチェックマークが多いほど、回復性が高いことになります。 

## <a name="app-proxy-regions"></a>アプリ プロキシのリージョン

お客様にとってどのリージョンに緑色のチェックマークが必要かという点については、今後お伝えできるように準備を進めています。 当面は、すべてのリージョンにチェックマークが付いていることを確認してください。 お住まいのリージョンとは無関係に、米国中部も必要なリージョンとなります。

ツールで正しい結果が得られるようにするには、以下の点に注意してください。

-   コネクタをインストールしたサーバーからブラウザーでツールを開く。

-   コネクタに適用されるプロキシまたはファイアウォールが、このページにも適用されるようにする。 これを設定するため、Internet Explorer で、**[設定]** -&gt; **[インターネット オプション]** -&gt; **[接続]** -&gt; **[LAN の設定]** の順に進みます。 このページに、[LAN にプロキシ サーバーを使用する] フィールドがあります。 このボックスをオンにし、[アドレス] フィールドにプロキシ アドレスを入力します。

## <a name="next-steps"></a>次のステップ
[Azure AD アプリケーション プロキシ コネクタについて](application-proxy-understand-connectors.md)

