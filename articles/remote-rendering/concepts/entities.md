---
title: エンティティ
description: Azure Remote Rendering API のスコープ内のエンティティの定義
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 20de83e190a419b95c99c1c1238eb931910feb82
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020288"
---
# <a name="entities"></a>エンティティ

*エンティティ*とは、領域内の移動可能なオブジェクトを表すもので、リモートでレンダリングされるコンテンツの基本的な構成要素です。

## <a name="entity-properties"></a>エンティティのプロパティ

エンティティには、位置、回転、および拡大縮小によって定義された変換機能があります。 エンティティは、単独では注目すべき機能はありません。 代わりに、エンティティにアタッチされるコンポーネントによって動作が追加されます。 たとえば、[CutPlaneComponent](../overview/features/cut-planes.md) をアタッチすると、エンティティの位置に切断面が作成されます。

エンティティ自体の最も重要な側面は、階層と、結果として生じる階層の変換です。 たとえば、共有されている 1 つの親エンティティに複数のエンティティが子としてアタッチされている場合、親エンティティの変換を変更することによって、これらのエンティティをすべて同時に移動、回転、および拡大縮小することができます。

エンティティはその親によって一意に所有されます。つまり、親が `Entity.Destroy()` で破棄されると、その子と、接続されているすべての[コンポーネント](components.md)も破棄されます。 したがって、シーンからモデルを削除するには、`AzureSession.Actions.LoadModelAsync()` またはその SAS バリアント `AzureSession.Actions.LoadModelFromSASAsync()` によって返される、モデルのルート ノードに対して `Destroy` を呼び出します。

エンティティは、サーバーがコンテンツを読み込むとき、またはユーザーがオブジェクトをシーンに追加するときに作成されます。 たとえば、ユーザーがメッシュの内部を視覚化するために切断面を追加する場合は、その面が存在するはずのエンティティを作成して、それに切断面コンポーネントを追加できます。

## <a name="create-an-entity"></a>エンティティの作成

シーンに新しいエンティティを追加する (たとえば、モデルを読み込むためのルート オブジェクトとして渡すか、またはコンポーネントをアタッチするために) には、次のコードを使用します。

```cs
Entity CreateNewEntity(AzureSession session)
{
    Entity entity = session.Actions.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<AzureSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Actions()->CreateEntity())
    {
        entity = entityRes.value();
        entity->SetPosition(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

## <a name="query-functions"></a>クエリ関数

エンティティには、同期呼び出しと非同期呼び出しの 2 種類のクエリ関数があります。 同期クエリは、クライアント上に存在し、計算をあまり伴わないデータにのみ使用できます。 例として、コンポーネント、オブジェクトの相対変換、または親子関係のクエリがあります。 非同期クエリは、サーバー上に存在し、クライアントで実行するにはコストがかかりすぎる追加の計算を伴うデータにのみ使用されます。 例として、空間境界のクエリやメタデータの クエリがあります。

### <a name="querying-components"></a>コンポーネントのクエリ

特定の種類のコンポーネントを検索するには、`FindComponentOfType` を使用します。

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>変換のクエリ

変換のクエリは、オブジェクトに対する同期呼び出しです。 API を使用してクエリされる変換は、オブジェクトの親を基準としたローカル空間の変換であることに注意する必要があります。 ローカル空間とワールド空間が同一であるルート オブジェクトは例外です。

> [!NOTE]
> 任意のオブジェクトのワールド空間の変換をクエリする専用の API はありません。

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = entity->GetPosition();
Quaternion rotation = entity->GetRotation();
```


### <a name="querying-spatial-bounds"></a>空間境界のクエリ

境界のクエリは、1 つのエンティティをルートとして使用して、完全なオブジェクト階層で機能する非同期呼び出しです。 [オブジェクトの境界](object-bounds.md)に関する専用の章をご覧ください。

### <a name="querying-metadata"></a>メタデータのクエリ

メタデータは、オブジェクトに格納されている追加データであり、サーバーによって無視されます。 オブジェクトのメタデータは、基本的に (名前と値の) ペアのセットです。_値_ には、数値型、ブール型、または文字列型を指定できます。 メタデータは、モデルと共にエクスポートできます。

メタデータのクエリは、特定のエンティティに対する非同期呼び出しです。 このクエリは、サブ グラフのマージされた情報ではなく、単一エンティティのメタデータのみを返します。

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

```cpp
ApiHandle<MetadataQueryAsync> metaDataQuery = *entity->QueryMetaDataAsync();
metaDataQuery->Completed([](const ApiHandle<MetadataQueryAsync>& query)
    {
        if (query->GetIsRanToCompletion())
        {
            ApiHandle<ObjectMetaData> metaData = query->GetResult();
            ApiHandle<ObjectMetaDataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
            int64_t intValue = *entry->GetAsInt64();

            // ...
        }
    });
```

オブジェクトがメタデータを保持していない場合でも、クエリは成功します。

## <a name="next-steps"></a>次のステップ

* [コンポーネント](components.md)
* [オブジェクトの境界](object-bounds.md)
