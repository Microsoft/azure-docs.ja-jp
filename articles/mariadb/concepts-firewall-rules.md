---
title: ファイアウォール規則 - Azure Database for MariaDB
description: ファイアウォール規則を使用して、ご利用の Azure Database for MariaDB サーバーへの接続を有効にする方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/17/2020
ms.openlocfilehash: 4b5898629c373e31d94ad09ca4af66de0428a7a2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87047616"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Azure Database for MariaDB サーバーのファイアウォール規則
ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてサーバーへのアクセス権を付与します。

ファイアウォールを構成するには、受け入れ可能な IP アドレスの範囲を指定するファイアウォール規則を作成します。 ファイアウォール規則はサーバー レベルで作成できます。

**ファイアウォール規則**:この規則により、クライアントは、Azure Database for MariaDB サーバー全体、つまり、同じ論理サーバー内のすべてのデータベースにアクセスできるようになります。 サーバー レベルのファイアウォール規則を構成するには、Azure Portal または Azure CLI コマンドを使用します。 サーバー レベルのファイアウォール規則を作成するには、サブスクリプション所有者またはサブスクリプション共同作成者である必要があります。

## <a name="firewall-overview"></a>ファイアウォールの概要
既定では、Azure Database for MariaDB サーバーへのすべてのデータベース アクセスが、ファイアウォールによってブロックされます。 他のコンピューターからサーバーの使用を開始するには、サーバー レベルのファイアウォール規則を 1 つ以上指定して、サーバーへのアクセスを有効にする必要があります。 ファイアウォール規則を使用して、インターネットからのアクセスを許可する IP アドレス範囲を指定します。 Azure Portal Web サイト自体へのアクセスは、ファイアウォール規則の影響は受けません。

インターネットおよび Azure からの接続の試行は、次の図に示されるように、Azure Database for MariaDB データベースに到達する前にファイアウォールを通過する必要があります。

![ファイアウォールのしくみを示すサンプル フロー](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>インターネットからの接続
サーバーレベルのファイアウォール規則は、Azure Database for MariaDB サーバー上のすべてのデータベースに適用されます。

要求の IP アドレスがサーバーレベルのファイアウォール規則で指定されたいずれかの IP アドレス範囲内にある場合は、接続が許可されます。

要求の IP アドレスがデータベース レベルのファイアウォール規則またはサーバー レベルのファイアウォール規則で指定された IP アドレス範囲外にある場合、接続要求は失敗します。

## <a name="connecting-from-azure"></a>Azure からの接続
任意のアプリケーションまたはサービスの発信 IP アドレスを見つけ、これらの個々の IP アドレスまたは範囲へのアクセスを明示的に許可することをお勧めします。 たとえば、Azure App Service の発信 IP アドレスを見つけることも、仮想マシンまたはその他のリソースに関連付けられているパブリック IP を使用することもできます (サービス エンドポイントを介した仮想マシンのプライベート IP との接続の詳細については、以下を参照してください)。 

ご使用の Azure サービスに対して固定の発信 IP アドレスが使用できない場合は、すべての Azure データセンターの IP アドレスからの接続を有効にすることを検討できます。 この設定は、Azure portal から **[接続のセキュリティ]** ウィンドウで、 **[Azure サービスへのアクセス許可]** オプションを **[オン]** にし、 **[保存]** を押すことで設定できます。 Azure CLI からは、ファイアウォール規則の設定で開始アドレスと終了アドレスを 0.0.0.0 にすることで同じことができます。 接続試行が許可されていない場合、この要求は Azure Database for MariaDB サーバーに到達しません。

> [!IMPORTANT]
> **[Azure サービスへのアクセス許可]** オプションでは、他のお客様のサブスクリプションからの接続を含む、Azure からのすべての接続を許可するようにファイアウォールが構成されます。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。
> 

![ポータルで [Azure サービスへのアクセスを許可] を構成する](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>VNet からの接続
VNet から Azure Database for MariaDB サーバーに安全に接続するには、[VNet サービス エンドポイント](./concepts-data-access-security-vnet.md)の使用を検討してください。 

## <a name="programmatically-managing-firewall-rules"></a>ファイアウォール規則のプログラムによる管理
ファイアウォール規則は、Azure Portal に加え、Azure CLI を使用してプログラムで管理することができます。 

「[Azure CLI を使用した Azure Database for MariaDB ファイアウォール規則の作成と管理](./howto-manage-firewall-cli.md)」も参照してください。

## <a name="troubleshooting-firewall-issues"></a>ファイアウォールの問題のトラブルシューティング
Microsoft Azure Database for MariaDB サーバー サービスに期待どおりにアクセスできない場合は、次の点を検討してください。

* **許可一覧に変更が反映されない:** Azure Database for MariaDB サーバー ファイアウォールの構成に対する変更が反映されるまで最大 5 分の遅延が発生する場合があります。

* **ログインが許可されない、または正しくないパスワードが使用された:** Azure Database for MariaDB サーバーでは、ログインのアクセス許可がないか、使用したパスワードが正しくない場合、Azure Database for MariaDB サーバーへの接続は拒否されます。 ファイアウォール設定の作成は、クライアントに対し、サーバーへの接続を試行する機会のみを提供します。それぞれのクライアントは、必要なセキュリティ資格情報を提供する必要があります。

* **動的 IP アドレス:** 動的 IP アドレス指定によるインターネット接続を使用しており、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。

   * Azure Database for MariaDB サーバーにアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲について、インターネット サービス プロバイダー (ISP) に問い合わせ、ファイアウォール規則として、IP アドレス範囲を追加してください。

   * 動的 IP アドレスの代わりに、静的 IP アドレスを取得し、ファイアウォール規則として、IP アドレス範囲を追加してください。

* **サーバーの IP がパブリックのように見える:** Azure Database for MariaDB サーバーへの接続は、パブリックにアクセス可能な Azure ゲートウェイ経由でルーティングされます。 しかし、実際のサーバー IP は、ファイアウォールによって保護されています。 詳細については、[接続アーキテクチャに関する記事](concepts-connectivity-architecture.md)をご覧ください。 

* **許可されている IP を使用して Azure リソースから接続できない:** 接続元のサブネットに対して、**Microsoft.Sql** サービス エンドポイントが有効になっているかどうかを確認します。 サブネットで **Microsoft.Sql** が有効になっている場合は、そのサブネットで [VNet サービス エンドポイント規則](concepts-data-access-security-vnet.md)のみを使用することを指定します。

   たとえば、**Microsoft.Sql** 有効になっていても、対応する VNet ルールがないサブネット内の Azure VM から接続している場合、次のエラーが表示されることがあります: `FATAL: Client from Azure Virtual Networks is not allowed to access the server`


## <a name="next-steps"></a>次のステップ
- [Azure portal を使用した Azure Database for MariaDB ファイアウォール規則の作成と管理](./howto-manage-firewall-portal.md)
- [Azure CLI を使用した Azure Database for MariaDB ファイアウォール規則の作成と管理](./howto-manage-firewall-cli.md)
- [Azure Database for MariaDB での VNet サービス エンドポイント](./concepts-data-access-security-vnet.md)
