---
title: "Java SDK から Maven への移行 - Maven を使用するように古い Azure Service Fabric Java アプリケーションを更新する | Microsoft Docs"
description: "Service Fabric Java SDK を使用していた古い Java アプリケーションを更新し、Service Fabric Java 依存関係を Maven からフェッチします。 このセットアップを完了すると、古い Java アプリケーションをビルドすることができます。"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: saysa
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 2123c5f26d77045bd22af56a844fdbf222930e7b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>Maven から Java ライブラリをフェッチするよう以前の Java Service Fabric アプリケーションを更新する
最近、Service Fabric Java バイナリが Service Fabric Java SDK から Maven ホスティングに移行されました。 これで、**mavencentral** を使用して最新の Service Fabric Java 依存関係をフェッチできるようになりました。 このクイック スタートでは、以前に Service Fabric Java SDK と共に使用するように作成した既存の Java アプリケーションを更新します。Maven ベースのビルドに対応するように、Yeoman テンプレートまたは Eclipse を使用します。

## <a name="prerequisites"></a>前提条件
1. 最初に既存の Java SDK をアンインストールする必要があります。

  ```bash
  sudo dpkg -r servicefabricsdkjava
  ```
2. [ここ](service-fabric-cli.md)に記載されている手順に従って、最新の Service Fabric CLI をインストールします。

3. Service Fabric Java アプリケーションをビルドして操作するには、必ず JDK 1.8 と Gradle がインストールされている必要があります。 まだインストールしていない場合は、以下を実行して、JDK 1.8 (openjdk-8-jdk) と Gradle をインストールすることができます。

 ```bash
 sudo apt-get install openjdk-8-jdk-headless
 sudo apt-get install gradle
 ```
4. [ここ](service-fabric-application-lifecycle-sfctl.md)に記載されている手順に従って、新しい Service Fabric CLI を使用するようにアプリケーションのインストール/アンインストール スクリプトを更新します。 参考にするために、入門用の[例](https://github.com/Azure-Samples/service-fabric-java-getting-started)を参照することができます。

>[!TIP]
> Service Fabric Java SDK をアンインストールすると、Yeoman は機能しなくなります。 Service Fabric Yeoman Java テンプレート ジェネレーターを起動して動作させるには、[ここ](service-fabric-create-your-first-linux-application-with-java.md)に記載されている前提条件に従ってください。

## <a name="service-fabric-java-libraries-on-maven"></a>Maven 上の Service Fabric Java ライブラリ
Service Fabric Java ライブラリは、Maven でホストされてきました。 **mavenCentral** から Service Fabric Java ライブラリを使用するために、プロジェクトの ``pom.xml`` または ``build.gradle`` に依存関係を追加することができます。

### <a name="actors"></a>アクター

アプリケーションの Service Fabric Reliable Actors サポート。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.10.0'
  }
  ```

### <a name="services"></a>サービス

アプリケーションの Service Fabric ステートレス サービス サポート。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.10.0'
  }
  ```

### <a name="others"></a>その他
#### <a name="transport"></a>トランスポート

Service Fabric Java アプリケーションのトランスポート層サポート。 トランスポート層でプログラムを作成する場合以外は、Reliable Actors または Service アプリケーションにこの依存関係を明示的に追加する必要はありません。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.10.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric サポート

Service Fabric のシステム レベルのサポート。ネイティブの Service Fabric ランタイムと対話します。 Reliable Actors または Service アプリケーションにこの依存関係を明示的に追加する必要はありません。 上記の他の依存関係を取り込むときに、Maven から自動的にフェッチされます。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.10.0'
  }
  ```


## <a name="migrating-service-fabric-stateless-service"></a>Service Fabric ステートレス サービスの移行

Maven からフェッチされた Service Fabric 依存関係を使用して既存の Service Fabric ステートレス Java サービスをビルドできるようにするには、サービス内の ``build.gradle`` ファイルを更新する必要があります。 以前は、次のようになっていました。
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```
現在は、Maven から依存関係をフェッチするために、**更新された** ``build.gradle`` には次のような対応する部分があります。
```
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```
一般に、Service Fabric ステートレス Java サービスに対するビルド スクリプトがどのようなものであるかを把握するには、入門用の例で任意のサンプルを参照してください。 EchoServer サンプルの build.gradle は、[ここ](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/Services/EchoServer/EchoServer1.0/EchoServerService/build.gradle)にあります。

## <a name="migrating-service-fabric-actor-service"></a>Service Fabric アクター サービスの移行

Maven からフェッチされた Service Fabric 依存関係を使用して既存の Service Fabric Actor Java アプリケーションをビルドできるようにするには、インターフェイス パッケージ内とサービス パッケージ内の ``build.gradle`` ファイルを更新する必要があります。 TestClient パッケージがある場合は、それも更新する必要があります。 そのため、アクター ``Myactor`` の場合は、次の場所を更新する必要があります。
```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>インターフェイス プロジェクト用のビルド スクリプトの更新

以前は、次のようになっていました。
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```
現在は、Maven から依存関係をフェッチするために、**更新された** ``build.gradle`` には次のような対応する部分があります。
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>アクター プロジェクト用のビルド スクリプトの更新

以前は、次のようになっていました。
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```
現在は、Maven から依存関係をフェッチするために、**更新された** ``build.gradle`` には次のような対応する部分があります。
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>テスト クライアント プロジェクト用のビルド スクリプトの更新

ここでの変更は、前のセクション (アクター プロジェクト) で説明した変更と似ています。 以前は、Gradle スクリプトは次のようなものでした。
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
      compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
  destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```
現在は、Maven から依存関係をフェッチするために、**更新された** ``build.gradle`` には次のような対応する部分があります。
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>次のステップ

* [Yeoman を使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse 用の Service Fabric プラグインを使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-get-started-eclipse.md)
* [Service Fabric CLI を使用した Service Fabric クラスターの対話操作](service-fabric-cli.md)

