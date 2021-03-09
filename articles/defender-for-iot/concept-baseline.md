---
title: ベースラインとカスタム チェック
description: Azure Defender for IoT のベースラインの概念について説明します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: 04fe87cd69efc4c064b8fbdc596a5f9e187abbb1
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102242"
---
# <a name="azure-defender-for-iot-baseline-and-custom-checks"></a>Azure Defender for IoT のベースラインとカスタム チェック

この記事では、Defender for IoT のベースラインについて説明し、ベースラインのカスタム チェックに関連するすべてのプロパティの概要を示します。

## <a name="baseline"></a>ベースライン

ベースラインは、デバイスごとの標準的な動作を確立し、異常な動作や想定される基準からの逸脱を容易に明確化できるようにすることです。

## <a name="baseline-custom-checks"></a>ベースラインのカスタム チェック

ベースラインのカスタム チェックでは、デバイスの **モジュール ID ツイン** を使用して、各デバイスのベースラインに対するカスタム チェック リストが確立されます。

## <a name="setting-baseline-properties"></a>ベースライン プロパティの設定

1. IoT Hub で、変更するデバイスを見つけて選択します。

1. デバイスをクリックした後、**azureiotsecurity** モジュールをクリックします。

1. **[モジュール ID ツイン]** をクリックします。

1. **ベースラインのカスタム チェック** ファイルをデバイスにアップロードします。

1. ベースライン プロパティをセキュリティ モジュールに追加し、 **[保存]** をクリックします。

### <a name="baseline-custom-check-file-example"></a>ベースラインのカスタム チェック ファイルの例

ベースラインのカスタム チェックを構成するには:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>ベースラインのカスタム チェックのプロパティ

| 名前| Status | 有効な値| 既定値| 説明 |
|------|-----|------|-----|-----|
|baselineCustomChecksEnabled|必須: true |有効な値: **Boolean** |既定値: **false** |優先度の高いメッセージが送信されるまでの最大時間間隔。|
|baselineCustomChecksFilePath |必須: true|有効な値: **String**、**null** |既定値: **null** |ベースラインの xml 構成の完全パス|
|baselineCustomChecksFileHash |必須: true|有効な値: **String**、**null** |既定値: **null** |xml 構成ファイルの `sha256sum`。 追加情報については [sha256sum リファレンス](https://linux.die.net/man/1/sha256sum)を使用してください。 |

その他のベースラインの例を確認するには、[カスタム ベースラインの例 1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) と[カスタム ベースラインの例 2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml) を参照してください。

## <a name="next-steps"></a>次のステップ

- [生セキュリティ データ](how-to-security-data-access.md)にアクセスする
- [デバイスを調査する](how-to-investigate-device.md)
- [セキュリティに関する推奨事項](concept-recommendations.md)を理解して調べる
- [セキュリティ アラート](concept-security-alerts.md)を理解して調べる
