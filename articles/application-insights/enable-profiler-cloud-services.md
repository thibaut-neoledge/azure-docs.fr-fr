---
title: Activer Azure Application Insights Profiler sur les ressources de calcul Azure | Microsoft Docs
description: "Découvrez comment configurer le profileur dans une application ASP.NET hébergée par une ressource Azure Cloud Services."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.openlocfilehash: 65ba755f35df7bd09dd652ac6fccf96a878c6ca9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-application-insights-profiler-on-an-azure-cloud-services-resource"></a>Activer Application Insights Profiler pour une ressource Azure Cloud Services

Cette procédure pas à pas montre comment activer Azure Application Insights Profiler sur une application ASP.NET hébergée par une ressource Azure Cloud Services. Les exemples incluent la prise en charge de machines virtuelles Azure, de groupes de machines virtuelles identiques et de Azure Service Fabric. Les exemples s’appuient tous sur des modèles qui prennent en charge le modèle de déploiement Azure Resource Manager. Pour en savoir plus sur le modèle de déploiement, consultez [Azure Resource Manager et le déploiement classique : Comprendre les modèles de déploiement et l’état de vos ressources](/azure-resource-manager/resource-manager-deployment-model).

## <a name="overview"></a>Vue d'ensemble

Le schéma suivant illustre le fonctionnement du profileur pour les ressources Azure Cloud Services. Il utilise une machine virtuelle Azure comme exemple.

![Vue d’ensemble](./media/enable-profiler-compute/overview.png) Afin de collecter des informations pour le traitement et l’affichage sur le portail Azure, vous devez installer le composant Agent de diagnostics pour les ressources Azure Cloud Services. Le reste de la procédure pas à pas fournit des conseils sur la façon d’installer et de configurer l’agent de diagnostics pour activer Application Insights Profiler.

## <a name="prerequisites-for-the-walkthrough"></a>Configuration requise pour la procédure pas à pas

* Un modèle de déploiement Resource Manager qui installe les agents du profileur sur les machines virtuelles ([WindowsVirtualMachine.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)) ou les groupes de machines virtuelles identiques ([WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)).

* Une instance Application Insights activée pour le profilage. Pour obtenir des instructions, voir [Activer le profil](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler).

* Une instance .NET Framework 4.6.1 ou version ultérieure installée sur la ressource Azure Cloud Services cible.

## <a name="create-a-resource-group-in-your-azure-subscription"></a>Créer un groupe de ressources dans votre abonnement Azure
L’exemple suivant montre comment créer un groupe de ressources à l’aide d’un script PowerShell :

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>Créer une ressource Application Insights dans le groupe de ressources
Dans le panneau **Application Insights**, saisissez les informations associées à votre ressource, comme illustré dans cet exemple : 

![Panneau Application Insights](./media/enable-profiler-compute/createai.png)

## <a name="apply-an-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>Appliquer une clé d’instrumentation Application Insights dans le modèle Azure Resource Manager

1. Si vous n’avez pas encore téléchargé le modèle, téléchargez-le depuis [GitHub](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json).

2. Trouvez la clé Application Insights.
   
   ![Emplacement de la clé](./media/enable-profiler-compute/copyaikey.png)

3. Remplacez la valeur du modèle.
   
   ![Valeur remplacée dans le modèle](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-an-azure-vm-to-host-the-web-application"></a>Créer une machine virtuelle Azure pour héberger l’application web
1. Créez une chaîne sécurisée pour enregistrer le mot de passe.

   ```
   $password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
   ```

2. Déployez le modèle Azure Resource Manager.

   Remplacez le répertoire dans la console PowerShell par le dossier qui contient votre modèle Resource Manager. Pour déployer le modèle, exécutez la commande suivante :

   ```
   New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
   ```

Après exécution du script, vous devriez trouver une machine virtuelle nommée **MyWindowsVM** dans votre groupe de ressources.

## <a name="configure-web-deploy-on-the-vm"></a>Configurer Web Deploy sur la machine virtuelle
Assurez-vous que l’extension Web Deploy est activée sur votre machine virtuelle pour pouvoir publier votre application web à partir de Visual Studio.

Pour installer Web Deploy manuellement sur une machine virtuelle via WebPI, voir [Installation et configuration de Web Deploy sur IIS 8.0 ou version ultérieure](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later). Pour voir un exemple illustrant l’automatisation de l’installation de Web Deploy à l’aide du modèle Azure Resource Manager, consultez l’article [Créer, configurer et déployer une application web sur une machine virtuelle Azure](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/).

Si vous déployez une application ASP.NET MVC, accédez au Gestionnaire de serveur, sélectionnez **Ajouter des rôles et des fonctionnalités** > **Serveur Web (IIS)** > **Serveur Web** > **Développement d’applications** et activez ASP.NET 4.5 sur votre serveur.

![Ajouter ASP.NET](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-the-azure-application-insights-sdk-for-your-project"></a>Installer le kit de développement logiciel (SDK) Azure Application Insights pour votre projet
1. Ouvrez votre application web ASP.NET dans Visual Studio.

2. Cliquez avec le bouton droit sur le projet et sélectionnez **Ajouter** > **Services connectés**.

3. Sélectionnez **Application Insights**.

4. Suivez les instructions figurant dans cette page. Sélectionnez la ressource Application Insights que vous avez créée précédemment.

5. Sélectionnez le bouton **Inscrire**.


## <a name="publish-the-project-to-an-azure-vm"></a>Publier le projet sur une machine virtuelle Azure
Il existe plusieurs façons de publier une application sur une machine virtuelle Azure. Vous pouvez par exemple utiliser Visual Studio 2017.

1. Cliquez avec le bouton droit sur le projet et sélectionnez **Publier**.

2. Sélectionnez **Machines virtuelles Microsoft Azure** comme cible de publication et suivez les étapes indiquées.

   ![Publish-FromVS](./media/enable-profiler-compute/publishtoVM.png)

3. Exécutez un test de charge sur votre application. Les résultats doivent s’afficher dans la page web du portail de l’instance Application Insights.


## <a name="enable-the-profiler"></a>Activer le profileur

1. Accédez au panneau **Performances** Application Insights, puis cliquez sur **Profileur** dans l’angle supérieur droit pour le configurer.

   ![Bouton Configurer le profileur](./media/enable-profiler-compute/PerformanceTriageViewPofilerButton.png)

2. Sélectionnez **Activer le profileur**.

   ![Bouton Activer le profileur](./media/enable-profiler-compute/enableprofiler2.png)


## <a name="add-a-performance-test-to-your-application"></a>Ajouter un test de performance à votre application
Suivez ces étapes pour collecter des exemples de données à afficher dans Application Insights Profiler :

1. Accédez à la ressource Application Insights que vous avez créée précédemment. 

2. Affichez le panneau **Disponibilité** et ajoutez un test de performance qui envoie des requêtes web à l’URL de votre application. 

   ![Ajouter un test de performance](./media/enable-profiler-compute/AvailabilityTest.png)

## <a name="view-your-performance-data"></a>Afficher vos données de performances

1. Attendez 10 à 15 minutes que le profileur collecte et analyse les données. 

2. Accédez au panneau **Performances** dans votre ressource Application Insights et voyez comment votre application s’est comportée lorsqu’elle était soumise à une charge importante. Concentrez-vous sur l’opération lente qui vous intéresse avec suffisamment d’expérience d’utilisation en triant la grille des opérations par rapport à la colonne Nombre. Déterminez quelles plages de durée ont des traces du profileur en examinant la voie du profileur au-dessus de la distribution de durée. Notez que plus vous analysez votre application, plus le profileur collecte de traces, et donc plus la distribution est couverte dans les exemples de codes enrichis pris en charge par les traces du profileur. 

   ![Traces du profileur dans la vue de triage des performances](./media/enable-profiler-compute/PerformanceTriageViewProfilerTraces.png)

    Vous pouvez effectuer un zoom avant sur la plage de durée qui vous intéresse, comme le troisième pic au niveau du 95e centile. Cette opération limite le nombre d’exemples et de traces du profileur dans les boutons permettant d’effectuer des actions. 

    ![Zoom sur la plage de durée](./media/enable-profiler-compute/DurationRangeZoomedTo95th.png)

    Cliquez maintenant sur le bouton **Traces du profileur** afin d’ouvrir le profileur avec la trace appropriée.

3. Sélectionnez l’icône située sous **Exemples** pour ouvrir le panneau **Vue de la trace**.

   ![Ouverture du panneau Vue de la trace](./media/enable-profiler-compute/traceview.png)


## <a name="work-with-an-existing-template"></a>Utiliser un modèle existant

1. Recherchez la déclaration de ressource Azure Diagnostics dans votre modèle de déploiement.
   
   Si vous n’avez pas de déclaration, vous pouvez en créer une qui ressemble à la déclaration présentée dans l’exemple suivant. Vous pouvez mettre à jour le modèle à partir du [site web Azure Resource Explorer](https://resources.azure.com).

2. Remplacez le serveur de publication `Microsoft.Azure.Diagnostics` par `AIP.Diagnostics.Test`.

3. Pour `typeHandlerVersion`, utilisez `0.0`.

4. Assurez-vous que la valeur `autoUpgradeMinorVersion` est définie sur `true`.

5. Ajoutez la nouvelle instance de récepteur `ApplicationInsightsProfiler` dans l’objet de paramètres `WadCfg`, comme indiqué dans l’exemple suivant :

```
"resources": [
        {
          "type": "extensions",
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "apiVersion": "2016-03-30",
          "properties": {
            "publisher": "AIP.Diagnostics.Test",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "WadCfg": {
                "SinksConfig": {
                  "Sink": [
                    {
                      "name": "Give a descriptive short name. E.g.: MyApplicationInsightsProfilerSink",
                      "ApplicationInsightsProfiler": "Enter the Application Insights instance instrumentation key guid here"
                    }
                  ]
                },
                "DiagnosticMonitorConfiguration": {
                    ...
                }
                ...
              }
              ...
            }
            ...
          }
          ...
]
```

## <a name="enable-the-profiler-on-virtual-machine-scale-sets"></a>Activer le profileur sur des groupes de machines virtuelles identiques
Pour savoir comment activer le profileur, téléchargez le modèle [WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json). Appliquez les mêmes modifications effectuées dans un modèle de machine virtuelle à la ressource d’extension de diagnostics du groupe de machines virtuelles identiques.

Vérifiez que chaque instance du groupe de machines virtuelles identiques a accès à Internet. L’agent du profileur peut ainsi envoyer les échantillons collectés à Application Insights en vue de leur analyse et de leur affichage.

## <a name="enable-the-profiler-on-service-fabric-applications"></a>Activer le profileur sur les applications Service Fabric
1. Configurez le cluster Service Fabric avec l’extension Azure Diagnostics qui installe l’agent du profileur.

2. Installez le kit SDK Application Insights dans le projet et configurez la clé Application Insights.

3. Ajoutez du code d’application pour instrumenter la télémétrie.

### <a name="provision-the-service-fabric-cluster-to-have-the-azure-diagnostics-extension-that-installs-the-profiler-agent"></a>Configurer le cluster Service Fabric avec l’extension Azure Diagnostics qui installe l’agent du profileur
Un cluster Service Fabric peut être sécurisé ou non sécurisé. Vous pouvez définir un cluster de passerelle pour qu’il soit non sécurisé et ainsi qu’il ne nécessite aucun certificat pour l’accès. Les clusters qui hébergent les données et la logique métier doivent être sécurisés. Vous pouvez activer le profileur sur les clusters Service Fabric sécurisés et non sécurisés. Cette procédure pas à pas utilise un cluster non sécurisé comme exemple pour expliquer quelles modifications doivent être effectuées pour activer le profileur. Vous pouvez configurer un cluster sécurisé de la même façon.

1. Téléchargez le fichier [ServiceFabricCluster.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json). Comme pour les machines virtuelles et les groupes de machines virtuelles identiques, remplacez `Application_Insights_Key` par votre clé Application Insights :

   ```
   "publisher": "AIP.Diagnostics.Test",
                 "settings": {
                   "WadCfg": {
                     "SinksConfig": {
                       "Sink": [
                         {
                           "name": "MyApplicationInsightsProfilerSinkVMSS",
                           "ApplicationInsightsProfiler": "[Application_Insights_Key]"
                         }
                       ]
                     },
   ```

2. Déployez le modèle à l’aide d’un script PowerShell :

   ```
   Login-AzureRmAccount
   New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
   New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

   ```

### <a name="install-the-application-insights-sdk-in-the-project-and-configure-the-application-insights-key"></a>Installer le kit SDK Application Insights dans le projet et configurer la clé Application Insights
Installez le kit SDK Application Insights à partir du [package NuGet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Assurez-vous que vous installez une version stable, 2.3 ou ultérieure. 

Pour plus d’informations sur la configuration d’Application Insights dans vos projets, voir [Utilisation de Service Fabric avec Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).

### <a name="add-application-code-to-instrument-telemetry"></a>Ajouter du code d’application pour instrumenter la télémétrie
1. Pour tout bloc de code que vous souhaitez instrumenter, encadrez-le par une instruction using. 

   Dans l’exemple suivant, la méthode `RunAsync` effectue un travail, et la classe `telemetryClient` capture la télémétrie une fois qu’elle a démarré. L’événement doit avoir un nom unique dans toute l’application.

   ```
   protected override async Task RunAsync(CancellationToken cancellationToken)
       {
           // TODO: Replace the following sample code with your own logic
           //       or remove this RunAsync override if it's not needed in your service.

           while (true)
           {
               using( var operation = telemetryClient.StartOperation<RequestTelemetry>("[Insert_Event_Unique_Name]"))
               {
                   cancellationToken.ThrowIfCancellationRequested();

                   ++this.iterations;

                   ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", this.iterations);

                   await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
               }

           }
       }
   ```

2. Déployez votre application sur le cluster Service Fabric. Attendez que l’application s’exécute pendant 10 minutes. Pour un meilleur effet, vous pouvez exécuter un test de charge sur l’application. Accédez au panneau **Performances** du portail Application Insights où vous devriez voir des exemples de traces de profilage.

<!---
Commenting out these sections for now
## Enable the Profiler on Cloud Services applications
[TODO]
## Enable the Profiler on classic Azure Virtual Machines
[TODO]
## Enable the Profiler on on-premise servers
[TODO]
--->

## <a name="next-steps"></a>Étapes suivantes

- Trouvez de l’aide pour résoudre les problèmes rencontrés avec le profileur en consultant l’article [Résolution des problèmes de profileur](app-insights-profiler.md#troubleshooting).

- Pour en savoir plus sur le profileur, consultez l’article [Application Insights Profiler](app-insights-profiler.md).
