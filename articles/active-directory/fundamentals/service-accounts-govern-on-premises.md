---
title: オンプレミス サービス アカウントの管理 | Azure Active Directory
description: サービス アカウントのアカウント ライフサイクル プロセスを作成して実行するためのガイド
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10ea524620f810e0bf1dddc230716031bbc10e69
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642375"
---
# <a name="governing-on-premises-service-accounts"></a>オンプレミス サービス アカウントの管理

Windows Active Directory には、4 種類のオンプレミス サービス アカウントがあります。

* [グループ管理サービス アカウント](service-accounts-group-managed.md) (gMSA)

* [スタンドアロン管理サービス アカウント](service-accounts-standalone-managed.md) (sMSA)

* [コンピューター アカウント](service-accounts-computer.md)

* [サービス アカウントとして機能するユーザー アカウント](service-accounts-user-on-premises.md)


サービス アカウントを厳重に管理して次のことを行うことが重要です。 

* ユースケースの要件と目的に基づいてサービス アカウントを保護する。

* サービス アカウントとその資格情報のライフサイクルを管理する。

* さらされるリスクと付与されているアクセス許可に基づいてサービス アカウントを評価する。 

* Active Directory と Azure Active Directory に、広範囲に及ぶ可能性があるアクセス許可が付与された古いサービス アカウントがないことを確認する。

## <a name="principles-for-creating-a-new-service-account"></a>新しいサービス アカウントを作成するための原則

新しいサービス アカウントを作成するときは、次の基準を使用してください。

| 原則| 考慮事項 | 
| - |- | 
| サービス アカウントのマッピング| サービス アカウントは、1 つのサービス、アプリケーション、またはスクリプトに関連付けます。 |
| 所有権| アカウントをリクエストし、その責任を引き受ける所有者がいることを確認します。 |
| Scope| スコープを明確に定義し、サービス アカウントの使用期間を予測します。 |
| 目的| 1 つの特定の目的のためにサービス アカウントを作成します。 |
| 特権| 次のようにして最小特権の原則を適用します。 <br>管理者などの組み込みグループに割り当てない。<br> 必要に応じてローカル マシンの特権を削除する。<br>アクセスを調整し、ディレクトリへのアクセスについては Active Directory の委任を使用する。<br>詳細なアクセス許可を使用する。<br>ユーザーベースのサービス アカウントに対してアカウントの有効期限と場所に基づく制限を設定する |
| 監視と監査の使用| サインイン データを監視し、それが目的の使用方法に一致していることを確認します。 異常な使用に対するアラートを設定します。 |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>ユーザー アカウントに最小特権を適用し、アカウントの乱用を制限する

サービス アカウントとして使用されるユーザー アカウントでは、次の設定を使用します。

* [**アカウントの有効期限**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps&preserve-view=true): 継続する必要があると判断されない限り、レビュー期間後の設定された時間にサービス アカウントが自動的に期限切れになるように設定します。

*  **ログオンできるワークステーション**: サービス アカウントがサインインできる場所のアクセス許可を制限します。 マシン上でローカルに実行され、そのマシン上のリソースにのみアクセスする場合は、それ以外の場所でのログオンを制限します。

* [**パスワードを変更できない**](/powershell/module/addsadministration/set-aduser): パラメーターを false に設定することで、サービス アカウントが自身のパスワードを変更できないようにします。

 
## <a name="build-a-lifecycle-management-process"></a>ライフサイクル管理プロセスを作成する

サービス アカウントのセキュリティを維持するには、必要性を特定した時点から、それらが使用停止になるまで管理する必要があります。 

サービス アカウントのライフサイクル管理には、次のプロセスを使用します。

1. アカウントの使用状況に関する情報を収集する
1. サービス アカウントとアプリを構成管理データベース (CMDB) にオンボードする
1. リスク評価または正式なレビューを実行する
1. サービス アカウントを作成し、制限を適用する。
1. 定期的なレビューをスケジュールして実行する。 必要に応じてアクセス許可とスコープを調整する。
1. 必要に応じてアカウントをプロビジョニング解除する。

### <a name="collect-usage-information-for-the-service-account"></a>サービス アカウントの使用状況に関する情報を収集する

各サービス アカウントの関連するビジネス情報を収集します。 下の表には、収集すべき最低限の情報を示していますが、アカウントの存在に関するビジネス ケースを作成するために必要なものをすべて収集する必要があります。

| Data| 詳細 |
| - | - |
| 所有者| サービス アカウントの説明責任を負うユーザーまたはグループ |
| 目的| サービス アカウントの目的 |
| アクセス許可 (スコープ)| 予期されるアクセス許可のセット |
| 構成管理データベース (CMDB) のリンク| ターゲットのスクリプトまたはアプリケーションと所有者とのクロスリンク サービス アカウント |
| リスク| セキュリティ リスク評価に基づくリスクとビジネスへの影響のスコアリング |
| 有効期間| アカウントの有効期限または再認定のスケジュールを有効にするための予想最長有効期間 |


 

アカウントのリクエストをセルフサービスにし、関連情報を要求するのが理想的です。 所有者は、アプリケーションまたはビジネスの所有者、IT メンバー、またはインフラストラクチャの所有者の場合があります。 Microsoft Forms などのツールをこのリクエストと関連情報に使用すると、アカウントが承認された場合に、CMDB インベントリ ツールに簡単に移植できます。

### <a name="onboard-service-account-to-cmdb"></a>サービス アカウントを CMDB にオンボードする

収集した情報を CMDB 型アプリケーションに格納します。 ビジネス情報に加えて、他のインフラストラクチャ、アプリ、およびプロセスへのすべての依存関係を含めます。  この中央リポジトリを使用すると、次のことが容易になります。

* リスクを評価する。

* 必要な制限が適用されたサービス アカウントを構成する。

* 関連する機能とセキュリティの依存関係を把握する。

* セキュリティと継続的な必要性に関して定期的なレビューを実施する。

* サービス アカウントの確認、廃止、変更について所有者に問い合わせる。

Web サイトを実行するために使用され、1 つ以上の SQL データベースへの接続権限があるサービス アカウントを考えてみます。 このサービス アカウントについて CMDB に格納される情報は、次のようになります。

|Data | 詳細|
| - | - |
| 所有者、Deputy| John Bloom、Anna Mayers |
| 目的| HR Web ページを実行し、HR データベースに接続します。 データベースにアクセスするときにはエンド ユーザーの権限を借用できます。 |
| アクセス許可、スコープ| HR-WEBServer: ローカルにログオンし、Web ページを実行します<br>HR-SQL1: ローカルにログオンし、すべての HR* データベースを読み取ります<br>HR-SQL2: ローカルにログオンし、SALARY* データベースを読み取ります |
| Cost Center| 883944 |
| 評価されるリスク| 中、ビジネスへの影響: 中、個人情報: 中 |
| アカウントの制限| ログオン先: 前述のサーバーのみ、パスワードを変更できない、MBI パスワード ポリシー、 |
| 有効期間| 無制限 |
| レビュー サイクル| 半年ごと (所有者別、セキュリティ チーム別、プライバシー別) |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>サービス アカウントの使用状況に関するリスク評価または正式なレビューを実行する

そのアクセス許可と目的を考慮して、セキュリティ侵害が発生した場合に、そのアカウントによってその関連アプリケーションやサービス、およびインフラストラクチャに対して発生するリスクを評価します。 直接的および間接的の両方のリスクを考慮します。 

* 敵対者が直接アクセスを取得できるものは何か。

* サービス アカウントがアクセスできるその他の情報またはシステムとは。

* そのアカウントを使用して追加のアクセス許可を付与できるか。

* アクセス許可が変更された場合、どのようにしてそれを知ることができるか。

リスク評価が実施されて文書化されると、次のようなものに対して影響が生じる可能性があります。

* アカウントの制限

* アカウントの有効期間

* アカウントのレビュー要件 (頻度とレビュー担当者)

### <a name="create-a-service-account-and-apply-account-restrictions"></a>サービス アカウントを作成し、アカウントの制限を適用する

サービス アカウントの作成は、関連情報を CMDB 内に文書化して、リスク評価を実行した後にのみ行ってください。 アカウントの制限は、リスク評価に合わせる必要があります。 評価に関連する場合は、次の制限事項を考慮してください。

* [アカウントの有効期限](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps&preserve-view=true)

   * サービス アカウントとして使用されるすべてのユーザー アカウントに対して、現実的で限定された使用終了日を定義します。 これを設定するには、"アカウントの有効期限" フラグを使用します。 詳細については、「[Set-ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration)」を参照してください。 

* ログオン先 ([LogonWorkstation](/powershell/module/addsadministration/set-aduser))

* [パスワード ポリシー](../../active-directory-domain-services/password-policy.md)の要件

* 特権ユーザーのみを対象とした管理が保証される [OU の場所](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous)での作成

* サービス アカウントと[サービス アカウントの使用](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html)に対する[変更を検出する](/windows/security/threat-protection/auditing/audit-directory-service-changes)監査を設定して収集します。

運用環境に移行する準備ができたら、サービス アカウントにアクセス権を安全に付与します。 

### <a name="schedule-regular-reviews-of-service-accounts"></a>サービス アカウントの定期的なレビューをスケジュールする

中および高のリスクとして分類されたサービス アカウントの定期的なレビューを設定します。 レビューには次のものが含まれている必要があります。 

* アカウントの継続的必要性、および特権とスコープの正当性に対する所有者による認証。

* 上位および下流の接続に関する評価など、プライバシーおよびセキュリティのチームによるレビュー。

* 意図された目的のためだけに使用されていることを確認する監査からのデータ

### <a name="deprovision-service-accounts"></a>サービス アカウントをプロビジョニング解除する

プロビジョニング解除プロセスでは、まずアクセス許可と監視を削除してから、必要に応じてアカウントを削除します。

サービス アカウントのプロビジョニング解除は、次の場合に行います。

* サービス アカウントの作成目的であるスクリプトまたはアプリケーションが廃止されている。

* サービス アカウントの使用目的であるスクリプトまたはアプリケーション内の機能 (特定のリソースへのアクセスなど) が廃止されている。

* サービス アカウントが別のサービス アカウントに置き換えられている。

すべてのアクセス許可を削除したら、こちらのプロセスを使用してアカウントを削除します。

1. 関連するアプリケーションまたはスクリプトがプロビジョニング解除されたら、関連するサービス アカウントのサインインとリソース アクセスを監視して、それが別のプロセスで使用されていないことを確認します。 もう必要ないことが確信できたら、次の手順に進んでください。

2. サービス アカウントのサインインを無効にして、もう必要ないことを確認します。 アカウントを無効のままにする必要がある期間に関するビジネス ポリシーを作成します。

3. 無効のままにするポリシーが満たされたら、サービス アカウントを削除します。 

   * MSA の場合は、PowerShell を使用して[アンインストール](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps&preserve-view=true)することも、管理されたサービス アカウント コンテナーから手動で削除することもできます。

   * コンピューターまたはユーザー アカウントの場合は、Active Directory で手動でアカウントを削除できます。

## <a name="next-steps"></a>次のステップ
サービス アカウントのセキュリティ保護に関する次の記事を参照してください。

* [オンプレミス サービス アカウントの概要](service-accounts-on-premises.md)

* [グループ管理サービス アカウントをセキュリティで保護する](service-accounts-group-managed.md)

* [スタンドアロン管理サービス アカウントをセキュリティで保護する](service-accounts-standalone-managed.md)

* [コンピューター アカウントをセキュリティで保護する](service-accounts-computer.md)

* [ユーザー アカウントをセキュリティで保護する](service-accounts-user-on-premises.md)

* [オンプレミス サービス アカウントを管理する](service-accounts-govern-on-premises.md)