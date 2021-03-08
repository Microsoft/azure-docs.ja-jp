---
title: 個々のセンサーの管理
description: アクティブ化ファイルの管理、バックアップの実行、スタンドアロン センサーの更新など、個々のセンサーを管理する方法について説明します。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 02/02/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: c8bb44d323574f6815aa570b271ed4c0df1fc6be
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526920"
---
# <a name="manage-individual-sensors"></a>個々のセンサーの管理

この記事では、個々のセンサーを管理する方法について説明します。 具体的には、アクティブ化ファイルの管理、バックアップの実行、スタンドアロン センサーの更新などのタスクがあります。

また、オンプレミス管理コンソールから、複数のセンサーを同時に管理できる特定のセンサー管理タスクを実行することもできます。

センサーのオンボードと登録には Azure portal を使用します。

## <a name="manage-sensor-activation-files"></a>センサーのアクティブ化ファイルの管理

お使いのセンサーは、Azure portal から Azure Defender for IoT でオンボードされたものです。 各センサーは、ローカル接続のセンサーまたはクラウド接続のセンサーとしてオンボードされました。

デプロイする各センサーには一意のアクティブ化ファイルがアップロードされます。 新しいファイルを使用するタイミングと方法について詳しくは、「[新しいアクティブ化ファイルをアップロードする](#upload-new-activation-files)」をご覧ください。 ファイルをアップロードできない場合は、「[アクティブ化ファイルのアップロードのトラブルシューティング」](#troubleshoot-activation-file-upload)をご覧ください。

### <a name="about-activation-files-for-locally-connected-sensors"></a>ローカル接続のセンサー用のアクティブ化ファイルについて

ローカル接続のセンサーは、Azure サブスクリプションに関連付けられます。 ローカル接続のセンサー用のアクティブ化ファイルには、有効期限が含まれています。 この日付の 1 か月前になると、センサーのコンソールの一番上に警告メッセージが表示されます。 アクティブ化ファイルの更新が済むまで、警告は表示されたままになります。

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="システム設定のスクリーンショット。":::

アクティブ化ファイルの有効期限が切れている場合でも、引き続き Defender for IoT の機能を使用できます。 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>クラウド接続のセンサー用のアクティブ化ファイルについて

クラウド接続のセンサーは、Defender for IoT ハブに関連付けられます。 これらのセンサーには、アクティブ化ファイルの期間による制限はありません。 クラウド接続のセンサー用のアクティブ化ファイルは、Defender for IoT ハブとの接続を確保する目的で使用されます。

### <a name="upload-new-activation-files"></a>新しいアクティブ化ファイルをアップロードする

次の場合に、オンボードされたセンサー用の新しいアクティブ化ファイルのアップロードが必要になることがあります。

- ローカル接続のセンサーでアクティブ化ファイルの有効期限が切れる。 

- 別のセンサー管理モードで作業する。 

- クラウド接続のセンサーに新しい Defender for IoT ハブを割り当てる必要がある。

新しいアクティブ化ファイルを追加するには:

1. **[センサー管理]** ページにアクセスします。

2. 新しいアクティブ化ファイルをアップロードするセンサーを選択します。

3. それを削除します。

4. 新しいモードの **[オンボード]** ページから、または新しい Defender for IoT ハブを使用して、センサーをもう一度オンボードします。

5. **[アクティブ化ファイルのダウンロード]** ページからアクティブ化ファイルをダウンロードします。

6. ファイルを保存します。

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Defender for IoT ハブからアクティブ化ファイルをダウンロードします。":::

7. Defender for IoT センサー コンソールにサインインします。

8. センサー コンソールで、 **[システム設定]**  >  **[再アクティブ化]** の順に選択します。

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="[システム設定] 画面の [再アクティブ化] の選択。":::

9. **[アップロード]** を選択し、保存したファイルを選択します。

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="保存したファイルをアップロードします。":::

10. **[アクティブ化]** を選びます。

### <a name="troubleshoot-activation-file-upload"></a>アクティブ化ファイルのアップロードをトラブルシューティングする

アクティブ化ファイルをアップロードできなかった場合は、エラー メッセージが表示されます。 次のイベントが発生している可能性があります。

- **ローカル接続のセンサーの場合**: アクティブ化ファイルが有効ではありません。 ファイルが有効でない場合は、Defender for IoT ポータルにアクセスします。 **[センサー管理]** ページで、無効なファイルを含むセンサーを選択し、新しいアクティブ化ファイルをダウンロードします。

- **クラウド接続のセンサーの場合**: センサーがインターネットに接続できません。 センサーのネットワーク構成を確認します。 センサーがインターネットにアクセスするために Web プロキシ経由で接続する必要がある場合は、 **[センサーのネットワーク構成]** 画面でプロキシ サーバーが正しく構成されていることを確認します。 ファイアウォールやプロキシで \*.azure-devices.net:443 が許可されていることを確認します。 ワイルドカードがサポートされていない場合、または細かく制御する必要がある場合は、特定の Defender for IoT ハブの FQDN をお使いのファイアウォールやプロキシで開く必要があります。 詳しくは、「[リファレンス - IoT Hub エンドポイント](../iot-hub/iot-hub-devguide-endpoints.md)」をご覧ください。  

- **クラウド接続のセンサーの場合**: アクティブ化ファイルは有効ですが、Defender for IoT で拒否されました。 この問題を解決できない場合は、Defender for IoT ポータルの [サイトとセンサー] ページから、別のアクティブ化をダウンロードできます。 それでもうまくいかない場合は、Microsoft サポートにお問い合わせください。

## <a name="manage-certificates"></a>証明書の管理

センサーのインストール後に、ローカルの自己署名証明書が生成され、センサーの Web アプリケーションにアクセスするために使用されます。 管理者ユーザーは、センサーに初めてログインするときに、SSL/TLS 証明書を指定するように求められます。  初回セットアップについて詳しくは、[サインインとセンサーのアクティブ化](how-to-activate-and-set-up-your-sensor.md)に関する記事をご覧ください。

ここでは、証明書の更新、証明書の CLI コマンドの使用、サポートされている証明書と証明書のパラメーターについて説明します。

### <a name="about-certificates"></a>証明書について

Azure Defender for IoT では、SSL/TLS 証明書を使用して以下の処理を行います。

- CA 署名証明書をアップロードして、組織から要求された特定の証明書と暗号化の要件を満たします。

- 管理コンソールと接続されたセンサーの間、および管理コンソールと高可用性管理コンソールの間の検証を許可します。 証明書失効リストと証明書の有効期限に基づいて検証が評価されます。 *検証に失敗した場合は、管理コンソールとセンサー間の通信が停止し、検証エラーがコンソールに表示されます。* このオプションは、インストール後に既定で有効になります。

 サード パーティの転送ルール (SYSLOG、Splunk、または ServiceNow に送信されるアラート情報など) や Active Directory との通信は検証されません。

#### <a name="ssl-certificates"></a>SSL 証明書の数

Defender for IoT センサーとオンプレミスの管理コンソールは、次の機能のために SSL と TLS 証明書を使用します。 

 - ユーザーとアプライアンスの Web コンソールとの間のセキュリティで保護された通信。 
 
 - センサーおよびオンプレミスの管理コンソール上での REST API へのセキュリティで保護された通信。
 
 - センサーとオンプレミスの管理コンソールとの間のセキュリティで保護された通信。 

インストールされると、アプライアンスによって、ローカルの自己署名証明書が生成され、Web コンソールへの暫定的なアクセスが可能になります。 エンタープライズ SSL および TLS 証明書は、[`cyberx-xsense-certificate-import`](#cli-commands) コマンド ライン ツールを使用してインストールできます。

 > [!NOTE]
 > アプライアンスがセッションのクライアントおよびイニシエーターである統合および転送ルールでは、特定の証明書が使用され、システム証明書に関連付けられません。  
 >
 >このような場合、証明書は通常、サーバーから受信されます。そうでなければ、統合をセットアップするための特定の証明書が提供される非対称暗号化を使用します。

アプライアンスでは、一意の証明書ファイルを使用できます。 証明書を置換する必要がある場合は、アップロードします。

- バージョン 10.0 以降では、証明書は [システム設定] メニューから置き換えることができます。

- 10.0 より前のバージョンでは、SSL 証明書はコマンド ライン ツールを使用して置き換えることができます。

### <a name="update-certificates"></a>証明書を更新する

センサー管理者ユーザーは、証明書を更新できます。

証明書を更新するには:  

1. **[システム設定]** を選択します。

1. **[SSL/TLS 証明書]** を選択します。
1. 証明書を削除または編集し、新しいものを追加します。

    - 証明書名を追加します。
    
    - CRT ファイルとキー ファイルをアップロードし、パスフレーズを入力します。
    - 必要に応じて PEM ファイルをアップロードします。

検証設定を変更するには:

1. **[証明書の検証を有効にする]** トグルを有効または無効にします。

1. **[保存]** を選択します。

オプションが有効になっていて、検証に失敗した場合は、管理コンソールとセンサー間の通信が停止し、検証エラーがコンソールに表示されます。

### <a name="certificate-support"></a>証明書のサポート

次の証明書がサポートされています。

- プライベートおよびエンタープライズ キー インフラストラクチャ (プライベート PKI)

- 公開キー基盤 (パブリック PKI) 

- アプライアンス上でローカルに生成された (ローカルに自己署名された) もの。 

> [!IMPORTANT]
> 自己署名証明書の使用はお勧めしません。 この種類の接続はセキュリティで保護されていないため、テスト環境でのみ使用してください。 証明書の所有者を検証できず、システムのセキュリティを維持することもできないため、自己署名証明書は実稼働ネットワークには使用しないでください。

### <a name="supported-ssl-certificates"></a>サポートされる SSL 証明書 

次のパラメーターがサポートされています。 

**証明書 CRT**

- ドメイン名のプライマリ証明書ファイル

- 署名アルゴリズム = SHA256RSA
- 署名ハッシュ アルゴリズム = SHA256
- 有効期間の開始 = 有効な過去の日付
- 有効期間の終了 = 有効な将来の日付
- 公開キー = RSA 2048 ビット (最小) または 4096 ビット
- CRL 配布ポイント = .crl ファイルへの URL
- サブジェクト CN = URL (ワイルドカード証明書を使用可能)。例: Sensor.contoso.<span>com または *.contoso.<span>com
- サブジェクトの国 (C) = 定義済み。例: US
- サブジェクトの組織単位 (OU) = 定義済み。例: Contoso Labs
- サブジェクトの組織 (O) = 定義済み。例: Contoso Inc.

**キー ファイル**

- CSR を作成したときに生成されたキー ファイル。

- RSA 2048 ビット (最小) または 4096 ビット

 > [!Note]
 > 4096 ビットのキーの長さを使用する場合:
 > - 各接続の開始時の SSL ハンドシェイクが遅くなります。  
 > - ハンドシェイク中の CPU 使用率が増加します。 

**証明書チェーン**

- CA から提供された中間証明書ファイル (存在する場合)

- サーバーの証明書を発行した CA の証明書をファイルの先頭に配置し、その後にルートまでの他の証明書を配置する必要があります。 
- バッグ属性を含めることができます。

**パスフレーズ**

- 1 つのキーがサポートされています。

- 証明書をインポートするときに設定します。

他のパラメーターを含む証明書が機能する場合がありますが、Microsoft ではサポートされません。

#### <a name="encryption-key-artifacts"></a>暗号化キー成果物

**.pem - 証明書コンテナー ファイル**

Privacy Enhanced Mail (PEM) ファイルは、メールをセキュリティで保護するために使用される一般的なファイルの種類です。 最近では、PEM ファイルは証明書と共に使用され、x509 ASN1 キーを使用します。  

コンテナー ファイルは、RFC 1421 から 1424 で定義されています。これは、公開証明書のみを含む可能性があるコンテナー形式です。 たとえば、Apache では、CA 証明書、ファイル、ETC、SSL、CERTS がインストールされます。 これには公開キー、秘密キー、ルート証明書など、証明書チェーン全体が含まれる場合があります。  

また、CSR を PKCS10 形式としてエンコードすることもでき、それを PEM に変換できます。

**.cert、.cer、.crt  - 証明書コンテナー ファイル**

異なる拡張子を使用した `.pem` または `.der` 形式のファイル。 このファイルは、エクスプローラーで証明書として認識されます。 `.pem`  ファイルはエクスプローラーで認識されません。

**.key – 秘密キー ファイル**

キー ファイルは PEM ファイルと同じ形式ですが、拡張子は異なります。

#### <a name="additional-commonly-available-key-artifacts"></a>その他の一般的に利用可能な主要成果物

**.csr  - 証明書署名要求**  

このファイルは、証明機関に送信するために使用されます。 実際の形式は、RFC 2986 で定義されている PKCS10 で、要求された証明書の一部またはすべてのキーの詳細が含まれている場合があります。 たとえば、サブジェクト、組織、都道府県などです。 これは、CA によって署名され、引き換えに証明書を受け取る、証明書の公開キーです。  

返される証明書は公開証明書であり、公開キーは含まれていますが、秘密キーは含まれていません。 

**.pkcs12 .pfx .p12 - パスワード コンテナー** 

当初は公開キー暗号化標準 (PKCS) として RSA によって定義されましたが、12 バリアントは当初は Microsoft によって拡張され、その後 RFC 7292 として提出されました。  

このコンテナー形式では、公開とプライベートの証明書のペアを含むパスワードが必要です。 `.pem`  ファイルとは異なり、このコンテナーは完全に暗号化されます。  

OpenSSL を使用して、公開および秘密キーの両方を含む `.pem`  ファイルに変換できます:  `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**der - バイナリでエンコードされた PEM**。

ASN.1 構文をバイナリでエンコードするには、`.pem`  ファイル (単に Base64 でエンコードされた `.der` ファイル) を使用します。 

OpenSSL では、これらのファイルを `.pem` に変換できます:  `openssl x509 -inform der -in to-convert.der -out converted.pem`  

これらのファイルは、Windows によって証明書ファイルとして認識されます。 既定では、Windows では証明書は別の拡張子を持つ `.der` 形式のファイルとしてエクスポートされます。  

**.crl  - 証明書の失効リスト**  
証明機関によって、有効期限が切れる前に証明書を承認解除する方法としてこれらが生成されます。
 
##### <a name="cli-commands"></a>CLI コマンド

`cyberx-xsense-certificate-import` CLI コマンドを使用して、証明書をインポートします。 このツールを使用するには、WinSCP や Wget などのツールを使用して、証明書ファイルをデバイスにアップロードする必要があります。

このコマンドは、次の入力フラグをサポートしています。

- `-h`:コマンドライン ヘルプの構文を表示します。

- `--crt`:証明書ファイル (.crt 拡張子) へのパス。

- `--key`: \*.key ファイル。 キーの長さは、2,048 ビット以上である必要があります。

- `--chain`:証明書チェーン ファイルへのパス (省略可能)。

- `--pass`:証明書の暗号化に使用されるパスフレーズ (省略可能)。

- `--passphrase-set`:既定値 = `False` (使用しない)。 前の証明書で指定された前のパスフレーズを使用するには、`True` に設定します (省略可能)。

CLI コマンドを使用する場合:

- アプライアンスで証明書ファイルが読み取り可能であることを確認します。

- 証明書のドメイン名と IP が、IT 部門が計画した構成と一致していることを確認します。

### <a name="use-openssl-to-manage-certificates"></a>OpenSSL を使用して証明書を管理する

次のコマンドを使用して証明書を管理します。

| [説明] | CLI コマンド |
|--|--|
| 新しい秘密キーと証明書署名要求を生成する | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| 自己署名証明書の生成 | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| 既存の秘密キーの証明書署名要求 (CSR) を生成する | `openssl req -out CSR.csr -key privateKey.key -new` |
| 既存の証明書に基づく証明書署名要求を生成する | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| 秘密キーからパスフレーズを削除する | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

証明書、CSR、または秘密キー内の情報を確認する必要がある場合は、次のコマンドを使用します。

| [説明] | CLI コマンド |
|--|--|
| 証明書署名要求 (CSR) を確認する | `openssl req -text -noout -verify -in CSR.csr` |
| 秘密キーを確認する | `openssl rsa -in privateKey.key -check` |
| 証明書を確認する | `openssl x509 -in certificate.crt -text -noout`  |

秘密キーが証明書と一致しない、またはサイトにインストールした証明書が信頼されていないというエラーが表示された場合は、次のコマンドを使用してエラーを修正します。

| [説明] | CLI コマンド |
|--|--|
| 公開キーの MD5 ハッシュを確認して、CSR または秘密キーの内容と一致していることを確認する | 1. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> 2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

証明書とキーを別の形式に変換して、特定の種類のサーバーまたはソフトウェアと互換性を持たせるには、次のコマンドを使用します。

| [説明] | CLI コマンド |
|--|--|
| DER ファイル (.crt .cer .der) を PEM に変換する  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| PEM ファイルを DER に変換する | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| 秘密キーと証明書を含む PKCS#12 ファイル (.pfx. p12) を PEM に変換する | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />`-nocerts` を追加して秘密キーのみを出力するか、`-nokeys` を追加して証明書のみを出力することができます。 |
| PEM 証明書ファイルと秘密キーを PKCS#12 (.pfx. p12) に変換する | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="connect-a-sensor-to-the-management-console"></a>管理コンソールへのセンサーの接続

ここでは、センサーとオンプレミス管理コンソール間の接続を確認する方法について説明します。 この操作は、エアギャップ ネットワークで作業しているときに、センサーから管理コンソールにデバイスやアラートの情報を送信する場合に必要となります。 この接続により、管理コンソールでセンサーにシステム設定をプッシュしたり、センサーに対して他の管理タスクを実行したりすることもできます。

接続するには:

1. オンプレミス管理コンソールにサインインします。

2. **[システム設定]** を選択します。

3. **[センサーのセットアップ – 接続文字列]** セクションで、自動生成された接続文字列をコピーします。

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="この画面から接続文字列をコピーします。"::: 

4. センサー コンソールにサインインします。

5. 左側のペインで、 **[システム設定]** を選択します。

6. **[管理コンソール接続]** を選択します。

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="[管理コンソール接続] ダイアログ ボックスのスクリーンショット。":::

7. 接続文字列を **[接続文字列]** ボックスに貼り付けて、 **[接続]** を選択します。

8. オンプレミス管理コンソールの **[サイトの管理]** ウィンドウで、センサーをゾーンに割り当てます。

## <a name="change-the-name-of-a-sensor"></a>センサーの名前の変更

センサー コンソールの名前を変更できます。 コンソールの Web ブラウザー、各種コンソール ウィンドウ、トラブルシューティング ログに新しい名前が表示されます。

センサー名を変更するプロセスは、ローカル接続のセンサーとクラウド接続のセンサーで異なります。 既定の名前は **sensor** です。

### <a name="change-the-name-of-a-locally-connected-sensor"></a>ローカル接続のセンサーの名前を変更する

名前を変更するには:

1. コンソールの左ペインの下部にある現在のセンサー ラベルを選択します。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="センサー ラベルを示すスクリーンショット。":::

1. **[センサー名の編集]** ダイアログ ボックスで、名前を入力します。

1. **[保存]** を選択します。 新しい名前が適用されます。

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>クラウド接続のセンサーの名前を変更する

センサーをクラウド接続のセンサーとして登録した場合は、登録中に割り当てられた名前によってセンサー名が定義されています。 その名前は、初めてサインインしたときにアップロードしたアクティブ化ファイルに含まれています。 センサーの名前を変更するには、新しいアクティブ化ファイルをアップロードする必要があります。

名前を変更するには:

1. Azure Defender for IoT ポータルで、[サイトとセンサー] ページに移動します。

1. [サイトとセンサー] ページからセンサーを削除します。

1. [はじめに] ページで **[センサーのオンボード]** を選択して、新しい名前で登録します。

1. 新しいアクティブ化ファイルをダウンロードします。

1. Defender for IoT センサー コンソールにサインインします。

1. センサー コンソールで、 **[システム設定]** を選択し、 **[再アクティブ化]** を選択します。

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="アクティブ化ファイルをアップロードしてセンサーを再アクティブ化します。":::

1. **[アップロード]** を選択し、保存したファイルを選択します。

1. **[アクティブ化]** を選びます。

## <a name="update-the-sensor-network-configuration"></a>センサーのネットワーク構成の更新

センサーのネットワーク構成は、センサーのインストール中に定義されています。 構成パラメーターを変更できます。 また、プロキシ構成を設定することもできます。

新しい IP アドレスを作成する場合は、もう一度サインインすることが必要になる場合があります。

構成を変更するには:

1. サイド メニューで、 **[システム設定]** を選択します。

2. **[システム設定]** ウィンドウで、 **[ネットワーク]** を選択します。

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="ネットワーク設定を構成します。":::

3. パラメーターを設定します。

    | パラメーター | [説明] |
    |--|--|
    | IP アドレス | センサーの IP アドレス |
    | サブネット マスク | マスク アドレス |
    | デフォルト ゲートウェイ | 既定のゲートウェイ アドレス |
    | DNS | DNS サーバー アドレス |
    | hostname | センサーのホスト名 |
    | プロキシ | プロキシ ホストとポート名 |

4. **[保存]** を選択します。

## <a name="synchronize-time-zones-on-the-sensor"></a>センサーでのタイム ゾーンの同期

すべてのユーザーに同じ時刻とリージョンが表示されるように、センサーの時刻とリージョンを構成できます。

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="時刻とリージョンを構成します。":::

| パラメーター | [説明] |
|--|--|
| タイム ゾーン | タイム ゾーン定義の対象:<br />- アラート<br />- 傾向および統計ウィジェット<br />- データ マイニング レポート<br />   \- リスク評価レポート<br />- 攻撃ベクトル |
| 日付の形式 | 次の形式オプションのいずれかを選択します。<br />- dd/MM/yyyy HH:mm:ss<br />- MM/dd/yyyy HH:mm:ss<br />- yyyy/MM/dd HH:mm:ss |
| 日付と時刻 | 選択した形式で現在の日付とローカル時刻を表示します。<br />たとえば、実際の場所が米国のニューヨークであっても、タイム ゾーンがヨーロッパのベルリンに設定されている場合、時刻はベルリンのローカル時刻に従って表示されます。 |

センサーの時刻を構成するには:

1. サイド メニューで、 **[システム設定]** を選択します。

2. **[システム設定]** ウィンドウで、 **[時刻とリージョン]** を選択します。

3. パラメーターを設定し、 **[保存]** を選択します。

## <a name="set-up-backup-and-restore-files"></a>バックアップおよび復元ファイルの設定

システムのバックアップは、毎日午前 3 時に自動的に実行されます。 データはセンサーの別のディスクに保存されます。 既定の場所は `/var/cyberx/backups` です。

このファイルを内部ネットワークに自動的に転送できます。

> [!NOTE]
> - バックアップと復元手順は、同じバージョン間でのみ実行できます。
> - 一部のアーキテクチャでは、バックアップが無効になっています。 これは `/var/cyberx/properties/backup.properties` ファイルで有効にすることができます。

オンプレミス管理コンソールを使用してセンサーを制御すると、センサーのバックアップ スケジュールを使ってこれらのバックアップを収集し、管理コンソールまたは外部のバックアップ サーバーに保存することができます。

**バックアップされるもの:** 構成とデータ。

**バックアップされないもの:** PCAP ファイルとログ。 PCAP とログのバックアップと復元は手動で行うことができます。

センサーのバックアップ ファイルには、次の形式で自動的に名前が付けられます: `<sensor name>-backup-version-<version>-<date>.tar`。 たとえば `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar` です。

バックアップを構成するには:

- 管理者アカウントにサインインし、「`$ sudo cyberx-xsense-system-backup`」と入力します。

最新のバックアップ ファイルを復元するには:

- 管理者アカウントにサインインし、「`$ sudo cyberx-xsense-system-restore`」と入力します。

外部の SMB サーバーにバックアップを保存するには:

1. 外部の SMB サーバーに共有フォルダーを作成します。

    SMB サーバーへのアクセスに必要なフォルダー パス、ユーザー名、パスワードを取得します。

2. センサーで、バックアップ用のディレクトリを作成します。

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. `fstab`を編集します:

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. SMB サーバー用に共有する資格情報を編集および作成します。

    `sudo nano /etc/samba/user` 

5. 次の項目を追加します。

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. ディレクトリをマウントします。

    `sudo mount -a`

7. Defender for IoT センサー上の共有フォルダーに、バックアップ ディレクトリを構成します。  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>センサーを復元する

バックアップは、センサー コンソールからと、CLI を使用して復元できます。

**コンソールから復元するには:**

- センサーの **[システム設定]** ウィンドウから **[イメージの復元]** を選択します。

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="ボタンを選択してイメージを復元します。":::

コンソールに復元エラーが表示されます。

**CLI を使用して復元するには:**

- 管理者アカウントにサインインし、「`$ sudo cyberx-management-system-restore`」と入力します。

## <a name="update-a-standalone-sensor-version"></a>スタンドアロン センサーのバージョンの更新

次の手順では、センサー コンソールを使用してスタンドアロン センサーを更新する方法について説明します。 更新プロセスには約 30 分かかります。

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。

2. Defender for IoT にアクセスします。

3. **[更新プログラム]** ページに移動します。

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Defender for IoT の [更新プログラム] ページのスクリーンショット。":::

4. **[センサー]** セクションの **[ダウンロード]** を選択し、ファイルを保存します。

5. センサー コンソールのサイドバーで、 **[システム設定]** を選択します。

6. **[バージョンのアップグレード]** ペインで、 **[アップグレード]** を選択します。

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="アップグレード ペインのスクリーンショット。":::

7. Defender for IoT の **[更新プログラム]** ページからダウンロードしたファイルを選択します。

8. 更新プロセスが開始され、その間にシステムが 2 回再起動されます。 最初の再起動後 (更新プロセスが完了する前) に、サインイン ウィンドウが表示された状態でシステムが開きます。 サインインすると、サイドバーの左下にアップグレード バージョンが表示されます。

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="サインイン後に表示されるアップグレード バージョンのスクリーンショット。":::

## <a name="forward-sensor-failure-alerts"></a>センサーの障害アラートの転送

以下に関する詳細情報を提供するために、サード パーティにアラートを転送できます。

- 切断されたセンサー

- リモート バックアップの失敗

この情報は、システム通知の転送ルールを作成するときに送信されます。

> [!NOTE]
> 管理者はシステム通知を送信できます。

通知を送信するには:

1. オンプレミス管理コンソールにサインインします。
1. サイド メニューから **[転送]** を選択します。
1. 転送ルールを作成します。
1. **[システム通知の報告]** を選択します。

転送ルールについて詳しくは、「[アラート情報を転送する](how-to-forward-alert-information-to-partners.md)」をご覧ください。

## <a name="adjust-system-properties"></a>システム プロパティの調整

システム プロパティにより、センサーのさまざまな操作と設定が制御されます。 それらを編集または変更すると、センサー コンソールの操作に悪影響を及ぼす可能性があります。

設定を変更する前に、[Microsoft サポート](https://support.microsoft.com/)にお問い合わせください。

システム プロパティにアクセスするには:

1. オンプレミス管理コンソールまたはセンサーにサインインします。

2. **[システム設定]** を選択します。

3. **[全般]** セクションの **[システム プロパティ]** を選択します。

## <a name="see-also"></a>関連項目

[脅威インテリジェンスの調査とパッケージ](how-to-work-with-threat-intelligence-packages.md)

[管理コンソールからセンサーを管理する](how-to-manage-sensors-from-the-on-premises-management-console.md)