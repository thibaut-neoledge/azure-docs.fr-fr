<properties
   pageTitle="Déploiement d'applications Service Fabric"
   description="Comment déployer et supprimer des applications dans Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="alexwun"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/23/2015"
   ms.author="alexwun"/>

# Déployer une application

Une fois qu'un [type d'application a été packagé][10], il est prêt pour le déploiement dans un cluster Service Fabric. Le déploiement implique les trois étapes suivantes :

1. Téléchargement du package d'application
2. Enregistrement du type d'application
3. Création de l'instance d'application

>[AZURE.NOTE]Si vous utilisez Visual Studio pour déployer et déboguer des applications dans votre cluster de développement local, toutes les étapes décrites ci-dessous sont gérées automatiquement en appelant les scripts PowerShell contenus dans le dossier Scripts du projet d'application. Cet article fournit le contexte des actions des scripts afin que vous puissiez effectuer les mêmes opérations en dehors de Visual Studio.

## Télécharger le package d'application

Le téléchargement du package d'application s'effectue dans un emplacement accessible par les composants internes de Service Fabric et peut être effectué via PowerShell. Avant d'exécuter des commandes PowerShell dans le cadre de cet article, commencez toujours par vous connecter au cluster Service Fabric à l'aide de **Connect-ServiceFabricCluster**.

Par exemple, prenons un dossier nommé *MyApplicationType* contenant le manifeste d'application, le ou les manifestes de service et les packages de code/configuration/données requis, la commande **Copy-ServiceFabricApplicationPackage** télécharge le package. Par exemple :

~~~
PS D:\temp> dir

    Directory: D:\temp

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----         3/19/2015   8:11 PM            MyApplicationType

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Copy-ServiceFabricApplicationPackage MyApplicationType
Copy application package succeeded

PS D:\temp>
~~~

## Enregistrer le package d'application

L'enregistrement du package d'application déclare le type et la version de l'application dans le manifeste d'application à utiliser. Le système lit le package téléchargé à l'étape précédente, vérifie le package (équivaut à exécuter **Test-ServiceFabricApplicationPackage** localement), traite le contenu du package et copie le package traité dans un emplacement système interne.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

La commande **Register-ServiceFabricApplicationType** retourne un résultat une fois seulement que le package d'application a été correctement copié par le système. La durée de l'opération dépend du contenu du package d'application. Le paramètre **-TimeoutSec** peut être utilisé pour fournir un délai d'attente plus long si nécessaire (le délai d'attente par défaut est de 60 secondes).

La commande **Get-ServiceFabricApplicationType** répertorie toutes les versions de type d'application correctement enregistrées.

## Création de l'application

Une application peut être instanciée à l'aide de n'importe quelle version de type d'application qui a été correctement enregistrée via la commande **New-ServiceFabricApplication**. Le nom de chaque application doit commencer par le schéma *fabric:* et être unique pour chaque instance d'application. S'il existe des services par défaut définis dans le manifeste d'application du type d'application cible, ils sont également créés à cette étape.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

La commande **Get-ServiceFabricApplication** répertorie toutes les instances d'application qui ont été correctement créées ainsi que leur état global.

La commande **Get-ServiceFabricService** répertorie toutes les instances de service qui ont été correctement créées dans une instance d'application donnée. Les services par défaut (le cas échéant) sont répertoriés ici.

Plusieurs instances d'application peuvent être créées pour une version donnée d'un type d'application enregistré. Chaque instance de l'application s'exécute en isolement, avec ses propres répertoire de travail et processus.

## Supprimer une application

Quand une instance d'application n'est plus utile, elle peut être définitivement supprimée à l'aide de la commande **Remove-ServiceFabricApplication**. Cette action supprime également automatiquement tous les services qui appartiennent à l'application, et supprime définitivement tous les états de service. Cette opération ne peut pas être annulée et l'état de l'application ne peut pas être récupéré.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Quand une version donnée d'un type d'application n'est plus utile, son enregistrement doit être annulé à l'aide de la commande **Unregister-ServiceFabricApplicationType**. L'annulation de l'enregistrement des types inutilisés libère l'espace de stockage utilisé par le contenu du package d'application de ce type dans le magasin d'images. L'enregistrement d'un type d'application peut être annulé s'il ne contient aucune instance d'application ou s'il n'est référencé par aucune mise à niveau d'application.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

<!--
## Next steps

TODO [Upgrade applications][11]
-->

## Résolution de problèmes

### Copy-ServiceFabricApplicationPackage demande un ImageStoreConnectionString

L'environnement du SDK Service Fabric doit déjà être configuré avec les valeurs par défaut correctes. Toutefois, si nécessaire, l'ImageStoreConnectionString pour toutes les commandes doit correspondre à la valeur utilisée par le cluster Service Fabric, qui se trouve dans le manifeste de cluster récupéré à l'aide de la commande **Get-ServiceFabricClusterManifest** :

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## Étapes suivantes

[Mise à niveau des applications Service Fabric](service-fabric-application-upgrade.md)

[Présentation de l'intégrité de Service Fabric](service-fabric-health-introduction.md)

[Diagnostiquer et résoudre les problèmes d'un service Service Fabric](service-fabric-diagnose-monitor-your-service-index.md)

[Modéliser une application dans Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
 

<!---HONumber=Oct15_HO3-->