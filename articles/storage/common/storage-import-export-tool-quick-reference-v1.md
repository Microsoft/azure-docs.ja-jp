---
title: "Azure Import/Export ツールのインポート ジョブのコマンドのクイック リファレンス - v1 | Microsoft Docs"
description: "Azure Import/Export ツールで頻繁に使用されるインポート ジョブ コマンドのコマンド リファレンスです。 このリファレンスでは、Import/Export ツール v1 について説明します。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 370cf6fae7ae106e8341f65086c8b8187d335746
ms.contentlocale: ja-jp
ms.lasthandoff: 08/23/2017

---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>インポート ジョブで頻繁に使用するコマンドのクイック リファレンス
このセクションでは、よく使用されるコマンドのクイック リファレンスを提供します。 詳細な使用方法については、「[インポート ジョブ用のハード ドライブを準備する](../storage-import-export-tool-preparing-hard-drives-import-v1.md)」を参照してください。  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>既にデータがコピーされたハード ドライブを準備します。
 次のコマンドは、既にデータがコピーされているが、まだ BitLocker で暗号化されていないハード ドライブを準備します。  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>1 つのディレクトリをハード ドライブにコピーする  
 BitLocker でまだ暗号化されていないハード ドライブに 1 つのソース ディレクトリをコピーするサンプル コマンドを次に示します。  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>2 つのディレクトリをハード ドライブにコピーする  
 2 つのソース ディレクトリをドライブにコピーするには、次のコマンドを使用します。  
  
 最初のコマンドは、共通のパラメーターのほか、ログ ディレクトリ、ストレージ アカウント キー、コピー先のドライブ文字と `format/encrypt` の要件を指定します。  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 2 番目のコマンドでは、ジャーナル ファイル、新しいセッション ID、およびコピー元とコピー先の場所を指定します。  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>2 番目のコピー セッションでサイズの大きなファイルをハード ドライブにコピーする  
 前のコピー セッションで準備されたドライブにサイズの大きな 1 つのファイルをコピーするサンプル コマンドを次に示します。  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>次のステップ

* [インポート ジョブ用のハード ドライブを準備するためのサンプル ワークフロー](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)

