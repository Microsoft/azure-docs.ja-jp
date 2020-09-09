---
title: Azure Monitor での IIS ログ | Microsoft Docs
description: インターネット インフォメーション サービス (IIS) は、Azure Monitor が収集できるログ ファイル内にユーザー アクティビティを格納します。  この記事では、IIS ログの収集を構成する方法、および Azure Monitor で作成されるレコードの詳細について説明します。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 0bca809d6c25594c1c614f694e71e39a4f61e2a4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87008185"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Azure Monitor での IIS ログを収集する
インターネット インフォメーション サービス (IIS) は、Azure Monitor が収集して[ログ データ](data-platform.md)として格納できるログ ファイル内にユーザー アクティビティを格納します。

![IIS ログ](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>IIS ログの構成
Azure Monitor は IIS によって作成されたログ ファイルからエントリを収集するため、[ログを記録するように IIS を構成する](/previous-versions/orphan-topics/ws.11/hh831775(v=ws.11))必要があります。

Azure Monitor は W3C 形式で格納された IIS ログ ファイルのみをサポートし、カスタム フィールドや IIS 詳細ログはサポートしていません。 ログは NCSA または IIS のネイティブ形式では収集されません。

Azure Monitor での IIS ログは、[[詳細設定] メニュー](agent-data-sources.md#configuring-data-sources)から構成します。  必要な構成は、 **[Collect W3C format IIS log files]** (W3C 形式の IIS ログ ファイルを収集する) を選択することのみです。


## <a name="data-collection"></a>データ コレクション
Azure Monitor では、ログのタイムスタンプが変更されるたびに、各エージェントから IIS ログ エントリが収集されます。 ログは **5 分**ごとに読み取られます。 何らかの理由で、新しいファイルの作成時に IIS でロールオーバー時間の前にタイムスタンプが更新されない場合、新しいファイルの作成後にエントリが収集されます。 新しいファイル作成の頻度は、IIS サイトの **[Log File Rollover Schedule]\(ログ ファイルのロールオーバー スケジュール\)** 設定によって制御されます。この設定の既定値は 1 日に 1 回です。 設定が **[毎時]** である場合、Azure Monitor では 1 時間ごとにログが収集されます。 設定が **[毎日]** である場合、Azure Monitor では 24 時間ごとにログが収集されます。


## <a name="iis-log-record-properties"></a>IIS ログ レコードのプロパティ
IIS ログ レコードの型は **W3CIISLog** になり、次の表に示すプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| Computer |イベント収集元のコンピューターの名前。 |
| cIP |クライアントの IP アドレス。 |
| csMethod |要求のメソッド (GET、POST など) |
| csReferer |現在のサイトに到達するまでにユーザーがリンクをフォローした元のサイト |
| csUserAgent |クライアントのブラウザーの種類。 |
| csUserName |サーバーにアクセスした認証されたユーザーの名前。 匿名ユーザーはハイフンで示されます。 |
| csUriStem |要求のターゲット (Web ページなど) |
| csUriQuery |クライアントが実行を試行していたクエリ (存在する場合) |
| ManagementGroupName |Operations Manager エージェントの管理グループの名前。  その他のエージェントの場合、これは AOI-\<workspace ID\> です |
| RemoteIPCountry |クライアントの IP アドレスの国や地域。 |
| RemoteIPLatitude |クライアントの IP アドレスの緯度。 |
| RemoteIPLongitude |クライアントの IP アドレスの経度。 |
| scStatus |HTTP 状態コード。 |
| scSubStatus |副状態エラー コード。 |
| scWin32Status |Windows 状態コード。 |
| sIP |Web サーバーの IP アドレス。 |
| SourceSystem |OpsMgr |
| sPort |クライアントが接続されているサーバー上のポート。 |
| sSiteName |IIS サイトの名前。 |
| TimeGenerated |エントリがログに記録された日付と時刻。 |
| TimeTaken |要求の処理にかかった時間 (ミリ秒) |

## <a name="log-queries-with-iis-logs"></a>IIS ログでのログ クエリ
次の表は、IIS ログ レコードを取得するログ クエリのさまざまな例をまとめたものです。

| クエリ | 説明 |
|:--- |:--- |
| W3CIISLog |IIS ログのすべてのレコード。 |
| W3CIISLog &#124; where scStatus==500 |戻り状態が 500 であるすべての IIS ログ レコード。 |
| W3CIISLog &#124; summarize count() by cIP |クライアントの IP アドレス別の IIS ログ エントリの数。 |
| W3CIISLog &#124; where csHost=="www\.contoso.com" &#124; summarize count() by csUriStem |ホスト www\.contoso.com の URL 別の IIS ログ エントリの数。 |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |各 IIS コンピューターによって受信された合計バイト数。 |

## <a name="next-steps"></a>次のステップ
* 分析のために他の[データ ソース](agent-data-sources.md)を収集するように Azure Monitor を構成します。
* [ログ クエリ](../log-query/log-query-overview.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。
