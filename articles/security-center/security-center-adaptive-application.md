---
title: Azure Security Center におけるアダプティブ アプリケーション制御
description: このドキュメントは、Azure コンピューターで実行されるアプリケーションのホワイトリストを作成するために、Azure Security Center でアダプティブ アプリケーション制御を使用する際に役に立ちます。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/23/2019
ms.author: memildin
ms.openlocfilehash: 1dc94c5ec08cc27fb1819ccc16fd766c62aad796
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604682"
---
# <a name="adaptive-application-controls"></a>アダプティブ アプリケーション制御
このチュートリアルを使用して、Azure Security Center でアプリケーション制御を構成する方法を説明します。

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Security Center でのアダプティブ アプリケーション制御とは
適応型アプリケーション制御は、お使いの Azure および Azure 以外のコンピューター (Windows および Linux) 上でどのアプリケーションが実行できるかを制御できる、Azure Security Center によるインテリジェントかつ自動化されたエンドツーエンドのソリューションです。 いくつかの利点のうち、これは特にマルウェアからコンピューターを強化することに役立ちます。 Security Center では、機械学習を使用して、お使いのコンピューター上で実行されているアプリケーションを分析し、このインテリジェンスから許可リストを作成します。 この機能を使用すると、アプリケーション許可リスト ポリシーを構成および保守するプロセスが非常にシンプルになり、以下のことが実現できます。

- 悪意のあるアプリケーション (マルウェア対策ソリューションでは見逃される可能性のあるものを含む) の実行が試行されるのをブロックまたは警告する。
- ライセンスのあるソフトウェアのみを使用するという組織のセキュリティ ポリシーに準拠する。
- 環境内で使用されることが望ましくないソフトウェアを避ける。
- 古くてサポートされていないアプリが実行されることを避ける。
- 組織で許可されていない特定のソフトウェア ツールが実行されないようにする。
- アプリの使用状況を通じて、機密データへのアクセスを IT 部門が制御できるようにする。

> [!NOTE]
> Azure 以外のコンピューターおよび Linux コンピューターについては、監査モードでのみ適応型アプリケーション制御がサポートされています。

## <a name="how-to-enable-adaptive-application-controls"></a>アダプティブ アプリケーション制御を有効にする方法

適応型アプリケーション制御は、構成済みのコンピューターのグループに対する実行が許可される一連のアプリケーションを定義するのに役立ちます。 この機能は、Azure と Azure 以外の Windows (すべてのバージョン、クラシック、または Azure Resource Manager) コンピューターおよび Linux コンピューターで使用できます。 アプリケーションの許可リストを構成するには、次の手順に従います。

1. **[Security Center]** ダッシュボードを開きます。

1. 左側のウィンドウで、 **[高度なクラウド防御]** の **[適応型アプリケーション制御]** を選択します。

    [![防衛](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png#lightbox)

**[適応型アプリケーション制御]** ページが表示されます。

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

**[Groups of VMs]\(VM のグループ\)** セクションには、次の 3 つのタブがあります。

* **[構成済み]** : アプリケーション制御で構成された VM が含まれているグループの一覧。
* **[推奨]** : アプリケーション制御が推奨されるグループの一覧。 Security Center では機械学習が使用され、VM が常に同じアプリケーションを実行しているかどうかに基づいて、アプリケーション制御の候補となる VM が特定されます。
* **[推奨なし]** : アプリケーション制御の推奨がない VM が含まれているグループの一覧。 たとえば、実行されるアプリケーションが常に変化していて、一定の状態にならない VM などです。

> [!NOTE]
> Security Center は、財産的価値のあるクラスタリング アルゴリズムを使用して VM のグループを作成します。これにより、同様の VM には、最適な推奨アプリケーション制御ポリシーが確実に適用されます。
>
>

### <a name="configure-a-new-application-control-policy"></a>新しいアプリケーション制御ポリシーの構成

1. アプリケーション制御の推奨があるグループの一覧を表示するには、 **[推奨]** タブを選択します。

   ![推奨](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   一覧には次の項目が含まれています。

   - **グループ名**: サブスクリプションとグループの名前
   - **VM およびコンピューター**: グループ内の仮想マシンの数
   - **状態**: 推奨事項の状態
   - **重大度**: 推奨事項の重大度レベル

2. グループをクリックして、 **[アプリケーションの制御に関する規則の作成]** オプションを開きます。

   [![アプリケーション制御規則](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. **[VM の選択]** で、推奨されている VM の一覧を確認し、アプリケーションのホワイトリスト登録ポリシーを適用しないすべての VM のチェック ボックスをオフにします。 今度は、次の 2 つの一覧が表示されます。

   - **[推奨アプリケーション]** : このグループ内の VM での使用頻度が高く、実行を許可することが推奨されるアプリケーションの一覧。
   - **[その他のアプリケーション]** : このグループ内の VM での使用頻度が低いか、または "利用可能" (下記参照) として知られていることから、確認することが推奨されるアプリケーションの一覧。

4. それぞれの一覧のアプリケーションを確認し、適用したくないアプリケーションについてはチェック ボックスをオフにします。 各一覧には次の項目が含まれています。

   - **[名前]** : 証明書情報またはアプリケーションの完全なパス
   - **[ファイルの種類]** : アプリケーションのファイルの種類。 これは、EXE、スクリプト、MSI、またはこれらの種類の任意の順列になります。
   - **[利用可能]** : 警告アイコンは、アプリケーションの許可リストをバイパスするために、特定のアプリケーションが攻撃者によって使用される恐れがある場合を示します。 これらのアプリケーションは、承認前に確認することをお勧めします。
   - **[ユーザー]** : アプリケーションの実行を許可することが推奨されるユーザー

5. 選択を終了したら、 **[作成]** を選択します。 <br>
   [作成] を選択すると、Windows サーバー (AppLocker) 上で使用可能な組み込みアプリケーションの許可リスト ソリューションに対応する適切な規則が、Azure Security Center によって自動的に作成されます。

> [!NOTE]
> - VM のグループごとにベースラインを作成し、固有の推奨事項を提示するために、Security Center では最低 2 週間分のデータが必要となります。 Security Center Standard レベルを初めてご利用になる場合、最初は一連の VM が *[推奨なし]* タブに表示されます。
> - Security Center からのアダプティブ アプリケーション制御では、GPO またはローカル セキュリティ ポリシーによって既に AppLocker ポリシーが有効になっている VM はサポートされていません。
> -  Security Center では、セキュリティのベスト プラクティスとして、許可されるアプリケーションとして選択されるアプリケーションに対して発行元規則の作成が常に試行されます。アプリケーションに発行元情報がない (つまり署名されていない) 場合にのみ、特定のアプリケーションの完全なパスに対するパス規則が作成されます。
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>アプリケーション制御で構成されたグループの編集および監視

1. アプリケーションの許可リスト ポリシーを利用して構成されたグループを編集および監視するには、 **[適応型アプリケーション制御]** ページに戻り、 **[VM のグループ]** 下にある **[構成済み]** を選択します。

   ![グループ](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   一覧には次の項目が含まれています。

   - **グループ名**: サブスクリプションとグループの名前
   - **VM およびコンピューター**: グループ内の仮想マシンの数
   - **[モード]** :監査モードでは、許可リストにないアプリケーションの実行を試行すると、ログに記録されます。強制の場合は、許可リストにない限り、アプリケーションの実行は許可されません
   - **[Alerts]\(アラート\)** : 現在のすべての違反

2. **[アプリケーション制御ポリシーの編集]** ページで変更を行うには、グループをクリックします。

   ![保護](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. **[保護モード]** では、次のいずれかを選択できます。

   - **[監査]** : このモードでは、アプリケーション制御ソリューションによって規則が強制されず、保護されている VM のアクティビティの監査だけが行われます。 このオプションをお勧めするのは、ターゲット VM でアプリの実行をブロックする前に、まず全体的な動作を観察したい場合です。
   - **[強制]** : このモードでは、アプリケーション制御ソリューションによって規則が強制され、実行を許可されていないアプリケーションがブロックされます。

   > [!NOTE]
   > -  **強制**保護モードは、今後さらなる通知があるまでは無効になります。
   > - 既に説明したように、既定では新しいアプリケーション制御ポリシーは常に "*監査*" モードで構成されます。 
   >

4. **[ポリシーの拡張機能]** で、許可したい任意のアプリケーションのパスを追加します。 これらのパスを追加すると、Security Center によって、選択された VMS グループ内にある VM に対するアプリケーションの許可リスト ポリシーが更新されます。また、既に設定されている規則に加えて、これらのアプリケーションのための適切な規則が作成されます。

5. **[最近のアラート]** セクションに一覧表示されている現在の違反を確認します。 各行をクリックすると、Azure Security Center 内の **[Alerts]\(アラート\)** ページにリダイレクトされ、関連付けられた VM で Azure Security Center によって検出されたすべてのアラートが表示されます。
   - **[Alerts]\(アラート\)** : ログに記録されたすべての違反。
   - **[VM の数]** : この種類のアラートがある仮想マシンの数。

6. **[発行元のホワイトリスト登録に関する規則]** 、 **[パスのホワイトリスト登録に関する規則]** 、 **[ハッシュのホワイトリスト登録に関する規則]** では、規則コレクションの種類に従って、グループ内の VM 上で現在どのアプリケーションのホワイトリスト登録に関する規則が構成されているかを確認できます。 各規則について、次の内容が表示されます。

   - **[Rule]\(規則\)** :アプリケーションの実行が許可されるかどうかを判断するために AppLocker がアプリケーションを調べる際に使用される特定のパラメーター。
   - **[ファイルの種類]** :特定の規則の対象となるファイルの種類。 これは次のいずれかになります。EXE、スクリプト、MSI、またはそれらのファイルの種類で構成された任意の順列。
   - **[ユーザー]** :アプリケーションのホワイトリスト登録に関する規則の対象となるアプリケーションの実行が許可されているユーザーの名前または人数。

   ![ホワイトリスト登録に関する規則](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. 特定の規則を削除する場合、または許可されたユーザーを編集する場合は、各行の末尾にある 3 つの点をクリックします。

8. **適応型アプリケーション制御**ポリシーに変更を加えた後、 **[保存]** をクリックします。

### <a name="not-recommended-list"></a>非推奨の一覧

Security Center では、安定した一連のアプリケーションが実行されている仮想マシンについてのみ、アプリケーションのホワイトリスト登録ポリシーを推奨しています。 関連する VM 上のアプリケーションが常に異なる場合、推奨は作成されません。

![推奨](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

一覧には、以下が含まれています。
- **グループ名**: サブスクリプションとグループの名前
- **VM およびコンピューター**: グループ内の仮想マシンの数

Azure Security Center では、推奨されない VM のグループに対してもアプリケーションのホワイトリスト登録ポリシーを定義することができます。 これらのグループに対するアプリケーションのホワイトリスト登録ポリシーを構成する場合も、前述の同じ原則に従ってください。

## <a name="move-a-vm-from-one-group-to-another"></a>VM をあるグループから別のグループに移動する

 VM をあるグループから別のグループに移動すると、適用されていたアプリケーション制御ポリシーが移動先のグループの設定に変更されます。 また、構成済みのグループから未構成のグループに VM を移動することもできますが、その結果、VM に適用されていたアプリケーション制御ポリシーが削除されます。

 1. **[適応型アプリケーション制御]** ページの **[構成済み]** タブから、移動対象の VM が現在属しているグループをクリックします。
1. **[構成された VM とコンピューター]** をクリックします。
1. VM の行で 3 つのドットをクリックし、 **[移動]** をクリックします。 **[コンピューターを別のグループに移動する]** ウィンドウが開きます。

    ![保護](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. VM の移動先のグループを選択して **[コンピューターの移動]** をクリックし、 **[保存]** をクリックします。

    ![保護](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> **[コンピューターの移動]** をクリックしたら、必ず **[保存]** をクリックしてください。 **[保存]** をクリックしないと、コンピューターは移動されません。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure と Azure 以外の VM で実行されるアプリケーションをホワイトリストに登録するために、Azure Security Center で適応型アプリケーション制御を使用する方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
