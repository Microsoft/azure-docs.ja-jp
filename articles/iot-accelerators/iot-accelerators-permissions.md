---
title: Azure IoT ソリューション サイトを使用する - Azure | Microsoft Docs
description: AzureIoTSolutions.com の Web サイトを使用してソリューション アクセラレータをデプロイする方法について説明します。
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 5a088cb07fa74b65e497de54be8c97bd51d31c16
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387838"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>azureiotsolutions.com サイトを使用してソリューション アクセラレータをデプロイする

Azure IoT ソリューション アクセラレータを [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) から Azure サブスクリプションにデプロイできます。 AzureIoTSolutions.com は、Microsoft オープンソースとパートナーの両方のソリューション アクセラレータをホストします。 これらのソリューション アクセラレータは、[Azure IoT 参照アーキテクチャ](/azure/architecture/reference-architectures/iot)と連携します。 このサイトを使用すると、ソリューション アクセラレータをデモまたは運用環境としてすばやくデプロイできます。

:::image type="content" source="media/iot-accelerators-permissions/iotsolutionscom.png" alt-text="IoT ソリューションのホーム ページ":::

> [!TIP]
> デプロイ プロセスに対するよりきめ細かな制御が必要な場合は、CLI を使用してソリューション アクセラレータをデプロイできます。

ソリューション アクセラレータは、次の構成でデプロイできます。

* **Standard**:運用環境を開発するための拡張インフラストラクチャ デプロイ。 Azure Container Service により、マイクロサービスが複数の Azure 仮想マシンにデプロイされます。 個々のマイクロサービスをホストする Docker コンテナーは、Kubernetes によって調整されます。
* **Basic**:デモンストレーションのため、またはデプロイをテストするための低コスト バージョン。 すべてのマイクロサービスが 1 つの Azure 仮想マシンにデプロイされます。
* **Local**: テストと開発のためのローカル コンピューターのデプロイ。 このアプローチでは、マイクロサービスをローカル Docker コンテナーにデプロイし、クラウド内の IoT Hub、Azure Cosmos DB、および Azure Storage サービスに接続します。

各ソリューション アクセラレータは、IoT Hub、Azure Stream Analytics、Cosmos DB など、Azure サービスの異なる組み合わせを使用します。 詳細については、[AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) にアクセスしてソリューション アクセラレータを選択してください。

## <a name="sign-in-at-azureiotsolutionscom"></a>azureiotsolutions.com でサインインする

ソリューション アクセラレータをデプロイするには、Azure サブスクリプションに関連付けられた資格情報を使用して AzureIoTSolutions.com でサインインする必要があります。 アカウントが複数の Microsoft Azure Active Directory (AD) テナントに関連付けられている場合は、**アカウント選択のドロップダウン** を使用して、使用するディレクトリを選択できます。

ソリューション アクセラレータをデプロイし、ユーザーを管理し、Azure サービスを管理するためのアクセス許可は、選択されたディレクトリ内のロールによって異なります。 ソリューション アクセラレータに関連付けられた一般的な Azure AD ロールは次のとおりです。

* **全体管理者**: Azure AD テナントごとに多数の [全体管理者](../active-directory/roles/permissions-reference.md)がいてもかまいません。

  * Azure AD テナントを作成したユーザーは、既定でそのテナントの全体管理者になります。
  * 全体管理者は、基本的なソリューション アクセラレータと標準のソリューション アクセラレータをデプロイできます。

* **ドメイン ユーザー**: Azure AD テナントごとに多数のドメイン ユーザーがいてもかまいません。 ドメイン ユーザーは、基本的なソリューション アクセラレータをデプロイできます。

* **ゲスト ユーザー**: Azure AD テナントごとに多数のゲスト ユーザーがいてもかまいません。 ゲスト ユーザーは、Azure AD テナントでソリューション アクセラレータをデプロイできません。

Azure AD 内のユーザーとロールの詳細については、次のリソースを参照してください。

* [Azure Active Directory でのユーザーの作成](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [ユーザーをアプリに割り当てる](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>デバイスを選択する

AzureIoTSolutions.com サイトは、[Azure Certified for IoT デバイス カタログ](https://catalog.azureiotsolutions.com/)にリンクします。

このカタログには、ユーザーが IoT ソリューションの構築を開始するためにソリューション アクセラレータに接続できる数百の認定された IoT ハードウェア デバイスが一覧表示されています。

![デバイス カタログ](media/iot-accelerators-permissions/devicecatalog.png)

ユーザーがハードウェア製造元である場合は、 **[パートナーになる]** をクリックして、Certified for IoT プログラムでの Microsoft とのパートナー提携について確認してください。

## <a name="next-steps"></a>次のステップ

IoT ソリューション アクセラレータの 1 つを試してみるには、クイック スタートを参照してください。[接続済みファクトリ ソリューションを試す](quickstart-connected-factory-deploy.md)
