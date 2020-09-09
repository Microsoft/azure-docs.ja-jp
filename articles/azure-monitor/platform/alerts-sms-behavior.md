---
title: アクション グループの SMS アラート動作
description: SMS メッセージの形式と登録解除、再登録、ヘルプ要求の SMS メッセージに応答する方法について説明します。
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.subservice: alerts
ms.openlocfilehash: ff3d055840d69623234a799c62b1d8a6efd96ec5
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327091"
---
# <a name="sms-alert-behavior-in-action-groups"></a>アクション グループの SMS アラート動作

## <a name="overview"></a>概要 
アクション グループを使用して、アクションのリストを構成できます。 これらのグループはアラートを定義するときに使用し、アラートがトリガーされたときに特定のアクション グループに通知が送信されるようにします。 サポートされているアクションの 1 つは SMS です。SMS 通知は双方向通信をサポートします。 ユーザーは SMS に次のように対応できます。

- **アラートの登録を解除する:** ユーザーはすべてのアクション グループまたは単一のアクション グループのすべての SMS アラートの登録を解除できます。
- **アラートに再登録する:** ユーザーはすべてのアクション グループまたは単一のアクション グループのすべての SMS アラートに再登録できます。  
- **サポートを要求する:** ユーザーは SMS で追加情報を問い合わせることができます。 ユーザーはこの記事にリダイレクトされます。

この記事は、ユーザーの次のロケールに基づいて、SMS アラートの動作とユーザーが実行できる応答アクションについて説明します。

## <a name="receiving-an-sms-alert"></a>SMS アラートを受信する
アクション グループの一部として構成されている SMS の受信者は、アラートがトリガーされたときに SMS を受け取ります。 SMS には、次の情報が含まれています。
* このアラートの送信先のアクション グループの短い名前
* アラートのタイトル

| 応答 | 説明 |
| ----- | ----------- |
| DISABLE `<Action Group Short name>` | そのアクション グループからのそれ以上の SMS を無効にします |
| ENABLE `<Action Group Short name>` | そのアクション グループからの SMS を再度有効にします |
| STOP | すべてのアクション グループからのそれ以上の SMS を無効にします |
| START | すべてのアクション グループからの SMS を再度有効にします |
| HELP | この記事へのリンクが記載された応答がユーザーに送信されます。 |

>[!NOTE]
>SMS アラートの登録を解除したユーザーが新しいアクション グループに追加されると、その新しいアクション グループの SMS アラートを受け取りますが、以前のすべてのアクション グループの登録は解除されたままになります。

## <a name="next-steps"></a>次の手順
[アクティビティ ログ アラートの概要](alerts-overview.md)を把握し、アラートを生成する方法について学習してください  
[SMS レートの制限](alerts-rate-limiting.md)について学習します  
[アクション グループ](./action-groups.md)の詳細を確認する

