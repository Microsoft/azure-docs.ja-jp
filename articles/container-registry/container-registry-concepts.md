---
title: リポジトリとイメージについて
description: Azure のコンテナー レジストリ、リポジトリ、およびコンテナー イメージの主要な概念について紹介します。
ms.topic: article
ms.date: 06/16/2020
ms.openlocfilehash: f3a3e2a00b4fb35f9e9dd1415d5c197aef0d39b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390450"
---
# <a name="about-registries-repositories-and-images"></a>レジストリ、リポジトリ、イメージについて

この記事では、コンテナー レジストリ、リポジトリ、およびコンテナー イメージと関連する成果物の主要な概念について紹介します。 

## <a name="registry"></a>レジストリ

コンテナー "*レジストリ*" は、コンテナー イメージを格納して配布するサービスです。 Docker Hub は、オープンソース コミュニティをサポートし、一般的なイメージ カタログとしてサービスを提供するパブリック コンテナー レジストリです。 Azure Container Registry では、統合認証、ネットワーク上の近い場所へのデプロイに対応したグローバルな配布と信頼性をサポートする [geo レプリケーション](container-registry-geo-replication.md)、[仮想ネットワークとファイアウォール構成](container-registry-vnet.md)、[タグ ロック](container-registry-image-lock.md)、その他多くの拡張機能を使用して、ユーザーがイメージを直接制御できるようにします。 

Docker コンテナー イメージに加え、Azure Container Registry では、Open Container Initiative (OCI) イメージ形式を含む、関連[コンテンツ成果物](container-registry-image-formats.md)がサポートされます。

## <a name="content-addressable-elements-of-an-artifact"></a>成果物のアドレス指定可能なコンテンツ要素

Azure Container Registry 内の成果物のアドレスには、次の要素が含まれています。 

`[loginUrl]/[repository:][tag]`

* **loginUrl** - レジストリ ホストの完全修飾名。 Azure Container Registry のレジストリ ホストは、*myregistry*.azurecr.io (すべて小文字) の形式です。 Docker またはその他のクライアント ツールを使用して Azure Container Registry との間で成果物をプルまたはプッシュするには、loginUrl を指定する必要があります。 
* **repository** - 1 つまたは複数の関連するイメージまたは成果物の論理グループの名前。たとえば、アプリケーションまたはベース オペレーティング システムのイメージです。 *名前空間*パスを含めることができます。 
* **tag** - リポジトリに格納されているイメージまたは成果物の特定バージョンの識別子。

たとえば、Azure Container Registry のイメージの完全名は次ようになります。

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

これらの要素の詳細については、以下のセクションを参照してください。

## <a name="repository-name"></a>リポジトリ名です

"*リポジトリ*" は、名前が同じでタグが異なるコンテナー イメージまたはその他の成果物のコレクションです。 たとえば、次の 3 つのイメージは "acr-helloworld" リポジトリ内にあります。


- *acr-helloworld:latest*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

リポジトリ名には、[名前空間](container-registry-best-practices.md#repository-namespaces)を含めることもできます。 名前空間を使用すると、スラッシュ区切りの名前を使用して、組織内の関連するリポジトリと成果物の所有権を識別できます。 ただし、レジストリでは、階層としてではなく、すべてのリポジトリが個別に管理されます。 次に例を示します。

- *marketing/campaign10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *product-returns/web-submission:20180604*
- *product-returns/legacy-integrator:20180715*

リポジトリ名には、小文字の英数字、ピリオド、ダッシュ、アンダースコア、およびスラッシュのみを含めることができます。 

リポジトリの完全な名前付け規則については、[Open Container Initiative Distribution Specification](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview) (オープン コンテナー イニシアチブの配布仕様) を参照してください。

## <a name="image"></a>Image

レジストリ内のコンテナー イメージまたはその他の成果物は、1 つまたは複数のタグに関連付けられており、1 つまたは複数のレイヤーを持ち、マニフェストによって識別されます。 これらのコンポーネントの相互関係を理解しておくと、レジストリを効率的に管理するのに役立ちます。

### <a name="tag"></a>タグ

イメージまたはその他の成果物の "*タグ*" は、そのバージョンを指定します。 リポジトリ内の 1 つの成果物に、1 つまたは多数のタグを割り当てることができ、"タグを割り当てない" ことも可能です。 つまり、イメージからすべてのタグを削除しながら、イメージのデータ (そのレイヤー) はレジストリに引き続き残しておくことができます。

リポジトリ (またはリポジトリと名前空間) とタグの組み合わせで、イメージの名前が定義されます。 プッシュ操作またはプル操作で名前を指定することにより、イメージをプッシュおよびプルできます。 Docker コマンドにタグが指定されていない場合、既定ではタグ `latest` が使用されます。

コンテナー イメージにタグを付ける方法は、コンテナー イメージを開発またはデプロイするシナリオによって決まります。 たとえば、基本イメージを維持する場合は安定したタグ、イメージをデプロイする場合は一意のタグが推奨されます。 詳細については、「[コンテナー イメージのタグ付けとバージョン管理に関する推奨事項](container-registry-image-tag-version.md)」を参照してください。

タグの名前付け規則については、[Docker のドキュメント](https://docs.docker.com/engine/reference/commandline/tag/)を参照してください。

### <a name="layer"></a>レイヤー

コンテナー イメージは 1 つ以上の "*レイヤー*" で構成されており、各レイヤーはイメージを定義する Dockerfile 内の行に対応しています。 ストレージの効率が良くなるように、レジストリ内のイメージは共通レイヤーを共有しています。 たとえば、異なるリポジトリ内の複数のイメージが同じ Alpine Linux ベース レイヤーを共有している場合がありますが、レジストリに格納されるのはそのレイヤーの 1 つのコピーだけです。

レイヤーの共有により、共通レイヤーを共有する複数のイメージを含むノードへのレイヤーの配布も最適化されます。 たとえば、ノード上に既に存在するイメージがそのベースとして Alpine Linux レイヤーを含む場合、同じレイヤーを参照する異なるイメージをそれ以降にプルしても、レイヤーはノードに転送されません。 代わりに、ノードに既に存在するレイヤーが参照されます。

安全な分離を確保し潜在的なレイヤー操作から保護するために、レイヤーはレジストリ間で共有されません。

### <a name="manifest"></a>Manifest

コンテナー レジストリにプッシュされる各コンテナー イメージまたは成果物は、"*マニフェスト*" と関連付けられます。 イメージがプッシュされるときにレジストリによって生成されるマニフェストは、イメージを一意に示し、そのレイヤーを指定します。 Azure CLI コマンド [az acr repository show-manifests][az-acr-repository-show-manifests] を使用して、リポジトリのマニフェストを一覧表示できます。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

たとえば、"acr-helloworld" リポジトリのマニフェストの一覧は次のようになります。

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>マニフェスト ダイジェスト

マニフェストは、一意の SHA-256 ハッシュつまり "*マニフェスト ダイジェスト*" によって識別されます。 各イメージまたは成果物は、タグ付きかどうかにかかわらず、そのダイジェストによって識別されます。 ダイジェストの値は、イメージのレイヤー データが別のイメージと同じ場合であっても一意です。 このメカニズムにより、同じタグが付けられたイメージをレジストリに繰り返しプッシュできます。 たとえば、各イメージはその一意のダイジェストによって識別されるため、`myimage:latest` をレジストリに繰り返しプッシュしてもエラーにはなりません。

プル操作ではダイジェストを指定することにより、レジストリからイメージをプルできます。 システムによっては、ダイジェストによってプルするように構成されている場合があります。同じタグが付けられたイメージが後でレジストリにプッシュされた場合でも、ダイジェストによって、プルされているイメージのバージョンが保証されるためです。

たとえば、マニフェスト ダイジェストによる "acr-helloworld" リポジトリからのイメージのプルは次のようになります。

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> 同じタグを持つ変更されたイメージを繰り返しプッシュする場合、孤立したイメージ (タグは付けられていないが、それでもレジストリの領域を消費するイメージ) が作成されることがあります。 Azure CLI または Azure portal でタグを使用してイメージを一覧表示または表示したとき、タグなしのイメージは表示されません。 ただし、それらのレイヤーはまだ存在し、レジストリの領域を消費します。 特定のレイヤーを指すマニフェストが他に存在しなければ (つまり最後のマニフェストであれば)、タグの付いていないイメージを削除することで、レジストリの領域は解放されます。 タグの付いていないイメージによって使用されている領域を開放する方法の詳細については、「[Azure Container Registry のコンテナー イメージを削除する](container-registry-delete.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Container Registry での[イメージ ストレージ](container-registry-storage.md)と[サポートされているコンテンツの形式](container-registry-image-formats.md)について詳細を確認します。

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


