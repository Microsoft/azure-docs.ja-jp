---
title: アラート抑制ルールを使用することにより、Azure Security Center で擬陽性や、その他の不要なセキュリティ アラートが発生するのを抑制できます。
description: この記事では、Azure Security Center の抑制ルールを使用して、不要なセキュリティ アラートが表示されないようにする方法を説明します。
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 05/04/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 341373c9a8429f335f3064db7a94973d34e0ca1c
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042502"
---
# <a name="suppress-alerts-from-azure-security-centers-threat-protection"></a>Azure Security Center の脅威の防止からのアラートを抑制する

このページでは、アラート抑制ルールを使用することにより、Azure Security Center で擬陽性や他の不要なセキュリティ アラートが発生するのを抑制する方法について説明します。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|プレビュー|
|価格:|Free レベル<br>(ほとんどのセキュリティ アラートは Standard レベルのみを対象とします)|
|必要なロールとアクセス許可:|**セキュリティ管理者**および**所有者**は、ルールを作成および削除できます。<br>**セキュリティ閲覧者**および**閲覧者**は、ルールを表示できます。|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||




## <a name="what-are-suppression-rules"></a>抑制ルールとは

Azure Security Center の脅威防止コンポーネントでは、ユーザーの環境のいずれかの領域で脅威を検出すると、セキュリティ アラートが生成されます。

1 つのアラートが特に注意する必要がないか無関係である場合は、手動で無視することもできます。 そうする代わりに、抑制ルール機能を使用して、今後は同様のアラートを自動的に無視することもできます。 通常は、次のような場合に抑制ルールを使用します。

- ユーザーが擬陽性と判断したアラートを抑制する

- あまりにも頻繁にトリガーされるため有用ではないアラートを抑制する

抑制ルールは、アラートが自動的に無視される条件を定義します。

> [!CAUTION]
> セキュリティ アラートを抑制すると、Security Center の脅威防止は低下します。 抑制ルールを使用する場合は、その潜在的な影響を慎重に確認し、長期的に監視する必要があります。

![アラートの抑制ルールの作成](media\alerts-suppression-rules\create-suppression-rule.gif)

## <a name="create-a-suppression-rule"></a>抑制ルールを作成する

次のいくつかの方法で、不要なセキュリティ アラートを抑制するルールを作成できます。

- 管理グループ レベルでアラートを抑制するには、Azure Policy を使用します。

- サブスクリプション レベルでアラートを抑制するには、以下で説明するように、Azure portal または REST API を使用できます。

抑制ルールで無視できるのは、選択したサブスクリプションで既にトリガーされたアラートのみです。

Azure portal で直接ルールを作成するには:

1. Security Center の [セキュリティ アラート] ページから:

    - 今後表示される必要のない特定のアラートを見つけて、そのアラートの省略記号メニュー (...) から **[Create suppression rule]\(抑制ルールの作成\)** を選択します。

        [![**[Create suppression rule]\(抑制ルールの作成\)** オプション](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox)

    - または、ページの上部にある **[抑制ルール]** リンクを選択し、[抑制ルール] ページで **[新しい抑制ルールの作成]** を選択します。

        ![[新しい抑制ルールの作成]** ボタン](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. [新しい抑制ルール] ペインで、作成した新しいルールの詳細を入力します。

    - ルールでは、**すべてのリソース**でそのアラートを無視して、今後はこれと同様のアラートが一切表示されないようにすることができます。 
    
    - ルールでは、アラートが特定の IP アドレス、プロセス名、ユーザー アカウント、Azure リソース、または場所に関係する場合など、**特定の条件**でそのアラートを無視することもできます。

    > [!TIP]
    > 特定のアラートから新しいルールのページを開いた場合は、そのアラートとサブスクリプションが新しいルールに自動的に構成されます。 **[新しい抑制ルールの作成]** リンクを使用した場合は、選択したサブスクリプションはポータルの現在のフィルターと一致します。

    [![抑制ルールの作成ペイン](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)

1. ルールの詳細を入力します。

    - **名前** - ルールの名前。 ルール名は、文字または数字で始まり、2 から 50 文字で指定する必要があります。ダッシュ (-) とアンダースコア (_) 以外の記号は使用できません。 
    - **状態** - 有効または無効。
    - **理由** - あらかじめ登録された理由のいずれかを選択するか、合うものがない場合は [その他] を選択します。
    - **有効期限** - そのルールの終了日時。 ルールは最大 6 か月間実行できます。

1. 必要に応じて、 **[シミュレート]** ボタンを使用してルールをテストし、そのルールがアクティブだったなら、どれほどのアラートが無視されていたかを確認することができます。

1. ルールを保存します。 

## <a name="edit-a-suppression-rules"></a>抑制ルールを編集する

作成したルールを編集する場合は、[抑制ルール] ページを使用します。

1. Security Center の [セキュリティ アラート] ページから、そのページの上部の **[抑制ルール]** リンクを選択します。

1. [抑制ルール] ページが開き、選択したサブスクリプションのすべてのルールが表示されます。

    [![抑制ルールの一覧](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. 1 つのルールを編集する場合は、そのルールの省略記号メニュー (...) を開き、 **[編集]** を選択します。

1. 必要な変更を加えて、 **[適用]** を選択します。 

## <a name="delete-a-suppression-rule"></a>抑制ルールを削除する

作成した 1 つまたは複数のルールを削除する場合は、[抑制ルール] ページを使用します。

1. Security Center の [セキュリティ アラート] ページから、そのページの上部の **[抑制ルール]** リンクを選択します。

1. [抑制ルール] ページが開き、選択したサブスクリプションのすべてのルールが表示されます。

1. 1 つのルールを削除する場合は、そのルールの省略記号メニュー (...) を開き、 **[削除]** を選択します。

1. 複数のルールを削除する場合は、削除するルールのチェック ボックスを選択し、 **[削除]** を選択します。

    ![1 つまたは複数の抑制ルールの削除](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="view-suppressed-alerts"></a>抑制されたアラートを表示する

有効にした抑制ルールと一致するアラートは、引き続き生成されますが、その状態は **[dismissed]\(無視\)** に設定されます。 その状態は、Azure portal でも確認できますが、Security Center の [セキュリティ アラート] にアクセスして確認することもできます。 

> [!TIP]
> [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) では、抑制されたアラートのインシデントは作成されません。 他の SIEM の場合は、そのアラートの状態 ([dismissed]\(無視\)) を使用すれば、抑制されたアラートをフィルター処理できます。

作成したルールに従って無視されたアラートを表示するには、Security Center のフィルターを使用します。

* Security Center の [セキュリティ アラート] ページから、フィルター オプションを開き、 **[Dismissed]\(無視\)** を選択します。  

   [![無視されたアラートの表示](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="create-and-manage-suppression-rules-with-the-api"></a>API を使用して抑制ルールの作成および管理を行う

Security Center の REST API を使用して、アラート抑制ルールを作成、表示、または削除することができます。 

抑制ルールに関連する REST API の HTTP メソッドは次のとおりです。

- **PUT**: 指定されたサブスクリプションで抑制ルールを作成または更新する。

- **GET**:

    - 指定されたサブスクリプションに対して構成されているすべてのルールを一覧表示する。 このメソッドは、該当するルールの配列を返します。

    - 指定されたサブスクリプションの特定のルールの詳細を取得する。 このメソッドは、1 つの抑制ルールを返します。

    - 設計フェーズのままで、抑制ルールの影響をシミュレートする。 この呼び出しは、そのルールがアクティブだったなら無視されていたはずの既存のアラートを識別します。

- **DELETE**: 既存のルールを削除します (ただし、そのルールによって既に無視されているアラートの状態は変更されません)。

詳細と使用例については、[API のドキュメント](https://docs.microsoft.com/rest/api/securitycenter/)を参照してください。 


## <a name="next-steps"></a>次のステップ

この記事では、不要なアラートを自動的に無視する、Azure Security Center の抑制ルールについて説明しました。

Azure Security Center のセキュリティ アラートの詳細については、次のページを参照してください。

- [セキュリティ アラートと意図のキル チェーン](alerts-reference.md) - Azure Security Center の Threat Protection モジュールで表示される可能性のあるセキュリティ アラートのリファレンス ガイドです。
- [Azure Security Center での脅威の防止](threat-protection.md) - Azure Security Center の Threat Protection モジュールによって監視される、ユーザーの環境のさまざまな側面について説明しています。