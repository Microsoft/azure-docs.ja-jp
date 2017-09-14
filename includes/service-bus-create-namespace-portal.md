Azure の Service Bus キューを使用するには、Azure 全体で一意となる名前を備えた名前空間を最初に作成しておく必要があります。 名前空間は、アプリケーション内で Service Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。

名前空間を作成するには:

1. [Azure Portal][Azure portal] にログオンします。
2. ポータルの左側のナビゲーション ウィンドウで、**[新規]** をクリックし、**[Enterprise Integration]**、**[Service Bus]** の順にクリックします。
3. **[名前空間の作成]** ダイアログで、名前空間の名前を入力します。 その名前が使用できるかどうかがすぐに自動で確認されます。
4. 入力した名前空間の名前が使用できることを確認したら、価格レベル (Basic、Standard、Premium) を選択します。
5. **[サブスクリプション]** フィールドで、名前空間を作成する Azure サブスクリプションを選択します。
6. **[リソース グループ]** フィールドで、名前空間を追加する既存のリソース グループを選択するか、新しいリソース グループを作成します。      
7. **[場所]**で、名前空間をホストする国またはリージョンを選択します。
   
    ![[名前空間の作成]][create-namespace]
8. **[作成]**をクリックします。 これで、システムによってサービス名前空間が作成され、有効になります。 システムがアカウントのリソースを準備し 終わるまでに、数分間かかる場合があります。

### <a name="obtain-the-management-credentials"></a>管理資格情報の取得
新しい名前空間を作成すると、Shared Access Signature (SAS) の初期規則が自動的に生成され、あらゆる角度から名前空間を完全に制御することを可能にするプライマリ キーとセカンダリ キーのペアが関連付けられます。 その他、通常の送信者と受信者を対象に、より権限を制限した規則を作成する方法については、「[Service Bus の認証と承認](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md)」を参照してください。 初期規則をコピーするには、次の手順を実行します。 

1. 名前空間の一覧で、新しく作成した名前空間の名前をクリックします。
2. 名前空間ブレードで、**[共有アクセス ポリシー]** をクリックします。
3. **[共有アクセス ポリシー]** ブレードで、**[RootManageSharedAccessKey]** をクリックします。
   
    ![connection-info][connection-info]
4. **[ポリシー: RootManageSharedAccessKey]** ブレードで、**[接続文字列 - プライマリ キー]** の横にあるコピー ボタンをクリックし、後で使用するために接続文字列をクリップボードにコピーします。 この値をメモ帳などに一時的に貼り付けます。
   
    ![connection-string][connection-string]

5. 前の手順を繰り返し、**[プライマリ キー]** の値をコピーして、後で使用するために一時的な場所に貼り付けます。

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
