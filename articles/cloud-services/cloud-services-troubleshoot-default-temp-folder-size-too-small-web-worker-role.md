<properties
   pageTitle="ロールに対する既定の TEMP フォルダーのサイズが小さすぎる | Microsoft Azure"
   description="クラウド サービス ロールでは、TEMP フォルダー用の領域量が限られています。この記事では、領域不足の回避方法に関する推奨事項をいくつか示します。"
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/19/2016"
   ms.author="v-six" />

# クラウド サービスの Web/worker ロールに対する既定の一時フォルダーのサイズが小さすぎる

クラウド サービスの worker または Web ロールの既定の一時ディレクトリの最大サイズは 100 MB ですが、ある時点でいっぱいになる可能性があります。この記事では、一時ディレクトリの領域不足を回避する方法について説明します。

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## 領域が不足する理由

標準的な Windows 環境変数 TEMP と TMP は、アプリケーションで実行されているコードで使用可能です。TEMP と TMP は両方とも、最大サイズが 100 MB の 1 つのディレクトリを指します。このディレクトリに格納されているデータは、クラウド サービスのライフサイクル全体で保持されません。クラウド サービスのロール インスタンスがリサイクルされると、ディレクトリはクリーニングされます。

## 問題の修正に関する推奨事項

次のいずれかの代替手段を実装します。

- ローカル ストレージ リソースを構成し、TEMP や TMP を使用せずに直接アクセスします。アプリケーション内で実行されているコードからローカル ストレージ リソースにアクセスするには、[RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) メソッドを呼び出します。

- ローカル ストレージ リソースを構成し、TEMP と TMP ディレクトリがローカル ストレージ リソースのパスを指すように指定します。この変更は [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) メソッド内で実行する必要があります。

次のコード例では、OnStart メソッド内から TEMP および TMP のターゲット ディレクトリを変更する方法を示します。


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## 次のステップ

ブログ「[How to increase the size of the Azure Web Role ASP.NET Temporary Folder (Azure Web ロール ASP.NET の一時フォルダーのサイズを増やす方法)](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx)」の説明をご覧ください。

クラウド サービスの他の[トラブルシューティングに関する記事](/?tag=top-support-issue&product=cloud-services)を参照します。

Azure PaaS コンピューターの診断データを使用してクラウド サービス ロールの問題をトラブルシューティングする方法については、[Kevin Williamson によるブログ シリーズ](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)をご覧ください。

<!---HONumber=AcomDC_0727_2016-->