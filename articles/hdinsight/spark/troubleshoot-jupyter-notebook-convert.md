---
title: Azure HDInsight で Jupyter ノートブックを作成できない
description: Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: 61e7cd8d37108b8f4eea88c4f6b6b2a8cdbfd605
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186796"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Azure HDInsight で Jupyter ノートブックを作成できない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Jupyter ノートブックを開始するとき、次のようなエラー メッセージが表示されます。

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>原因

バージョンの不一致。

## <a name="resolution"></a>解決策

1. [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用してクラスターに接続します。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 次のコマンドを実行して `_version.py` を開きます。

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. **5** を **4** に変更すると、変更された行は次のようになります。

    ```python
    version_info = (4, 0, 3)
    ```

    **Ctrl + X**、**Y**、**Enter** キーを入力して変更を保存ます。

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。

1. Jupyter サービスを再起動します。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
