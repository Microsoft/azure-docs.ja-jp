---
title: Azure IoT Hub の IP 接続フィルター | Microsoft Docs
description: Azure IoT Hub への特定の IP アドレスからの接続をブロックする IP フィルター処理を使用する方法。 個別の IP アドレスまたは一定の範囲の IP アドレスからの接続をブロックできます。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: robinsh
ms.openlocfilehash: 1ba3c89ea4f964f9e6fd5f902aab29a83a058f25
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074719"
---
# <a name="use-ip-filters"></a>IP フィルターの使用

セキュリティは、Azure IoT Hub をベースとするすべての IoT ソリューションの重要な側面です。 場合によっては、セキュリティ構成の一部として、デバイスが接続できる IP アドレスを明示的に指定する必要があります。 *IP フィルター*機能を使用すると、特定の IPv4 アドレスからのトラフィックを拒否または許可するための規則を構成できます。

## <a name="when-to-use"></a>使用する場合

特定の IP アドレスの IoT Hub エンドポイントをブロックすると有用な特定のユース ケースには、次の 2 つがあります。

* IoT Hub が指定された範囲の IP アドレスからのトラフィックのみを受信し、それ以外のトラフィックをすべて拒否する必要がある場合。 たとえば、IoT Hub を [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) と共に使用して、IoT Hub とオンプレミス インフラストラクチャとの間にプライベート接続を作成する場合が該当します。

* IoT Hub の管理者によって疑わしいと識別された IP アドレスからのトラフィックを拒否する必要がある場合。

## <a name="how-filter-rules-are-applied"></a>フィルター規則の適用方法

IP フィルター規則は、IoT Hub サービス レベルで適用されます。 したがって、IP フィルター規則は、サポートされているプロトコルを使用するデバイスおよびバックエンド アプリからのすべての接続に適用されます。 ただし、(IoT Hub 接続文字列経由ではなく) [組み込みイベント ハブ互換エンドポイント](iot-hub-devguide-messages-read-builtin.md)から直接読み取るクライアントには IP フィルター規則は適用されません。 

IoT ハブの拒否 IP 規則に一致する IP アドレスからの接続試行に対しては、認証されていないアクセスを示す 401 状態コードとその説明が返されます。 IP 規則に関する記述は応答メッセージに含まれません。 IP アドレスを拒否すると、Azure Stream Analytics、Azure Virtual Machines、Azure portal のデバイス エクスプローラーなど、他の Azure サービスが IoT Hub と対話できなくなる可能性があります。

> [!NOTE]
> Azure Stream Analytics (ASA) を使用し、IP フィルターを有効にして IoT Hub からメッセージを読み取る必要がある場合、イベント ハブと互換性のある名前と IoT Hub のエンドポイントを利用し、ASA に[イベント ハブ ストリーム入力](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs)を手動で追加します。

## <a name="default-setting"></a>既定の設定

既定では、ポータルの IoT ハブの **[IP フィルター]** は空白になっています。 この既定の設定は、ハブが任意の IP アドレスからの接続を受け入れることを意味します。 この既定の設定は、IP アドレス範囲 0.0.0.0/0 を受け入れる規則と同じです。

[IP フィルター設定] ページに移動するには、 **[ネットワーク]** 、 **[パブリック アクセス]** 、 **[Selected IP Ranges]\(選択された IP 範囲\)** の順に選択します。

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="IoT Hub の既定の IP フィルター設定":::

## <a name="add-or-edit-an-ip-filter-rule"></a>IP フィルター規則の追加または編集

IP フィルター規則を追加するには、 **[+ IP フィルター規則の追加]** を選択します。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="IoT Hub への IP フィルター規則の追加":::

**[IP フィルター規則の追加]** を選択したら、フィールドに入力します。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="[IP フィルター規則の追加] を選択した後":::

* IP フィルター規則の**名前**を指定します。 これは、一意であり、長さが最大 128 文字の、大文字と小文字を区別しない英数字の文字列である必要があります。 ASCII 7 ビット英数字と `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` のみを使用できます。

* 1 つの IPv4 アドレスか、または CIDR 表記法で記述した IP アドレス ブロックを指定します。 たとえば、CIDR 表記 192.168.100.0/22 は、192.168.100.0 ～ 192.168.103.255 までの 1,024 個の IPv4 アドレスを表します。

* IP フィルター規則の **[アクション]** として **[許可]** または **[ブロック]** を選択します。

フィールドに入力したら、 **[保存]** を選択して規則を保存します。 更新が進行中であることを通知するアラートが表示されます。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="IP フィルター規則の保存に関する通知":::

IP フィルター規則が最大値の 10 個に達すると、 **[追加]** オプションは無効になります。

既存の規則を編集するには、変更するデータを選択して変更を加え、 **[保存]** を選択して編集内容を保存します。

## <a name="delete-an-ip-filter-rule"></a>IP フィルター規則の削除

IP フィルター規則を削除するには、その行のごみ箱アイコンを選択し、次に **[保存]** を選択します。 規則が削除されて、変更が保存されます。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="IoT Hub の IP フィルター規則の削除":::

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Azure CLI を使用した IP フィルターの取得および更新

[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) を使用して、IoT Hub の IP フィルターを取得および更新できます。

IoT Hub の現在の IP フィルターを取得するには、以下を実行します。

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

これにより、既存の IP フィルターが `properties.ipFilterRules` キーの下にリストされている JSON オブジェクトが返されます。

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

IoT Hub の新しい IP フィルターを追加するには、以下を実行します。

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

IoT Hub の既存の IP フィルターを削除するには、以下を実行します。

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

`<ipFilterIndexToRemove>` は、IoT Hub の `properties.ipFilterRules` の IP フィルターの順序に対応している必要があるので注意してください。

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Azure PowerShell を使用した IP フィルターの取得および更新

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/) を使用して、IoT Hub の IP フィルターを取得および設定できます。

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>REST を使用した IP フィルター規則の更新

Azure リソース プロバイダーの REST エンドポイントを使用することでも、IoT Hub の IP フィルターを取得および変更できます。 [createorupdate メソッド](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate)の `properties.ipFilterRules` を参照してください。

## <a name="ip-filter-rule-evaluation"></a>IP フィルター規則の評価

IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

たとえば、範囲 192.168.100.0/22 内のアドレスを許可し、それ以外のアドレスをすべて拒否するには、グリッドの最初の規則でアドレス範囲 192.168.100.0/22 を許可する必要があります。 さらに、次の規則で範囲 0.0.0.0/0 を使用してすべてのアドレスを拒否する必要があります。

グリッド内の IP フィルター規則の順序を変更するには、行の先頭のドットが縦に 3 つ並んだ部分をクリックし、ドラッグ アンド ドロップします。

新しい IP フィルター規則の順序を保存するには、 **[保存]** をクリックします。

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-rule-order.png" alt-text="IoT Hub の IP フィルター規則の順序の変更":::

## <a name="next-steps"></a>次のステップ

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub メトリック](iot-hub-metrics.md)
