---
title: Windows 用 Azure シリアル コンソール | Microsoft Docs
description: Azure Virtual Machines および仮想マシン スケール セット用の双方向シリアル コンソール。
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: c30999a5f0239e60c842084b60b44c165fb7182e
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424002"
---
# <a name="azure-serial-console-for-windows"></a>Windows 用 Azure シリアル コンソール

Azure portal のシリアル コンソールでは、Windows 仮想マシン (VM) および仮想マシン スケール セット インスタンス用のテキスト ベースのコンソールへのアクセスが提供されます。 このシリアル接続は、ネットワークやオペレーティング システムの状態には関係なく、VM または仮想マシン スケール セット インスタンスの COM1 シリアル ポートに接続してそのポートへのアクセスを提供します。 このシリアル コンソールは Azure portal を使用してのみアクセスでき、VM または仮想マシン スケール セットへの共同作成者以上のアクセス ロールを持つユーザーに対してのみ許可されます。

シリアル コンソールは、VM と仮想マシン スケール セット インスタンスに対して同じ方法で動作します。 このドキュメントでは、特に記載のない限り、VM という記述にはすべて仮想マシン スケール セット インスタンスが暗黙的に含まれます。

シリアル コンソールは、グローバル Azure リージョンで一般公開されており、Azure Government ではパブリック プレビュー段階にあります。 Azure China Cloud ではまだ利用できません。

Linux のシリアル コンソールのドキュメントについては、「[Linux 用 Azure シリアル コンソール](serial-console-linux.md)」をご覧ください。

> [!NOTE]
> シリアル コンソールは、現在マネージド ブート診断ストレージ アカウントとの完全な互換性がありません。 シリアル コンソールを使用するには、カスタム ストレージ アカウントを使用していることを確認してください。


## <a name="prerequisites"></a>前提条件

* VM または仮想マシン スケール セット インスタンスは、リソース管理デプロイ モデルを使用する必要があります。 クラシック デプロイはサポートされていません。

- シリアル コンソールを使用するアカウントには、VM の[仮想マシン共同作成者ロール](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)および[ブート診断](boot-diagnostics.md)ストレージ アカウントが必要です

- VM または仮想マシン スケール セット インスタンスには、パスワード ベースのユーザーが必要です。 このアカウントは、VM アクセス拡張機能の[パスワードのリセット](../extensions/vmaccess.md#reset-password)機能を使用して作成することができます。 **[サポート + トラブルシューティング]** セクションの **[パスワードのリセット]** を選択します。

* 仮想マシン スケール セット インスタンス用 VM では、[ブート診断](boot-diagnostics.md)が有効になっている必要があります。

    ![ブート診断の設定](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Windows Server 向けのシリアル コンソール機能を有効にする

> [!NOTE]
> シリアル コンソールに何も表示されていない場合は、VM または仮想マシン スケール セットでブート診断が有効になっていることを確認してください。

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>カスタム イメージまたは古いイメージでシリアル コンソールを有効にする
Azure の新しい Windows Server イメージでは、既定で [Special Administration Console](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v=ws.10)) (SAC) が有効です。 SAC は Windows のサーバー バージョンではサポートされていますが、クライアント バージョン (Windows 10、Windows 8、Windows 7 など) では使用できません。

以前の Windows Server イメージ (2018 年 2 月より前に作成されたもの) では、Azure portal の実行コマンド機能を使用してシリアル コンソールを自動的に有効にすることができます。 Azure Portal で、 **[実行コマンド]** を選択し、一覧から **EnableEMS** という名前のコマンドを選択します。

![実行コマンドの一覧](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

あるいは、2018 年 2 月より前に作成された Windows VM/仮想マシン スケール セット用のシリアル コンソールを手動で有効にするには、次の手順に従います。

1. リモート デスクトップを使用して Windows 仮想マシンに接続します。
1. 管理コマンド プロンプトで次のコマンドを実行します。
    - `bcdedit /ems {current} on`、PowerShell の場合は `bcdedit /ems '{current}' on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. システムを再起動して、SAC コンソールを有効にします。

    ![SAC コンソール](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

必要に応じて、SAC をオフラインでも有効にすることができます。

1. SAC を構成する Windows ディスクをデータ ディスクとして既存の VM にアタッチします。

1. 管理コマンド プロンプトで次のコマンドを実行します。
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>SAC が有効になっているかどうかを知る方法

[SAC](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v=ws.10)) が有効になっていない場合、シリアル コンソールに SAC プロンプトは表示されません。 VM の正常性情報が表示される場合もありますが、それ以外は空白になります。 2018 年 2 月より前に作成された Windows Server イメージを使用している場合、SAC が有効になっていない可能性があります。

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>シリアル コンソールの Windows ブート メニューの有効化

Windows ブート ローダーのプロンプトを有効にしてシリアル コンソールに表示する必要がある場合は、ブート構成データに次のオプションを追加します。 詳細については、[bcdedit](/windows-hardware/drivers/devtest/bcdedit--set) に関するページをご覧ください。

1. リモート デスクトップを使用して、Windows VM または仮想マシン スケール セット インスタンスに接続します。

1. 管理コマンド プロンプトで次のコマンドを実行します。
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. システムを再起動して、ブート メニューを有効にします。

> [!NOTE]
> ブート マネージャー メニューの表示に対して設定するタイムアウトは、OS ブート時間に影響します。 10 秒のタイムアウト値が短すぎるか、長すぎると思われる場合は、別の値に設定します。

## <a name="use-serial-console"></a>シリアル コンソールの使用

### <a name="use-cmd-or-powershell-in-serial-console"></a>シリアル コンソールで CMD または PowerShell を使用する

1. シリアル コンソールに接続します。 正常に接続できたら、プロンプトは **SAC>** になります。

   ![SAC に接続する](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)
1. 「`cmd`」と入力して、CMD インスタンスがあるチャネルを作成します。

1. 「`ch -si 1`」と入力するか、`<esc>+<tab>` のショートカット キーを押して、CMD インスタンスを実行しているチャネルに切り替えます。

1. **Enter** キーを押して、管理アクセス許可を持つサインイン資格情報を入力します。

1. 有効な資格情報を入力すると、CMD インスタンスが開きます。

1. PowerShell インスタンスを起動するには、CMD インスタンスに「`PowerShell`」と入力し、**Enter** キーを押します。

   ![PowerShell インスタンスを開く](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>NMI 呼び出しにシリアル コンソールを使用する
マスク不可能割り込み (NMI) は、仮想マシン上のソフトウェアで無視されない信号を作成するために設計されています。 従来より、NMI は、特定の応答時間を要したシステム上でのハードウェアの問題を監視するために使用されてきました。 現在、プログラマーやシステム管理者は、応答していないシステムのデバッグやトラブルシューティングのためのメカニズムとして、NMI をよく使用しています。

シリアル コンソールは、コマンド バーのキーボード アイコンを使用して、NMI を Azure 仮想マシンに送信するために使用できます。 NMI が配信された後は、仮想マシン構成によってシステムの応答方法が制御されます。 NMI を受信したときにメモリ ダンプ ファイルをクラッシュして作成するように Windows を構成できます。

![NMI を送信する](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

NMI を受信したときにクラッシュ ダンプ ファイルを作成するように Windows を構成する方法については、「[NMI を使用してクラッシュ ダンプ ファイルを生成する方法](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)」を参照してください。

### <a name="use-function-keys-in-serial-console"></a>シリアル コンソールでファンクション キーを使用する
Windows VM のシリアル コンソールでファンクション キーを有効にして使用することができます。 シリアル コンソールのドロップダウンで F8 キーを押すと、詳細ブート設定メニューを簡単に表示することができますが、シリアル コンソールは他のすべてのファンクション キーと互換性があります。 シリアル コンソールを使用しているコンピューターによっては、キーボードの **Fn** + **F1** (または F2、F3 など) を押すことが必要になる場合があります。

### <a name="use-wsl-in-serial-console"></a>シリアル コンソールで WSL を使用する
Windows Subsystem for Linux (WSL) は Windows Server 2019 以降で有効なので、Windows Server 2019 以降を実行している場合は、シリアル コンソール内で WSL を有効にして使用することもできます。 この点は、Linux コマンドも熟知しているユーザーに役立つ可能性があります。 WSL for Windows Server を有効にする手順については、[インストール ガイド](/windows/wsl/install-on-server)に関するページを参照してください。

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>シリアル コンソール内で Windows VM/仮想マシン スケール セット インスタンスを再起動します
電源ボタンに移動し、[Restart VM] (VM の再起動) をクリックすることによって、シリアル コンソール内で再起動を開始できます。 これにより VM の再起動が始まり、Azure portal 内に再起動に関する通知が表示されます。

これは、シリアル コンソールから離れることなくブート メニューにアクセスしたい状況で役立ちます。

![Windows シリアル コンソールでの再起動](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>シリアル コンソールを無効にする
既定では、すべてのサブスクリプションは、シリアル コンソールのアクセスが有効になっています。 サブスクリプション レベルまたは VM/仮想マシン スケール セット レベルのいずれかで、シリアル コンソールを無効にすることができます。 詳細な手順については、「[Azure シリアル コンソールの有効化と無効化](./serial-console-enable-disable.md)」を参照してください。

## <a name="serial-console-security"></a>シリアル コンソールのセキュリティ

### <a name="access-security"></a>アクセス セキュリティ
シリアル コンソールへのアクセスは、仮想マシンに対する[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)以上のアクセス ロールを持つユーザーだけが限定されています。 シリアル コンソールには [Azure portal](https://portal.azure.com) からアクセスするため、Azure Active Directory テナントに多要素認証が必要な場合は、シリアル コンソールへのアクセスにも MFA が必要になります。

### <a name="channel-security"></a>チャネル セキュリティ
やり取りされるすべてのデータがネットワーク上で暗号化されます。

### <a name="audit-logs"></a>監査ログ
現在、シリアル コンソールへのすべてのアクセスが、仮想マシンの[ブート診断](./boot-diagnostics.md)ログに記録されます。 これらのログへのアクセスは、Azure 仮想マシン管理者が所有し、制御します。

> [!CAUTION]
> コンソールのアクセス パスワードはログに記録されません。 ただし、コンソール内で実行されるコマンドにパスワード、シークレット、ユーザー名、またはその他の形式の個人を特定できる情報 (PII) が含まれていたり、出力されたりした場合、それらの情報は VM のブート診断ログに書き込まれます。 それらは、シリアル コンソールのスクロールバック機能の実装の一部として、表示される他のすべてのテキストと共に書き込まれます。 これらのログは循環型であり、診断ストレージ アカウントに対する読み取りアクセス許可を持つユーザーだけがアクセスできます。 ただし、シークレットや PII が含まれている可能性のあるものにはリモート デスクトップを使用するというベスト プラクティスに従うことをお勧めします。

### <a name="concurrent-usage"></a>同時使用
ユーザーがシリアル コンソールに接続しているときに、別のユーザーがその同じ仮想マシンへのアクセスを要求し、その要求が成功した場合、最初のユーザーが切断され、2 番目のユーザーが同じセッションに接続されます。

> [!CAUTION]
> これは、切断されたユーザーはログアウトされないことを意味します。(SIGHUP または同様のメカニズムを使用して) 切断時に強制的にログアウトする機能は、まだロードマップにあります。 Windows では SAC で自動タイムアウトが有効になっていますが、Linux ではターミナルのタイムアウト設定を構成できます。

## <a name="accessibility"></a>アクセシビリティ
アクセシビリティは、Azure シリアル コンソールの重点事項です。 そのため、視覚や聴覚に障碍があるユーザーや、マウスを使用できない可能性があるユーザーがシリアル コンソールにアクセスできるようにしました。

### <a name="keyboard-navigation"></a>キーボード ナビゲーション
Azure portal からシリアル コンソール インターフェイスでナビゲートするには、キーボードの **Tab** キーを使用します。 現在の場所が画面上で強調表示されます。 シリアル コンソール ウィンドウのフォーカスを解除するには、キーボードの **Ctrl**+**F6** キーを押します。

### <a name="use-the-serial-console-with-a-screen-reader"></a>スクリーン リーダーでシリアル コンソールを使用する
シリアル コンソールには、スクリーン リーダーのサポートが組み込まれています。 スクリーン リーダーを有効にしてナビゲートすると、現在選択されているボタンの代替テキストをスクリーン リーダーで読み上げることができます。

## <a name="common-scenarios-for-accessing-the-serial-console"></a>シリアル コンソールにアクセスする一般的なシナリオ

シナリオ          | シリアル コンソールでのアクション
:------------------|:-----------------------------------------
不適切なファイアウォール規則 | シリアル コンソールにアクセスし、Windows ファイアウォール規則を修正します。
ファイル システムの破損/チェック | シリアル コンソールにアクセスし、ファイルシステムを復旧します。
RDP 構成の問題 | シリアル コンソールにアクセスし、設定を変更します。 詳しくは、[RDP のドキュメント](/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)をご覧ください。
システムのネットワーク ロックダウン | Azure portal からシリアル コンソールにアクセスして、システムを管理します。 一部のネットワーク コマンドについては、「[Windows コマンド - CMD と PowerShell](serial-console-cmd-ps-commands.md)」を参照してください。
ブートローダーの操作 | シリアル コンソールを使用して BCD にアクセスします。 詳細については、「[シリアル コンソールの Windows ブート メニューの有効化](#enable-the-windows-boot-menu-in-the-serial-console)」を参照してください。

## <a name="known-issues"></a>既知の問題
Microsoft は、シリアル コンソールおよび VM のオペレーティング システムには問題がいくつかあることを認識しています。 Windows VM のそのような問題と軽減手順を以下に示します。 これらの問題と軽減策は、VM と仮想マシン スケール セット インスタンスの両方に適用されます。 表示されているエラーと一致しない場合は、[シリアル コンソールに関する一般的なエラー](./serial-console-errors.md)に関する記事で一般的なシリアル コンソール サービス エラーを参照してください。

問題                             |   対応策
:---------------------------------|:--------------------------------------------|
接続バナーの後に **Enter** キーを押しても、サインイン プロンプトが表示されない。 | 詳細については、[Enter キーを押しても何も実行されない](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)問題に関するページを参照してください。 このエラーは、Windows がシリアル ポートに正常に接続できない原因となるカスタム VM、堅牢化されたアプライアンス、ブート構成を実行している場合に発生する可能性があります。 このエラーは Windows 10 VM を実行している場合にも発生します。Windows Server VM のみが EMS を有効にするように構成されているからです。
Windows VM に接続したときに、正常性情報だけが表示される| このエラーは、Windows イメージ用に Special Administration Console が有効になっていない場合に発生します。 Windows VM で SAC を手動で有効にする方法については、「[カスタム イメージまたは古いイメージでシリアル コンソールを有効にする](#enable-the-serial-console-in-custom-or-older-images)」を参照してください。 詳細については、[Windows 正常性シグナル](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)に関するページをご覧ください。
SAC がブラウザーのシリアル コンソール領域全体に表示されない | これは、Windows とターミナル エミュレーターに関する既知の問題です。 両方のチームでこの問題を追跡していますが、現在は軽減策がありません。
カーネル デバッグを有効にすると、SAC プロンプトで入力できない。 | VM に RDP で接続して、管理者特権でのコマンド プロンプトから `bcdedit /debug {current} off` を実行します。 RDP で接続できない場合は、代わりに OS ディスクを別の Azure VM に接続して、データ ディスクとしてアタッチされている間に `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` を実行して変更し、ディスクを戻すこともできます。
元のコンテンツに反復する文字が含まれる場合、SAC の PowerShell に貼り付けると 3 文字目が生成される。 | 回避策は、`Remove-Module PSReadLine` を実行して、現在のセッションから PSReadLine モジュールをアンロードすることです。 このアクションは、モジュールを削除またはアンインストールしません。
一部のキーボード入力で、不適切な SAC 出力が生成される (例: **[A**、 **[3~** )。 | [VT100](https://aka.ms/vtsequences) エスケープ シーケンスは SAC プロンプトでサポートされていません。
長い文字列を貼り付けると機能しない。 | シリアル コンソールでは、シリアル ポートの帯域幅に対する過負荷を防止するために、ターミナルに貼り付けられる文字列の長さが 2048 文字に制限されます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q.フィードバックを送信するにはどうすればよいですか?**

A. GitHub の問題を作成したフィードバックの提供 https://aka.ms/serialconsolefeedback します。 (あまりお勧めしませんが) azserialhelp@microsoft.com、または https://feedback.azure.com の仮想マシン カテゴリでフィードバックをお送りいただくこともできます。

**Q.シリアル コンソールでは、コピー/貼り付けが可能ですか。**

A. はい。 **Ctrl**+**Shift**+**C** キーでコピーし、**Ctrl**+**Shift**+**V** キーでターミナルに貼り付けます。

**Q.私のサブスクリプションのシリアル コンソールは誰が有効にしたり、無効にしたりできますか。**

A. あるサブスクリプション レベルでシリアル コンソールを有効または無効にするには、そのサブスクリプションの書き込み権限が必要です。 書き込み権限が与えられているロールには、管理者ロールと所有者ロールが含まれます。 カスタム ロールにも、書き込み権限が与えることができます。

**Q.私の VM のシリアル コンソールには誰がアクセスできますか。**

A. VM のシリアル コンソールにアクセスするには、VM に対して仮想マシン共同作成者以上のロールが必要です。

**Q.シリアル コンソールに何も表示されません。どうすればよいでしょうか。**

A. シリアル コンソールのアクセスに関して、おそらく、イメージが間違って構成されています。 シリアル コンソールを有効にするようにイメージを構成する方法については、「[カスタム イメージまたは古いイメージでシリアル コンソールを有効にする](#enable-the-serial-console-in-custom-or-older-images)」を参照してください。

**Q.シリアル コンソールは仮想マシン スケール セットに利用できますか。**

A. はい。 「[仮想マシン スケール セット用のシリアル コンソール](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)」を参照してください

## <a name="next-steps"></a>次のステップ
* Windows SAC で使用できる CMD および PowerShell コマンドの詳細なガイドについては、「[Windows コマンド -CMD と PowerShell](serial-console-cmd-ps-commands.md)」を参照してください。
* シリアル コンソールは、[Linux](serial-console-linux.md) VM でも使用できます。
* ブート診断の詳細については、[こちら](boot-diagnostics.md)を参照してください。
