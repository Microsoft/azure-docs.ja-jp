---
title: "アプリケーションのアップグレード: データのシリアル化 | Microsoft Docs"
description: "データのシリアル化のベスト プラクティスとデータのシリアル化がアプリケーションのローリング アップグレードに与える影響"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 6aa3ac7842df4657fca7f6b4264e1c6fe52dc0c6
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017


---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>データのシリアル化がアプリケーションのアップグレードに与える影響
[アプリケーションのローリング アップグレード](service-fabric-application-upgrade.md)では、アップグレードはノードのサブセットに、一度に 1 つのアップグレード ドメインのみに適用されます。 このプロセス中に、一部のアップグレード ドメインがアプリケーションの新しいバージョンになり、一部のアップグレード ドメインはアプリケーションの以前のバージョンになります。 ロールアウト時に、アプリケーションの新しいバージョンは、古いバージョンのデータを、アプリケーションの古いバージョンは新しいバージョンのデータを読み取ることができる必要があります。 データ形式の上位互換性と下位互換性がない場合は、アップグレードが失敗するか、データが失われたり、破損したりするおそれがあります。 この記事では、データ形式の構成要素と、データが上位互換性と下位互換性を確保するためのベスト プラクティスについて説明します。

## <a name="what-makes-up-your-data-format"></a>データ形式の構成要素
Azure Service Fabric では、永続化されレプリケートされたデータが C# クラスから取得されます。 [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) を使用するアプリケーションの場合、このデータは信頼性の高いディクショナリとキューのオブジェクトです。 [Reliable Actors](service-fabric-reliable-actors-introduction.md)を使用するアプリケーションの場合は、アクターのバッキング ステートです。 これらの c# クラスは、永続化されレプリケートされるよう、シリアル化可能である必要があります。 そのため、データの形式は、シリアル化されるフィールドやプロパティ、シリアル化の方法によっても定義されます。 たとえば、`IReliableDictionary<int, MyClass>` では、データはシリアル化された `int` で、シリアル化された `MyClass` です。

### <a name="code-changes-that-result-in-a-data-format-change"></a>結果的にデータ形式が変更されるコード変更
C# クラスによって、データ形式が決まるため、クラスへの変更によってデータ形式が変わることがあります。 ローリング アップグレードで、データ形式の変更を処理できるようにするには、特に注意する必要があります。 データ形式が変更される可能性のある例:

* フィールドまたはプロパティを追加または削除する
* フィールドまたはプロパティの名前を変更する
* フィールドまたはプロパティの種類を変更する
* クラスの名前または名前空間を変更する

### <a name="data-contract-as-the-default-serializer"></a>データ コントラクトが既定のシリアライザーです
通常、このシリアライザーには、データが古いまたは *新しい* バージョンの場合でも、データを読み取り、現在のバージョンにシリアル化を解除する役割があり。 既定のシリアライザーは、バージョン管理を適切に定義された規則を持つ [データ コントラクト シリアライザー](https://msdn.microsoft.com/library/ms733127.aspx)です。 Reliable Collections では、シリアライザーをオーバーライドできますが、Reliable Actors では現在のところオーバーライドできません。 データ シリアライザーは、ローリング アップグレードを有効にする際に重要な役割を果たします。 データ コントラクト シリアライザーは、Service Fabric アプリケーションに推奨されるシリアライザーです。

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>データの形式がローリング アップグレードに与える影響
ローリング アップグレード中に、シリアライザーが古いバージョンと *新しい* バージョンのデータを発見する可能性がある主なシナリオは 2 つあります。

1. ノードがアップグレードされ、バックアップを開始すると、新しいシリアライザーは古いバージョンで保存されたデータを読み込みます。
2. ローリング アップグレード中、クラスターにコードの古いバージョンと新しいバージョンが混在します。 レプリカは異なるアップグレード ドメインで配置されることがあり、互いにデータを送信するため、新規と既存の両方のバージョンのデータが、新規または既存のバージョンのシリアライザーによって検出される可能性があります。

> [!NOTE]
> ここでは、"新しいバージョン" と "古いバージョン" は実行中のコードのバージョンを指します。 "新しいシリアライザー" は、アプリケーションの新しいバージョンで実行される、シリアライザーのコードを指します。 "新しいデータ" は、アプリケーションの新しいバージョンからのシリアライズされた C# クラスを指します。
> 
> 

コードとデータ形式の 2 つのバージョンは、上位互換性と下位互換性の両方がある必要があります。 互換性がない場合は、ローリング アップグレードが失敗するか、データが失われる可能性があります。 コードまたはシリアライザーが古いバージョンを検出した時に、例外またはエラーをスローする可能性があるため、ローリング アップグレードが失敗することがあります。 たとえば、新しいプロパティが追加され、古いシリアライザーが逆シリアル化中にそれを破棄する場合、データが失われることがあります。

データ コントラクトは、データの互換性を確保するための推奨されるソリューションです。 これには、フィールドの追加、削除、変更用に適切に定義されたバージョン管理規則があります。 また、不明なフィールドの処理、シリアル化と逆シリアル化プロセスへのフッキング、クラスの継承に対するサポートもあります。 詳細については、[「データ コントラクトの使用」](https://msdn.microsoft.com/library/ms733127.aspx)をご覧ください。

## <a name="next-steps"></a>次のステップ
[Visual Studio を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial.md) に関する記事では、Visual Studio を使用してアプリケーションをアップグレードする方法について説明します。

[PowerShell を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial-powershell.md) に関する記事では、PowerShell を使用したアプリケーションのアップグレードについて説明します。

[アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)を使用して、アプリケーションのアップグレード方法を制御します。

「 [高度なトピック](service-fabric-application-upgrade-advanced.md)」を参照して、アプリケーションをアップグレードするときの高度な機能の使用方法を学習します。

「 [アプリケーションのアップグレードのトラブルシューティング ](service-fabric-application-upgrade-troubleshooting.md)」の手順を参照して、アプリケーションのアップグレードでの一般的な問題を修正します。


