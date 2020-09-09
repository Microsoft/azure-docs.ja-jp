---
title: Azure Active Directory Identity Protection の FAQ
description: Azure AD Identity Protection についてよく寄せられる質問
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140ad45d9c4f6b6f49a4ea4aefb9298e58a2cf10
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "75443566"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Azure Active Directory Identity Protection についてよく寄せられる質問

## <a name="dismiss-user-risk-known-issues"></a>"ユーザー リスクを無視する" の既知の問題

従来の Identity Protection で**ユーザー リスクを無視する**と、Identity Protection のユーザーのリスク履歴のアクターが **Azure AD** に設定されます。

Identity Protection で**ユーザー リスクを無視する**と、Identity Protection のユーザーのリスク履歴のアクターが **\<Admin’s name with a hyperlink pointing to user’s blade\>** に設定されます。

現在、ユーザー リスクの無視フローに遅延が発生するという既知の問題があります。 "ユーザー リスク ポリシー" がある場合、このポリシーによって、[ユーザー リスクを無視する] をクリックしてから数分以内に、無視されたユーザーへの適用が停止されます。 ただし、無視されたユーザーの "リスク状態" が UX で更新されるまでに既知の遅延があります。 回避策としては、ブラウザー レベルでページを更新して最新のユーザーの "リスク状態" を確認します。

## <a name="risky-users-report-known-issues"></a>"危険なユーザー レポート" の既知の問題

**[ユーザー名]** フィールドに対するクエリでは大文字と小文字が区別されますが、 **[名前]** フィールドに対するクエリでは大文字と小文字が区別されません。

**[日付を次のように表示]** を切り替えると、 **[リスクの最終更新日時]** 列が非表示になります。 列を再度追加するには、[危険なユーザー] ブレードの上部にある **[列]** をクリックします。

従来の Identity Protection で**すべてのイベントを無視する**と、リスク検出の状態が **[クローズ (解決済み)]** に設定されます。

## <a name="risky-sign-ins-report-known-issues"></a>"リスクの高いサインイン レポート" の既知の問題

リスク検出で **[解決]** を行うと、状態が **[ユーザーが、リスク ベースのポリシーに要求された MFA に成功しました]** に設定されます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="why-is-a-user-is-at-risk"></a>ユーザーがリスクにさらされるのはなぜですか?

Azure AD Identity Protection のお客様であれば、[危険なユーザー](howto-identity-protection-investigate-risk.md#risky-users)のビューにアクセスして、危険度の高いユーザーをクリックしてください。 下部のドロワーの [リスクの履歴] タブに、ユーザー リスクが変化する原因となったすべてのイベントが表示されます。 そのユーザーの危険なサインインをすべて表示するには、[ユーザーの危険なサインイン] をクリックします。 このユーザーの危険の検出をすべて表示するには、[ユーザーのリスクの検出] をクリックします。

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>特定の種類の検出のレポートを取得する方法はありますか?

リスク検出のビューにアクセスして、[検出の種類] でフィルター処理します。 その後、上部の **[ダウンロード]** ボタンを使用して、.CSV または JSON 形式でこのレポートをダウンロードできます。 詳細については、記事「[方法: リスクを調査する方法](howto-identity-protection-investigate-risk.md#risk-detections)。

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>リスク検出ごとに独自のリスク レベルを設定できないのはなぜですか?

Identity Protection のリスク レベルは、検出の精度に基づいており、Microsoft の教師あり機械学習によって強化されています。 管理者は、ユーザー エクスペリエンスをカスタマイズするために、ユーザー リスク ポリシーとサインイン リスク ポリシーに対して特定のユーザーまたはグループを含めたり除外したりすることができます。

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>サインインの場所が、ユーザーが実際にサインインした場所と一致しないのはなぜですか?

IP の地理的位置情報のマッピングについては、業界全体の課題となっています。 サインイン レポートに記載されている場所が実際の場所と一致しないと思われる場合は、Microsoft サポートにご連絡ください。 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>以前の UI で行っていたように、特定のリスクの検出を終了する方法はありますか?

リンクされたサインインが侵害されているか、または安全であると確認することで、リスク検出に関するフィードバックを提供できます。 サインインに関するフィードバックは、そのサインインに対して行われたすべての検出に関連しています。 サインインにリンクされていない検出を終了する場合は、ユーザー レベルでそのフィードバックを提供することができます。 詳細については、[方法: Azure AD Identity Protection でリスクに関するフィードバックを提供する](howto-identity-protection-risk-feedback.md)」を参照してください。

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>ユーザーに何が起きているかを把握するために、どのくらいまで時間を遡ることができますか?

- [危険なユーザー](howto-identity-protection-investigate-risk.md#risky-users)のビューには、すべての過去のサインインに基づくユーザーのリスク期間が表示されます。 
- [危険なサインイン](howto-identity-protection-investigate-risk.md#risky-sign-ins)のビューには、過去 30 日間のリスクの高いサインインが表示されます。 
- [リスク検出](howto-identity-protection-investigate-risk.md#risk-detections)のビューには、過去 90 日間に見つかったリスクの検出が表示されます。

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>特定の検出に関する詳細を確認するには、どうすればよいですか?

「[リスクとは](concept-identity-protection-risks.md#risk-types-and-detection)」の記事に、すべてのリスクの検出について説明されています。 Azure portal 上の検出の横にある (i) 記号にマウスを合わせると、検出の詳細を確認できます。

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Identity Protection のフィードバック メカニズムはどのように機能しますか?

**侵害ありと確認する** (サインイン時) – サインインが ID 所有者によって実行されなかったことを Azure AD Identity Protection に通知し、侵害を示します。

- このフィードバックを受けて、サインインおよびユーザー リスクの状態が **[セキュリティ侵害の確認済み]** に設定され、リスク レベルが **[高]** に設定されます。

- さらに、リスク評価における今後の改善のために、Microsoft の機械学習システムに情報が送信されます。

    > [!NOTE]
    > ユーザーが既に修復されている場合は、 **[侵害ありと確認する]** をクリックしないでください。クリックすると、サインインおよびユーザー リスクの状態が **[セキュリティ侵害の確認済み]** に設定され、リスク レベルが **[高]** に設定されるためです。

**安全と確認する** (サインイン時) – サインインが ID 所有者によって実行されたことを Azure AD Identity Protection に通知し、侵害を示しません。

- このフィードバックを受けて、(ユーザーではなく) サインイン リスクの状態が **[安全であるとの確認済み]** に設定され、リスク レベルが **-** に設定されます。

- さらに、リスク評価における今後の改善のために、Microsoft の機械学習システムに情報が送信されます。

    > [!NOTE]
    > ユーザーが侵害されていないと思われる場合は、サインイン レベルで **[安全であるとの確認済み]** を使用するのではなく、ユーザー レベルで **[ユーザー リスクを無視する]** を使用してください。 ユーザー レベルで **[ユーザー リスクを無視する]** を使用すると、ユーザーリスク、および過去に発生したリスクの高いサインインとリスク検出がすべて閉じられます。

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Identity Protection にリスクの高いサインインやリスク検出が表示されていなくても、低 (またはそれ以上の) リスク スコアを持つユーザーが表示されるのはなぜですか?

ユーザー リスクが本質的には累積され、期限切れにならないことを考えると、直近のリスクの高いサインインやリスク検出が Identity Protection に表示されていない場合でも、ユーザーには低またはそれ以上のユーザー リスクが設定されている可能性があります。 この状況は、危険なサインインとリスク検出の詳細が保存される期間を超えて、ユーザーに対して単一の悪意のあるアクティビティが行われた場合に発生する可能性があります。 悪意のあるアクターがお客様の環境で ID を侵害した状態で 140 日以上経過してから攻撃を開始することが知られているため、ユーザー リスクが期限切れにならないように設定されています。 お客様はユーザーのリスクのタイムラインを確認して、ユーザーがリスクにさらされている理由を判断できます。これを行うには、次のようにします。 `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>サインインに関連付けられている検出のリスクが低または中の場合に、サインインの「サインイン リスク (集計)」スコアが「高」になるのはなぜですか?

集計リスク スコアが高であるのは、サインインの他の特徴、またはそのサインインに対して複数の検出が行われたという事実に基づく可能性があります。 逆に、サインインに関連付けられている検出のリスクが「高」の場合でも、サインインのサインイン リスク (集計) が「中」になることがあります。 
