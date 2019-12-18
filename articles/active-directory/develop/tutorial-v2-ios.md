---
title: iOS と macOS の基本 - Microsoft ID プラットフォーム | Azure
description: iOS と macOS (Swift) アプリケーションで Microsoft ID プラットフォームを使用してアクセス トークンを必要とする API を呼び出す方法
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64f161d94d622ae76932e88be52df6f068bfc945
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964670"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>iOS または macOS アプリからユーザーのサインインを行い、Microsoft Graph を呼び出す

このチュートリアルでは、iOS または macOS アプリを Microsoft ID プラットフォームと統合する方法について説明します。 アプリによって、ユーザーがサインインされ、Microsoft Graph API を呼び出すためのアクセス トークンが取得されて、Microsoft Graph API への要求が行われます。  

このガイドを完了すると、アプリケーションは、個人用の Microsoft アカウント (outlook.com、live.com など) と、Azure Active Directory を使用する会社や組織の職場または学校アカウントのサインインを受け入れるようになります。

## <a name="how-this-tutorial-works"></a>このチュートリアルのしくみ

![このチュートリアルで生成されたサンプル アプリの動作の紹介](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

このチュートリアルのアプリでは、ユーザーがサインインされ、ユーザーに代わってデータが取得されます。  このデータは、承認を要求し、Microsoft ID プラットフォームによって保護される API (ここでは Microsoft Graph API) を介してアクセスされます。

具体的には次のとおりです。

* アプリがブラウザーまたは Microsoft Authenticator を介してユーザーをサインインします。
* エンド ユーザーがアプリケーションから要求されたアクセス許可を受け入れます。
* アプリケーションには、Microsoft Graph API 用のアクセス トークンが発行されます。
* アクセス トークンは、Web API への HTTP 要求に含められます。
* Microsoft Graph の応答を処理します。

このサンプルでは、Microsoft Authentication Library (MSAL) を使用して認証が実装されます。 MSAL により、トークンが自動的に更新され、デバイス上の他のアプリとの間のシングル サインオン (SSO) が提供されて、アカウントが管理されます。

このチュートリアルは、iOS アプリと macOS アプリの両方に適用されます。 これら 2 つのプラットフォームでは、いくつかの手順が異なることに注意してください。 

## <a name="prerequisites"></a>前提条件

- このガイドのアプリをビルドするには、XCode バージョン 10.x 以降が必要です。 XCode は、[iTunes の Web サイト](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode のダウンロード URL")からダウンロードできます。
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc))。 依存関係マネージャーを使用するか、ライブラリを手動で追加できます。 以下の手順でその方法を示します。

このチュートリアルでは新しいプロジェクトを作成します。 代わりに完了したチュートリアルをダウンロードする場合は、コードをダウンロードしてください。
- [iOS のサンプル コード](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [macOS のサンプル コード](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

1. Xcode を開き、 **[Create a new Xcode project (新しい Xcode プロジェクトを作成)]** を選択します。
2. iOS アプリの場合は、 **[iOS]**  >  **[単一ビュー アプリ]** を選択し、 **[次へ]** を選択します。
3. macOS アプリの場合は、 **[macOS]**  >  **[Cocoa アプリ]** を選択し、 **[次へ]** を選択します。
4. 製品名を指定します。
5. **[言語]** を **[Swift]** に設定し、 **[次へ]** を選択します。
6. アプリを作成するフォルダーを選択し、 **[作成]** をクリックします。

## <a name="register-your-application"></a>アプリケーションの登録

1. [Azure portal](https://aka.ms/MobileAppReg) に移動します
2. [[アプリの登録] ブレード](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)を開き、 **[+ 新しい登録]** をクリックします。
3. アプリの **[名前]** を入力した後、リダイレクト URI を設定しないで、 **[登録]** をクリックします。
4. 表示されたウィンドウの **[管理]** セクションで、 **[認証]** を選択します。

5. 画面の上部付近にある **[新しいエクスペリエンスを試す]** をクリックして新しいアプリの登録エクスペリエンスを開き、 **[+ 新規登録]**  >  **[+ プラットフォームを追加]**  >  **[iOS]** をクリックします。
    - プロジェクトのバンドル ID を入力します。 コードをダウンロードした場合は `com.microsoft.identitysample.MSALiOS` です。 独自のプロジェクトを作成している場合は、Xcode でそのプロジェクトを選択し、 **[全般]** タブを開きます。 **[ID]** セクションにバンドル ID が表示されます。
    - macOS の場合は、iOS エクスペリエンスも使用する必要があることに注意してください。 
6. [`Configure`] をクリックし、後でアプリを構成するときに入力できるように、 **[iOS の構成]** ページに表示される **[MSAL 構成]** を保存しておきます。  **[Done]** をクリックします。

## <a name="add-msal"></a>MSAL の追加

次のいずれかの方法を選択して、アプリに MSAL ライブラリをインストールします。

### <a name="cocoapods"></a>CocoaPods

1. [CocoaPods](https://cocoapods.org/) を使用している場合は、まずプロジェクトの `.xcodeproj` ファイルと同じフォルダーに `podfile` という名前の空のファイルを作成してから、`MSAL` をインストールします。 次を `podfile` に追加します。

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. `<your-target-here>` を自分のプロジェクト名に置き換えます。
3. ターミナル ウィンドウで、作成した `podfile` を含むフォルダーに移動し、`pod install` を実行して MSAL ライブラリをインストールします。
4. Xcode を閉じて `<your project name>.xcworkspace` を開き、Xcode にプロジェクトを再度読み込みます。

### <a name="carthage"></a>Carthage

[Carthage](https://github.com/Carthage/Carthage) を使用している場合は、`Cartfile` に追加することで `MSAL` をインストールします。

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

ターミナル ウィンドウから、更新された `Cartfile` と同じディレクトリで次のコマンドを実行して、Carthage でのプロジェクトの依存関係を更新します。

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>手動

Git サブモジュールを使用するか、最新のリリースをチェックアウトして、アプリケーションでフレームワークとして使用することもできます。

## <a name="add-your-app-registration"></a>アプリ登録の追加

次に、アプリ登録をコードに追加します。 

まず、次の import ステートメントを `ViewController.swift` および `AppDelegate.swift` ファイルの先頭に追加します。

```swift
import MSAL
```

次に、`ViewController.swift` (`viewDidLoad()` の前) に次のコードを追加します。

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/" // the Microsoft Graph endpoint
let kScopes: [String] = ["https://graph.microsoft.com/user.read"] // request permission to read the profile of the signed-in user
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
```

上記で変更する必要がある値は、`kClientID` に割り当てる[アプリケーション ID](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id) の値だけです。 この値は、このチュートリアルの最初の Azure portal でアプリケーションを登録する手順で保存した、MSAL 構成データの一部です。

## <a name="for-ios-only-configure-url-schemes"></a>iOS のみ: URL スキームを構成する

この手順では、サインイン後にユーザーがアプリにリダイレクトできるように、`CFBundleURLSchemes` を登録します。 なお、`LSApplicationQueriesSchemes` では、アプリでの Microsoft Authenticator の使用も許可されます。

Xcode で、`Info.plist` をソース コード ファイルとして開き、`<dict>` セクション内に以下を追加します。 `[BUNDLE_ID]` を、Azure portal で使用した値に置き換えます。コードをダウンロードした場合は `com.microsoft.identitysample.MSALiOS` です。 独自のプロジェクトを作成している場合は、Xcode でそのプロジェクトを選択し、 **[全般]** タブを開きます。 **[ID]** セクションにバンドル ID が表示されます。

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>macOS のみ: App Sandboxを構成する

1. [Xcode プロジェクトの設定] > **[機能] タブ** >  **[App Sandbox]** に移動します。
2. **[発信接続 (クライアント)]** チェックボックスをオンにします。 

## <a name="create-your-apps-ui"></a>アプリの UI の作成

ここで、次のコードを `ViewController` クラスに追加して、Microsoft Graph API を呼び出すボタン、サインアウトするボタン、および出力を表示するためのテキスト ビューを含む UI を作成します。

### <a name="ios-ui"></a>iOS UI

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

### <a name="macos-ui"></a>macOS UI

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = NSButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.title = "Call Microsoft Graph API"
        callGraphButton.target = self
        callGraphButton.action = #selector(callGraphAPI(_:))
        callGraphButton.bezelStyle = .rounded
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        
        // Add sign out button
        signOutButton = NSButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.title = "Sign Out"
        signOutButton.target = self
        signOutButton.action = #selector(signOut(_:))
        signOutButton.bezelStyle = .texturedRounded
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = NSTextView()
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
        loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
        loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
    }
```

次に、`ViewController` クラス内でも、`viewDidLoad()` メソッドを次のように置き換えます。

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
    }
```

## <a name="use-msal"></a>MSAL の使用

### <a name="initialize-msal"></a>MSAL の初期化

`ViewController` クラスに次の `initMSAL` メソッドを追加します。

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

`ViewController` クラスの `initMSAL` メソッドの後ろに以下を追加します。

### <a name="ios-code"></a>iOS コード:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>macOS コード:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
        self.webViewParameters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>iOS のみ: サインイン コールバックを処理する

`AppDelegate.swift` ファイルを開きます。 サインイン後のコールバックを処理するには、次のように `appDelegate` クラスに `MSALPublicClientApplication.handleMSALResponse` を追加します。

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Xcode 11 を使用している場合は**、代わりに MSAL コールバックを `SceneDelegate.swift` に配置する必要があります。
以前の iOS との互換性を保持するために UISceneDelegate と UIApplicationDelegate の両方をサポートしている場合は、MSAL コールバックを両方のファイルに配置する必要があります。

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

#### <a name="acquire-tokens"></a>トークンの取得

これで、アプリケーションの UI 処理ロジックを実装し、MSAL を介して対話的にトークンを取得できるようになりました。

MSAL では、トークンを取得するための主要なメソッドとして `acquireTokenSilently()` と `acquireTokenInteractively()` が公開されています。 

- `acquireTokenSilently()` では、ユーザーのサインインが行われ、アカウントが存在すればユーザーとの対話なしにトークンが取得されます。

- ユーザーをサインインしようとすると、`acquireTokenInteractively()` によって常に UI が表示されます。 ブラウザーでセッション Cookie を使用するか、または Microsoft 認証システムでアカウントを使用して、対話型の SSO エクスペリエンスが提供される場合もあります。

以下のコードを `ViewController` クラスに追加します。

```swift
    @objc func callGraphAPI(_ sender: AnyObject) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>対話形式でのユーザー トークンの取得

下記のコードでは、`MSALInteractiveTokenParameters` オブジェクトを作成して `acquireToken` を呼び出すことによって、トークンを初めて取得します。 次に、以下を行うコードを追加します。

1. スコープを指定して `MSALInteractiveTokenParameters` を作成する。
2. 作成されたパラメーターを使用して `acquireToken()` を呼び出す。
3. エラーを処理する。 詳細については、[iOS および macOS 用の MSAL のエラー処理に関するガイド](msal-handling-exceptions.md)を参照してください。
4. 正常なケースを処理する。

次のコードを `ViewController` クラスに追加します。

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
        
    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in
            
        // #3
        if let error = error {
                
            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }
            
        guard let result = result else {
                
            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }
            
        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateSignOutButton(enabled: true)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>トークンの自動取得

更新されたトークンを自動で取得するには、次のコードを `ViewController` クラスに追加します。 これにより、`MSALSilentTokenParameters` オブジェクトが作成され、`acquireTokenSilent()` が呼び出されます。

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API を呼び出す 

トークンを取得したら、アプリでそれを HTTP ヘッダーで使用して、許可された要求を Microsoft Graph に対して行うことができます。

| ヘッダー キー    | value                 |
| ------------- | --------------------- |
| Authorization | Bearer \<access-token> |

以下のコードを `ViewController` クラスに追加します。

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

Microsoft Graph API の詳細については、[Microsoft Graph API](https://graph.microsoft.com) に関するページを参照してください。

### <a name="use-msal-for-sign-out"></a>サインアウトに MSAL を使用する

次に、サインアウトのサポートを追加します。

> [!Important]
> MSAL でサインアウトすると、ユーザーに関する既知の情報がすべてこのアプリケーションから削除されますが、ユーザーのデバイスではアクティブなセッションが維持されます。 ユーザーが再びサインインを試みた場合、サインイン UI が表示されることがありますが、デバイス セッションはまだアクティブであるため、資格情報を再入力する必要はありません。

サインアウト機能を追加するには、`ViewController` クラスの内部に次のコードを追加します。 このメソッドでは、すべてのアカウントが順次削除されます。

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.updateLogging(text: "")
            self.updateSignOutButton(enabled: false)
            self.accessToken = ""
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>トークンのキャッシュの有効化

MSAL では、既定でアプリのトークンが iOS または macOS のキーチェーンにキャッシュされます。 

トークンのキャッシュを有効化するには:
1. アプリケーションが正しく署名されていることを確認します。
2. Xcode プロジェクトの設定 > **[機能] タブ** >  **[Enable Keychain Sharing]\(キーチェーン共有を有効にする\)** に移動します。
3. [ **+** ] をクリックし、次の **[キーチェーン グループ]** エントリを入力します。3.a iOS の場合は `com.microsoft.adalcache` を入力します。3.b macOS の場合は `com.microsoft.identity.universalstorage` を入力します。

### <a name="add-helper-methods"></a>ヘルパー メソッドの追加
次のヘルパー メソッドを `ViewController` クラスに追加して、サンプルを完成させます。

### <a name="ios-ui"></a>iOS UI:

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="macos-ui"></a>macOS UI:

```swift
func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
```



### <a name="multi-account-applications"></a>マルチアカウント アプリケーション

このアプリは、シングル アカウントのシナリオを対象にビルドされています。 MSAL ではマルチアカウントのシナリオもサポートされますが、その場合はアプリで追加の作業が必要となります。 トークンを必要とする各アクションに使用するアカウントをユーザーが選択するための UI を作成する必要があります。 あるいは、アプリでヒューリスティックを実装して、`getAccounts()` メソッドによって使用するアカウントを選択することもできます。

## <a name="test-your-app"></a>アプリをテストする

### <a name="run-locally"></a>ローカルで実行する

アプリをビルドし、テスト デバイスまたはシミュレーターに展開します。 サインインして、Azure AD または個人用 Microsoft アカウントのトークンを取得できるようになります。

ユーザーは、アプリに初めてサインインするときに、Microsoft Identity から、要求されたアクセス許可に同意するよう求められます。  ほとんどのユーザーは同意できますが、一部の Azure AD テナントではユーザーによる同意が無効になっており、全ユーザーに代わって管理者が同意を行う必要があります。 このシナリオをサポートするには、Azure portal でアプリのスコープを登録します。

サインインした後、Microsoft Graph の `/me` エンドポイントから返されたデータがアプリに表示されます。

## <a name="get-help"></a>問い合わせ

このチュートリアルまたは Microsoft ID プラットフォームで問題が発生した場合は、[ヘルプとサポート](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)に関する記事をご覧ください。

Microsoft ID プラットフォームの改善にご協力ください。 簡単な 2 つの質問からなるアンケートに記入し、ご意見をお聞かせください。

> [!div class="nextstepaction"]
> [Microsoft ID プラットフォームのアンケート](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
