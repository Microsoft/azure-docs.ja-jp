---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 27a4d775b8d88e02be69655e5adfc6155f867ef6
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581233"
---
適切な SSL 証明書により、暗号化された情報が適切なサーバーに確実に送信されます。 暗号化だけでなく、証明書により認証もできます。 デバイスの PowerShell インターフェイス経由で、信頼された独自の SSL 証明書をアップロードすることができます。

1. [PowerShell インターフェイスに接続する](#connect-to-the-powershell-interface)。
2. `Set-HcsCertificate` コマンドレットを使用して証明書をアップロードします。 入力を求められたら、次のパラメーターを指定します。

   - `CertificateFilePath` - *.pfx* 形式の証明書を含む共有へのパス。
   - `CertificatePassword` - 証明書を保護するために使用するパスワード。
   - `Credentials` - 証明書を含む共有にアクセスするためのユーザー名。 入力を求められたら、ネットワーク共有へのパスワードを指定します。

     このコマンドレットの使用例を次に示します。

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```
