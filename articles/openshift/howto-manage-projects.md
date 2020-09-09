---
title: Azure Red Hat OpenShift でのリソースの管理 | Microsoft Docs
description: Azure Red Hat OpenShift クラスター内でプロジェクト、テンプレート、イメージ ストリームを管理する
services: openshift
keywords: Red Hat OpenShift プロジェクト要求セルフプロビジョナー
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: 35474df96254b158a39b6d913171c553bf30aea4
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816441"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift クラスター内でプロジェクト、テンプレート、イメージ ストリームを管理する

OpenShift Container Platform では、プロジェクトを使用して、関連するオブジェクトをグループ化および分離します。 管理者は、開発者に特定のプロジェクトへのアクセス権を付与したり、独自のプロジェクトの作成を許可したり、個々のプロジェクトへの管理権限を付与したりできます。

## <a name="self-provisioning-projects"></a>プロジェクトのセルフプロビジョニング

開発者による独自プロジェクトの作成を許可することができます。 API エンドポイントは、project-request という名前のテンプレートに従ってプロジェクトをプロビジョニングする役割を担います。 Web コンソールと `oc new-project` コマンドは、開発者が新しいプロジェクトを作成するときにこのエンドポイントを使用します。

プロジェクト要求が送信されると、API はテンプレート内の次のパラメーターを置き換えます。

| パラメーター               | 説明                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | プロジェクトの名前です。 必須。             |
| PROJECT_DISPLAYNAME     | プロジェクトの表示名です。 空の場合もあります。 |
| PROJECT_DESCRIPTION     | プロジェクトの説明です。 空の場合もあります。  |
| PROJECT_ADMIN_USER      | 管理ユーザーのユーザー名です。       |
| PROJECT_REQUESTING_USER | 要求元ユーザーのユーザー名です。           |

API へのアクセスは、セルフプロビジョナー クラスター ロール バインドを持つ開発者に付与されます。 この機能は、既定ですべての認証された開発者が使用できます。

## <a name="modify-the-template-for-a-new-project"></a>新しいプロジェクトのテンプレートを変更する 

1. `customer-admin` 特権を持つユーザーとしてログインします。

2. 既定の project-request テンプレートを編集します。

   ```
   oc edit template project-request -n openshift
   ```

3. 注釈 `openshift.io/reconcile-protect: "true"` を追加して、Azure Red Hat OpenShift (ARO) の更新プロセスから既定のプロジェクト テンプレートを削除します。

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   project-request テンプレートは、ARO の更新プロセスによって更新されなくなります。 これにより、お客様はテンプレートをカスタマイズし、クラスターの更新時にそれらのカスタマイズ内容を保持できます。

## <a name="disable-the-self-provisioning-role"></a>セルフプロビジョニング ロールを無効にする

認証されたユーザー グループが新しいプロジェクトのセルフプロビジョニングをできないようにすることが可能です。

1. `customer-admin` 特権を持つユーザーとしてログインします。

2. セルフプロビジョナー クラスター ロール バインドを編集します。

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. 注釈 `openshift.io/reconcile-protect: "true"` を追加して、ARO の更新プロセスからロールを削除します。

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. `system:authenticated:oauth` によってプロジェクトが作成されないように、クラスター ロール バインドを変更します。

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>既定のテンプレートと imageStreams の管理

Azure Red Hat OpenShift では、`openshift` 名前空間内の既定のテンプレートとイメージ ストリームの更新を無効にすることができます。
`openshift` 名前空間のすべての `Templates` および `ImageStreams` に対する更新を無効にするには、次のようにします。

1. `customer-admin` 特権を持つユーザーとしてログインします。

2. `openshift` 名前空間を編集します。

   ```
   oc edit namespace openshift
   ```

3. 注釈 `openshift.io/reconcile-protect: "true"` を追加して、ARO の更新プロセスから `openshift` 名前空間を削除します。

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   `openshift` 名前空間内の個々のオブジェクトは、それに注釈 `openshift.io/reconcile-protect: "true"` を追加することで、更新プロセスから削除できます。

## <a name="next-steps"></a>次のステップ

次のチュートリアルを試してください。
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift クラスターを作成する](tutorial-create-cluster.md)
