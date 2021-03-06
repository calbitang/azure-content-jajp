<properties
   pageTitle="Azure Search .NET SDK バージョン 1.1 へのアップグレード | Microsoft Azure | ホスト型クラウド検索サービス"
   description="Azure Search .NET SDK バージョン 1.1 へのアップグレード"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/15/2016"
   ms.author="brjohnst"/>

# Azure Search .NET SDK バージョン 2.0-preview へのアップグレード

バージョン 1.1 以前の [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) を使用している場合は、この記事を参考にして、次のメジャー バージョンである 2.0-preview を使用するようにアプリケーションをアップグレードできます。

例を含む SDK の一般的なチュートリアルについては、「[.NET アプリケーションから Azure Search を使用する方法](search-howto-dotnet-sdk.md)」を参照してください。

Azure Search .NET SDK のバージョン 2.0-preview には、以前のバージョンからの変更がいくつか含まれています。ほとんどは小さなものなので、コードの変更に必要な作業は最小限で済みます。新しいバージョンの SDK を使用するようにコードを変更する方法については、「[アップグレードの手順](#UpgradeSteps)」を参照してください。

> [AZURE.NOTE] 0.13-preview 以前のバージョンを使用している場合は、バージョン 1.1 にアップグレードした後、2.0-preview にアップグレードする必要があります。手順については、「[付録: バージョン 1.1 にアップグレードするための手順](#UpgradeStepsV1)」を参照してください。

<a name="WhatsNew"></a>
## バージョン 2.0-preview の新機能

バージョン 2.0-preview は、Azure Search REST API のプレビュー バージョン (具体的には 2015-02-28-Preview) をターゲットとする Azure Search .NET SDK の最初のバージョンです。これにより、以下のような Azure Search の多数のプレビュー機能を .NET アプリケーションから使用することが可能になります。

- [カスタム アナライザー](https://aka.ms/customanalyzers)
- [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) と [Azure Table Storage](search-howto-indexing-azure-tables.md) のインデクサーのサポート
- [フィールド マッピング](search-indexer-field-mappings.md)によるインデクサーのカスタマイズ
- インデックスの定義、インデクサー、およびデータ ソースの安全な同時更新を有効にする Etag のサポート
- .NET Core と .NET Portable Profile 111 のサポート

<a name="UpgradeSteps"></a>
## アップグレードの手順

最初に、NuGet パッケージ マネージャー コンソールを使用して、または Visual Studio でプロジェクト参照を右クリックして [NuGet パッケージの管理...] を選択することで、`Microsoft.Azure.Search` の NuGet 参照を更新します。Visual Studio で NuGet の [パッケージの管理] ウィンドウで [プレリリースを含める] を選択するか、NuGet パッケージ マネージャー コンソールを使用している場合は `-IncludePrerelease` スイッチを使用して、プレリリース パッケージを有効にします。

NuGet が新しいパッケージとその依存関係をダウンロードした後、プロジェクトをリビルドします。コードの構成方法に応じて、正常にリビルドされます。リビルドされれば、準備は完了です。

ビルドが失敗した場合は、次のようにビルド エラーが表示されます。

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

次の手順として、このビルド エラーを修正します。エラーの原因と修正方法については、「[バージョン 2.0-preview における重大な変更](#ListOfChanges)」を参照してください。

古いメソッドまたはプロパティに関連するビルド警告が表示される場合があります。それらの警告には、非推奨の機能の代わりに何を使用するかについての指示が含まれます。たとえば、アプリケーションで `SearchRequestOptions.RequestId` プロパティ使用している場合は、「`"This property is deprecated. Please use ClientRequestId instead."`」という警告が表示されます。

すべてのビルド エラーを修正した後、必要に応じて新しい機能を利用するようにアプリケーションを変更できます。SDK の新機能の詳細については、「[バージョン 2.0-preview の新機能](#WhatsNew)」を参照してください。

<a name="ListOfChanges"></a>
## バージョン 2.0-preview における重大な変更

アプリケーションのリビルドの他に、コードを変更する必要があるバージョン 2.0-preview の大幅な変更は 1 つしかありません。

### Indexes.GetClient の戻り型

`Indexes.GetClient` メソッドには新しい戻り型があります。前に返されていたのは `SearchIndexClient` ですが、バージョン 2.0-preview では、これが `ISearchIndexClient` に変更されています。これは、単体テストで `ISearchIndexClient` の模擬実装を返すことによって `GetClient` メソッドの模擬テストを実行したいユーザーをサポートするための変更です。

#### 例

次のようなコードがあるものとします。

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

この場合、次のように変更することでビルド エラーを解決できます。

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

## まとめ
Azure Search .NET SDK の使用方法の詳細については、最近更新された[方法](search-howto-dotnet-sdk.md)に関する記事を参照してください。

SDK についてのご意見をお待ちしております。問題が発生した場合は、[Azure Search の MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/ja-JP/home?forum=azuresearch)でご質問ください。バグを発見した場合は、[Azure .NET SDK の GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/issues)で問題を報告できます。問題のタイトルの前に、必ず "Search SDK: " を付けてください。

Azure Search をお使いいただきありがとうございます。

<a name="UpgradeStepsV1"></a>
## 付録: バージョン 1.1 にアップグレードするための手順 

> [AZURE.NOTE] このセクションは、Azure Search .NET SDK バージョン 0.13-preview 以前を使用しているユーザーにのみ適用されます。

最初に、NuGet パッケージ マネージャー コンソールを使用して、または Visual Studio でプロジェクト参照を右クリックして [NuGet パッケージの管理...] を選択することで、`Microsoft.Azure.Search` の NuGet 参照を更新します。

NuGet が新しいパッケージとその依存関係をダウンロードした後、プロジェクトをリビルドします。

以前にバージョン 1.0.0-preview、1.0.1-preview、または 1.0.2-preview を使用していた場合、ビルドは成功するはずであり、次に進む準備が整います。

0\.13.0-preview 以前のバージョンを使用していた場合、次のようなビルド エラーが表示されます。

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

次のステップとして、ビルド エラーを 1 つずつ修正します。ほとんどの修正では、SDK で名前が変更されたクラスとメソッドの名前を変更する必要があります。このような名前変更の一覧については、「[バージョン 1.1 における重大な変更の一覧](#ListOfChangesV1)」を参照してください。

カスタム クラスを使用してドキュメントをモデル化していて、それらのクラスに null 非許容プリミティブ型のプロパティ (たとえば、C# での `int` や `bool`) がある場合、1.1 バージョンの SDK で行われたバグ修正について認識しておく必要があります。詳細については、「[バージョン 1.1 でのバグ修正](#BugFixesV1)」を参照してください。

最後に、ビルド エラーを修正した後は、必要に応じて、新しい機能を利用するようにアプリケーションを変更できます。

<a name="ListOfChangesV1"></a>
### バージョン 1.1 における重大な変更の一覧

以下の一覧は、変更によってアプリケーション コードが影響を受ける可能性の高い順になっています。

#### IndexBatch と IndexAction の変更

`IndexBatch.Create` は名前が `IndexBatch.New` に変更され、`params` 引数はなくなりました。`IndexBatch.New` は、異なる種類のアクション (マージ、削除など) が混在するバッチに使用できます。さらに、すべてのアクションが同じバッチを作成するための新しい静的メソッドがあります。`Delete`、`Merge`、`MergeOrUpload`、および `Upload` です。

`IndexAction` はパブリック コンストラクターを持たなくなり、そのプロパティは変更可能になっています。さまざまな目的のアクションの作成には、新しい静的メソッドである `Delete`、`Merge`、`MergeOrUpload`、および `Upload` を使用する必要があります。`IndexAction.Create` は削除されました。ドキュメントだけを受け取るオーバーロードを使用していた場合は、代わりに `Upload` を使用してください。

##### 例

次のようなコードがあるものとします。

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

この場合、次のように変更することでビルド エラーを解決できます。

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

必要であれば、さらに次のように単純化できます。

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### IndexBatchException の変更

`IndexBatchException.IndexResponse` プロパティは名前が `IndexingResults` に変更され、型は `IList<IndexingResult>` になりました。

##### 例

次のようなコードがあるものとします。

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

この場合、次のように変更することでビルド エラーを解決できます。

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
#### 操作メソッドの変更

Azure Search .NET SDK の各操作は、同期および非同期の呼び出し元に対するメソッドのオーバーロードのセットとして公開されます。これらのメソッド オーバーロードのシグネチャと要素分解は、バージョン 1.1 で変更されました。

たとえば、旧バージョンの SDK の「インデックス統計の取得」操作は、次のようなシグネチャを公開しました。

`IIndexOperations` で、次のように記述します。

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

`IndexOperationsExtensions` で、次のように記述します。

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

バージョン 1.1 での同じ操作のメソッド シグネチャは、次のようになります。

`IIndexesOperations` で、次のように記述します。

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

`IndexesOperationsExtensions` で、次のように記述します。

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

バージョン 1.1 以降では、Azure Search .NET SDK での操作メソッドの編成が異なります。

 - 省略可能なパラメーターが、追加メソッドのオーバーロードではなく、既定のパラメーターとしてモデル化されるようになっています。これにより、メソッドのオーバーロードの数が、場合によっては大幅に減少します。
 - 拡張メソッドで、HTTP の重要ではない細部の多くが呼び出し元に開示しないようになっています。たとえば、以前のバージョンの SDK では、HTTP 状態コードで応答オブジェクトが返されましたが、操作メソッドはエラーを示す状態コードに対して `CloudException` をスローするため、応答オブジェクトをチェックする必要はほとんどありませんでした。新しい拡張メソッドはモデル オブジェクトだけを返すので、コードでラップを解除する必要がある問題が減ります。
 - 逆に、コア インターフェイスでは、必要がある場合に HTTP レベルでの詳細な制御を提供するメソッドが公開されるようになっています。要求に含めるカスタム HTTP ヘッダーを渡すことができ、`AzureOperationResponse<T>` 型の戻り値を使用すると操作の `HttpRequestMessage` および `HttpResponseMessage` に直接アクセスできます。`AzureOperationResponse` は `Microsoft.Rest.Azure` 名前空間で定義されており、`Hyak.Common.OperationResponse` に代わるものです。

#### ScoringParameters の変更

最新の SDK に `ScoringParameter` という新しいクラスが追加され、検索クエリでスコアリング プロファイルにパラメーターを簡単に指定できるようになりました。これまで、`SearchParameters` クラスの `ScoringProfiles` プロパティは `IList<string>` と型指定されていました。このプロパティが `IList<ScoringParameter>` と型指定されるようになりました。

##### 例

次のようなコードがあるものとします。

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

この場合、次のように変更することでビルド エラーを解決できます。

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### モデル クラスの変更

「[操作メソッドの変更](#OperationMethodChanges)」で説明されているシグネチャの変更により、`Microsoft.Azure.Search.Models` 名前空間の多くのクラスの名前が変更されるか、またはクラスが削除されました。次に例を示します。

 - `IndexDefinitionResponse` は `AzureOperationResponse<Index>` によって置き換えられました
 - `DocumentSearchResponse` の名前が `DocumentSearchResult` に変更されました
 - `IndexResult` の名前が `IndexingResult` に変更されました
 - `Documents.Count()` は、`DocumentCountResponse` ではなく、ドキュメントの数を含む `long` を返すようになりました
 - `IndexGetStatisticsResponse` の名前が `IndexGetStatisticsResult` に変更されました
 - `IndexListResponse` の名前が `IndexListResult` に変更されました

まとめると、モデル オブジェクトをラップするためだけに存在していた `OperationResponse` 派生クラスが削除されました。残りのクラスについては、サフィックスが `Response` から `Result` に変更されました。

##### 例

次のようなコードがあるものとします。

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

この場合、次のように変更することでビルド エラーを解決できます。

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

##### 応答クラスと IEnumerable

コードに影響する可能性のあるその他の変更として、コレクションを保持する応答クラスが `IEnumerable<T>` を実装しなくなりました。代わりに、コレクション プロパティに直接アクセスすることができます。たとえば、次のようなコードがあるものとします。

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

この場合、次のように変更することでビルド エラーを解決できます。

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### Web アプリケーションに関する重要な注意事項

`DocumentSearchResponse` を直接シリアル化して検索結果をブラウザーに送信する Web アプリケーションがある場合、コードを変更する必要があります。変更しないと、結果が正しくシリアル化されません。たとえば、次のようなコードがあるものとします。

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

これを変更するには、検索応答の `.Results` プロパティを取得して検索結果のレンダリングを修正します。

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

開発者自身がコード内でこのようなケースを探す必要があります。`JsonResult.Data` は `object` 型であるため、**コンパイラは警告を生成しません**。

#### CloudException の変更

`CloudException` クラスは、`Hyak.Common` 名前空間から `Microsoft.Rest.Azure` 名前空間に移動されました。また、その `Error` プロパティの名前が `Body` に変更されています。

#### SearchServiceClient と SearchIndexClient の変更

`Credentials` プロパティの型が、`SearchCredentials` からその基本クラス `ServiceClientCredentials` に変更されました。`SearchIndexClient` または `SearchServiceClient` の `SearchCredentials` にアクセスする必要がある場合は、新しい `SearchCredentials` プロパティを使用してください。

古いバージョンの SDK では、`SearchServiceClient` および `SearchIndexClient` に `HttpClient` パラメーターを受け取るコンストラクターがありました。これらは、`HttpClientHandler` と、`DelegatingHandler` オブジェクトの配列を受け取るコンストラクターに置き換えられました。これにより、必要な場合に前処理 HTTP 要求にカスタム ハンドラーをインストールするのが容易になります。

最後に、`Uri` および `SearchCredentials` を受け取るコンストラクターが変更されています。たとえば、次のようなコードがあるものとします。

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

この場合、次のように変更することでビルド エラーを解決できます。

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

また、資格情報パラメーターの型が `ServiceClientCredentials` に変更されたことにも注意してください。`SearchCredentials` は `ServiceClientCredentials` から派生しているので、コードへの影響はないものと思われます。

#### 要求 ID の受け渡し

古いバージョンの SDK では、要求 ID は `SearchServiceClient` または `SearchIndexClient` で設定でき、REST API へのすべての要求に組み込まれていました。これは、サポートに連絡する必要がある場合、Search サービスに関する問題のトラブルシューティングに役立ちました。しかし、すべての操作に同じ ID を使用するのではなく、操作ごとに一意の要求 ID を設定する方が便利です。このため、`SearchServiceClient` および `SearchIndexClient` の `SetClientRequestId` メソッドは削除されました。代わりに、省略可能な `SearchRequestOptions` パラメーターを使用して、各操作メソッドに要求 ID を渡すことができます。

> [AZURE.NOTE] SDK の今後のリリースでは、他の Azure SDK で使用されている方法と整合性があるように、クライアント オブジェクトでグローバルに要求 ID を設定する新しいメカニズムが追加されます。

#### 例

次のようなコードがあるものとします。

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

この場合、次のように変更することでビルド エラーを解決できます。

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### インターフェイス名の変更

操作グループのインターフェイス名が、対応するプロパティ名と一致するようにすべて変更されています。

 - `ISearchServiceClient.Indexes` の型の名前が、`IIndexOperations` から `IIndexesOperations` に変更されました。
 - `ISearchServiceClient.Indexers` の型の名前が、`IIndexerOperations` から `IIndexersOperations` に変更されました。
 - `ISearchServiceClient.DataSources` の型の名前が、`IDataSourceOperations` から `IDataSourcesOperations` に変更されました。
 - `ISearchIndexClient.Documents` の型の名前が、`IDocumentOperations` から `IDocumentsOperations` に変更されました。

テスト目的でこれらのインターフェイスのモックを作成した場合を除き、この変更がコードに影響する可能性はありません。

<a name="BugFixesV1"></a>
### バージョン 1.1 でのバグ修正

古いバージョンの Azure Search .NET SDK には、カスタム モデル クラスのシリアル化に関するバグがありました。このバグは、null 非許容値型のプロパティを使用してカスタム モデル クラスを作成した場合に発生する可能性がありました。

#### 再現手順

null 非許容値型のプロパティを使用してカスタム モデル クラスを作成します。たとえば、`int?` ではなく `int` 型のパブリック `UnitCount` プロパティを追加します。

その型の既定値 (たとえば、`int` の場合は 0) でドキュメントのインデックスを設定した場合、フィールドは Azure Search では null になります。その後、そのドキュメントを検索した場合、`Search` の呼び出しで、`null` を `int` に変換できないことを示す `JsonSerializationException` がスローされます。

また、意図した値ではなく null がインデックスに書き込まれるため、フィルターは期待どおりに機能しません。

#### 修正の詳細

この問題は、SDK のバージョン 1.1 で解決されました。次のようなモデル クラスがあるものとします。

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

さらに `IntValue` を 0 に設定します。現在では、この値はネットワーク上で正しく 0 としてシリアル化され、インデックスに 0 と格納されるようになっています。ラウンドトリップも予期したとおりに動作します。

この方法で注意すべき潜在的な問題が 1 つあります。null 非許容プロパティを使用する種類のモデルを使用する場合、対応するフィールドに null 値が含まれるドキュメントがインデックス内に存在しないことを、開発者が**保証する**必要があります。SDK も Azure Search REST API も、このことを強制する役には立ちません。

これは単なる仮定上の問題ではありません。`Edm.Int32` 型の既存のインデックスに新しいフィールドを追加する場合を考えてみてください。インデックスの定義を更新した後、(Azure Search ではすべての型が null を許容するので) すべてのドキュメントでその新しいフィールドの値が null になります。その後、そのフィールドが null 非許容型の `int` プロパティであるモデル クラスを使用した場合、ドキュメントを取得しようとすると、次のような `JsonSerializationException` が発生します。

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

このため、ベスト プラクティスとして、モデル クラスではまだ null 許容型を使用することをお勧めします。

このバグの詳細と修正については、[GitHub でのこの問題の解説](https://github.com/Azure/azure-sdk-for-net/issues/1063)を参照してください。

<!---HONumber=AcomDC_0817_2016-->