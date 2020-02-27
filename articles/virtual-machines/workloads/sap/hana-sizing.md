---
title: SAP HANA on Azure (L インスタンス) のサイズ変更 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) のサイズ変更。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 404f8318816edcc2cfd1c50ca42304ff6ec93039
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616904"
---
# <a name="sizing"></a>サイズ変更

通常、HANA L インスタンスのサイズ変更は HANA のサイズ変更とまったく同じです。 デプロイ済みの既存のシステムを他の RDBMS から HANA に移行する場合、既存の SAP システムで実行される複数のレポートが SAP から提供されています。 データベースを HANA に移行する場合、これらのレポートによってデータが確認され、HANA インスタンスのメモリ要件が計算されます。 これらのレポートを実行する方法と、レポートの最新の修正プログラムまたはバージョンを入手する方法の詳細については、次の SAP ノートをご覧ください。

- [SAP Note #1793345 - Sizing for SAP Suite on HANA (SAP ノート #1793345 - HANA での SAP Suite のサイズ変更)](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite on HANA and S/4 HANA sizing report (SAP ノート #1872170 - Suite on HANA および S/4 HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - FAQ:SAP BW on HANA のサイズ変更レポート](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - Sizing Report for BW on HANA (SAP ノート #1736976 - BW on HANA のサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - New Sizing Report for BW on HANA (SAP ノート #2296290 - BW on HANA の新しいサイズ変更レポート)](https://launchpad.support.sap.com/#/notes/2296290)

緑色のフィールドの実装では、SAP Quick Sizer を使用して、HANA 上への SAP ソフトウェアの実装に必要なメモリを計算できます。

データ量の増加に伴って、HANA のメモリ要件が増加します。 将来的に必要になるメモリ容量を予測できるように、現在のメモリ使用量に注意してください。 メモリ要件に基づいて、いずれかの HANA L インスタンス SKU に要求をマップできます。

**次の手順**
- [オンボードの要件](hana-onboarding-requirements.md)を参照してください