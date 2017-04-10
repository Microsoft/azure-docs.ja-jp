---
title: "VM のネットワーク スループットのテスト | Microsoft Docs"
description: "Azure 仮想マシンのネットワーク スループットをテストする方法について説明します。"
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: steveesp
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: d05bed3b92836bf496804c9d40b5a62a96ffbc3d
ms.lasthandoff: 03/06/2017


---

# <a name="bandwidththroughput-testing-ntttcp"></a>帯域幅/スループットのテスト (NTTTCP)

ネットワーク スループットのパフォーマンスを Azure でテストするための最善の方法は、テストするネットワークをターゲットとするツールを使用して、パフォーマンスに影響する可能性があるその他のリソースの使用を最小限に抑えることです。 NTTTCP を使用することをお勧めします。

このツールを、同じサイズの&2; つの Azure VM にコピーします。 片方の VM は送信側として、他方は受信側として機能します。

#### <a name="deploying-vms-for-testing"></a>テスト用の VM のデプロイ
このテストの目的を達成するには、2 つの VM が同じクラウド サービス内にあるか同じ可用性セット内に含まれている必要があります。これは、内部 IP アドレスの使用と、ロード バランサーのテストからの除外を行えるようにするためです。 VIP を使用してテストすることは可能ですが、この種類のテストはこのドキュメントの範囲外です。
 
受信側の IP アドレスをメモしておきます。 その IP を "a.b.c.r" と呼びましょう。

VM 上のコアの数をメモしておきます。 これを "\#num\_cores" と呼びましょう。
 
送信側 VM と 受信側 VM で、NTTTCP テストを 300 秒間 (5 分間) 実行します。

ヒント: このテストを初めて設定するときは、フィードバックをすぐに取得するために、もっと短い時間でテストを実行できます。 ツールが想定どおりに動作したら、最も正確な結果を得るために、テスト時間を 300 秒に延長します。

> [!NOTE]
> **送信側と受信側**には、**同じ**テスト時間パラメーター (-t) を指定する必要があります。

1 つの TCP ストリームを10 秒間テストするには:

受信側パラメーター: ntttcp -r -t 10 -P 1

送信側パラメーター: ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> 上記のサンプルは、構成を確認する場合にのみ使用する必要があります。 テストの有効な例については、このドキュメントの後半で説明します。

## <a name="testing-vms-running-windows"></a>Windows を実行している VM のテスト:

#### <a name="get-ntttcp-onto-the-vms"></a>VM 上に NTTTCP を取得する

<https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769> から最新バージョンをダウンロードします。

移動している場合は、<https://www.bing.com/search?q=ntttcp+download>\< で検索します。1 番目のの検索結果として表示されるはずです。

NTTTCP は、独立したフォルダー (C:\\tools など) に配置することを検討してください。

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>NTTTCP が Windows ファイアウォールを通過することを許可する
受信側で、Windows ファイアウォールの許可ルールを作成して、NTTTCP トラフィックの到着を許可します。 特定の TCP ポートの受信を許可するのではなく、名前によって NTTTCP プログラム全体を許可するほうが簡単です。

次のように、NTTTCP が Windows ファイアウォールを通過することを許可します。

netsh advfirewall firewall add rule program=\<パス\>\\ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

たとえば、ntttcp.exe を "C:\\tools" フォルダーにコピーした場合、このコマンドは次のようになります。 

netsh advfirewall firewall add rule program=c:\\tools\\ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

#### <a name="running-ntttcp-tests"></a>NTTTCP テストの実行

受信側で NTTTCP を開始します (PowerShell からではなく **CMD から実行**します)。

ntttcp -r –m [2\*\#num\_cores],\*,a.b.c.r -t 300

VM に 4 つのコアと IP アドレス 10.0.0.4 がある場合、コードは次のようになります。

ntttcp -r –m 8,\*,10.0.0.4 -t 300


送信側で NTTTCP を開始します (PowerShell からではなく **CMD から実行**します)。

ntttcp -s –m 8,\*,10.0.0.4 -t 300 

結果を待ちます。


## <a name="testing-vms-running-linux"></a>LINUX を実行している VM のテスト:

nttcp-for-linux を使用します。 これは、<https://github.com/Microsoft/ntttcp-for-linux> から入手できます。

Linux VM (送信側と受信側の両方) で、次のコマンドを実行して、VM 上に ntttcp-for-linux を準備します。

CentOS - Install Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Install Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
両方で作成とインストールを行います。
``` bash
 git clone <https://github.com/Microsoft/ntttcp-for-linux>
 cd ntttcp-for-linux/src
 make && make install
```

Windows の例のように、Linux の受信側 IP は 10.0.0.4 とします。

受信側で、NTTTCP-for-Linux を起動します。

``` bash
ntttcp -r -t 300
```

送信側で、次のコマンドを実行します。

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
時間パラメーターが指定されない場合、テストの長さは既定値の 60 秒になります。

## <a name="next-steps"></a>次のステップ
* 結果によっては、シナリオに適合するように[マシンのネットワーク スループットの最適化](virtual-network-optimize-network-bandwidth.md)を実行できる余地がある場合があります。
* 「[Azure Virtual Network についてよく寄せられる質問 (FAQ)](virtual-networks-faq.md)」を参照します。

