---
title: 正常性チェックのエラー リファレンス - Azure Container Registry
description: Azure Container Registry で az acr check-health 診断コマンドを実行することによって検出された問題のエラー コードと考えられる解決策
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3bb81f33823fff5fb47f5bbbf6bb9b798b26d8af
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840360"
---
# <a name="health-check-error-reference"></a>正常性チェックのエラー リファレンス

以下は、[az acr check-health][az-acr-check-health] コマンドによって返されるエラー コードの詳細です。 各エラーについて、考えられる解決策が示されています。

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

このエラーは、CLI の Docker クライアントが見つからなかったことを意味します。 その結果、Docker のバージョンの検出、Docker デーモンの状態の評価、docker pull コマンドの実行の各チェックは実行されません。

*考えられる解決策*: Docker クライアントをインストールします。Docker パスをシステム変数に追加します。

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

このエラーは、Docker デーモンの状態が利用できないか、または CLI を使用して状態にアクセスできなかったことを意味します。 その結果、Docker 操作 (`docker login` や `docker pull` など) を CLI から使用できません。

*考えられる解決策*: Docker デーモンを再起動するか、正しくインストールされていることを確認します。

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

このエラーは、CLI が `docker --version` コマンドを実行できなかったことを意味します。

*考えられる解決策*: コマンドを手動で実行してみます。最新の CLI バージョンを使用していることを確認し、エラー メッセージを調べます。

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

このエラーは、CLI がサンプル イメージを環境にプルできなかったことを意味します。

*考えられる解決策*: イメージをプルするために必要なすべてのコンポーネントが正しく実行されていることを確認します。

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

このエラーは、CLI が Helm クライアントを見つけることができなかったため、他の Helm 操作を実行できないことを意味します。

*考えられる解決策*: Helm クライアントがインストールされていることを確認し、そのパスがシステム環境変数に追加されていることを確認します。

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

このエラーは、インストールされている Helm のバージョンを CLI が特定できなかったことを意味します。 これは、使用されている Azure CLI のバージョン (または Helm のバージョン) が古い場合に発生する可能性があります。

*考えられる解決策*: 最新の Azure CLI バージョンまたは推奨される Helm バージョンに更新します。コマンドを手動で実行し、エラー メッセージを調べます。

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

このエラーは、指定されたレジストリ ログイン サーバーの DNS に対して ping を実行したが応答がなかったこと (つまり、DNS を使用できないこと) を意味します。 これは、接続の問題を示している可能性があります。 または、レジストリが存在しない、ユーザーが (ログイン サーバーを正しく取得するための) レジストリに対するアクセス許可を持っていない、あるいはターゲット レジストリが Azure CLI で使用されているものとは異なるクラウドにある可能性があります。

*考えられる解決策*: 接続を検証します。レジストリのスペルと、そのレジストリが存在することを確認します。ユーザーがレジストリに対する適切なアクセス許可を持っていること、およびレジストリのクラウドが Azure CLI で使用されているものと同じであることを確認します。

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

このエラーは、指定されたレジストリのチャレンジ エンドポイントが HTTP ステータス 403 Forbidden で応答したことを意味します。 このエラーは、おそらく仮想ネットワーク構成が原因で、ユーザーがレジストリにアクセスできないことを意味します。 現在構成されているファイアウォール規則を確認するには、`az acr show --query networkRuleSet --name <registry>` を実行します。

*考えられる解決策*: 仮想ネットワーク規則を削除するか、現在のクライアント IP アドレスを許可リストに追加します。

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

このエラーは、ターゲット レジストリのチャレンジ エンドポイントがチャレンジを発行しなかったことを意味します。

*考えられる解決策*: しばらくしてからもう一度試してください。 エラーが解決しない場合は、 https://aka.ms/acr/issues で問題を開いてください。

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

このエラーは、ターゲット レジストリのチャレンジ エンドポイントがチャレンジを発行したが、レジストリで Azure Active Directory 認証がサポートされていないことを意味します。

*考えられる解決策*: 管理者の資格情報を使用するなど、別の方法で認証してみます。 ユーザーが Azure Active Directory を使用して認証する必要がある場合は、 https://aka.ms/acr/issues で問題を開いてください。

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

このエラーは、レジストリ ログイン サーバーが更新トークンで応答しなかったため、ターゲット レジストリへのアクセスが拒否されたことを意味します。 このエラーは、ユーザーがレジストリに対する適切なアクセス許可を持っていない場合、または Azure CLI のユーザーの資格情報が古い場合に発生する可能性があります。

*考えられる解決策*: ユーザーがレジストリに対する適切なアクセス許可を持っているかどうかを確認します。`az login` を実行して、アクセス許可、トークン、資格情報を更新します。

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

このエラーは、レジストリ ログイン サーバーがアクセス トークンで応答しなかったため、ターゲット レジストリへのアクセスが拒否されたことを意味します。 このエラーは、ユーザーがレジストリに対する適切なアクセス許可を持っていない場合、または Azure CLI のユーザーの資格情報が古い場合に発生する可能性があります。

*考えられる解決策*: ユーザーがレジストリに対する適切なアクセス許可を持っているかどうかを確認します。`az login` を実行して、アクセス許可、トークン、資格情報を更新します。

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

このエラーは、クライアントがコンテナー レジストリへのセキュリティで保護された接続を確立できなかったことを意味します。 このエラーは一般に、プロキシ サーバーの実行中または使用中に発生します。

*考えられる解決策*: プロキシの内側の動作について詳しくは、[こちらをご覧ください](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy)。

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

このエラーは、CLI が指定されたレジストリのログイン サーバーを見つけることができず、現在のクラウドの既定のサフィックスが見つからなかったことを意味します。 このエラーは、レジストリが存在しない場合、ユーザーがレジストリに対する適切なアクセス許可を持っていない場合、レジストリのクラウドと現在の Azure CLI のクラウドが一致しない場合、または Azure CLI のバージョンが古い場合に発生する可能性があります。

*考えられる解決策*: スペルが正しいこと、およびレジストリが存在することを確認します。ユーザーがレジストリに対する適切なアクセス許可を持っていること、およびレジストリと CLI 環境のクラウドが一致していることを確認します。Azure CLI を最新バージョンに更新します。

## <a name="next-steps"></a>次の手順

レジストリの正常性チェックのオプションについては、「[Azure コンテナー レジストリの正常性のチェック](container-registry-check-health.md)」をご覧ください。

Azure Container Registry に関するよくあるご質問や他の既知の問題については、[FAQ](container-registry-faq.md) をご覧ください。





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
