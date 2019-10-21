---
title: Azure Service Fabric で実行されているコンテナーに証明書をインポートする| Microsoft Docs
description: Service Fabric コンテナー サービスに証明書ファイルをインポートする方法について説明します。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 496d47c88636b11375d97cc4bc50efd9d468d645
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170460"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Service Fabric で実行されているコンテナーに証明書ファイルをインポートする

証明書を指定して、コンテナー サービスをセキュリティで保護することができます。 Service Fabric には、コンテナー内のサービスから、Windows または Linux クラスター (バージョン 5.7 以降) のノードにインストールされている証明書にアクセスできるしくみがあります。 この証明書は、クラスターのすべてのノードの LocalMachine 下の証明書ストアにインストールする必要があります。 証明書に対応する秘密キーが利用可能、アクセス可能、および (Windows 上で) エクスポート可能である必要があります。 次のスニペットのように、証明書情報は `ContainerHostPolicies` タグのアプリケーション マニフェストで提供されます。

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Windows クラスターでは、アプリケーションの起動時にランタイムが、参照されている各証明書とそれに対応する秘密キーを、ランダム生成パスワードによってセキュリティで保護される PFX ファイルにエクスポートします。 PFX ファイルとパスワード ファイルには、コンテナー内で次の環境変数を使用してそれぞれアクセスできます。 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Linux クラスターの場合、証明書 (PEM) は X509StoreName で指定されているストアからコンテナーにコピーされます。 Linux での対応する環境変数は次のとおりです。

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

または、必要な形式の証明書が既にあり、コンテナー内の証明書にアクセスする場合は、アプリ パッケージ内にデータ パッケージを作成し、アプリケーション マニフェストで次のように指定します。

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

コンテナーへの証明書ファイルのインポートは、コンテナー サービスまたはプロセスによって行われます。 証明書をインポートするには、`setupentrypoint.sh` スクリプトを使うか、コンテナー プロセス内でカスタム コードを実行します。 PFX ファイルをインポートする C# のサンプル コードは次のとおりです。

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
この PFX 証明書は、アプリケーションやサービスの認証、または他のサービスとのセキュリティで保護された通信に、使うことができます。 既定では、ファイルは SYSTEM の ACL にだけ登録されます。 サービス の必要に応じて、他のアカウントの ACL に登録できます。

次の手順については、次の記事を参照してください。

* [Windows Server 2016 上での Service Fabric への Windows コンテナーのデプロイ](service-fabric-get-started-containers.md)
* [Linux 上での Service Fabric への Docker コンテナーのデプロイ](service-fabric-get-started-containers-linux.md)
