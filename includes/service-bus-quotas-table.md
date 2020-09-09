---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 07/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d22b0100074a230451e5c6b3967fa5dbc8ae3f56
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515923"
---
次の表に、Azure Service Bus メッセージングに固有のクォータ情報を示します。 Service Bus の価格と他のクォータについては、「[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」をご覧ください。

| クォータ名 | Scope | Notes | 値 |
| --- | --- | --- | --- |
| Azure サブスクリプションごとの Basic または Standard 名前空間の最大数 |名前空間 |追加の Basic または Standard 名前空間に関する後続の要求は Azure portal で拒否されます。 |100|
| Azure サブスクリプションごとの Premium 名前空間の最大数 |名前空間 |追加の Premium 名前空間に関する後続の要求はポータルで拒否されます。 |100 |
| キューまたはトピックのサイズ |Entity |キューまたはトピックの作成時に定義されます。 <br/><br/> 後続の受信メッセージが拒否され、呼び出し元コードが例外を受け取ります。 |1、2、3、4、または 5 GB。<br /><br />Premium SKU と、[パーティション分割](/azure/service-bus-messaging/service-bus-partitioning)が有効な Standard SKU では、キューまたはトピックの最大サイズは 80 GB です。 |
| 名前空間のコンカレント接続数 |名前空間 |追加の接続に関する後続の要求は拒否され、呼び出し元コードが例外を受け取ります。 REST 操作は、TCP 同時接続数に加算されません。 |ネット メッセージング:1,000。<br /><br />AMQP:5,000。 |
| キュー、トピック、またはサブスクリプション エンティティの同時受信要求数 |Entity |後続の受信要求が拒否され、呼び出し元コードが例外を受け取ります。 このクォータは、1 つのトピックのすべてのサブスクリプションの同時受信操作の合計数に適用されます。 |5,000 |
| 名前空間あたりのトピック数またはキュー数 |名前空間 |以後、名前空間でのトピックまたはキューの新規作成要求が拒否されます。 その結果、([Azure portal][Azure portal] で構成されていれば) エラー メッセージが生成されます。 管理 API から呼び出された場合は、呼び出し元のコードが例外を受け取ります。 |Basic または Standard レベルでは 10,000。 名前空間のトピックとキューの合計数は、10,000 以下にする必要があります。 <br/><br/>Premium レベルでは、メッセージング ユニット (MU) あたり 1,000 です。 上限は 4,000 です。 |
| 名前空間あたりの[パーティション分割されたトピックまたはキュー](/azure/service-bus-messaging/service-bus-partitioning)の数 |名前空間 |以後、名前空間でのパーティション分割されたトピックまたはキューの新規作成要求が拒否されます。 その結果、([Azure portal][Azure portal] で構成されていれば) エラー メッセージが生成されます。 管理 API から呼び出された場合、呼び出し元コードが **QuotaExceededException** 例外を受け取ります。 |Basic レベルと Standard レベル: 100。<br/><br/>パーティション分割されたエンティティは、[Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) レベルではサポートされていません。<br/><br />パーティション分割された各キューまたはトピックは、名前空間あたり 1,000 エンティティのクォータに加算されます。 |
| メッセージング エンティティのパスの最大サイズ: キューまたはトピック |Entity |- |260 文字。 |
| メッセージング エンティティ名の最大サイズ: 名前空間、サブスクリプション、またはサブスクリプション規則 |Entity |- |50 文字。 |
| [メッセージ ID](/dotnet/api/microsoft.azure.servicebus.message.messageid) の最大サイズ | Entity |- | 128 |
| メッセージ [セッション ID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) の最大サイズ | Entity |- | 128 |
| キュー、トピック、またはサブスクリプション エンティティのメッセージ サイズ |Entity |これらのクォータを超える受信メッセージは拒否され、呼び出し元コードが例外を受け取ります。 |メッセージの最大サイズ:[Standard レベル](../articles/service-bus-messaging/service-bus-premium-messaging.md)では 256 KB、[Premium レベル](../articles/service-bus-messaging/service-bus-premium-messaging.md)では 1 MB。 <br /><br />システムのオーバーヘッドのため、この制限はこれらの値よりも小さくなります。<br /><br />ヘッダーの最大サイズ:64 KB。<br /><br />プロパティ バッグ内のヘッダー プロパティの最大数: **バイト/int.MaxValue**。<br /><br />プロパティ バッグ内のプロパティの最大サイズ:明示的な制限はありません。 ヘッダーの最大サイズによって制限されます。 |
| キュー、トピック、またはサブスクリプション エンティティのメッセージ プロパティ サイズ |Entity | 例外 `SerializationException` が生成されます。 |各プロパティの最大メッセージ プロパティ サイズは 32,000 です。 すべてのプロパティの合計サイズが 64,000 を超えることはできません。 この制限は、[Brokered Message](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) のヘッダー全体に適用されます。このヘッダーには、ユーザー プロパティとシステム プロパティ ([Sequence Number](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber)、[Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)、[Message ID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid) など) の両方が含まれます。 |
| トピックごとのサブスクリプション数 |Entity |以後、そのトピックに対するサブスクリプションの新規作成要求は拒否されます。 その結果、ポータルで構成されている場合は、エラー メッセージが表示されます。 管理 API から呼び出された場合は、呼び出し元のコードが例外を受け取ります。 |Standard レベルでは、トピックあたり 2,000。 |
| トピックごとの SQL フィルターの数 |Entity |そのトピックに追加のフィルターを作成するための後続の要求は拒否され、呼び出し元コードが例外を受け取ります。 |2,000 |
| トピックごとの関連付けフィルターの数 |Entity |そのトピックに追加のフィルターを作成するための後続の要求は拒否され、呼び出し元コードが例外を受け取ります。 |100,000 |
| SQL フィルターまたはアクションのサイズ |名前空間 |追加のフィルターを作成するための後続の要求は拒否され、呼び出し元コードが例外を受け取ります。 |フィルター条件文字列の最大長:1,024 (1 K)。<br /><br />規則アクション文字列の最大長:1,024 (1 K)。<br /><br />規則アクションごとの式の最大数:32. |
| 名前空間、キュー、トピックごとの [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 規則の数 |エンティティ、名前空間 |追加の規則を作成するための後続の要求は拒否され、呼び出し元コードが例外を受け取ります。 |エンティティ型ごとの規則の最大数:12. <br /><br /> Service Bus 名前空間に構成されている規則は、キューやトピックのすべての型に適用されます。 |
| トランザクションあたりのメッセージ数 | トランザクション | それ以上の受信メッセージは拒否され、呼び出し元コードが "1 回のトランザクションで 100 個を超えるメッセージを送信することができない" ことを示す例外を受け取ります。 | 100 <br /><br /> **Send()** 操作および **SendAsync()** 操作両方に対して。 |
| 仮想ネットワークと IP フィルター規則の数 | 名前空間 | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
