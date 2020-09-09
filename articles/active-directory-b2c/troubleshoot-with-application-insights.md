---
title: Application Insights でのカスタム ポリシーのトラブルシューティング
titleSuffix: Azure AD B2C
description: カスタム ポリシーの実行を追跡するための Application Insights の設定方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9b5bc3f87296ea1af5de28178df6d8f27c965476
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116083"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Application Insights を使用して Azure Active Directory B2C のログを収集する

この記事では、カスタム ポリシーの問題を診断できるよう、Active Directory B2C (Azure AD B2C) からログを収集する手順を説明します。 Application Insights は、例外を診断したり、アプリケーションのパフォーマンスの問題を視覚化したりするための手段を提供します。 Azure AD B2C には、Application Insights にデータを送信するための機能が含まれます。

ここで説明する詳細なアクティビティ ログは、カスタム ポリシーを開発している間**だけ**、有効にする必要があります。

> [!WARNING]
> 運用環境では開発モードを有効にしないでください。 ログを使うと、ID プロバイダーとの間で送受信されるすべての要求を収集できます。 開発者には、Application Insights ログで収集されるすべての個人データに対する責任があります。 これらの詳細ログは、ポリシーが**開発者モード**で配置されている場合にのみ収集されます。

## <a name="set-up-application-insights"></a>Application Insights を設定する

サブスクリプションに Application Insights のインスタンスがまだない場合は、作成します。

1. [Azure portal](https://portal.azure.com) にサインインする
1. 上部のメニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、(Azure AD B2C のディレクトリではなく) お使いの Azure サブスクリプションが含まれるディレクトリを選択します。
1. 左のナビゲーション メニューで **[リソースの作成]** を選択します。
1. **Application Insights** を探して選択し、 **[作成]** を選択します。
1. フォームに漏れなく入力し、 **[確認および作成]** を選択してから、 **[作成]** を選択します。
1. デプロイが完了したら、 **[リソースに移動]** を選択します。
1. Application Insights のメニューの **[構成]** で、 **[プロパティ]** を選択します。
1. 後のステップで使用する **[インストルメンテーション キー]** を記録しておきます。

## <a name="configure-the-custom-policy"></a>カスタム ポリシーを構成する

1. 証明書利用者 (RP) ファイルを開きます (例: *SignUpOrSignin.xml*).
1. 次の属性を `<TrustFrameworkPolicy>` 要素に追加します:

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. まだ存在しない場合は、子ノード `<UserJourneyBehaviors>` を `<RelyingParty>` ノードに追加します。 `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` の直後に配置する必要があります。
1. 次のノードを `<UserJourneyBehaviors>` 要素の子として追加します。 必ず、`{Your Application Insights Key}` の部分を、前に記録した Application Insights の**インストルメンテーション キー**に置き換えます。

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * 処理パイプラインを通してテレメトリを迅速化するよう `DeveloperMode="true"` により Application Insights に指示されます。 開発には適していますが、大量のボリュームでは制限されます。
    * `ClientEnabled="true"` を指定すると、ページ ビューとクライアント側エラーを追跡するための Application Insights クライアント側スクリプトが送信されます。 これらは、Application Insights ポータルの **browserTimings** テーブルで確認できます。 `ClientEnabled= "true"` を設定することにより、Application Insights がページ スクリプトに追加され、ページの読み込みと AJAX 呼び出しのタイミング、ブラウザーの例外や AJAX エラーの数と詳細、ユーザー数とセッション数を取得できます。 このフィールドは**オプション**であり、既定では `false` に設定されます。
    * `ServerEnabled="true"` は、既存の UserJourneyRecorder JSON をカスタム イベントとして Application Insights に送信します。

    次に例を示します。

    ```xml
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. ポリシーをアップロードします。

## <a name="see-the-logs-in-application-insights"></a>Application Insights でログを表示する

Application Insights で新しいログが確認できるようになるまで、少し時間がかかります (通常は 5 分以内)。

1. [Azure Portal](https://portal.azure.com) で作成した Application Insights リソースを開きます。
1. **[概要]** メニューで、 **[分析]** を選択します。
1. Application Insights で新しいタブを開きます。

次に示すのは、ログを確認するために使用できるクエリの一覧です。

| クエリ | 説明 |
|---------------------|--------------------|
`traces` | Azure AD B2C によって生成されたすべてのログを確認します |
`traces | where timestamp > ago(1d)` | Azure AD B2C によって生成された直近 1 日分のすべてのログを確認します

エントリは長い可能性があります。 詳細を確認する際は CSV にエクスポートしてください。

クエリの実行について詳しくは、「[Azure Monitor のログ クエリの概要](../azure-monitor/log-query/log-query-overview.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

ID 開発者を支援するためのユーザー体験ビューアーがコミュニティによって開発されています。 このビューアーは、Application Insights インスタンスからデータを読み取り、ユーザー体験イベントの適切に構造化された表示を提供します。 ソース コードを入手し、独自のソリューションでデプロイできます。

ユーザー体験プレーヤーは Microsoft によってサポートされておらず、厳密に現状のまま利用可能です。

Application Insights からイベントを読み取るビューアーのバージョンは、GitHub の次の場所にあります。

[Azure-Samples/active-directory-b2c-advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
