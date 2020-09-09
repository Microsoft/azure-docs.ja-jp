---
title: アプリケーション管理:ベスト プラクティスと推奨事項 | Microsoft Docs
description: Azure Active Directory でアプリケーションを管理するためのベスト プラクティスと推奨事項について説明します。 自動プロビジョニングの使用と、アプリケーション プロキシを使用したオンプレミス アプリの発行について説明します。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: d32728c1f388e9013b922d1f60d30e65d350bbc1
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642437"
---
# <a name="application-management-best-practices"></a>アプリケーション管理のベスト プラクティス

この記事には、Azure Active Directory (Azure AD) でのアプリケーションの管理、自動プロビジョニングの使用、アプリケーション プロキシを使用したオンプレミス アプリの発行に関する推奨事項とベスト プラクティスが記載されています。

## <a name="cloud-app-and-single-sign-on-recommendations"></a>クラウド アプリとシングル サインオンに関する推奨事項
| 推奨 | 説明 |
| --- | --- |
| アプリについて Azure AD アプリケーション ギャラリーをチェックする  | Azure AD には、エンタープライズ シングル サインオン (SSO) に対応した、事前に統合された何千ものアプリケーションが含まれるギャラリーがあります。 アプリ固有のセットアップのガイダンスについては、[SaaS アプリのチュートリアルの一覧](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)を参照してください。  | 
| SAML ベースのフェデレーション SSO を使用する  | アプリケーションがサポートしている場合は、パスワード ベースの SSO や ADFS ではなく、SAML ベースのフェデレーション SSO を Azure AD で使用します。  | 
| 証明書の署名には SHA-256 を使用する  | Azure AD は、既定では SHA-256 アルゴリズムを使用して、SAML 応答に署名します。 アプリケーションで SHA-1 が必要な場合以外は、SHA-256 を使用します (「[証明書署名オプション](certificate-signing-options.md)」と[アプリケーションのサインインの問題](application-sign-in-problem-application-error.md)に関するページを参照してください。)  | 
| ユーザー割り当てを要求する  | 既定では、ユーザーは自分への割り当てなしにエンタープライズ アプリケーションにアクセスできます。 ただし、アプリケーションでロールが公開されている場合、またはユーザーのマイ アプリにアプリケーションを表示する場合は、ユーザー割り当てが必要です。 ([アプリケーションの統合に関する開発者ガイド](developer-guidance-for-integrating-applications.md)を参照してください。)  | 
| マイ アプリをユーザー向けにデプロイする | `https://myapps.microsoft.com` の[マイ アプリ](end-user-experiences.md)は、割り当てられたクラウドベースのアプリケーションのために、単一のエントリ ポイントをユーザーに提供する Web ベースのポータルです。 グループ管理やセルフサービス パスワード リセットなどの追加機能が追加されても、ユーザーはマイ アプリでそれらを見つけることができます。 詳細については、「[マイ アプリのデプロイの計画](access-panel-deployment-plan.md)」を参照してください。
| グループの割り当てを使用する  | サブスクリプションに含まれる場合は、グループをアプリケーションに割り当てて、継続的なアクセス管理をグループ所有者に委任できるようにします。 ([アプリケーションの統合に関する開発者ガイド](developer-guidance-for-integrating-applications.md)を参照してください。)   | 
| 証明書管理のためのプロセスを確立する | 署名証明書の最長有効期間は 3 年です。 証明書の期限切れによる停止を防止または最小限にするには、ロールとメール配布リストを使用して、証明書関連の変更通知が厳重に監視されるようにします。 |

## <a name="provisioning-recommendations"></a>プロビジョニングに関する推奨事項
| 推奨 | 説明 |
| --- | --- |
| チュートリアルを利用してクラウド アプリでのプロビジョニングを設定する | 追加するギャラリー アプリのプロビジョニングを構成することに関する手順付きのガイダンスについては、[SaaS アプリのチュートリアルの一覧](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)を確認してください。 |
| プロビジョニング ログ (プレビュー) を使用して状態を監視する | [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)では、プロビジョニング サービスによって実行されたすべてのアクションに関する詳細 (個々のユーザーの状態を含む) が示されます。 |
| プロビジョニング通知メールに配布グループを割り当てる | プロビジョニング サービスによって送信される重大なアラートの可視性を高めるには、通知メールの設定に配布グループを割り当てます。 |


## <a name="application-proxy-recommendations"></a>アプリケーション プロキシに関する推奨事項
| 推奨 | 説明 |
| --- | --- |
| 内部リソースへのリモート アクセスにはアプリケーション プロキシを使用する | リモート ユーザーに内部リソースへのアクセスを付与する場合は、VPN やリバース プロキシの必要がなくなるアプリケーション プロキシをお勧めします。 待機時間が追加される可能性があるため、これは企業ネットワーク内からリソースにアクセスするためのものではありません。
| カスタム ドメインを使用する | アプリケーションのためにカスタム ドメインを設定し、ユーザーおよびアプリケーション間の URL が、ネットワークの内部または外部のどちらからでも機能するようにします ([カスタム ドメインの構成](application-proxy-configure-custom-domain.md)に関するページを参照してください)。 ブランドを制御したり、URL をカスタマイズしたりすることもできます。  カスタム ドメイン名の使用時には、マイクロソフト以外の信頼できる証明機関から公開証明書を取得することを計画します。 Azure アプリケーション プロキシでは、標準、([ワイルドカード](application-proxy-wildcard.md))、または SAN ベースの証明書がサポートされます。 ([アプリケーション プロキシの計画](application-proxy-deployment-plan.md)に関するページを参照してください。) |
| アプリケーション プロキシをデプロイする前にユーザーを同期する | アプリケーション プロキシをデプロイする前に、ユーザー ID をオンプレミス ディレクトリと同期するか、Azure AD 内に直接、ユーザー ID を作成します。 ID 同期によって、アプリ プロキシが発行したアプリケーションへのアクセスをユーザーに付与する前に、Azure AD でユーザーを事前に認証できます。 シングル サインオン (SSO) を実行するために必要なユーザー ID 情報を提供することもできます。 ([アプリケーション プロキシの計画](application-proxy-deployment-plan.md)に関するページを参照してください。) |
| 高可用性と負荷分散のためのマイクロソフトのヒントに従う | ユーザー、アプリケーション プロキシ コネクタ、およびバックエンド アプリ サーバー間でトラフィックがどのように流れるかを学び、パフォーマンスと負荷分散を最適化するためのヒントを得るには、「[アプリケーション プロキシ コネクタとアプリケーションの高可用性と負荷分散](application-proxy-high-availability-load-balancing.md)」を参照してください。 |
| 複数のコネクタを利用する | 回復性、可用性、およびスケールを高めるには、アプリケーション プロキシ コネクタを 2 つ以上使用します ([アプリケーション プロキシ コネクタ](application-proxy-connectors.md)に関するページを参照してください)。 コネクタ グループを作成し、各コネクタ グループに少なくとも 2 つのコネクタがあるようにします (3 つのコネクタが最適です)。 |
| アプリケーション サーバーの近くのコネクタ サーバーを見つけ、それらのサーバーが同じドメイン内にあるようにする | パフォーマンスを最適化するため、アプリケーション サーバーの付近に物理的にコネクタ サーバーを配置します ([ネットワーク トポロジの考慮事項](application-proxy-network-topology.md)に関するページを参照してください)。 また、コネクタ サーバーと Web アプリケーション サーバーは、同じ Active Directory ドメインに属しているか、信頼する側のドメインにまたがっている必要があります。 この構成は、統合 Windows 認証 (IWA) と Kerberos の制約付き委任 (KCD) を使用する SSO に必要です。 これらのサーバーが異なるドメインにある場合は、SSO のためにリソースベースの委任を使用する必要があります ([アプリケーション プロキシを使用したシングル サインオンのための KCD](application-proxy-configure-single-sign-on-with-kcd.md) に関するページを参照してください)。 |
| コネクタの自動更新を有効にする | 最新機能とバグ修正のために、コネクタの自動更新を有効にします。 Microsoft では、コネクタの最新バージョンと 1 つ前のバージョンを直接サポートしています。 ([アプリケーション プロキシのバージョン履歴](application-proxy-release-version-history.md)に関するページを参照してください。) |
| オンプレミスのプロキシはバイパスする | メンテナンスがより容易なように、オンプレミスのプロキシをバイパスし、Azure サービスに直接接続するようにコネクタを構成します。 ([アプリケーション プロキシ コネクタとプロキシ サーバー](application-proxy-configure-connectors-with-proxy-servers.md)に関するページを参照してください。) |
| Web アプリケーション プロキシ経由で Azure AD アプリケーション プロキシを使用する | ほとんどのオンプレミスのシナリオでは、Azure AD アプリケーション プロキシを使用します。 Web アプリケーション プロキシが推奨されるのは、AD FS 用のプロキシ サーバーを必要とするシナリオのみであり、その場合は Azure Active Directory のカスタム ドメインを使用することができません。 ([アプリケーション プロキシの移行](application-proxy-migration.md)に関するページを参照してください。) |
