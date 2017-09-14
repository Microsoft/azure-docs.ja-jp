---
title: "Azure Service Fabric マイクロサービス (プレビュー) をコンテナー化する方法"
description: "Azure Service Fabric は、Service Fabric マイクロサービスをコンテナー化する新機能を追加しました。 現在、この機能はプレビュー段階にあります。"
services: service-fabric
documentationcenter: .net
author: anmolah
manager: anmolah
editor: anmolah
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/04/2017
ms.author: anmola
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 6f8ad0bad8d1ae861e6b72f7e1a32ab0675813c2
ms.contentlocale: ja-jp
ms.lasthandoff: 08/12/2017

---
# <a name="how-to-containerize-your-service-fabric-reliable-services-and-reliable-actors-preview"></a>Service Fabric Reliable Services と Reliable Actors (プレビュー) をコンテナー化する方法

Service Fabric は、Service Fabric マイクロサービス (Reliable Services と Reliable Actor ベースのサービス) のコンテナー化をサポートしています。 詳細については、「[Service Fabric とコンテナー](service-fabric-containers-overview.md)」を参照してください。


 この機能はプレビュー段階です。この記事では、コンテナー内で実行されているサービスを取得するさまざまな手順について説明します。  

> [!NOTE]
> この機能はプレビュー段階であり、実稼働環境ではサポートされません。 現在、この機能は Windows でのみ機能します。

## <a name="steps-to-containerize-your-service-fabric-application"></a>Service Fabric アプリケーションをコンテナー化する手順

1. Visual Studio で Service Fabric アプリケーションを開きます。

2. クラス [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) をプロジェクトに追加します。 このクラスのコードは、コンテナー内で実行すると、アプリケーション内の Service Fabric ランタイム バイナリを適切に読み込むことができるヘルパーです。

3. コンテナー化するコード パッケージごとに、プログラムのエントリ ポイントのローダーを初期化します。 次のコード スニペットに示されている静的コンストラクターをプログラム エントリ ポイント ファイルに追加します。

  ```csharp
  namespace MyApplication
  {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
  ```

4. プロジェクトをビルドして[パッケージ](service-fabric-package-apps.md#Package-App)を作成します。 ビルドしてパッケージを作成するには、ソリューション エクスプローラーでアプリケーション プロジェクトを右クリックして **[パッケージ]** コマンドを選択します。

5. コンテナー化する必要があるコード パッケージごとに、PowerShell スクリプト [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1) を実行します。 使用方法は次のとおりです。
  ```powershell
    $codePackagePath = 'Path to the code package to containerize.'
    $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
    $applicationExeName = 'Name of the ode package executable.'
    CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
 ```
  次のスクリプトでは、$dockerPackageOutputDirectoryPath に Docker アーティファクトを含むフォルダーを作成します。 生成された Docker ファイルを変更して、必要に応じて任意のポートを開いたり、セットアップ スクリプトを実行したりします。

6. 次に、Docker コンテナー パッケージを[ビルド](service-fabric-get-started-containers.md#Build-Containers)してリポジトリに[プッシュ](service-fabric-get-started-containers.md#Push-Containers)します。

7. ApplicationManifest.xml と ServiceManifest.xml を変更し、コンテナー イメージ、リポジトリ情報、レジストリ認証、ポートとホスト間のマッピングを追加します。 マニフェストを変更する方法については、「[Windows で初めての Service Fabric コンテナー アプリケーションを作成する](service-fabric-get-started-containers.md)」を参照してください。 サービス マニフェストのコード パッケージ定義は、対応するコンテナー イメージで置き換える必要があります。 EntryPoint 型を ContainerHost 型に変更します。

  ```xml
<!-- Code package is your service executable. -->
<CodePackage Name="Code" Version="1.0.0">
  <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
  </EntryPoint>
  <!-- Pass environment variables to your container: -->    
</CodePackage>
  ```

8. レプリケーターとサービス エンドポイントについてポートとホスト間のマッピングを追加します。 Service Fabric によって実行時に両方のポートが割り当てられているため、割り当て済みポートをマッピングに使用するには、ContainerPort をゼロに設定します。

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
  </ContainerHostPolicies>
</Policies>
 ```

9. このアプリケーションをテストするには、バージョン 5.7 以降を実行しているクラスターにデプロイする必要があります。 また、このプレビュー機能を有効にするようにクラスター設定を編集および更新する必要があります。 この[記事](service-fabric-cluster-fabric-settings.md)の手順に従って、次のように設定を追加します。
```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
```
10. 次に、編集したアプリケーション パッケージをこのクラスターに[デプロイ](service-fabric-deploy-remove-applications.md)します。

以上の手順で、クラスターを実行しているコンテナー化された Service Fabric アプリケーションが作成されます。

## <a name="next-steps"></a>次のステップ
* [Service Fabric でのコンテナー](service-fabric-get-started-containers.md)の実行について確認します。
* Service Fabric の[アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)について確認します。

