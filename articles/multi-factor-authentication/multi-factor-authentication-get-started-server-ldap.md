---
title: "LDAP 認証と Azure MFA Server | Microsoft Docs"
description: "LDAP 認証と Azure Multi-Factor Authentication Server をデプロイする際に役立つ Azure Multi-Factor Authentication のページです。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: e1a68568-53d1-4365-9e41-50925ad00869
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1c6386dda94a3e0ca6eb340f542d04cb336159c3
ms.openlocfilehash: 0de97050e385e3efb9e63bbf934712157ab0d0af


---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP 認証と Azure Multi-Factor Authentication Server
既定では、Azure Multi-Factor Authentication Server は、Active Directory からユーザーをインポートするか同期するように構成されます。 ただし、ADAM ディレクトリや特定の Active Directory ドメイン コントローラーなどの別の LDAP ディレクトリにバインドするように構成できます。 LDAP 経由でディレクトリに接続している場合は、認証を実行する LDAP プロキシとして機能するように Azure Multi-Factor Authentication Server を構成できます。 RADIUS ターゲットとしての LDAP バインドの使用、IIS 認証使用時のユーザーの事前認証、Azure MFA ユーザー ポータルでのプライマリ認証も許可します。

Azure Multi-Factor Authentication を LDAP プロキシとして使用するには、LDAP クライアント (VPN アプライアンス、アプリケーションなど) と LDAP ディレクトリ サーバーの間に Azure Multi-Factor Authentication Server を挿入します。 クライアント サーバーと LDAP ディレクトリの両方と通信するように Azure Multi-Factor Authentication Server を構成する必要があります。 この構成の中で、Azure Multi-Factor Authentication Server は、クライアント サーバーとアプリケーションから LDAP 要求を受け取り、プライマリ資格情報を検証するためにターゲット LDAP ディレクトリ サーバーに要求を転送します。 LDAP ディレクトリからの応答でプライマリ資格情報が有効であることが示された場合、Azure Multi-Factor Authentication は第 2 識別子認証を実行し、応答を LDAP クライアントに送信します。 LDAP サーバーの認証と 2 段階目の認証の両方が成功した場合のみ、認証全体が成功します。

## <a name="ldap-authentication-configuration"></a>LDAP 認証の構成
LDAP 認証を構成するには、Azure Multi-Factor Authentication Server を Windows サーバーにインストールします。 次の手順に従います。

### <a name="add-an-ldap-client"></a>LDAP クライアントを追加する

1. Azure Multi-Factor Authentication Server で、左側のメニューの [LDAP 認証] アイコンをクリックします。
2. **[LDAP 認証を有効にする]** チェック ボックスをオンにします。

        ![LDAP Authentication](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)

3. Azure Multi-Factor Authentication LDAP サービスを、LDAP 要求をリッスンする標準ポート以外のポートにバインドする必要がある場合は、[クライアント] タブで TCP ポートと SSL ポートを変更します。
4. クライアントから Azure Multi-Factor Authentication Server に対して LDAPS を使用する場合は、Server が実行されているサーバーに SSL 証明書をインストールする必要があります。 [SSL 証明書] ボックスの横にある **[参照]**  ボタンをクリックし、セキュリティで保護された接続で使用するインストール済みの証明書を選択します。
5. **[追加]** をクリックします。
6. [LDAP クライアントの追加] ダイアログ ボックスで、Server に対して認証するアプライアンス、サーバー、またはアプリケーションの IP アドレスを入力し、アプリケーション名 (省略可能) を入力します。 アプリケーション名は Azure Multi-Factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。
7. すべてのユーザーがサーバーにインポート済みであるかインポート予定であり、2 段階認証の対象となる場合は、**[Require Azure Multi-Factor Authentication user match (Azure Multi-Factor Authentication のユーザー照合が必要)]** ボックスをオンにします。 多数のユーザーがまだサーバーにインポートされていない、または 2 段階認証から除外される場合、ボックスはオフのままにします。 この機能の追加情報については、ヘルプ ファイルを参照してください。

さらに LDAP クライアントを追加するには、上記の手順を繰り返します。

### <a name="configure-the-ldap-directory-connection"></a>LDAP ディレクトリ接続を構成する

Azure Multi-Factor Authentication が LDAP 認証を受けるように構成するときは、これらの認証を LDAP ディレクトリに委任する必要があります。 したがって、[ターゲット] タブには LDAP ターゲットを使用する 1 つのオプションだけがグレー表示されます。

1. LDAP ディレクトリ接続を構成するには、**[ディレクトリ統合]** アイコンをクリックします。
2. [設定] タブで、**[特定の LDAP 構成を使用する]** ラジオ ボタンを選択します。
3. **[編集]** を選択します。
4. [LDAP 構成の編集] ダイアログ ボックスで、LDAP ディレクトリに接続するために必要な情報を各フィールドに入力します。 各フィールドの説明は、Azure Multi-Factor Authentication Server のヘルプ ファイルに含まれています。

    ![ディレクトリ統合](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)

5. **[テスト]** ボタンをクリックして、LDAP 接続をテストします。
6. LDAP 接続テストが成功した場合、**[OK]** ボタンをクリックします。
7. **[フィルター]** タブをクリックします。 Server は、Active Directory からコンテナー、セキュリティ グループ、およびユーザーを読み込むように事前構成されています。 別の LDAP ディレクトリにバインドする場合は、表示されるフィルターの編集が必要になることがあります。 フィルターの詳細については、**[ヘルプ]** リンクをクリックしてください。
8. **[属性]** タブをクリックします。 Server は、Active Directory から属性をマッピングするように事前構成されています。
9. 別の LDAP ディレクトリにバインドしたり、事前構成されている属性のマッピングを変更する場合は、**[編集]** をクリックします。
10. [属性の編集] ダイアログ ボックスで、ディレクトリの LDAP 属性マッピングを変更します。 属性名はキーボードから入力するか、**** 各フィールドの横にある […] ボタンをクリックして選択できます。 属性の詳細については、**[ヘルプ]** リンクをクリックしてください。
11. **[OK]** ボタンをクリックします。
12. **[会社の設定]** アイコンをクリックし、**[ユーザー名の解決]** タブを選択します。
13. ドメインに参加しているサーバーから Active Directory に接続する場合は、**[Windows セキュリティ識別子 (SID) をユーザー名の照合に使用する]** を選択したままにします。 それ以外の場合は、**[LDAP 一意識別子の属性をユーザー名の照合に使用する]** を選択します。 

**[LDAP 一意識別子の属性をユーザー名の照合に使用する]** が選択されている場合、Azure Multi-Factor Authentication Server は、各ユーザーを LDAP ディレクトリ内の一意識別子に解決しようとします。 [ディレクトリ統合] の [属性] タブで定義されたユーザー名の各属性に対して LDAP 検索が実行されます。 ユーザーが認証されると、ユーザー名が LDAP ディレクトリ内の一意識別子に解決され、その一意識別子が Azure Multi-Factor Authentication データ ファイル内のユーザーとの照合に使用されます。 これにより、大文字と小文字を区別せず、ユーザー名の形式 (長短) を問わない比較が可能になります。

上記の手順を完了すると、MFA Server は構成されたクライアントからの LDAP アクセス要求のリッスンを構成されたポートで開始し、認証用にこれらの要求を LDAP ディレクトリにプロキシするように設定されます。

## <a name="ldap-client-configuration"></a>LDAP クライアントの構成
LDAP クライアントを構成するには、次のガイドラインに従います。

* Azure Multi-Factor Authentication Server でのアプライアンス、サーバー、アプリケーションの LDAP 経由の認証を、あたかも LDAP ディレクトリで行われるかのように構成します。 サーバー名または IP アドレスを Azure Multi-Factor Authentication Server のサーバー名または IP アドレスにすること以外は、LDAP ディレクトリに直接接続するために通常使用するものと同じ設定を使用します。
* LDAP ディレクトリでユーザーの資格情報を検証し、2 段階目の認証を実行し、応答を受け取って LDAP アクセス要求に応答する時間があるように、LDAP タイムアウトは 30 ～ 60 秒に構成します。
* LDAPS を使用する場合、LDAP クエリを行うアプライアンスまたはサーバーは、Azure Multi-Factor Authentication Server にインストールされている SSL 証明書を信頼する必要があります。




<!--HONumber=Feb17_HO3-->


