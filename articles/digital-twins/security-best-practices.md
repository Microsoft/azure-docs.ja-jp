---
title: セキュリティのベスト プラクティスの概要 - Azure Digital Twins | Microsoft Docs
description: Azure Digital Twins とモノのインターネットのセキュリティのベスト プラクティスについて説明します。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: ba10a6105972eddc8474841b234d2eeddc994f5d
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948941"
---
# <a name="security-best-practices"></a>セキュリティのベスト プラクティス

Azure Digital Twins のセキュリティにより、IoT グラフ内の特定のリソースとアクションへのアクセスを正確に制御できます。 これは、[ロールベースのアクセス制御](./security-role-based-access-control.md)と呼ばれるきめ細かいロールとアクセス許可の管理によって行われます。

Azure Digital Twins では、Azure Active Directory (Azure AD) など、Azure IoT に存在する他のセキュリティ機能も使用されます。 そのため、Azure Digital Twins 上に構築されるアプリケーションの構成とセキュリティ保護には、現在推奨されているものと同じ多くの [Azure IoT のセキュリティ プラクティス](../iot-fundamentals/iot-security-best-practices.md)を使用することが含まれます。

この記事では、従うべき主要なベスト プラクティスの概要を示します。

> [!IMPORTANT]
> IoT スペースの最大限のセキュリティを確保するには、追加のセキュリティ リソースを確認してください。 デバイス ベンダーを必ず含めます。

> [!TIP]
> [Azure Security Center for IoT](https://docs.microsoft.com/azure/asc-for-iot/) を使用すると、IoT セキュリティの脅威および脆弱性を検出することができます。

## <a name="iot-security-best-practices"></a>IoT セキュリティのベスト プラクティス

IoT デバイスを安全にセキュリティで保護するためのいくつかの重要なプラクティスは、次のとおりです。

> [!div class="checklist"]
> * IoT スペースに接続されている各デバイスを、不正操作防止の方法で保護します。
> * IoT スペース内の各デバイス、センサー、人の役割を制限します。 侵害された場合、影響は最小限に抑えられます。
> * デバイスの IP アドレスのフィルター処理の潜在的な使用とポートの制約を考慮します。
> * パフォーマンスを向上させるために、I/O とデバイスの帯域幅を制限します。 レート制限により、サービス拒否攻撃を防ぐことでセキュリティを向上させることができます。
> * デバイスのファームウェアを最新に保ちます。
> * デバイス、ネットワーク、およびゲートウェイのセキュリティに関するベスト ラクティスが絶えず改良され進化するように、定期的に監査および検証を行います。

IoT スペースを安全にセキュリティで保護するためのいくつかの重要なプラクティスは、次のとおりです。

> [!div class="checklist"]
> * 保存済み、格納済み、または永続的なデータを暗号化します。
> * パスワードまたはキーを定期的に変更または更新する必要があります。
> * ロールによって、アクセスとアクセス許可を慎重に制限します。 後の「[ロールベースのアクセス制御のベスト プラクティス](#role-based-access-control-best-practices)」セクションをご覧ください。
> * 各ネットワーク上のデバイスが他と分離されるように、ネットワーク トポロジの分割を検討してください。
> * 強力な暗号化を使用します。 長いパスワードを要求し、セキュリティで保護されたプロトコルと 2 要素認証を使用します。

IoT リソースを[監視](./how-to-configure-monitoring.md)して、通常の操作の範囲外である外れ値、脅威、またはリソース パラメーターを見張ります。 監視の管理には Azure Analytics を使用します。

> [!NOTE]
> イベントの処理と監視について詳しくは、[Azure Digital Twins でのイベントとメッセージのルーティング](./concepts-events-routing.md)に関する記事をご覧ください。

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory のベスト プラクティス

Azure Digital Twins は、Azure Active Directory を使用してユーザーを認証し、アプリケーションを保護します。 Azure Active Directory では、さまざまな最新アーキテクチャ用の認証がサポートされます。 すべては、OAuth 2.0 や OpenID Connect などの業界標準のプロトコルに基づいています。 Azure Active Directory 用の IoT スペースをセキュリティで保護するためのいくつかの重要なプラクティスは、次のとおりです。

> [!div class="checklist"]
> * Azure Active Directory アプリのシークレットとキーは、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) などのセキュリティで保護された場所に保存します。
> * 認証するアプリ シークレットではなく、信頼する[証明機関](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)によって発行された証明書を使用します。
> * トークンに対するアクセスの OAuth 2.0 スコープを制限します。
> * トークンが有効である期間と、トークンが有効のままになっているかどうかを確認します。
> * トークンが有効である期間を適切な期間に設定します。
> * 期限切れのトークンを更新します。

## <a name="role-based-access-control-best-practices"></a>ロールベースのアクセス制御のベスト プラクティス

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>次の手順

* Azure IoT のベスト プラクティスの詳細については、「[IoT セキュリティのベスト プラクティス](../iot-fundamentals/iot-security-best-practices.md)」をご覧ください。

* ロールベースのアクセス制御の詳細については、[ロールベースのアクセス制御](./security-role-based-access-control.md)に関するページをご覧ください。

* 認証については、[API を使用した認証](./security-authenticating-apis.md)に関するページをご覧ください。