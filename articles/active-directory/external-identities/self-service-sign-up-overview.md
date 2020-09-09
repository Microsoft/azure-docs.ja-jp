---
title: 外部 ID に対するセルフサービス サインアップ - Azure AD
description: セルフサービス サインアップを有効にすることで、外部ユーザーが自分でアプリケーションにサインアップできるようにする方法について説明します。 セルフサービス サインアップのユーザー フローをカスタマイズして、個人用に設定されたサインアップ エクスペリエンスを作成します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34b08e2e530843dd98c87e424812706247388228
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87907384"
---
# <a name="self-service-sign-up-preview"></a>セルフサービス サインアップ (プレビュー)

> [!NOTE]
> セルフサービス サインアップは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

アプリケーションを外部ユーザーと共有する場合、アプリケーションにアクセスする必要があるユーザーが常に事前にわかっているとは限りません。 個人に招待を直接送信する代わりに、セルフサービス サインアップを有効にすることで、外部ユーザーが自分で特定のアプリケーションにサインアップできるようにすることができます。 セルフサービス サインアップのユーザー フローをカスタマイズすることにより、個人用に設定されたサインアップ エクスペリエンスを作成できます。 たとえば、Azure AD またはソーシャル ID プロバイダーにサインアップするオプションを提供したり、サインアップ プロセス中にユーザーに関する情報を収集したりできます。

> [!NOTE]
> 組織によって構築されたアプリにユーザー フローを関連付けることができます。 ユーザー フローは、SharePoint や Teams などの Microsoft アプリには使用できません。

## <a name="user-flow-for-self-service-sign-up"></a>セルフサービス サインアップのユーザー フロー

セルフサービス サインアップのユーザー フローでは、共有するアプリケーションを通じて外部ユーザー向けのサインアップ エクスペリエンスが作成されます。 ユーザー フローは、1 つまたは複数のアプリケーションに関連付けることができます。 まず、テナントに対してセルフサービス サインアップを有効にし、外部ユーザーがサインインに使用できるようにする ID プロバイダーと統合します。 次に、サインアップ ユーザーフローを作成してカスタマイズし、アプリケーションをそれに割り当てます。
ユーザー フローの設定を構成して、ユーザーがアプリケーションにサインアップする方法を制御できます。

- サインインに使用するアカウントの種類 (Facebook などのソーシャル アカウント、または Azure AD アカウント)
- ユーザー サインアップから収集する属性 (名、郵便番号、居住国/地域など)

ユーザーがアプリケーションにサインインしようとすると、それが Web アプリケーション、モバイル アプリケーション、デスクトップ アプリケーション、シングルページ アプリケーション (SPA) のいずれであっても、アプリケーションによって、ユーザー フローで提供されるエンドポイントへの承認要求が開始されます。 ユーザー フローによって、ユーザーのエクスペリエンスが定義および制御されます。 ユーザーがサインアップ ユーザー フローが完了すると、Azure AD によってトークンが生成され、ユーザーはアプリケーションにリダイレクトされます。 サインアップが完了すると、ディレクトリ内のユーザーに対してゲスト アカウントがプロビジョニングされます。 複数のアプリケーションで同じユーザー フローを使用できます。

## <a name="example-of-self-service-sign-up"></a>セルフサービス サインアップの例

次の例では、ゲスト ユーザーに対するセルフサービス サインアップ機能を使用して、ソーシャル ID プロバイダーを Azure AD に組み込む方法を示します。  
Woodgrove のパートナーが Woodgrove アプリを開きます。 ユーザーがサプライヤー アカウントにサインアップすることを決定して、サプライヤー アカウントの要求を選択すると、セルフサービス サインアップのフローが開始されます。

![セルフサービス サインアップの開始ページの例](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

ユーザーは、選択した電子メールを使用してサインアップを行います。

![Facebook を選択してサインインする例](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Azure AD により、パートナーの Facebook アカウントを使用して Woodgrove との関係が作成され、サインアップした後にユーザーの新しいゲスト アカウントが作成されます。

Woodgrove は、氏名、勤務先名、ビジネス登録コード、電話番号など、ユーザーの詳細を知りたいと考えています。

![ユーザーのサインアップ属性を示す例](media/self-service-sign-up-overview/example-enter-user-attributes.png)

ユーザーは情報を入力し、サインアップ フローを続行して、必要なリソースにアクセスできるようになります。

![サインインしたユーザーを示す例](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>次のステップ

 詳細については、[アプリにセルフサービス サインアップを追加する](self-service-sign-up-user-flow.md)方法に関するページを参照してください。