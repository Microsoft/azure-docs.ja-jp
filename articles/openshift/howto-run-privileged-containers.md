---
title: Azure Red Hat OpenShift クラスター内で特権コンテナーを実行する | Microsoft Docs
description: 特権コンテナーを実行して、セキュリティとコンプライアンスを監視します。
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: ARO, OpenShift, aquasec, Twistlock, Red Hat
ms.openlocfilehash: 4241296a991283f14fbb294fdc059ecde58d6d75
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069755"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift クラスター内で特権コンテナーを実行する

Azure Red Hat OpenShift クラスターでは、任意の特権コンテナーを実行することはできません。
ARO クラスターでは、セキュリティ監視およびコンプライアンス用の 2 つのソリューションを実行できます。
このドキュメントでは、セキュリティ製品ベンダーによって提供されている、OpenShift のデプロイに関する一般的なドキュメントとの違いについて説明します。


ベンダーの指示に従う前に、これらの手順をお読みください。
以下に示す製品固有の手順のセクション タイトルは、ベンダーのドキュメントのセクション タイトルをそのまま引用しています。

## <a name="before-you-begin"></a>開始する前に

ほとんどのセキュリティ製品のドキュメントでは、cluster-admin 特権があることが前提とされています。
Azure Red Hat OpenShift では、顧客管理者がすべての特権を持っているわけではありません。 クラスター全体のリソースを変更するために必要なアクセス許可は制限されています。

まず、`oc get scc` を実行して、ユーザーが顧客管理者としてクラスターにログインしていることを確認します。 顧客管理者グループのメンバーであるすべてのユーザーには、クラスターのセキュリティ コンテキスト制約 (SCC) を表示するアクセス許可があります。

次に、`oc` バイナリ バージョンが `3.11.154` であることを確認します。
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Aqua Security での製品固有の手順
これから変更を加えるベースの手順は、[Aqua Security のデプロイに関するドキュメント](https://docs.aquasec.com/docs/openshift-red-hat)で確認できます。 ここでは、Aqua のデプロイに関するドキュメントの手順も併せて実行します。

最初の手順は、更新対象となる必要な SCC に注釈を付けることです。 これらの注釈により、クラスターの同期ポッドは、これらの SSC への変更を元に戻すことができなくなります。

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>手順 1:前提条件を準備する
必ず、cluster-admin ロールではなく ARO 顧客管理者としてクラスターにログインしてください。

プロジェクトとサービス アカウントを作成します。
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

aqua-account には、cluster-reader ロールを割り当てるのではなく、次のコマンドを使用して customer-admin-cluster ロールを割り当てます。
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

引き続き、手順 1 の残りの手順に従います。  それらの手順では、Aqua レジストリ用のシークレットを設定する方法について説明しています。

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>手順 2:Aqua Server、Aqua Database、Aqua Gateway をデプロイする
Aqua のドキュメントの手順に従って、aqua-console.yaml をインストールします。

用意されている `aqua-console.yaml` に変更を加えます。  `kind: ClusterRole` および `kind: ClusterRoleBinding` というラベルが付いた上位 2 つのオブジェクトを削除します。  この時点では、顧客管理者が `ClusterRole` および `ClusterRoleBinding` オブジェクトを変更するためのアクセス許可を持っていないため、これらのリソースは作成されません。

2 番目の変更は、`aqua-console.yaml` の `kind: Route` 部分に対して行います。 `aqua-console.yaml` ファイル内の `kind: Route` オブジェクトの yaml を次のように置き換えます。
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

残りの手順に従います。

### <a name="step-3-login-to-the-aqua-server"></a>手順 3:Aqua Server にログインする
このセクションの変更は一切ありません。  Aqua のドキュメントに従ってください。

Aqua Console アドレスを取得するには、次のコマンドを使用します。
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>手順 4:Aqua Enforcer をデプロイする
Enforcer のデプロイ時に、次のフィールドを設定します。

| フィールド          | Value         |
| -------------- | ------------- |
| オーケストレーター   | OpenShift     |
| ServiceAccount | aqua-account  |
| Project        | aqua-security |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Prisma Cloud および Twistlock での製品固有の手順

これから変更を加えるベースの手順は、[Prisma Cloud のデプロイに関するドキュメント](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)で確認できます。

最初に新しい OpenShift プロジェクトを作成します。
```
oc new-project twistlock
```

「コンソールをインストールする」セクションまではドキュメントに従い、内部コンテナー レジストリを作成するのではなく Prisma Cloud コンテナー レジストリを使用してください。

### <a name="install-console"></a>コンソールをインストールする

手順 2 の `oc create -f twistlock_console.yaml` では、名前空間の作成時にエラーが表示されます。
名前空間は事前に `oc new-project` コマンドを使用して作成されているため、これは無視してもかまいません。

### <a name="create-an-external-route-to-console"></a>コンソールへの外部ルートを作成する

ドキュメントのとおりに進められるほか。oc コマンドを使用する場合は次の手順を実行することもできます。
コンピューター上の twistlock_route.yaml という名前のファイルに次のルート定義をコピーします。
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
次に、以下を実行します。
```
oc create -f twistlock_route.yaml
```

このコマンドを使用して、Twistlock コンソールに URL を割り当てることができます: `oc get route twistlock-console -n twistlock`。

### <a name="configure-console"></a>コンソールを構成する

Twistlock のドキュメントに従います。

### <a name="install-defender"></a>Defender をインストールする

手順 2 の `oc create -f defender.yaml` では、クラスター ロールとクラスター ロール バインドの作成時にエラーが表示されます。
これらは無視してかまいません。

Defender は、コンピューティング ノードでのみデプロイされます。 ノード セレクターで制限する必要はありません。
