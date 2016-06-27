<properties
   pageTitle="Déploiement d’applications Service Fabric | Microsoft Azure"
   description="Comment déployer et supprimer des applications dans Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/14/2016"
   ms.author="ryanwi"/>

# Déployer une application

Une fois qu’un [type d’application a été packagé][10], il est prêt à être déployé dans un cluster Azure Service Fabric. Le déploiement implique les trois étapes suivantes :

1. Téléchargement du package d'application
2. Enregistrement du type d'application
3. Création de l'instance d'application

>[AZURE.NOTE] Si vous utilisez Visual Studio pour déployer et déboguer des applications dans votre cluster de développement local, toutes les étapes décrites ci-dessous sont gérées automatiquement à l’aide d’un script PowerShell contenus dans le dossier Scripts du projet d’application. Cet article fournit le contexte des actions des scripts afin que vous puissiez effectuer les mêmes opérations en dehors de Visual Studio.

## Télécharger le package d'application

Quand vous chargez le package d’application, celui-ci est placé dans un dossier accessible aux composants internes de Service Fabric. Vous pouvez utiliser PowerShell pour effectuer le chargement. Avant d’exécuter des commandes PowerShell dans le cadre de cet article, commencez toujours par vous connecter au cluster Service Fabric à l’aide de la commande **Connect-ServiceFabricCluster**.

Supposons que vous ayez un dossier nommé *MonTypeApplication* qui contienne le manifeste de l’application, les manifestes de service et les packages de code/configuration/données. La commande **Copy-ServiceFabricApplicationPackage** charge le package dans le magasin d’image du cluster. L’applet de commande **Get-ImageStoreConnectionStringFromClusterManifest**, qui fait partie du module PowerShell du SDK de Service Fabric sert à obtenir la chaîne de connexion au magasin d’image. Pour importer le module du kit de développement logiciel, exécutez *Import-Module «$ENV:ProgramFiles\\Microsoft SDKs\\Service Fabric\\Tools\\PSModule\\ServiceFabricSDK\\ServiceFabricSDK.psm1»*.

L’exemple suivant charge le package :

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

PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## Enregistrer le package d'application

L’enregistrement du package d’application a pour effet de rendre disponibles le type et la version de l’application déclarés dans le manifeste d’application. Le système lit le package téléchargé à l'étape précédente, vérifie le package (équivaut à exécuter **Test-ServiceFabricApplicationPackage** localement), traite le contenu du package et copie le package traité dans un emplacement système interne.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

La commande **Register-ServiceFabricApplicationType** ne retourne un résultat que lorsque le package d’application a été correctement copié par le système. La durée de l'opération dépend du contenu du package d'application. Le paramètre **-TimeoutSec** peut être utilisé pour fournir un délai d’attente plus long si nécessaire (le délai d’attente par défaut est de 60 secondes).

La commande **Get-ServiceFabricApplicationType** répertorie toutes les versions de types d’applications correctement inscrites.

## Création de l'application

Vous pouvez instancier une application à l’aide de n’importe quelle version de type d’application correctement inscrite via la commande **New-ServiceFabricApplication**. Le nom de chaque application doit commencer par le schéma *fabric:* et être unique pour chaque instance d'application. Si des services par défaut ont été définis dans le manifeste d’application du type d’application cible, ils seront également créés lors de cette étape.

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

La commande **Get-ServiceFabricApplication** répertorie toutes les instances d’applications qui ont été correctement créées, ainsi que leur état global.

La commande **Get-ServiceFabricService** répertorie toutes les instances de service qui ont été correctement créées dans une instance d'application donnée. Les services par défaut (le cas échéant) sont répertoriés ici.

Plusieurs instances d'application peuvent être créées pour une version donnée d'un type d'application enregistré. Chaque instance de l'application s'exécute en isolement, avec ses propres répertoire de travail et processus.

## Supprimer une application

Quand une instance d’application n’est plus utile, elle peut être définitivement supprimée à l’aide de la commande **Remove-ServiceFabricApplication**. Cette commande supprime automatiquement tous les services qui appartiennent à l’application, et supprime définitivement tous les états de service. Cette opération ne peut pas être annulée et l’état de l’application ne peut pas être récupéré.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Quand une version donnée d’un type d’application n’est plus utile, vous devez la désinscrire à l’aide de la commande **Unregister-ServiceFabricApplicationType**. La désinscription des types inutilisés libère l’espace de stockage utilisé par le contenu du package d’application de ce type dans le magasin d’images. Vous pouvez désinscrire un type d’application s’il ne contient aucune instance d’application et s’il n’est référencé par aucune mise à niveau d’application en attente.

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

## Résolution de problèmes

### Copy-ServiceFabricApplicationPackage demande un ImageStoreConnectionString

L'environnement du SDK Service Fabric doit déjà être configuré avec les valeurs par défaut correctes. Toutefois, si besoin, l’ImageStoreConnectionString de toutes les commandes doit correspondre à celui utilisé par le cluster Service Fabric. Il se trouve dans le manifeste de cluster récupéré à l’aide de la commande **Get-ServiceFabricClusterManifest** :

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

[Présentation de l’intégrité de Service Fabric](service-fabric-health-introduction.md)

[Diagnostiquer et résoudre les problèmes d'un service Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modéliser une application dans Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

<!---HONumber=AcomDC_0615_2016-->