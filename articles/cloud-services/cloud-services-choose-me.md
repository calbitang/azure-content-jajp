<properties
	pageTitle="Azure のコンピューティング オプション - Cloud Services | Microsoft Azure"
	description="Azure の次のコンピューティング ホスティング オプションとそのしくみについて学習します: App Service、Cloud Services、Virtual Machines"
	services="cloud-services"
    documentationCenter=""
	authors="Thraka"
	manager="timlt"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/05/2016"
	ms.author="adegeo"/>

# Cloud Services と他のサービスのどちらを選択すればよいか

Azure Cloud Services が最適な選択ですか? Azure にはアプリケーションを実行するための複数のホスティング モデルがあります。それぞれの実行モデルには異なるサービスが用意されているため、実行する内容に正確に基づいて実行モデルを選択してください。

[AZURE.INCLUDE [compute-table](../../includes/compute-options-table.md)]

<a name="tellmecs"></a>
## Cloud Services の概要

Cloud Services は、[サービスとしてのプラットフォーム](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) の 1 つの例です。このテクノロジは、[App Service](../app-service-web/app-service-web-overview.md) と同様に、スケーラブルで信頼性が高く、運用コストが低いアプリケーションをサポートするように設計されています。App Service と同様に、Cloud Services も VM 上でホストされています。しかし、VM に対してより多くのコントロールが可能です。独自のソフトウェアを Cloud Services の VM にインストールして、リモートで操作できます。

![cs\_diagram](./media/cloud-services-choose-me/diagram.png)

コントロール能力の高さは、使いやすさが低下することも意味します。追加のコントロール オプションを必要としない限り、通常は App Service の Web Apps の方が Cloud Services よりも迅速かつ容易に Web アプリケーションを実行できます。

テクノロジにはわずかな相違がある 2 つの VM オプションが用意されています。*Web ロール*のインスタンスは IIS を搭載している Windows Server を実行し、*Worker ロール*のインスタンスは同じ Windows Server で IIS を搭載していない種類を実行します。Cloud Services アプリケーションはこの 2 つのオプションの組み合わせに依存します。

これらのわずかな相違がある 2 つの VM ホスト オプションを任意に組み合わせて、クラウド サービスで使用できます。

* **Web ロール** IIS に自動的にデプロイされた Web アプリを搭載した Windows Server を実行します。

* **Worker ロール** IIS を搭載していない Windows Server を実行します。

たとえば、単純なアプリケーションは Web ロールだけを使用するかもしれませんが、複雑なアプリケーションは Web ロールを使用してユーザーから受け取った要求を処理してから、それらの要求が作成する作業を Worker ロールに渡して処理することがあります (この通信は、[Service Bus](../service-bus/service-bus-fundamentals-hybrid-solutions.md) または [Azure キュー](../storage/storage-introduction.md)を使用する可能性があります)。

上の図に示すように、1 つのアプリケーションのすべての VM は同じクラウド サービスで実行されます。このため、ユーザーは 1 つのパブリック IP アドレスを通してアプリケーションにアクセスし、要求はアプリケーションの VM 間で自動的に負荷分散されます。プラットフォームは、ハードウェアの単一障害点を回避する方法で、VM を Cloud Services アプリケーションで[スケールおよびデプロイ](cloud-services-how-to-scale.md)します。

アプリケーションは仮想マシンで実行しますが、Cloud Services は IaaS ではなく PaaS を提供することを理解することが重要です。次の例を考えてみましょう。Azure Virtual Machines のような IaaS では、アプリケーションが動作する環境を最初に作成して構成してから、この環境にアプリケーションをデプロイします。この方法では、パッチが適用された新しいオペレーティング システムのバージョンを各 VM にデプロイするなど、ほとんどの管理をユーザーが担当します。それに対し、PaaS では環境が既に存在するかのように管理できます。ユーザーはアプリケーションをデプロイするだけで済みます。オペレーティング システムの新バージョンのデプロイをはじめ、実行するプラットフォームの管理がユーザーに代わって処理されます。

## スケーリングと管理
Cloud Services では、ユーザーは仮想マシンを作成しません。代わりに、**Web ロール インスタンスを 3 個**、**Worker ロール インスタンスを 2 個**のように、それぞれがいくつ必要かを Azure に指示する設定ファイルを提供するだけで、プラットフォームがそれらを自動的に作成します。その場合も、バッキング VM の[サイズ](cloud-services-sizes-specs.md)を選択する必要がありますが、自身で明示的に作成する必要はありません。アプリケーションが高い負荷を処理する場合は、追加の VM を要求すると、Azure がそれらのインスタンスを作成します。負荷が減少した場合は、それらのインスタンスをシャットダウンして支払いを停止できます。

Cloud Services アプリケーションは通常、2 つの手順から成るプロセスで利用可能になります。最初に、開発者はプラットフォームのステージング領域に[アプリケーションをアップロード](cloud-services-how-to-create-deploy.md)します。アプリケーションを稼働する準備が整ったら、Microsoft Azure 管理ポータルを使用して運用開始を要求します。[ステージングと運用の切り替え](cloud-services-nodejs-stage-application.md)にはダウンタイムが生じないため、ユーザーに支障を与えることなく、実行中のアプリケーションを新バージョンにアップグレードできます。

## 監視
Cloud Services は監視も提供します。Azure Virtual Machines と同様に、故障した物理サーバーを検出し、そのサーバーで実行していた VM を別のマシンで再開します。さらに、Cloud Services はハードウェアの故障だけではなく、エラーが発生した VM やアプリケーションも検出します。Virtual Machines と異なり、各 Web ロール内と Worker ロール内にエージェントが含まれているので、エラーが発生したときに、新しい VM とアプリケーションのインスタンスを開始できます。

PaaS という Cloud Services の本質には、他の含意もあります。最も重要なことの 1 つは、このテクノロジを基盤に構築されるアプリケーションは、Web または Worker ロール インスタンスでエラーが発生したときに正しく実行するように記述することが必要です。これを実現するには、Cloud Services のアプリケーションがそれ自体の VM のファイル システムで状態を維持してはなりません。Azure Virtual Machines で作成された VM と異なり、Cloud Services VM への書き込みは永続的ではありません。その点で Virtual Machines のデータ ディスクとは大きく異なります。Cloud Services アプリケーションはすべての状態を SQL Database、BLOB、テーブルか、その他の外部ストレージに明示的に書き込む必要があります。この方法でアプリケーションを構築すると、スケーリングしやすく、耐障害性が備わります。この 2 つは Cloud Services の重要なゴールです。

## 次のステップ
[.NET でのクラウド サービス アプリの作成](cloud-services-dotnet-get-started.md) [Node.js でのクラウド サービス アプリの作成](cloud-services-nodejs-develop-deploy-app.md) [PHP でのクラウド サービス アプリの作成](../cloud-services-php-create-web-role.md) [Python でのクラウド サービス アプリの作成](cloud-services-python-ptvs.md)

<!---HONumber=AcomDC_0727_2016-->