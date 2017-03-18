---
title: "Déploiement d’applications Service Fabric | Microsoft Docs"
description: "Comment déployer et supprimer des applications dans Service Fabric"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: dbd4dd3cadf162ea18d58639d31589f7b9b8efc3
ms.openlocfilehash: 2dfdcd08501a63d62ec6ba565d1abc7d42c8c680
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-and-remove-applications-using-powershell"></a>Déployer et supprimer des applications avec PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

Après avoir [packagé un type d’application][10], celui-ci peut être déployé sur un cluster Azure Service Fabric. Le déploiement implique les trois étapes suivantes :

1. Charger le package d’application dans le magasin d’images
2. Enregistrer le type d’application
3. Créer l’instance d’application

Une fois qu’une application a été déployée et qu’une instance est exécutée dans le cluster, vous pouvez supprimer l’instance d’application et son type d’application. La suppression complète d’une application du cluster implique les étapes suivantes :

1. Supprimer l’instance d’application en cours d’exécution
2. Désinscrire le type d’application si vous n’en avez plus besoin
3. Télécharger le package d'application à partir du magasin d'images

Si vous utilisez [Visual Studio pour déployer et déboguer des applications](service-fabric-publish-app-remote-cluster.md) dans votre cluster de développement local, toutes les étapes précédentes sont gérées automatiquement à l’aide d’un script PowerShell.  Ce script se trouve dans le dossier *Scripts* du projet d’application. Cet article fournit des précisions sur les actions de ce script afin que vous puissiez effectuer les mêmes opérations en dehors de Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Connexion au cluster
Avant d’exécuter des commandes PowerShell dans le cadre de cet article, commencez toujours par vous connecter au cluster Service Fabric à l’aide de la commande [Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster). Pour vous connecter au cluster de développement local, exécutez la commande suivante :

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Pour obtenir des exemples de connexion à un cluster distant ou à un cluster sécurisé à l’aide d’Azure Active Directory, de certificats X509 ou de Windows Active Directory, consultez [Se connecter à un cluster sécurisé](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Télécharger le package d'application
Quand vous chargez le package d’application, celui-ci est placé dans un dossier accessible aux composants internes de Service Fabric. Si vous souhaitez vérifier le package de l’application en local, utilisez l’applet de commande [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage).  La commande [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) charge le package d’application dans le magasin d’images du cluster. L’applet de commande **Get-ImageStoreConnectionStringFromClusterManifest** , qui fait partie du module PowerShell du SDK de Service Fabric, sert à obtenir la chaîne de connexion au magasin d’images.  Pour importer le module du Kit de développement logiciel (SDK), exécutez :

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Supposons que vous génériez une application nommée *MyApplication* et que vous créiez un package pour cette application dans Visual Studio. Par défaut, le nom du type application répertorié dans le fichier ApplicationManifest.xml est « MyApplicationType ».  Le package d’application, qui contient le manifeste de l’application, les manifestes de service et les packages de code/configuration/données requis, se trouve sous *C:\Users\username\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

La commande suivante répertorie le contenu du package d’application :

```powershell
PS C:\> tree /f 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

L’exemple suivant charge le package dans le magasin d’images, dans un dossier nommé « MyApplicationV1 » :

```powershell
PS C:\> $path = 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

Si vous ne spécifiez pas le paramètre *-ApplicationPackagePathInImageStore*, le package d’application est copié dans le dossier « Debug » du magasin d’images.

Pour plus d’informations sur le magasin d’images et sur ImageStoreConnectionString, consultez la page [Comprendre la chaîne de connexion du magasin d’images](service-fabric-image-store-connection-string.md).

## <a name="register-the-application-package"></a>Enregistrer le package d'application
Le type et la version de l’application déclarés dans le manifeste de l’application deviennent utilisables à l’enregistrement du package de l’application. Le système lit le package chargé à l’étape précédente, vérifie le package, traite le contenu du package et copie le package traité dans un emplacement système interne.  

Exécutez l’applet de commande [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) pour inscrire le type d’application dans le cluster et le rendre disponible pour le déploiement :

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

« MyApplicationV1 » est le dossier du magasin d’images qui contient le package d’application. Le type d’application présentant le nom « MyApplicationType » et la version « 1.0.0 » (ces deux valeurs se trouvent dans le manifeste de l’application) est à présent inscrit dans le cluster.

La commande [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) ne renvoie un résultat que lorsque le package d’application a été correctement inscrit par le système. La durée de l’enregistrement dépend de la taille et du contenu du package de l’application. Si nécessaire, le paramètre **-TimeoutSec** peut être utilisé pour fournir un délai d’expiration plus long (le délai d’expiration par défaut est de 60 secondes).  S’il s’agit d’un package d’application volumineux et que vous rencontrez des problèmes d’expiration, utilisez le paramètre **- Async**.

La commande [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) répertorie toutes les versions de types d’applications correctement inscrites et l’état de leur inscription.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="create-the-application"></a>Création de l'application
Vous pouvez instancier une application à partir de n’importe quelle version de type d’application correctement inscrite à l’aide de l’applet de commande [New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication). Le nom de chaque application doit commencer par le schéma *fabric:* et être unique pour chaque instance d'application. Les éventuels services par défaut définis dans le manifeste de l’application du type de l’application cible sont également créés.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Plusieurs instances d'application peuvent être créées pour une version donnée d'un type d'application enregistré. Chaque instance de l’application s’exécute en isolement, avec ses propres répertoire de travail et processus.

Pour connaître les applications et services nommés en cours d’exécution dans le cluster, exécutez les applets de commande [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) et [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice) :

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Supprimer une application
Lorsque vous n’avez plus besoin d’une instance d’application, vous pouvez la supprimer définitivement à l’aide de son nom en utilisant l’applet de commande [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication). [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) supprime également automatiquement tous les services qui appartiennent à l’application, supprimant ainsi définitivement tous les états de service. Cette opération ne peut pas être annulée et l’état de l’application ne peut pas être récupéré.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Désinscrire un type d’application
Lorsque vous n’avez plus besoin d’un type d’application, il est recommandé de le désinscrire à l’aide de l’applet de commande [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype). La désinscription des types d’application inutilisés libère l’espace de stockage utilisé par le magasin d’images. Vous pouvez désinscrire un type d’application s’il ne contient aucune instance d’application et s’il n’est référencé par aucune mise à niveau d’application en attente.

Pour afficher les types d’applications actuellement inscrits dans le cluster, exécutez [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) :

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Pour désinscrire un type d’application spécifique, exécutez [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) :

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```
## <a name="remove-an-application-package-from-the-image-store"></a>Supprimer un package d’application du magasin d’images
Lorsque vous n’avez plus besoin d’un package d’application, vous pouvez le supprimer du magasin d’images pour libérer des ressources système.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

## <a name="troubleshooting"></a>Résolution des problèmes
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage demande un ImageStoreConnectionString
L'environnement du SDK Service Fabric doit déjà être configuré avec les valeurs par défaut correctes. Toutefois, si besoin, l’ImageStoreConnectionString de toutes les commandes doit correspondre à celui utilisé par le cluster Service Fabric. ImageStoreConnectionString se trouve dans le manifeste de cluster récupéré à l’aide de la commande [Get-ServiceFabricClusterManifest](/powershell/servicefabric/vlatest/get-servicefabricclustermanifest) :

```powershell
PS C:\> Get-ServiceFabricClusterManifest
```

ImageStoreConnectionString se trouve dans le manifeste de cluster :

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

## <a name="next-steps"></a>Étapes suivantes
[Mise à niveau des applications Service Fabric](service-fabric-application-upgrade.md)

[Présentation de l’intégrité de Service Fabric](service-fabric-health-introduction.md)

[Diagnostiquer et résoudre les problèmes d'un service Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modéliser une application dans Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

