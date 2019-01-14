---
title: 'サービス間認証: .NET SDK から Azure Active Directory を使用して Azure Data Lake Storage Gen1 に対する認証を行う | Microsoft Docs'
description: .NET SDK から Azure Active Directory を使用して Azure Data Lake Storage Gen1 に対するサービス間認証を行う方法について説明します
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 58d8cfdbd2ad5d7e727decfa3e3cfdd7151b0048
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250206"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Azure Data Lake Storage Gen1 に対する .NET SDK を使用したサービス間認証
> [!div class="op_single_selector"]
> * [Java の使用](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK の使用](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python の使用](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API の使用](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

この記事では、.NET SDK を使用して、Azure Data Lake Storage Gen1 に対するサービス間認証を行う方法について説明します。 .NET SDK を使用した Data Lake Storage Gen1 に対するエンド ユーザー認証については、[Data Lake Storage Gen1 による .NET SDK を使用したエンドユーザー認証](data-lake-store-end-user-authenticate-net-sdk.md)に関するページを参照してください。


## <a name="prerequisites"></a>前提条件
* **Visual Studio 2013、2015、2017**。 以下の手順では、Visual Studio 2017 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory "Web" アプリケーションを作成します**。 [Data Lake Storage Gen1 に対する Azure Active Directory を使用したサービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)の手順を完了している必要があります。

## <a name="create-a-net-application"></a>.NET アプリケーションの作成
1. Visual Studio を開き、コンソール アプリケーションを作成します。
2. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。
3. **[新しいプロジェクト]** で、次の値を入力するか、選択します。

   | プロパティ | 値 |
   | --- | --- |
   | Category |テンプレート/Visual C#/Windows |
   | テンプレート |コンソール アプリケーション |
   | Name |CreateADLApplication |
4. **[OK]** をクリックしてプロジェクトを作成します。

5. NuGet パッケージをプロジェクトに追加します。

   1. ソリューション エクスプローラーでプロジェクト名を右クリックし、 **[NuGet パッケージの管理]** をクリックします。
   2. **[NuGet パッケージ マネージャー]** タブで、**[パッケージ ソース]** が **nuget.org** に設定されており、**[プレリリースを含める]** チェック ボックスがオンになっていることを確認します。
   3. 以下の NuGet パッケージを検索してインストールします。

      * `Microsoft.Azure.Management.DataLake.Store` - このチュートリアルでは、v2.1.3-preview を使用します。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - このチュートリアルでは、v2.2.12 を使用します。

        ![NuGet ソースの追加](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "新しい Azure Data Lake アカウントの作成")
   4. **NuGet パッケージ マネージャー**を閉じます。

6. **Program.cs**を開き、既存のコードを削除し、次のステートメントに置き換えて、名前空間の参照を追加します。

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

## <a name="service-to-service-authentication-with-client-secret"></a>クライアント シークレットによるサービス間認証
このスニペットを .NET クライアント アプリケーションに追加します。 プレースホルダーの値を、Azure AD の Web アプリケーション (前提条件として一覧表示) から取得した値で置き換えます。  このスニペットを使用すると、Data Lake Storage Gen1 に対し、Azure AD Web アプリケーションのクライアント シークレット/キーを使用して、**非対話形式**でアプリケーションを認証することができます。 

    private static void Main(string[] args)
    {    
        // Service principal / appplication authentication with client secret / key
        // Use the client ID of an existing AAD "Web App" application.
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
        var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
        var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
    }

上記のスニペットでは、ヘルパー関数 `GetCreds_SPI_SecretKey` を使用しています。 このヘルパー関数のコードは[こちらの GitHub で](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey)入手できます。

## <a name="service-to-service-authentication-with-certificate"></a>証明書によるサービス間認証

このスニペットを .NET クライアント アプリケーションに追加します。 プレースホルダーの値を、Azure AD の Web アプリケーション (前提条件として一覧表示) から取得した値で置き換えます。 このスニペットを使用すると、Data Lake Storage Gen1 に対し、Azure AD Web アプリケーションの証明書を使用して、**非対話形式**でアプリケーションを認証することができます。 Azure AD アプリケーションを作成する方法の手順については、[証明書を使用したサービス プリンシパルの作成](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)に関するページを参照してください。

    
    private static void Main(string[] args)
    {
        // Service principal / application authentication with certificate
        // Use the client ID and certificate of an existing AAD "Web App" application.
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
        var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
        var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
    }

上記のスニペットでは、ヘルパー関数 `GetCreds_SPI_Cert` を使用しています。 このヘルパー関数のコードは[こちらの GitHub で](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert)入手できます。

## <a name="next-steps"></a>次の手順
この記事では、.NET SDK からサービス間認証を使って Data Lake Storage Gen1 に対して認証を行う方法について説明しました。 これで、.NET SDK を使用して Data Lake Storage Gen1 を操作する方法について説明した次の記事に進めるようになりました。

* [.NET SDK を使用した Data Lake Storage Gen1 に対するアカウント管理操作](data-lake-store-get-started-net-sdk.md)
* [.NET SDK を使用した Data Lake Storage Gen1 に対するデータ操作](data-lake-store-data-operations-net-sdk.md)


