---
title: "Empaqueter une application Azure Service Fabric | Microsoft Docs"
description: "Découvrez comment empaqueter une application Service Fabric avant son déploiement dans un cluster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/24/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: bc87185c56b2dc45f041136474b9fb1bf6afebc3
ms.lasthandoff: 04/13/2017


---
# <a name="package-an-application"></a>Empaqueter une application
Cet article explique comment empaqueter une application Service Fabric et la préparer pour le déploiement.

## <a name="package-layout"></a>Disposition du package
Le manifeste d'application, le(s) manifeste(s) de service et les autres fichiers de package nécessaires doivent être organisés selon une disposition spécifique en vue du déploiement dans un cluster Service Fabric. Les exemples de manifestes dans cet article doivent être organisés selon la structure de répertoires suivante :

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

Les dossiers sont nommés d'après les attributs **Name** de chaque élément correspondant. Par exemple, si le manifeste de service contient deux packages de code nommés **MyCodeA** et **MyCodeB**, deux dossiers de même nom contiennent les fichiers binaires nécessaires pour chaque package de code.

## <a name="use-setupentrypoint"></a>Utilisation de SetupEntrypoint
**SetupEntryPoint** est généralement utilisé lorsque vous avez besoin d’exécuter un fichier exécutable avant le démarrage du service ou si vous devez effectuer une opération avec des privilèges élevés. Par exemple :

* la configuration et l’initialisation de variables d'environnement dont le fichier exécutable du service a besoin, sans limitation aux seuls exécutables écrits via les modèles de programmation de Service Fabric. Par exemple, npm.exe a besoin de certaines variables d’environnement configurées pour le déploiement d’une application node.js.
* La configuration d’un contrôle d’accès via l’installation de certificats de sécurité.

Pour plus d’informations sur la façon de configurer **SetupEntryPoint**, consultez [Configurer la stratégie pour un point d’entrée de configuration de service](service-fabric-application-runas-security.md)  

## <a name="configure"></a>Configuration 
### <a name="build-a-package-by-using-visual-studio"></a>Création d'un package à l'aide de Visual Studio
Si vous utilisez Visual Studio 2015 pour créer votre application, vous pouvez utiliser la commande Package pour créer automatiquement un package qui correspond à la disposition décrite ci-dessus.

Pour créer un package, cliquez avec le bouton droit sur l'application dans l'Explorateur de solutions et choisissez la commande Package, comme indiqué ci-dessous :

![Empaquetage d'une application avec Visual Studio][vs-package-command]

Quand la création du package est terminée, l'emplacement du package est indiqué dans la fenêtre **Sortie**. L’étape de création du package se produit automatiquement quand vous déployez ou déboguez votre application dans Visual Studio.

### <a name="build-a-package-by-command-line"></a>Développer un package par ligne de commande
Vous pouvez également empaqueter votre application par programme en utilisant `msbuild.exe`. Sous le capot : voici ce que Visual Studio exécute pour obtenir le même résultat.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Test du package
Vous pouvez vérifier la structure du package localement via PowerShell à l’aide de la commande [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) .
Cette commande vérifie la présence de problèmes liés à l’analyse du manifeste, ainsi que toutes les références. Cette commande vérifie uniquement l’exactitude structurelle des répertoires et des fichiers du package.
Elle ne vérifie pas le code ou les données du contenu du package, mais uniquement si tous les fichiers nécessaires sont présents.

```
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Cette erreur indique que le fichier *MySetup.bat* référencé dans le manifeste de service **SetupEntryPoint** est manquant dans le package de code. Une fois le fichier manquant ajouté, la phase de vérification est terminée :

```
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

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
```

Si des [paramètres d’application](service-fabric-manage-multiple-environment-app-configuration.md) sont définis pour votre application, vous pouvez les transmettre dans [Test-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) pour assurer une validation correcte.

Si vous savez dans quel cluster l’application sera déployée, il est recommandé de transmettre la chaîne de connexion du magasin d’images. Dans ce cas, le package est également validé par rapport aux versions précédentes de l’application qui sont déjà exécutées dans le cluster. Par exemple, la validation permet de détecter si un package présentant la même version mais un contenu différent a déjà été déployé.  

Une fois que l’application a été correctement empaquetée et a passé la validation avec succès, déterminez si une compression est nécessaire en fonction de la taille et du nombre de fichiers. 

## <a name="compress-a-package"></a>Compresser un package
Lorsqu’un package est volumineux ou contient de nombreux fichiers, vous pouvez le compresser afin d’accélérer le déploiement. La compression réduit le nombre de fichiers et la taille du package.
Le [chargement du package d’application](service-fabric-deploy-remove-applications.md#upload-the-application-package) peut prendre plus de temps que pour le package non compressé, mais l’[inscription](service-fabric-deploy-remove-applications.md#register-the-application-package) et la [désinscription du type d’application](service-fabric-deploy-remove-applications.md#unregister-an-application-type) sont plus rapides.

Le mécanisme de déploiement est le même pour les packages compressés et non compressés. Si le package est compressé, il est stocké tel quel dans le magasin d’images du cluster et il est décompressé sur le nœud avant l’exécution de l’application.
La compression remplace le package Service Fabric valide par la version compressée. Le dossier doit autoriser l’accès en écriture. L’exécution d’une compression sur un package déjà compressé ne produit aucun effet. 

Vous pouvez compresser un package en exécutant la commande Powershell [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) avec le commutateur `CompressPackage`. Vous pouvez décompresser le package avec la même commande, en utilisant le commutateur `UncompressPackage`.

La commande suivante permet de compresser le package sans le copier dans le magasin d’images. Vous pouvez copier un package compressé dans un ou plusieurs clusters Service Fabric, selon vos besoins, en utilisant la commande [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) sans l’indicateur `SkipCopy`. Le package inclut désormais les fichiers compressés pour les packages `code`, `config` et `data`. Le manifeste de l’application et les manifestes de service ne sont pas compressés, car ils sont requis pour de nombreuses opérations internes (comme le partage de package ou l’extraction du nom du type de l’application et de la version pour certaines validations).
La compression des manifestes rendrait ces opérations inefficaces.

```
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
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Vous pouvez également compresser et copier le package en une seule fois avec la commande [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage).
Si le package est volumineux, prévoyez un délai d’expiration suffisamment long pour que la compression du package et le chargement dans le cluster aient le temps de se terminer.
```
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

En interne, Service Fabric calcule les sommes de contrôle des packages d’application à des fins de validation. Lorsque la compression est utilisée, les sommes de contrôle sont calculées dans les versions compressées de chaque package.
Si vous avez copié une version non compressée de votre package d’application et que vous souhaitez utiliser la compression pour le même package, vous devez modifier les versions des packages `code`, `config` et `data` pour éviter la non-concordance des sommes de contrôle. Si les packages sont identiques, au lieu de changer de version, vous pouvez utiliser [l’approvisionnement différé](service-fabric-application-upgrade-advanced.md). Vous n’avez pas à inclure le package inchangé avec cette option, il suffit de le référencer dans le manifeste de service.

De même, si vous avez chargé une version compressée du package et que vous souhaitez utiliser un package non compressé, vous devez mettre à jour les versions pour éviter la non-concordance des sommes de contrôle.

Le package est maintenant empaqueté correctement, validé et compressé (si nécessaire). Il est donc prêt pour le [déploiement](service-fabric-deploy-remove-applications.md) dans un ou plusieurs clusters Service Fabric.

## <a name="next-steps"></a>Étapes suivantes
[Déployer et supprimer des applications][10] décrit comment utiliser PowerShell pour gérer des instances d’application

[Gestion des paramètres d’application pour plusieurs environnements][11] décrit comment configurer les paramètres et les variables d’environnement pour différentes instances d’application.

[Configurer les stratégies de sécurité de votre application][12] décrit comment exécuter des services dans le cadre de stratégies de sécurité pour restreindre l’accès.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md

