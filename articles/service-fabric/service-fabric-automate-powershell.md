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
	ms.date="03/02/2016"
	ms.author="ryanwi"/>

# Automatisation du cycle de vie d’une application à l’aide de PowerShell

Plusieurs aspects du [cycle de vie des applications Service Fabric](service-fabric-application-lifecycle.md) peuvent être automatisés. Cet article vous montre comment utiliser PowerShell pour automatiser les tâches courantes de déploiement, de mise à niveau, de suppression et de test des applications Service Fabric.

## Composants requis
Avant de passer aux tâches décrites dans l’article, veillez à :

+ Vous familiariser avec les concepts de Service Fabric décrits [ue d’ensemble technique de Service Fabric](service-fabric-technical-overview.md).
+ [Installer les outils, le Kit de développement logiciel (SDK) et le runtime](service-fabric-get-started.md), ce qui a également pour effet d’installer le module PowerShell **ServiceFabric**.
+ [Activer l’exécution du script PowerShell](service-fabric-get-started.md#enable-powershell-script-execution).
+ Démarrer un cluster local. Ouvrir une nouvelle fenêtre PowerShell en tant qu’administrateur, puis exécuter le script de configuration de cluster à partir du dossier du Kit de développement logiciel (SDK) : `& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
+ Avant d’exécuter des commandes PowerShell décrites dans cet article, commencez par vous connecter au cluster Service Fabric local à l’aide de [**Connect-ServiceFabricCluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx) : `Connect-ServiceFabricCluster localhost:19000`
+ Les tâches suivantes nécessitent le déploiement des packages d’application v1 et v2 pour la mise à niveau. Téléchargez l’[exemple d’application **WordCount**](http://aka.ms/servicefabricsamples) (figurant parmi les exemples de mise en route). Générez et empaquetez l’application dans Visual Studio (cliquer avec le bouton droit sur **WordCount** dans l’Explorateur de solutions, puis sélectionner **Package**). Copiez le package v1 figurant dans `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` vers `C:\Temp\WordCount`. Copiez `C:\Temp\WordCount` vers `C:\Temp\WordCountV2`, en créant le package d’application v2 pour la mise à niveau. Ouvrez `C:\Temp\WordCountV2\ApplicationManifest.xml` dans un éditeur de texte. Dans l’élément **ApplicationManifest**, modifiez l’attribut **ApplicationTypeVersion** de « 1.0.0 » en « 2.0.0 ». Cette opération permet de mettre à jour le numéro de version de l’application. Enregistrez le fichier ApplicationManifest.xml modifié.

## Tâche : déployer une application Service Fabric

Une fois l’application créée et empaquetée (ou le package d’application téléchargé), vous pouvez la déployer dans un cluster Service Fabric local. Le déploiement implique le téléchargement du package d’application, l’enregistrement du type d’application et la création de l’instance de l’application. Utilisez les instructions de cette section pour déployer une application vers un cluster.

### Étape 1 : Téléchargement du package d’application
Le téléchargement du package d’application dans le magasin d’images s’effectue dans un emplacement accessible par les composants internes de Service Fabric. Le package d’application contient le manifeste d’application, les manifestes de service et les packages de code, configuration et données nécessaires pour créer les instances d’application et de service. La commande [**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) charge le package. Par exemple :

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### Étape 2 : Enregistrement du type d’application
L’enregistrement du package d’application a pour effet de rendre disponibles le type et la version de l’application déclarés dans le manifeste d’application. Le système lit le package téléchargé à l’étape 1, le vérifie (ce qui équivaut à exécuter Te[**st-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx) localement), traite son contenu, puis copie le package vers un emplacement système interne. Exécutez l’applet de commande [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCount
```
Pour afficher tous les types d’applications inscrits dans le cluster, exécutez l’applet de commande [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125871.aspx) :

```powershell
Get-ServiceFabricApplicationType
```

### Étape 3 : Création de l’instance d’application
Une application peut être instanciée à l'aide de n'importe quelle version de type d'application qui a été correctement enregistrée via la commande [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx). Le nom de chaque application déclaré lors du déploiement doit commencer par le schéma **fabric:** et être unique pour chaque instance d’application. Le nom et la version du type d’application sont déclarés dans le fichier **ApplicationManifest.xml** du package d’application. Si des services par défaut ont été définis dans le manifeste d’application du type d’application cible, ils seront également créés lors de cette étape.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

La commande [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) répertorie toutes les instances d’applications qui ont été correctement créées, ainsi que leur état global. La commande [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) répertorie toutes les instances de service qui ont été correctement créées dans une instance d'application donnée. Les services par défaut (le cas échéant) sont répertoriés.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## Tâche : mettre à niveau une application Service Fabric
Vous pouvez mettre à niveau une application Service Fabric précédemment déployée avec un package d’application mis à jour. Cette tâche met à niveau l’application WordCount qui a été déployée dans « Tâche : déployer une application Service Fabric ». Consultez le [didacticiel sur la mise à niveau d'une application Service Fabric](service-fabric-application-upgrade.md) pour plus d’informations.

Afin de simplifier cet exemple, seul le numéro de version de l’application a été mis à jour dans le package d’application WordCountV2 créé dans le cadre de la préparation des conditions préalables. Un scénario plus réaliste impliquerait une mise à jour de vos fichiers de code, de configuration ou de données, puis la reconstruction et l’empaquetage de l’application avec les numéros de version mis à jour.

### Étape 1 : chargement du package d’application mis à jour
L’application WordCount v1 est prête pour la mise à niveau. Si vous ouvrez une fenêtre PowerShell en tant qu’administrateur, puis tapez [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), la version 1.0.0 du type d’application WordCount est déployée.

À présent, copiez le package d’application mis à jour dans le magasin d’images Service Fabric (où les packages d’application sont stockés par Service Fabric). Le paramètre **ApplicationPackagePathInImageStore** indique à Service Fabric où il peut trouver le package d'application. La commande suivante copie le package d’application de **WordCountV2** dans le magasin d’images :

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### Étape 2 : enregistrement du type d’application mis à jour
L’étape suivante consiste à enregistrer la nouvelle version de l’application auprès de Service Fabric. Cette opération peut être effectuée à l’aide de l’applet de commande [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### Étape 3 : Démarrer la mise à niveau
Différents paramètres de mise à niveau, délais d’attente et critères d’intégrité peuvent être appliqués aux mises à niveau d’application. Pour en savoir plus, consultez les documents [Paramètres de mise à niveau d’application](service-fabric-application-upgrade-parameters.md) et [Mise à niveau](service-fabric-application-upgrade.md). L’intégralité des services et instances doivent avoir l’état _Sain_ après la mise à niveau. Définissez la valeur **HealthCheckStableDuration** sur 60 secondes (afin que les services soient intègres pendant au moins 20 secondes avant que la mise à niveau passe au domaine de mise à niveau suivant). Définissez également **UpgradeDomainTimeout** sur 1 200 secondes et **UpgradeTimeout** sur 3 000 secondes. Enfin, définissez **UpgradeFailureAction** sur **Restaurer**, afin de demander à Service Fabric de restaurer l’application à la version précédente en cas de problème pendant la mise à niveau.

Vous pouvez à présent démarrer la mise à niveau de l’application à l’aide de l’applet de commande [**Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Notez que le nom de l’application est le même que celui de l’application v1.0.0 précédemment déployée (fabric:/WordCount). Service Fabric utilise ce nom pour identifier l'application à mettre à niveau. Si vous définissez des délais d’attente trop courts, vous risquez de recevoir un message de dépassement de délai signalant le problème. Reportez-vous à la rubrique [Résoudre les problèmes de mise à niveau d’application](service-fabric-application-upgrade-troubleshooting.md) ou augmentez les délais d’attente.

### Étape 4 : vérification de la progression de la mise à niveau
Vous pouvez surveiller la progression de la mise à niveau de l’application à l’aide de [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou de l’applet de commande [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) :

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

Après quelques minutes, l’applet de commande [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) doit indiquer que tous les domaines de mise à niveau ont été mis à niveau (terminés).

## Tâche : tester une application Service Fabric

Pour écrire des services de haute qualité, les développeurs doivent pouvoir introduire des défauts de fiabilité, et ainsi tester la fiabilité des solutions qu’ils conçoivent. Service Fabric offre aux développeurs la possibilité de provoquer des actions erronées afin de tester les services en présence de défaillances à l’aide des scénarios de test chaos et de basculement. Pour en savoir plus, consultez [Vue d’ensemble de la testabilité](service-fabric-testability-overview.md).

### Étape 1 : Exécution du scénario de test chaos
Le scénario de chaos génère des erreurs dans l’ensemble du cluster Service Fabric. Le scénario compresse les erreurs habituellement étalées sur plusieurs mois voire plusieurs années en quelques heures. Combiner des erreurs à un taux d’erreurs élevé permet d’identifier des dysfonctionnements qui n’auraient pu être isolés autrement. L’exemple suivant exécute le scénario de test chaos pendant 60 minutes.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### Étape 2 : Exécution du scénario de test de basculement
Le scénario de test de basculement cible une partition de service spécifique, au lieu de l’ensemble du cluster, et n’affecte pas les autres services. Le scénario effectue une itération dans une séquence d’erreurs simulées et de validation de service, tandis que votre logique métier s’exécute. Un échec de validation de service indique une erreur nécessitant un examen approfondi. Le test de basculement incorpore une erreur à la fois, contrairement au scénario de test chaos, qui peut en introduire plusieurs simultanément. L'exemple suivant exécute le test de basculement pendant 60 minutes sur le service fabric:/WordCount/WordCountService.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## Tâche : supprimer une application Service Fabric
Vous pouvez supprimer une instance d’une application déployée, supprimer le type d’application configuré du cluster et supprimer le package d’application du magasin d’images.

### Étape 1 : Suppression d’une instance d’application
Quand une instance d’application n’est plus utile, elle peut être définitivement supprimée à l’aide de l’applet de commande [**Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx). Cette action supprime également automatiquement tous les services qui appartiennent à l'application, et supprime définitivement tous les états de service. Cette opération ne peut pas être annulée et l'état de l'application ne peut pas être récupéré.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### Étape 2 : Annulation de l’enregistrement du type d’application
Quand une version donnée d’un type d’application n’est plus utile, son enregistrement doit être annulé à l’aide de l’applet de commande [**Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx). L’annulation de l’enregistrement des types inutilisés libère l’espace de stockage utilisé par le package d’application dans le magasin d’images. L'enregistrement d'un type d'application peut être annulé s'il ne contient aucune instance d'application ou s'il n'est référencé par aucune mise à niveau d'application.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### Étape 3 : Suppression du package d’application
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

<!------HONumber=AcomDC_0309_2016-->