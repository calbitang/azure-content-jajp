<properties 
   pageTitle="インスタンスレベル パブリック IP (ILPIP) | Microsoft Azure"
   description="ILPIP (PIP) とその管理方法について説明します"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="jdial" />

# インスタンス レベル パブリック IP の概要
インスタンス レベル パブリック IP (ILPIP) は、VM またはロール インスタンスが存在するクラウド サービスではなく、VM またはロール インスタンスに直接割り当てることのできるパブリック IP アドレスです。これは、クラウド サービスに割り当てられる VIP (仮想 IP) の代わりにはなりません。むしろ、VM またはロール インスタンスに直接接続するときに使用できる追加の IP アドレスです。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager モデルを使用してこれらの手順を実行する](virtual-network-ip-addresses-overview-arm.md)方法について説明します。

Azure における [IP アドレス](virtual-network-ip-addresses-overview-classic.md)の動作を理解しておく必要があります。

>[AZURE.NOTE] これまで、ILPIP は、パブリック IP を意味する PIP という名前で呼ばれていました。

![Difference between ILPIP and VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

図 1 に示すように、クラウド サービスには VIP を使用してアクセスするのに対して、個々 の VM には通常 "VIP:&lt;ポート番号&gt;" を使用してアクセスします。ILPIP を特定の VM に割り当てることで、その IP アドレスを使用して VM に直接アクセスできます。

Azure でクラウド サービスを作成すると、対応する DNS A レコードが自動的に作成され、実際の VIP を使用するのではなく完全修飾ドメイン名 (FQDN) を使用してサービスにアクセスできるようになります。同じ処理が ILPIP に対しても行われ、ILPIP ではなく FQDN による VM またはロール インスタンスへのアクセスが許可されます。たとえば、*contosoadservice* という名前のクラウド サービスを作成し、*contosoweb* という名前の Web ロールの 2 つのインスタンスを構成した場合、Azure によってこれらのインスタンスに対して次の A レコードが登録されます。

- contosoweb\_IN\_0.contosoadservice.cloudapp.net
- contosoweb\_IN\_1.contosoadservice.cloudapp.net

>[AZURE.NOTE] 各 VM またはロール インスタンスに割り当てることができる ILPIP は 1 つだけです。サブスクリプションにつき最大 5 つの ILPIP を使用できます。現時点では、複数 NIC の VMは ILPIP でサポートされていません。

## ILPIP を要求する理由
VM またはロール インスタンスに直接割り当てた IP アドレスで接続できるようにする場合は、クラウド サービスの VIP:&lt;ポート番号&gt; を使用する代わりに、VM またはロール インスタンスの ILPIP を要求します。
- **パッシブ FTP** - VM の ILPIP を取得することですべてのポートのトラフィックを受信でき、トラフィックを受信するためにエンドポイントを開く必要はなくなります。これにより、ポートが動的に選択されるパッシブ FTP などのシナリオを実現できます。
- **送信 IP** - VM からの発信トラフィックは、送信元である ILPIP と共に送信されます。これは、外部エンティティに対して VM を一意に識別します。

## VM の作成中に ILPIP を要求する方法
次の PowerShell スクリプトでは、*FTPService* という名前の新しいクラウド サービスを作成した後、Azure のイメージを取得し、取得したイメージを使用して *FTPInstance* という名前の VM を作成します。さらに、ILPIP を使用するように VM を設定し、新しいサービスにその VM を追加します。

	New-AzureService -ServiceName FTPService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## VM の ILPIP 情報を取得する方法
上記のスクリプトで作成した VM の ILPIP 情報を表示するには、次の PowerShell コマンドを実行し、*PublicIPAddress* と *PublicIPName* の値を確認します。

	Get-AzureVM -Name FTPInstance -ServiceName FTPService

	DeploymentName              : FTPService
	Name                        : FTPInstance
	Label                       : 
	VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : ReadyRole
	IpAddress                   : 100.74.118.91
	InstanceStateDetails        : 
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : FTPInstance
	InstanceUpgradeDomain       : 0
	InstanceSize                : Small
	HostName                    : FTPInstance
	AvailabilitySetName         : 
	DNSName                     : http://ftpservice888.cloudapp.net/
	Status                      : ReadyRole
	GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
	ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
	PublicIPAddress             : 104.43.142.188
	PublicIPName                : ftpip
	NetworkInterfaces           : {}
	ServiceName                 : FTPService
	OperationDescription        : Get-AzureVM
	OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
	OperationStatus             : OK

## VM から ILPIP を削除する方法
上記のスクリプトで VM に追加された ILPIP を削除するには、次の PowerShell コマンドを実行します。
	
	Get-AzureVM -ServiceName FTPService -Name FTPInstance `
	| Remove-AzurePublicIP `
	| Update-AzureVM

## 既存の VM に ILPIP を追加する方法
上記のスクリプトを使用して作成した VM に ILPIP を追加するには、次のコマンドを実行します。

	Get-AzureVM -ServiceName FTPService -Name FTPInstance `
	| Set-AzurePublicIP -PublicIPName ftpip2 `
	| Update-AzureVM

## サービス構成ファイルを使用して VM に ILPIP を関連付ける方法
サービス構成 (CSCFG) ファイルを使用して、VM に ILPIP を関連付けることもできます。次のサンプル xml では、ロール インスタンスで *MyPublicIP* という名前の ILPIP を使用するようにクラウド サービスを構成する方法を示しています。
	
	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
	  <Role name="WebRole1">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
	    </ConfigurationSettings>
	  </Role>
      <NetworkConfiguration>
	    <VirtualNetworkSite name="VNet"/>
	    <AddressAssignments>
	      <InstanceAddress roleName="VMRolePersisted">
	        <Subnets>
	          <Subnet name="Subnet1"/>
	          <Subnet name="Subnet2"/>
	        </Subnets>
	        <PublicIPs>
	          <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
	        </PublicIPs>
	      </InstanceAddress>
	    </AddressAssignments>
	  </NetworkConfiguration>
	</ServiceConfiguration>

## 次のステップ

- クラシック デプロイ モデルの [IP アドレス指定](virtual-network-ip-addresses-overview-classic.md)の仕組みを理解します。

- [予約済み IP](virtual-networks-reserved-public-ip.md) について学習します。
 

<!---HONumber=AcomDC_0810_2016-->