<properties
	pageTitle="Automatiser la gestion d’applications Service Fabric à l’aide de PowerShell | Microsoft Azure"
	description="Déployer, mettre à niveau, tester et supprimer des applications Service Fabric à l’aide de PowerShell."
	services="service-fabric"
	documentationCenter=".net"
	authors="rwike77"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-fabric"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="ryanwi"/>

# Déployer, mettre à niveau, tester et supprimer des applications Service Fabric à l’aide de PowerShell

Cet article vous montre comment utiliser PowerShell pour automatiser les tâches courantes de déploiement, de mise à niveau, de suppression et de test des applications Service Fabric.

## Composants requis

Avant de continuer, veillez à [installer le runtime, le Kit de développement logiciel (SDK) et les outils](service-fabric-get-started.md). Cette opération installe également les modules PowerShell **ServiceFabric** et **ServiceFabricTestability**. Vous devez également [activer l’exécution de script PowerShell](service-fabric-get-started.md#enable-powershell-script-execution) et [installer et démarrer un cluster local](service-fabric-get-started.md#install-and-start-a-local-cluster) pour pouvoir exécuter les exemples mentionnés dans l’article.

Les exemples dans cet article utilisent l’[exemple d’application **WordCount**](http://aka.ms/servicefabricsamples) (dans les exemples de prise en main). Télécharger et générer l’exemple d’application

Avant d’exécuter des commandes PowerShell dans le cadre de cet article, commencez par vous connecter au cluster Service Fabric local à l’aide de [**Connect-ServiceFabricCluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx).

```powershell
Connect-ServiceFabricCluster localhost:19000
```

## Tâche : déployer une application Service Fabric

Une fois l’application créée et le type d’application empaqueté, vous pouvez déployer l’application dans un cluster Service Fabric local. Commencez par empaqueter l’application WordCount dans Visual Studio en cliquant avec le bouton droit sur **WordCount** dans l’Explorateur de solutions et en sélectionnant **Empaqueter**. Pour plus d’informations sur les fichiers manifestes du service et de l’application et sur la disposition du package, consultez [Modéliser une application dans Service Fabric](service-fabric-application-model.md). Le déploiement implique le téléchargement du package d’application, l’enregistrement du type d’application et la création de l’instance de l’application. Utilisez les instructions de cette section pour déployer une application vers un cluster.

### Étape 1 : Téléchargement du package d’application
Le téléchargement du package d’application dans le magasin d’images s’effectue dans un emplacement accessible par les composants internes de Service Fabric. Le package d’application contient les manifestes d’application nécessaires, les manifestes de service et les packages de code/configuration/données pour créer les instances d’application et de service. La commande [**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) charge le package. Par exemple :

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### Étape 2 : Enregistrement du type d’application
L’enregistrement du package d’application a pour effet de rendre disponibles le type et la version de l’application déclarés dans le manifeste d’application. Le système lit le package téléchargé à l'étape précédente, vérifie le package (équivaut à exécuter [**Test-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx) localement), traite le contenu du package et copie le package traité dans un emplacement système interne. Exécutez l’applet de commande [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCount
```
Pour voir les types d’application enregistrés dans le cluster, exécutez l’applet de commande :

```powershell
Get-ServiceFabricApplicationType
```

### Étape 3 : Création de l’instance d’application
Une application peut être instanciée à l'aide de n'importe quelle version de type d'application qui a été correctement enregistrée via la commande [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx). Le nom de chaque application doit commencer par le schéma **fabric:** et être unique pour chaque instance d'application. Le nom et la version du type d’application sont déclarés dans le fichier **ApplicationManifest.xml**. Si des services par défaut ont été définis dans le manifeste d’application du type d’application cible, ils seront également créés lors de cette étape.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

La commande [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) répertorie toutes les instances d’applications qui ont été correctement créées, ainsi que leur état global. La commande [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) répertorie toutes les instances de service qui ont été correctement créées dans une instance d'application donnée. Les services par défaut (le cas échéant) sont répertoriés.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## Tâche : mettre à niveau une application Service Fabric

Vous pouvez mettre à niveau une application Service Fabric précédemment déployée. Cette tâche met à niveau l’application WordCount qui a été déployée dans « Tâche : déployer une application Service Fabric ». Consultez le [didacticiel sur la mise à niveau d'une application Service Fabric](service-fabric-application-upgrade.md) pour plus d’informations.

### Étape 1 : Mise à jour de l’application

Apportez des modifications au code dans le service WordCount.

Une fois le code de service mis à jour, vous devez augmenter le numéro de version du service dans le fichier **ServiceManifest.xml** (situé dans le répertoire **PackageRoot** du projet WordCount). Recherchez l’élément **CodePackage** du fichier manifeste et modifiez la version du service en 2.0.0. Les lignes correspondantes dans le fichier ServiceManifest.xml doivent se présenter comme suit :

```xml
<ServiceManifest Name="WordCountServicePkg" Version="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="WordCountServiceType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="2.0.0">
	  ...
```

Vous devez maintenant mettre à jour le fichier ApplicationManifest.xml (situé sous le projet de l’application WordCount, sous la solution WordCount). Mettez à jour l’élément **ServiceManifestRef** de manière à utiliser la version 2.0.0.0 du projet **WordCountServicePkg**. Remplacez également la valeur 1.0.0.0 de l’élément **ApplicationTypeVersion** par 2.0.0.0. Les lignes correspondantes dans le fichier ApplicationManifest.xml doivent se présenter comme suit :

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="WordCount" ApplicationTypeVersion="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
...
<ServiceManifestRef ServiceManifestName="WordCountServicePkg" ServiceManifestVersion="2.0.0" />
```

Une fois que vous avez apporté ces modifications, enregistrez les fichiers et régénérez le projet WordCount. Empaquetez ensuite l'application mise à jour en cliquant avec le bouton droit sur le projet WordCount et en sélectionnant **Package**. Cette action doit créer un package d'application susceptible d'être déployé. Votre application mise à jour est prête à être déployée dès maintenant.

### Étape 2 : Copie et enregistrement du package d’application mis à jour

L’application est désormais créée, empaquetée, et prête à être mise à niveau. Si vous ouvrez une fenêtre PowerShell en tant qu’administrateur et saisissez [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), la commande doit indiquer que le type d’application 1.0.0 de WordCount est en cours de déploiement. Dans l'exemple de WordCount, le package d'application se trouve dans : *C:\\ServiceFabricSamples\\Services\\WordCount\\WordCount\\pkg\\Debug*.

À présent, copiez le package d’application mis à jour dans le magasin d’images Service Fabric (où les packages d’application sont stockés par Service Fabric). Le paramètre **ApplicationPackagePathInImageStore** indique à Service Fabric où il peut trouver le package d'application. La commande suivante copie le package d’application de **WordCountV2** dans le magasin d’images :

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2
```

L’étape suivante consiste à enregistrer la nouvelle version de l’application auprès de Service Fabric. Cette opération peut être effectuée à l’aide de l’applet de commande [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

Si cette commande n’aboutit pas, vous devrez peut-être régénérer le service, comme indiqué à l’étape 1.

### Étape 3 : Démarrer la mise à niveau
Différents paramètres de mise à niveau, délais d’attente et critères d’intégrité peuvent être appliqués aux mises à niveau d’application. Pour en savoir plus, consultez les documents [Paramètres de mise à niveau d’application](service-fabric-application-upgrade-parameters.md) et [Mise à niveau](service-fabric-application-upgrade.md). Pour cette procédure pas à pas, conservez les valeurs par défaut (et recommandées) des critères d’évaluation de l’intégrité du service. L’intégralité des services et instances doivent avoir l’état _Sain_ après la mise à niveau. Nous allons augmenter toutefois le paramètre **HealthCheckStableDuration** pour spécifier 60 secondes (de sorte que les services soient sains pendant au moins 20 secondes avant que la mise à niveau ne passe au domaine de mise à niveau suivant). Définissez également **UpgradeDomainTimeout** sur 1 200 secondes et **UpgradeTimeout** sur 3 000 secondes. Enfin, définissez **UpgradeFailureAction** sur **Restaurer**, afin de demander à Service Fabric de restaurer l’application à la version précédente en cas de problème pendant la mise à niveau.

Vous pouvez à présent démarrer la mise à niveau de l’application à l’aide de l’applet de commande [**Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Notez que le nom de l’application est le même que celui de l’application v1.0.0 précédemment déployée (fabric:/WordCount). Service Fabric utilise ce nom pour identifier l'application à mettre à niveau. Si vous définissez des délais d’attente trop courts, vous risquez de recevoir un message d’échec stipulant le problème. Reportez-vous à la rubrique [Résoudre les problèmes de mise à niveau d’application](service-fabric-application-upgrade-troubleshooting.md) ou augmentez les délais d’attente.

Vous pouvez surveiller la progression de la mise à niveau de l’application à l’aide des applets de commande [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) :

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

Quelques minutes plus tard, l’applet de commande [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) doit indiquer que tous les domaines de mise à niveau ont été mis à niveau (sont terminés).

## Tâche : tester une application Service Fabric

Pour écrire des services de haute qualité, les développeurs doivent pouvoir introduire des défauts de fiabilité, et ainsi tester la fiabilité des solutions qu’ils conçoivent. Service Fabric offre aux développeurs la possibilité de provoquer des actions erronées afin de tester les services en présence de défaillances à l’aide des scénarios de test chaos et de basculement. Pour en savoir plus, consultez [Vue d’ensemble de la testabilité](service-fabric-testability-overview.md).

### Étape 1 : Exécution du scénario de test chaos
Le scénario de chaos génère des erreurs dans l’ensemble du cluster Service Fabric. Le scénario compresse les erreurs habituellement étalées sur plusieurs mois voire plusieurs années en quelques heures. Combiner des erreurs à un taux d’erreurs élevé permet d’identifier des dysfonctionnements qui n’auraient pu être isolés autrement. L’exemple suivant exécute le scénario de test chaos pendant 60 minutes.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### Étape 2 : Exécution du scénario de test de basculement
Le scénario de test de basculement cible une partition de service spécifique, au lieu de l’ensemble du cluster, et n’affecte pas les autres services. Le scénario effectue une itération dans une séquence d’erreurs simulées et de validation de service, tandis que votre logique métier s’exécute. Un échec de validation de service indique une erreur nécessitant un examen approfondi. Le test de basculement incorpore une erreur à la fois, contrairement au scénario de test chaos, qui peut en introduire plusieurs simultanément. L'exemple suivant exécute le test de basculement pendant 60 minutes sur le service fabric:/WordCount/WordCountService.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Connect-ServiceFabricCluster

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## Tâche : supprimer une application Service Fabric
Vous pouvez supprimer une instance d’une application déployée, supprimer le type d’application configuré du cluster et supprimer le package d’application du magasin d’images.

### Étape 1 : Suppression d’une instance d’application
Quand une instance d’application n’est plus utile, elle peut être définitivement supprimée à l’aide de l’applet de commande [**Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx). Cette action supprime également automatiquement tous les services qui appartiennent à l'application, et supprime définitivement tous les états de service. Cette opération ne peut pas être annulée et l'état de l'application ne peut pas être récupéré.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### Étape 2 : Annulation de l’enregistrement du type d’application
Quand une version donnée d’un type d’application n’est plus utile, son enregistrement doit être annulé à l’aide de l’applet de commande [**Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx). L’annulation de l’enregistrement des types inutilisés libère l’espace de stockage utilisé par le package d’application dans le magasin d’images. L'enregistrement d'un type d'application peut être annulé s'il ne contient aucune instance d'application ou s'il n'est référencé par aucune mise à niveau d'application.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### Étape 3 : Suppression du package d’application
Une fois l’enregistrement du type d’application annulé, le package d’application peut être supprimé du magasin d’images à l’aide de l’applet de commande [**Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx).

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## Étapes suivantes
[Cycle de vie des applications de la structure du service](service-fabric-application-lifecycle.md)

[Déployer une application](service-fabric-deploy-remove-applications.md)

[Mise à niveau de l’application](service-fabric-application-upgrade.md)

[Applets de commande Azure Service Fabric](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Applets de commande de testabilité Azure Service Fabric](https://msdn.microsoft.com/library/azure/mt125844.aspx)

<!---HONumber=AcomDC_0128_2016-->