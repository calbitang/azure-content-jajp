<properties
	pageTitle="データ ソースのプロファイリングを行う方法"
	description="この記事では、Azure Data Catalog でデータ ソースを登録するときにテーブル レベルのデータ プロファイルと列レベルのデータ プロファイルを含める方法と、データ プロファイルを基にデータ ソースについて理解する方法について説明します。"
	services="data-catalog"
	documentationCenter=""
	authors="spelluru"
	manager="NA"
	editor=""
	tags=""/>
<tags
	ms.service="data-catalog"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-catalog"
	ms.date="06/27/2016"
	ms.author="spelluru"/>

# データ ソースのプロファイリング

## はじめに

**Microsoft Azure Data Catalog** は、完全に管理されたクラウド サービスであり、エンタープライズ データ ソースの登録のシステムと検出のシステムとして機能します。つまり、**Azure Data Catalog** を使用すると、ユーザーはデータ ソースを検出、理解、使用でき、組織は既存のデータからより多くの価値を引き出すことができます。データ ソースが **Azure Data Catalog** に登録されると、そのメタデータはサービスによってコピーされてインデックスが付けられます。ただし、これで終わりではありません。

**Azure Data Catalog** は、カタログでサポートされているデータ ソースからのデータを分析し、そのデータに関する統計と情報を収集します。これを**データのプロファイリング**といいます。データ資産のプロファイルは簡単に追加できます。データ資産を登録する際に、データ ソース登録ツールで **[データ プロファイルを含める]** を選択してください。

## データのプロファイリングとは

データのプロファイリングとは、登録されているデータ ソース内のデータを分析し、そのデータに関する統計と情報を収集する処理です。これらの統計情報は、データ ソースの検出時にユーザーが、ビジネス上の問題解決に向けたデータの適合性を判断する際に役立てられます。

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

データのプロファイリングは、次のデータ ソースでサポートされます。

- SQL Server (Azure SQL DB、Azure SQL Data Warehouse を含む) のテーブルとビュー
- Oracle のテーブルとビュー
- Teradata のテーブルとビュー
- Hive のテーブル

データ資産の登録時にデータ プロファイルを含めることで、データ ソースについて次の点が明らかになります。

-	ビジネス上の問題解決に利用できるか。
-	データが特定の標準やパターンに従っているか。
-	データ ソースの不規則性。
-	データをアプリケーションに統合するうえでどのような課題が考えられるか。

> [AZURE.NOTE] アプリケーションに対してどのようにデータを統合するかについて記述するドキュメントを資産に追加することもできます。[データ ソースの文書化の方法](data-catalog-how-to-documentation.md)を参照してください。


<a name="howto"/>
## データ ソースの登録時にデータ プロファイルを含める方法

データ ソースのプロファイルは簡単に追加できます。データ ソースを登録するときに、データ ソース登録ツールの **[登録されるオブジェクト]** パネルで **[データ プロファイルを含める]** を選択します。

![](media\data-catalog-data-profile\data-catalog-register-profile.png)

データ ソースを登録する方法の詳細については、「[データ ソースの登録方法](data-catalog-how-to-register.md)」と「[Azure Data Catalog の概要](data-catalog-get-started.md)」を参照してください。


## データ プロファイルを含んだデータ資産をフィルターで抽出する
データ プロファイルを含んだデータ資産を検出するには、検索語の 1 つとして「**has:tableDataProfiles**」または「**has:columnsDataProfiles**」を追加します。

> [AZURE.NOTE] データ ソース登録ツールの **[データ プロファイルを含める]** を選択すると、テーブル レベルのプロファイル情報と列レベルのプロファイル情報の両方が追加されますが、Data Catalog API で登録できるのは、いずれか一方のプロファイル情報を含んだデータ資産だけです。

## データ プロファイル情報の表示

プロファイルを含んだ適切なデータ ソースが見つかったら、そのデータ プロファイルの詳細を表示できます。データ プロファイルを表示するには、[Data Catalog ポータル] ウィンドウでデータ資産を選択し、**[データ プロファイル]** を選択します。

![](media\data-catalog-data-profile\data-catalog-view.png)

**[Azure Data Catalog]** のデータ プロファイルに、テーブルと列のプロファイル情報が表示されます。それぞれ表示される情報は以下のとおりです。

### オブジェクト データ プロファイル

-	行数
-	テーブルのサイズ
-	オブジェクトが最後に更新されたのはいつか

### 列データ プロファイル

- 列のデータ型
- 個別の値の数
- NULL 値を含んだ行の数
- 列の値の最小、最大、平均、標準偏差

## 概要
登録されているデータ資産についての統計と情報は、データのプロファイリングを通じて得ることができます。ユーザーはそれを基に、ビジネス上の問題解決に向けたデータの適合性を判断することができます。データ プロファイルは、データ ソースの注釈付けや文書化と共に、データについての理解を深める手段となります。


## 関連項目
-	[データ ソースの登録方法](data-catalog-how-to-register.md)
-	[Azure Data Catalog の概要](data-catalog-get-started.md)

<!---HONumber=AcomDC_0629_2016-->