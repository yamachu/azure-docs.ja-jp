---
title: Azure Monitor メトリック ストアの従来の Cloud Services にゲスト OS メトリックを送信する
description: Azure Monitor メトリック ストアの Cloud Services にゲスト OS メトリックを送信する
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 30b08062aa360c4a43dc1bfe9f574447b58521f5
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095213"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Azure Monitor メトリック ストアの従来の Cloud Services にゲスト OS メトリックを送信する 
Azure Monitor [診断拡張機能](azure-diagnostics.md)を使用すると、仮想マシン、クラウド サービス、または Service Fabric クラスターの一部として、ゲスト オペレーティング システム (ゲスト OS) からメトリックとログを収集できます。 拡張機能により、[多くの場所](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)にテレメトリを送信できます。

この記事では、従来の Azure Cloud Services 用のゲスト OS のパフォーマンス メトリックを Azure Monitor メトリック ストアに送信するプロセスについて説明します。 診断拡張機能バージョン 1.11 以降、標準プラットフォーム メトリックが既に収集されている Azure Monitor メトリック ストアに、メトリックを直接書き込むことができます。 

この場所にこれらを格納することで、プラットフォーム メトリックに対して使用できるのと同じアクションにアクセスできます。 アクションには、ほぼリアルタイムのアラート、グラフ作成、ルーティング、REST API からのアクセスなどの機能があります。  これまで、診断拡張機能は Azure Storage に書き込みましたが、Azure Monitor データ ストアには書き込みませんでした。  

この記事で説明されているプロセスは、Azure Cloud Services でのパフォーマンス カウンターに対してのみ機能します。 他のカスタム メトリックに対しては機能しません。 
   

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションで、[サービス管理者または共同管理者](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md)である必要があります。 

- サブスクリプションを [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#portal) に登録する必要があります。 

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) または [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) がインストールされている必要があります。


## <a name="provision-a-cloud-service-and-storage-account"></a>クラウド サービスとストレージ アカウントのプロビジョニング 

1. 従来のクラウド サービスを作成し、デプロイします。 従来の Cloud Services アプリケーションとデプロイのサンプルは、「[Azure Cloud Services と ASP.NET を使ってみる](../cloud-services/cloud-services-dotnet-get-started.md)」に示されています。 

2. 既存のストレージ アカウントを使用できるか、新しいストレージ アカウントを配置できます。 ストレージ アカウントが、作成した従来のクラウド サービスと同じ領域にある場合は最適です。 Azure portal で、**ストレージ アカウント**のリソース ブレードに移動し、**キー**を選択します。 ストレージ アカウント名とストレージ アカウント キーをメモしておきます。 この情報は後続の手順で必要になります。

   ![ストレージ アカウント キー](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>サービス プリンシパルの作成 

「[リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)」の手順を使用して、お使いの Azure Active Directory テナントでサービス プリンシパルを作成します。 このプロセスを進める際には、次の点に注意してください。 

  - サインイン URL には任意の URL を入力できます。  
  - このアプリ用に新しいクライアント シークレットを作成します。  
  - 後の手順で使用するために、キーとクライアント ID を保存します。  

以前の手順の*メトリック パブリッシャーの監視*で作成したアプリに、メトリックを発行するリソースへのアクセス許可を付与します。 アプリを使用して多くのリソースに対してカスタム メトリックを発行する予定である場合は、リソース グループまたはサブスクリプション レベルでこれらのアクセス許可を付与できます。  

> [!NOTE]
> 診断拡張機能では、サービス プリンシパルを使用して、Azure Monitor を認証し、クラウド サービス用のメトリックを発行します。

## <a name="author-diagnostics-extension-configuration"></a>診断拡張機能の構成の作成 

診断拡張機能の構成ファイルを準備します。 このファイルにより、お使いのクラウド サービスに対して、診断拡張機能が収集するパフォーマンス カウンターとログが決まります。 サンプルの診断構成ファイルを以下に示します。  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
        </PerformanceCounters> 
        <WindowsEventLog scheduledTransferPeriod="PT1M"> 
          <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" /> 
          <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" /> 
        </WindowsEventLog> 
        <CrashDumps> 
          <CrashDumpConfiguration processName="WaIISHost.exe" /> 
          <CrashDumpConfiguration processName="WaWorkerHost.exe" /> 
          <CrashDumpConfiguration processName="w3wp.exe" /> 
        </CrashDumps> 
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" /> 
      </DiagnosticMonitorConfiguration> 
      <SinksConfig> 
      </SinksConfig> 
    </WadCfg> 
    <StorageAccount /> 
  </PublicConfig> 
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <StorageAccount name="" endpoint="" /> 
</PrivateConfig> 
  <IsEnabled>true</IsEnabled> 
</DiagnosticsConfiguration> 
```

診断ファイルの "SinksConfig" セクションで、新しい Azure Monitor シンクを定義します。 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

収集するパフォーマンス カウンターをリストした構成ファイルのセクションに、Azure Monitor シンクを追加します。 このエントリにより、指定されたすべてのパフォーマンス カウンターが Azure Monitor にメトリックとしてルーティングされます。 パフォーマンス カウンターは必要に応じて追加したり、削除することができます。 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

最後に、プライベート構成に *Azure Monitor アカウント*セクションを追加します。 前の手順で作成したサービス プリンシパルのクライアント ID とシークレットを入力します。 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```
 
この診断ファイルをローカルに保存します。  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>診断拡張機能をクラウド サービスにデプロイします 

PowerShell を起動し、Azure にログインします。 

```PowerShell
Login-AzureRmAccount 
```

次のコマンドを使用して、先ほど作成したストレージ アカウントの詳細情報を格納します。 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
同様に、次のコマンドを使用して、診断ファイルのパスを変数に設定します。

```PowerShell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
次のコマンドを使用して、Azure Monitor シンクが構成された診断ファイルで、診断拡張機能をクラウド サービスに配置します。  

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> 診断拡張機能のインストールの一環として、引き続きストレージ アカウントを指定する必要があります。 診断構成ファイルで指定されたログやパフォーマンス カウンターは、指定したストレージ アカウントに書き込まれます。  

## <a name="plot-metrics-in-the-azure-portal"></a>Azure portal でメトリックをプロットします 

1. Azure Portal にアクセスします。 

   ![メトリック Azure portal](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

2. 左側のメニューで **[モニター]** を選択します。

3. **[モニター]** ブレードで、**[メトリックのプレビュー]** タブを選択します。

4. リソースのドロップダウン メニューで、お使いのクラシック クラウド サービスを選択します。

5. 名前空間のドロップダウン メニューで、**[azure.vm.windows.guest]** を選択します。 

6. メトリックのドロップダウン メニューで、**[Memory\Committed Bytes in Use]** を選択します。 

ディメンションのフィルタリング機能と分割機能を使用することで、特定のロールとロール インスタンスにより使用されている合計メモリを選択して表示することができます。 

 ![メトリック Azure portal](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>次の手順
- [カスタム メトリック](metrics-custom-overview.md)の詳細を確認します。



