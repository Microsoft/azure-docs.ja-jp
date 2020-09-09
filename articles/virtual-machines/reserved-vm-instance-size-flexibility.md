---
title: 仮想マシン サイズの柔軟性 - Azure Reserved VM Instances
description: 予約 VM インスタンスを購入する際に適用されるサイズ シリーズについて説明します。
author: manish-shukla01
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: manshuk
ms.openlocfilehash: 381023a0650b078a74c01ff7b698aebacb256147
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855503"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Reserved VM Instances での仮想マシン サイズの柔軟性

予約 VM インスタンスを購入する場合は、インスタンス サイズの柔軟性または容量の優先度を選択します。 予約 VM インスタンスの最適化設定の設定または変更の詳細については、「[予約 VM インスタンスの最適化設定を変更する](../cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)」を参照してください。

インスタンス サイズの柔軟性に向けて最適化された予約仮想マシン インスタンスを使用して、購入した予約を同じインスタンス サイズの柔軟性グループ内の仮想マシン (VM) サイズに適用できます。 たとえば、(Standard_DS5_v2 などの) DSv2 シリーズに記載されている VM サイズの予約を購入した場合、同じインスタンス サイズの柔軟性グループに記載されているその他の 4 つのサイズに予約割引を適用できます。

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

ただし、別のインスタンス サイズの柔軟性グループに記載されている VM サイズにその予約割引を適用することはできません。たとえば、DSv2 シリーズのハイ メモリの SKU に該当する Standard_DS11_v2、Standard_DS12_v2 などです。

インスタンス サイズの柔軟性グループ内で予約割引が適用される VM の数は、予約を購入するときに選択する VM サイズによって異なります。 また、これは実行させている VM のサイズにも依存します。 比率の列では、そのインスタンス サイズの柔軟性グループ内の各 VM サイズの相対的な占有領域が比較されています。 比率の値を使用して、実行させている VM に対して予約割引がどのように適用されるのか計算します。

## <a name="examples"></a>例

次の例では、DSv2 シリーズの表にあるサイズと比率を使用します。

予約仮想マシン インスタンスをサイズ Standard_DS4_v2 で購入するとします。ここで、比率、またはこのシリーズ内の他のサイズと比較した相対的なフットプリントは、8 です。

- シナリオ 1: Standard_DS1_v2 サイズの VM を、比率 1 で 8 つ実行します。 これらの 8 つの VM すべてに、予約割引が適用されます。
- シナリオ 2: Standard_DS2_v2 サイズの VM を、各比率 2 で2 つ実行します。 また、Standard_DS3_v2 サイズの VM を、比率 4 で実行します。 フットプリントの合計は 2 + 2 + 4 = 8 です。 したがって、これらの 3 つの VM すべてに、予約割引が適用されます。
- シナリオ 3: Standard_DS5_v2 を、比率 16 で 1 つ実行します。 その VM のコンピューティング コストの半分に、予約割引が適用されます。

次のセクションでは、インスタンス サイズの柔軟性に向けて最適化された予約 VM インスタンスを購入する際に、同じサイズ シリーズのグループ内にあるサイズを示します。

## <a name="instance-size-flexibility-ratio-for-vms"></a>VM のインスタンス サイズの柔軟性の比率 

次の CSV には、インスタンス サイズの柔軟性グループ、ArmSkuName、および比率が含まれています。  

[インスタンス サイズの柔軟性の比率](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

このファイルをプログラムで使用できるように、ファイルの URL とスキーマを固定したままにします。 データはまもなく API からも利用可能になる予定です。

## <a name="next-steps"></a>次のステップ

詳細については、「[Azure の予約とは](../cost-management-billing/reservations/save-compute-costs-reservations.md)」を参照してください。
