---
title: Activer Application Insights Profiler pour les applications Azure Compute | Microsoft Docs
description: "Découvrez comment configurer le profileur sur une application qui s’exécute dans Azure Compute."
services: application-insights
documentationcenter: 
author: ramach
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: b39b3b234112647ebeb531262d3b3876aee0b63b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2017
---
# <a name="enable-application-insights-profiler-on-azure-virtual-machines-service-fabric-and-cloud-services"></a>Activer Application Insights Profiler sur des machines virtuelles Azure, Service Fabric et des services cloud

Cette procédure pas à pas montre comment activer Azure Application Insights Profiler sur une application ASP.NET hébergée par une ressource Azure Compute.  
Les exemples incluent la prise en charge de Machines virtuelles Azure, Azure Virtual Machine Scale Sets, Azure Service Fabric et Azure Cloud Services.  
Les exemples s’appuient sur des modèles qui prennent en charge le modèle de déploiement [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).  


## <a name="overview"></a>Vue d’ensemble

Le schéma suivant illustre le fonctionnement du profileur pour les ressources Azure. Il utilise une machine virtuelle Azure comme exemple.

![Vue d'ensemble](./media/enable-profiler-compute/overview.png)


Pour une activation totale du profileur, vous devez effectuer la configuration à trois emplacements :

1. Panneau du portail de l’instance Application Insights.
2. Code source de l’application (par exemple, une application web ASP.NET).
3. Code source de la définition du déploiement d’environnement (par exemple, un fichier json de modèle de déploiement de machine virtuelle).


## <a name="configure-the-application-insights-instance"></a>Configurer l’instance Application Insights

Créez ou accédez au panneau du portail Azure pour l’instance Application Insights que vous souhaitez utiliser et notez sa clé d’instrumentation. Elle sera utile pour d’autres étapes de configuration :

  ![Emplacement de la clé](./media/enable-profiler-compute/CopyAIKey.png)

Cette instance doit être celle pour laquelle votre application est configurée pour envoyer des données de télémétrie à chaque demande.
Les résultats du profileur seront aussi disponibles dans cette instance.  

Toujours dans le portail Azure, suivez [Activer le profileur](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler) pour terminer la configuration de l’instance AI pour le profileur. Il n’est pas nécessaire de lier des applications web dans le cadre de cette procédure pas à pas. Vérifiez simplement que le profileur est activé dans le portail.


## <a name="configure-the-application-source-code"></a>Configurer le code source de l’application

Votre application doit être configurée pour envoyer des données de télémétrie à une instance Application Insights lors de chaque opération `Request`.  

1. Ajoutez le kit [Application Insights SDK](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview#get-started) à votre projet d’application. Vérifiez que les versions de package nuget sont les suivantes :  
  - Pour les applications ASP.NET : [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) version 2.3.0 ou ultérieure.
  - Pour les applications ASP.NET Core : [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) version 2.1.0 ou ultérieure.
  - Pour les autres applications .NET et .NET Core (par exemple, un service sans état Service Fabric ou un rôle worker de service cloud) : [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) ou [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 ou version ultérieure.  

2. Si votre application n’est *pas* une application ASP.NET ou ASP.NET Core (par exemple : rôles worker de services cloud, API sans état Service Fabric), la configuration d’instrumentation supplémentaire suivante est nécessaire :  

  2.1. Ajoutez le code suivant tôt dans la durée de vie de l’application.  

  ```csharp
  using Microsoft.ApplicationInsights.Extensibility;
  ...
  // Replace with your own Application Insights instrumentation key.
  TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
  ```
  Pour plus d’informations sur cette configuration de clé d’instrumentation générale, consultez [Using Service Fabric with Application Insights (Utilisation de Service Fabric avec Application Insights)](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  2.2. Pour tout bloc de code que vous souhaitez instrumenter, encadrez-le d’une instruction `StartOperation<RequestTelemetry>` `using`, comme dans l’exemple suivant :

  ```csharp
  using Microsoft.ApplicationInsights;
  using Microsoft.ApplicationInsights.DataContracts;
  ...
  var client = new TelemetryClient();
  ...
  using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
  {
    // ... Code I want to profile.
  }
  ```

> [!NOTE]  
> L’appel de `StartOperation<RequestTelemetry>` dans une autre étendue `StartOperation<RequestTelemetry>` n’est pas pris en charge. Vous pouvez utiliser `StartOperation<DependencyTelemetry>` dans l’étendue imbriquée à la place. Exemple :  

```csharp
using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
{
  try
  {
    ProductDetail details = new ProductDetail() { Id = productId };
    getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();

    // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
    using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
    {
        double price = await _priceDataBase.GetAsync(productId);
        if (IsTooCheap(price))
        {
            throw new PriceTooLowException(productId);
        }
        details.Price = price;
    }

    // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
    using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
    {
        details.Reviews = await _reviewDataBase.GetAsync(productId);
    }

    getDetailsOperation.Telemetry.Success = true;
    return details;
  }
  catch(Exception ex)
  {
    getDetailsOperation.Telemetry.Success = false;

    // This exception gets linked to the 'GetProductDetails' request telemetry.
    client.TrackException(ex);
    throw;
  }
}
```


## <a name="configure-the-environment-deployment-definition"></a>Configurer la définition du déploiement d’environnement

L’environnement où s’exécutent le profileur et votre application peut être une machine virtuelle, un groupe de machines virtuelles identiques, un cluster Service Fabric ou un service cloud.  

### <a name="virtual-machine-virtual-machine-scale-sets-or-service-fabric"></a>Machine virtuelle, Virtual Machine Scale Sets ou Service Fabric

Exemples complets :  
  * [Machine virtuelle](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Groupe de machines virtuelles identiques](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Cluster Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. Pour s’assurer que le [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou ultérieur est utilisé, il suffit de confirmer que le système d’exploitation déployé est `Windows Server 2012 R2` ou ultérieur.

2. Recherchez l’extension [Azure Diagnostics](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) dans le fichier de modèle de déploiement et ajoutez la section `SinksConfig` suivante comme élément enfant de `WadCfg`, en remplaçant la valeur de propriété `ApplicationInsightsProfiler` par votre propre clé d’instrumentation AI :  
```json
"SinksConfig": {
  "Sink": [
    {
      "name": "MyApplicationInsightsProfilerSink",
      "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
    }
  ]
}
```

Pour découvrir comment ajouter l’extension Diagnostics à votre modèle de déploiement, consultez les exemples et [ce guide](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


### <a name="cloud-services"></a>Services cloud

1. Pour s’assurer que le [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou ultérieur est utilisé, il suffit de confirmer que les fichiers `ServiceConfiguration.*.cscfg` contiennent `osFamily` égal à `"5"` ou ultérieur.

2. Recherchez le fichier `diagnostics.wadcfgx` [Azure Diagnostics](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) pour votre rôle d’application :  
![Emplacement du fichier de configuration de diagnostics](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
Si vous ne trouvez pas le fichier, consultez [ce guide](https://docs.microsoft.com/en-us/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them) pour savoir comment activer l’extension Diagnostics dans votre projet Azure Cloud Services.

3. Ajoutez la section `SinksConfig` suivante comme élément enfant de `WadCfg` :  
  ```xml
  <WadCfg>
    <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
    <SinksConfig>
      <Sink name="MyApplicationInsightsProfiler">
        <!-- Replace with your own Application Insights instrumentation key. -->
        <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
      </Sink>
    </SinksConfig>
  </WadCfg>
  ```

> [!NOTE]  
> Si le fichier `diagnostics.wadcfgx` contient également un autre récepteur de type `ApplicationInsights`, ces trois clés d’instrumentation doivent correspondre :  
>  * La clé d’instrumentation utilisée par votre application  
>  * La clé d’instrumentation utilisée par le récepteur `ApplicationInsights`  
>  * La clé d’instrumentation utilisée par le récepteur `ApplicationInsightsProfiler`  
>
>La valeur de clé d’instrumentation réelle utilisée par le récepteur `ApplicationInsights` se trouve dans les fichiers `ServiceConfiguration.*.cscfg`.  
>Après la publication du kit SDK Azure Visual Studio 15.5, seules les clés d’instrumentation utilisées par l’application et le récepteur `ApplicationInsightsProfiler` doivent correspondre l’une à l’autre.


## <a name="environment-deployment-and-runtime-configurations"></a>Configurations de runtime et déploiement d’environnement

1. Déployez la définition du déploiement d’environnement modifiée.  
En règle générale, pour appliquer les modifications, vous devez effectuer un déploiement de modèle complet ou une publication Cloud Services avec les applets de commande PowerShell ou Visual Studio.  
Voici une autre approche pour l’instance `Virtual Machines` existante qui touche uniquement son extension Diagnostics :  
```powershell
$ConfigFilePath = [IO.Path]::GetTempFileName()
# After exporting the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
(Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
# Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
# if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
# (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
```

2. Si l’application souhaitée s’exécute avec [IIS](https://www.microsoft.com/web/platform/server.aspx), vous devez activer la fonctionnalité `IIS Http Tracing` de Windows :  
  Établissez l’accès à distance à l’environnement, puis utilisez la fenêtre [Ajouter des fonctionnalités Windows]( https://docs.microsoft.com/en-us/iis/configuration/system.webserver/tracing/) ou exécutez la commande suivante dans PowerShell (en tant qu’administrateur) :  
  ```powershell
  Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
  ```  
  Si l’établissement de l’accès à distance est un problème, vous pouvez utiliser [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli) pour exécuter la commande suivante :  
  ```powershell
  az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
  ```


## <a name="how-to-enable-profiler-on-on-premises-servers"></a>Comment activer le profileur sur des serveurs locaux

Autrement dit, comment exécuter AI Profiler en mode autonome (non lié aux modifications d’extension Diagnostics).  
Nous ne prévoyons pas une prise en charge officielle du profileur pour les serveurs locaux.
Si vous souhaitez tester ce scénario, vous pouvez télécharger le code de prise en charge [ici](https://github.com/ramach-msft/AIProfiler-Standalone).  
Nous ne sommes *pas* responsables de la mise à jour de ce code, et nous ne répondons pas aux demandes de fonctionnalités ni aux questions relatives à des problèmes liés à ce code.


## <a name="next-steps"></a>Étapes suivantes

- Générez du trafic vers votre application (par exemple, lancez un [Test de disponibilité](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-monitor-web-app-availability)) et patientez 10 à 15 minutes pour que les traces commencent à être envoyées à l’instance Application Insights.

- Consultez [Traces du profileur](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler) dans le portail Azure.

- Trouvez de l’aide pour résoudre les problèmes rencontrés avec le profileur en consultant l’article [Résolution des problèmes du profileur](app-insights-profiler.md#troubleshooting).

- Pour en savoir plus sur le profileur, consultez l’article [Application Insights Profiler](app-insights-profiler.md).
