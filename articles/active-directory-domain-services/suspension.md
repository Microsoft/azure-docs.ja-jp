---
title: Azure AD Domain Services の中断されたドメイン | Microsoft Docs
description: Azure AD DS マネージド ドメインのさまざまな正常性状態と、中断されたドメインの復元方法について説明します。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 31a1c7cd72d57b9c680452d5e84f8fe78f47cebb
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71693307"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services の正常性状態の理解と中断されたドメインの解決

Azure Active Directory Domain Services (Azure AD DS) が長期間にわたりマネージド ドメインにサービスを提供できない場合、マネージド ドメインは中断済み状態になります。 その後、マネージド ドメインが中断状態のままである場合、自動的に削除されます。 Azure AD DS マネージド ドメインを正常な状態に保ち、中断を回避するには、できるだけ早くアラートを解決します。

この記事では、マネージド ドメインが中断される理由、および中断されたドメインを復旧する方法について説明します。

## <a name="overview-of-managed-domain-states"></a>マネージド ドメインの状態の概要

Azure AD DS マネージド ドメインのライフサイクルを通じて、その正常性を示すさまざまな状態があります。 マネージド ドメインで問題が報告された場合は、根本的な原因を迅速に解決して、機能低下が続く状態を防ぎます。

![中断に至るまでの Azure AD DS マネージド ドメインの状態の推移](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Azure AD DS マネージド ドメインは、次のいずれかの状態になります。

* [実行中](#running-state)
* [要注意](#needs-attention-state)
* [中断済み](#suspended-state)
* [削除済み](#deleted-state)

## <a name="running-state"></a>実行中の状態

正しく構成され、問題なく実行されている Azure AD DS マネージド ドメインは、"*実行中*" 状態です。 これは、マネージド ドメインの望ましい状態です。

### <a name="what-to-expect"></a>ウィザードの内容

* Azure プラットフォームでは、マネージド ドメインの正常性を定期的に監視できます。
* マネージド ドメインのドメイン コントローラーには、修正プログラムと更新プログラムが定期的に適用されます。
* Azure Active Directory からの変更が、マネージド ドメインに定期的に同期されます。
* マネージド ドメインのバックアップが定期的に取得されます。

## <a name="needs-attention-state"></a>要注意の状態

解決する必要がある 1 つ以上の問題がある Azure AD DS マネージド ドメインは、"*要注意*" 状態です。 マネージド ドメインの正常性ページには、アラートが一覧表示され、問題が発生している場所が示されます。 一部のアラートは一時的なものであり、Azure プラットフォームによって自動的に解決されます。 その他のアラートについては、提供される解決手順に従うことで問題を解決できます。 重大アラートがある場合は、さらなるトラブルシューティングの支援のために、[Azure サポート リクエストを開いて][azure-support]ください。

アラートの一例として、制限されたネットワーク セキュリティ グループがある場合が挙げられます。 この構成では、Azure プラットフォームはマネージド ドメインの更新および監視を行えない可能性があります。 アラートが生成され、状態が "*要注意*" に変わります。

詳細については、[Azure AD DS マネージド ドメインでのアラートのトラブルシューティング方法][resolve-alerts]に関するページを参照してください。

### <a name="what-to-expect"></a>ウィザードの内容

Azure AD DS マネージド ドメインが "*要注意*" 状態である場合、Azure プラットフォームは定期的なデータの監視、パッチ適用、更新、バックアップを行うことができない場合があります。 場合によっては (無効なネットワーク構成がある場合など)、マネージド ドメインのドメイン コントローラーに到達できないことがあります。

* アラートが解決されるまで、マネージド ドメインは異常な状態であり、継続的な正常性の監視が停止する場合があります。
* マネージド ドメインのドメイン コントローラーに、修正プログラムや更新プログラムを適用できません。
* Azure Active Directory からの変更が、マネージド ドメインに同期されないことがあります。
* マネージド ドメインのバックアップを作成できない場合があります。
* マネージド ドメインに影響を与えている重大でないアラートを解決すると、正常性は "*実行中*" 状態に戻ります。
* Azure プラットフォームがドメイン コントローラーに到達できない構成の問題に対しては、重大アラートがトリガーされます。 このような重大アラートが 15 日以内に解決されない場合、マネージド ドメインは "*中断済み*" 状態になります。

## <a name="suspended-state"></a>中断済みの状態

Azure AD DS マネージド ドメインは、次のいずれかの理由により、 **[中断済み]** 状態になります。

* 1 つ以上の重大アラートが、15 日以内に解決されなかった場合。
    * 重大アラートは、Azure AD DS で必要なリソースへのアクセスを妨げる誤った構成によって発生する可能性があります。 たとえば、マネージド ドメインで [AADDS104:ネットワーク エラー][alert-nsg] アラートが 16 日間以上解決されていない場合です。
* Azure サブスクリプションに請求の問題がある場合、または Azure サブスクリプションの有効期限が切れた場合。

Azure プラットフォームがドメインの管理、監視、パッチ適用、バックアップを行うことができない場合、マネージド ドメインは中断されます。 マネージド ドメインは 15 日間 "*中断済み*" 状態のままになります。 マネージド ドメインへのアクセスを維持するには、重大アラートを直ちに解決してください。

### <a name="what-to-expect"></a>ウィザードの内容

Azure AD DS マネージド ドメインが "*中断済み*" 状態である場合、次の動作が発生します。

* マネージド ドメインのドメイン コントローラーはプロビジョニング解除され、仮想ネットワーク内で到達できなくなります。
* インターネット経由でのマネージド ドメインへの Secure LDAP アクセスは、有効になっている場合、動作しなくなります。
* マネージド ドメインに対する認証、ドメイン参加済み VM へのログオン、または LDAP/LDAPS 経由での接続の際に失敗が発生します。
* マネージド ドメインのバックアップは行われなくなります。
* Azure AD との同期は停止します。

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>マネージド ドメインが中断されているかどうかを確認する方法

Azure portal の Azure AD DS の正常性ページに、ドメインが中断されていることを示す[アラート][resolve-alerts]が表示されます。 ドメインの状態も "*中断済み*" と表示されます。

### <a name="restore-a-suspended-domain"></a>中断されているドメインを復元する

"*中断済み*" 状態である Azure AD DS マネージド ドメインの正常性を復元するには、次の手順を実行します。

1. Azure portal で、**Domain services** を検索して選択します。
1. 一覧から Azure AD DS マネージド ドメイン (*contoso.com* など) を選択し、 **[正常性]** を選択します。
1. 中断の原因によって *AADDS503* や *AADDS504* などのアラートを選択します。
1. アラートで提供される解決リンクを選択し、手順に従ってそれを解決します。

マネージド ドメインは、最後のバックアップの日付までしか復元できません。 最後のバックアップの日付は、マネージド ドメインの **[正常性]** ページに表示されます。 最後のバックアップより後に発生した変更は復元できません。 マネージド ドメインのバックアップは、最大 30 日間保存されます。 30 日より古いバックアップは削除されます。

マネージド ドメインが "*中断済み*" 状態であるときにアラートを解決したら、正常な状態に戻すために [Azure サポート リクエストを開いてください][azure-support]。 30 日未満のバックアップがあれば、Azure サポートはマネージド ドメインを復元できます。

## <a name="deleted-state"></a>削除済みの状態

15 日間 "*中断済み*" 状態のままだった Azure AD DS マネージド ドメインは削除されます。 このプロセスは回復できません。

### <a name="what-to-expect"></a>ウィザードの内容

Azure AD DS マネージド ドメインが "*削除済み*" 状態になると、次の動作が行われます。

* マネージド ドメインのすべてのリソースとバックアップが削除されます。
* マネージド ドメインを復元することはできず、Azure AD DS を再び使用するには代替マネージド ドメインを作成する必要があります。
* 削除されたマネージド ドメインに対しては課金されません。

## <a name="next-steps"></a>次の手順

Azure AD DS マネージド ドメインの正常性を維持し、それが中断されるリスクを最小限に抑えるために、[マネージド ドメインのアラートを解決する][resolve-alerts]方法を確認します。

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
