---
title: 非対話型認証 .NET アプリケーション - Azure HDInsight
description: Azure HDInsight で非対話型認証 Microsoft .NET アプリケーションを作成する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/23/2019
ms.openlocfilehash: a696ba4f7bab85f2353e80d028220688531b3b69
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021767"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>非対話型認証 .NET HDInsight アプリケーションを作成する

アプリケーション独自の ID (非対話型) またはアプリケーションのサインイン ユーザーの ID (対話型) のいずれかで、Microsoft .NET Azure HDInsight アプリケーションを実行します。 この記事では、Azure に接続して HDInsight を管理する非対話型認証 .NET アプリケーションを作成する方法について説明します。 対話型アプリケーションのサンプルについては、「[Azure HDInsight への接続](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight)」をご覧ください。

非対話型 .NET アプリケーションでは、以下が必要です。

* Azure サブスクリプションのテナント ID (別名: *ディレクトリ ID*)。 「[Get tenant ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)」 (テナント ID を取得する) を参照してください。
* Azure Active Directory (Azure AD) アプリケーションのクライアント ID。 「[Azure Active Directory アプリケーションを作成する](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)」および「[アプリケーション ID と認証キーを取得する](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)」をご覧ください。
* Azure AD アプリケーションの秘密鍵。 「[アプリケーション ID と認証キーを取得する](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)」をご覧ください。

## <a name="prerequisites"></a>前提条件

HDInsight クラスター。 [使用に関するチュートリアル](hadoop/apache-hadoop-linux-tutorial-get-started.md)をご覧ください。

## <a name="assign-a-role-to-the-azure-ad-application"></a>Azure AD アプリケーションにロールを割り当てる

Azure AD アプリケーションに[ロール](../role-based-access-control/built-in-roles.md)を割り当てて、アクションを実行するためのアクセス許可を付与します。 スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。 アクセス許可は、スコープの下位レベルに継承されます たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションはリソース グループとその中のリソースを読み取ることができます。 この記事では、リソース グループ レベルでスコープを設定します。 詳細については、「[Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](../role-based-access-control/role-assignments-portal.md)」を参照してください。

**Azure AD アプリケーションに所有者ロールを追加するには**

1. [Azure portal](https://portal.azure.com) にサインインする
1. この記事の後半で Hive クエリを実行する HDInsight クラスターを含むリソース グループに移動します。 多数のリソース グループがある場合は、フィルターを使って目的のものを見つけることができます。
1. リソース グループ メニューで **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当て]** タブを選択して、現在のロールの割り当てを表示します。
1. ページの最上部で **[追加]** を選択します。
1. 説明に従って、所有者ロールを Azure AD アプリケーションに追加します。 ロールが正常に追加されると、アプリケーションが所有者ロールに一覧表示されます。

## <a name="develop-an-hdinsight-client-application"></a>HDInsight クライアント アプリケーションを開発する

1. C# コンソール アプリケーションを作成します。
2. 次の [NuGet](https://www.nuget.org/) パッケージを追加します。

    * `Install-Package Microsoft.Azure.Common.Authentication -Pre`
    * `Install-Package Microsoft.Azure.Management.HDInsight -Pre`
    * `Install-Package Microsoft.Azure.Management.Resources -Pre`

3. 次のコードを実行します。

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```

## <a name="next-steps"></a>次のステップ

* [Azure Portal で Azure Active Directory アプリケーションとサービス プリンシパルを作成する](../active-directory/develop/howto-create-service-principal-portal.md)。
* [Azure Resource Manager でサービス プリンシパルを認証する](../active-directory/develop/howto-authenticate-service-principal-powershell.md)方法を学習する。
* [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) について学習する。
