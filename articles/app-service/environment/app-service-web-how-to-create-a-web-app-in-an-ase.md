---
title: ASE v1 での Web アプリの作成
description: App Service Environment v1 で Web アプリを作成する方法について説明します このドキュメントは、レガシ v1 ASE を使用するお客様にのみ提供されます。
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d0ac8e0cf187d1a6a329f2c7332c9ec37018d4cf
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962521"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>App Service Environment v1 で Web アプリを作成する

> [!NOTE]
> この記事は、App Service Environment v1 に関するものです。  より強力なインフラストラクチャ上で実行できる、使いやすい新しいバージョンの App Service Environment があります。 新しいバージョンの詳細については、「[App Service Environment の概要](intro.md)」を参照してください。
> 

## <a name="overview"></a>概要
このチュートリアルでは、[App Service Environment v1](app-service-app-service-environment-intro.md) (ASE) で Web アプリと App Service プランを作成する方法について説明します。 

> [!NOTE]
> Web アプリを作成する方法を知りたいが、App Service 環境で行う必要はない場合は、「 [.NET web アプリを作成する](../quickstart-dotnetcore.md) 」か、その他の言語やフレームワーク向けの関連するチュートリアルを参照してください。
> 
> 

## <a name="prerequisites"></a>前提条件
このチュートリアルは、App Service 環境が作成済みであることを前提としています。 まだ完了していない場合は、「 [App Service 環境を作成する](app-service-web-how-to-create-an-app-service-environment.md)」を参照してください。 

## <a name="create-a-web-app"></a>Web アプリを作成する
1. [Azure Portal](https://portal.azure.com/) で、 **[リソースの作成] > [Web + モバイル] > [Web アプリ]** の順にクリックします。 
   
    ![Azure portal で Web アプリを作成する場所を示すスクリーンショット。][1]
2. サブスクリプションを選択します。  
   
    サブスクリプションを複数保有している場合、App Service 環境でアプリを作成するには、App Service 環境を作成するときに使用したものと同じサブスクリプションを使用する必要があります。 
3. リソース グループを選択または作成します。
   
    *リソース グループ*を使用すると、関連する Azure リソースを 1 つの単位として管理でき、アプリ用に *Azure のロールベースのアクセス制御 (Azure RBAC)* 規則を作成する際に便利です。 詳細については、「[Azure Resource Manager の概要][ResourceGroups]」を参照してください。 
4. App Service プランを選択または作成します。
   
    *App Service プラン*は、管理された Web アプリのセットです。  通常、価格を選択すると、課金される価格は個々のアプリではなく App Service プランに適用されます。 ASE では、ASP に記載されているものではなく、ASE に割り当てられているコンピューティング インスタンスに対して課金されます。  Web アプリのインスタンスの数を増やすには、App Service プランのインスタンスを増やします。これにより、そのプラン内のすべての Web アプリが影響を受けます。  サイトのスロット、VNET 統合などのいくつかの機能には、プラン内での数量制限があります。  詳細については、「[Azure App Service プランの概要](../overview-hosting-plans.md)」を参照してください。
   
    ASE で App Service プランを識別するには、プラン名の下に表示されている場所を確認します。  
   
    ![ASE の App Service プランが表示される場所を示すスクリーンショット。][5]
   
    App Service 環境に既に存在する App Service プランを使用する場合は、そのプランを選択します。 App Service プランを新たに作成する場合は、このチュートリアルの次のセクション「 [App Service 環境で App Service プランを作成する](#createplan)」を参照してください。
5. Web アプリの名前を入力し、 **[作成]** をクリックします。 
   
    ASE で外部 VIP を使用する場合、ASE でのアプリの URL は [*サイト名*].azurewebsites.net ではなく、[*サイト名*].[*App Service Environment の名前*].p.azurewebsites.net になります。
   
    ASE で内部 VIP を使用する場合、ASE 内のアプリの URL は [*サイト名*].[*ASE の作成時に指定されたサブドメイン*] になります。   
    ASE の作成時に ASP を選択すると、 **[名前]** の下のサブドメインが更新されます。

## <a name="create-an-app-service-plan"></a><a name="createplan"></a> App Service プランを作成する
App Service 環境に App Service プランを作成する場合、ASE には共有ワーカーがないため、ワーカーの選択肢が異なります。  使用する必要があるワーカーは、管理者によって ASE に割り当てられたワーカーです。つまり、新しいプランを作成するには、ASE ワーカー プールに既に存在しているすべてのプランのインスタンスの総数よりも多くのワーカーを ASE ワーカー プールに割り当てておく必要があります。  プランを作成するのに十分なワーカーが ASE ワーカー プールにない場合は、ASE の管理者に依頼してワーカーを追加してもらう必要があります。

App Service 環境でホストされる App Service プランに関するもう 1 つの違いは、価格の選択肢がないことです。  App Service 環境を利用する場合、システムによって使用されるコンピューティング リソースについて料金を支払い、その環境内のプランに対する追加料金はありません。  通常、App Service プランを作成する際に、価格プランを選択します。これに基づいて、支払い価格が決定されます。  App Service 環境は、基本的に、コンテンツを作成できるプライベートな場所です。  支払いは環境に対するもので、コンテンツをホストする操作を対象にしたものではありません。

次の手順は、チュートリアルの前のセクションで説明した、Web アプリの作成時に App Service プランを作成する方法を示しています。

1. プランの選択用 UI で **[新規作成]** をクリックし、ASE 以外で通常行う場合と同様に、プランの名前を指定します。
2. 場所の選択コントロールで、使用する ASE を選択します。
   
    App Service 環境は、実質的にプライベートなデプロイの場所であるため、[場所] に表示されます。 
   
    ![目的の ASE を選択するための場所の選択コントロールを示すスクリーンショット。][2]
   
    場所の選択コントロールで ASE を選択すると、App Service プランの作成用 UI が更新されます。  場所に ASE システムの名前とそのリージョンが表示され、価格プランの選択コントロールがワーカー プールの選択コントロールに置き換えられます。  
   
    ![場所の選択コントロールで ASE を選択した後の ASE システムの詳細を示すスクリーンショット。][3]

### <a name="selecting-a-worker-pool"></a>ワーカー プールの選択
Azure App Service 内の App Service 環境以外の通常の操作では、3 つのコンピューティング サイズが専用の価格プランで選択できるようになっています。  これと似た方法として、ASE では、ワーカーのプールを 3 つまで定義し、そのワーカー プールで使用するコンピューティング サイズを指定できます。  ASE のテナントから見れば、App Service プランの価格プランとコンピューティング サイズを選択する代わりに、 *ワーカー プール*と呼ばれるものを選択することになります。  

ワーカー プールの選択 UI では、そのワーカー プールに使用されるコンピューティング サイズがその名の下に表示されます。  "使用可能" な数は、そのプールで使用できるコンピューティング インスタンスの数を示します。  プール全体ではこの数を超えるインスタンスが実際に含まれている可能性がありますが、この値は単に使用されていないインスタンスの数を表します。  App Service 環境を調整してコンピューティング リソースを追加する必要がある場合は、「 [App Service Environment の構成](app-service-web-configure-an-app-service-environment.md)」を参照してください。

![ASE のワーカー プールを選択できる [ワーカー プール] ウィンドウを示すスクリーンショット。][4]

この例では、使用できるワーカー プールが 2 つしかありません。 これは、ASE 管理者によってこれらの 2 つのワーカー プールのみにホストが割り当てられているためです。  3 つ目のワーカー プールは、VM が割り当てられているときに表示されます。  

## <a name="after-web-app-creation"></a>Web アプリを作成した後
ASE での Web アプリの実行と App Service プランの管理に関して、いくつかの点を考慮する必要があります。  

既に説明したように、ASE の所有者は、システムのサイズに責任を持つため、目的の App Service プランをホストするのに十分な容量があることも保証する必要があります。 利用可能なワーカーがない場合は、App Service プランを作成できません。  これは、Web アプリをスケールアップする場合も同じです。  より多くのインスタンスが必要な場合は、App Service 環境の管理者に依頼してワーカーを追加してもらう必要があります。

Web アプリと App Service プランを作成した後は、これをスケールアップすることをお勧めします。  ASE では、アプリのフォールト トレランスを提供するために、常に App Service プランのインスタンスが 2 つ以上必要です。  ASE での App Service プランのスケーリングは、App Service プラン用 UI を使用して通常行うのと同じです。  スケーリングの詳細については、「 [App Service 環境内で Web アプリをスケーリングする方法](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: /powershell/azure/?view=azps-3.8.0