---
title: "Version préliminaire de Docker Compose dans Azure Service Fabric"
description: "Azure Service Fabric accepte le format Docker Compose pour vous permettre d’orchestrer plus facilement des conteneurs existants à l’aide de Service Fabric. Cette prise en charge est actuellement en mode préliminaire."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 519bab9d226f9d00ae0fa21348823d2d6b6cd2c9
ms.contentlocale: fr-fr
ms.lasthandoff: 09/26/2017

---
# <a name="docker-compose-application-support-in-azure-service-fabric-preview"></a>Prise en charge de l’application Docker Compose dans Azure Service Fabric (préversion)

Docker utilise le fichier [docker-compose.yml](https://docs.docker.com/compose) pour la définition d’applications à conteneurs multiples. Pour permettre aux clients familiarisés avec Docker d’orchestrer facilement des applications de conteneur existantes sur Azure Service Fabric, nous avons inclus la prise en charge en préversion de Docker Compose en mode natif dans la plateforme. Service Fabric peut accepter des fichiers `docker-compose.yml` version 3 ou ultérieure. 

Étant donné que cette prise en charge est disponible en préversion, seul un sous-ensemble des directives de Compose est reconnu. Par exemple, les mises à niveau d’application ne sont pas prises en charge. Toutefois, vous pouvez toujours supprimer et déployer des applications au lieu de les mettre à niveau.

Pour utiliser cette préversion, créez votre cluster en utilisant la version 5.7 ou plus du runtime Service Fabric via le portail Azure, ainsi que le Kit de développement logiciel (SDK) correspondant. 

> [!NOTE]
> Cette fonctionnalité est disponible en préversion et n’est pas prise en charge dans les environnements de production.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Déployer un fichier Docker Compose sur Service Fabric

Les commandes ci-après créent une application Service Fabric (nommée `TestContainerApp`), que vous pouvez surveiller et gérer comme n’importe quelle autre application Service Fabric. Vous pouvez utiliser le nom de l’application spécifiée pour des requêtes d’intégrité.

### <a name="use-powershell"></a>Utiliser PowerShell

Créez un déploiement Compose Service Fabric à partir d’un fichier docker-compose.yml en exécutant la commande suivante dans PowerShell :

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` et `RegistryPassword` font référence au nom d’utilisateur et au mot de passe du registre de conteneurs. Une fois que vous avez créé le déploiement, vous pouvez en vérifier l’état à l’aide de la commande suivante :

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp -GetAllPages
```

Pour supprimer le déploiement Compose, utilisez la commande suivante dans PowerShell :

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Utiliser l’interface CLI Azure Service Fabric (sfctl)

Vous pouvez également utiliser la commande CLI Service Fabric ci-après :

```azurecli
sfctl compose create --application-id TestContainerApp --compose-file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```

Après avoir créé l’application, vous pouvez en vérifier l’état à l’aide de la commande suivante :

```azurecli
sfctl compose status --application-id TestContainerApp [ --timeout ]
```

Pour supprimer l’application Compose, utilisez la commande ci-après :

```azurecli
sfctl compose remove  --application-id TestContainerApp [ --timeout ]
```

## <a name="supported-compose-directives"></a>Directives Compose prises en charge

Cette préversion prend en charge un sous-ensemble des options de configuration du format Compose version 3, incluant les primitives suivantes :

* Services > Déploiement > Réplicas
* Services > Déploiement > Placement > Contraintes
* Services > Déploiement > Ressources > Limites
    * -cpu-shares
    * -memory
    * -memory-swap
* Services > Commandes
* Services > Environnement
* Services > Ports
* Services > Image
* Services > Isolation (uniquement pour Windows)
* Services > Journalisation > Pilote
* Services > Journalisation > Pilote > Options
* Volume & déploiement > Volume

Configurez le cluster pour qu’il applique des limites de ressources, comme décrit dans l’article [Gouvernance des ressources Azure Service Fabric](service-fabric-resource-governance.md). Aucune des autres directives Docker Compose n’est prise en charge pour cette préversion.

## <a name="servicednsname-computation"></a>Calcul de ServiceDnsName

Si le nom de service que vous spécifiez dans un fichier Compose est un nom de domaine complet (autrement dit, s’il contient un point [.]), le nom DNS inscrit par Service Fabric est `<ServiceName>` (y compris le point). Dans le cas contraire, chaque segment de chemin d’accès dans le nom de l’application devient une étiquette de domaine dans le nom DNS du service, le premier segment de chemin devenant l’étiquette du domaine de premier niveau.

Par exemple, si le nom de l’application spécifié est `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` est le nom DNS inscrit.

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Différences entre le Compose (définition d’instance) et le modèle d’application Service Fabric (définition de type)

Un fichier docker-compose.yml décrit un ensemble déployable de conteneurs, y compris leurs propriétés et leurs configurations.
Par exemple, le fichier peut contenir des variables d’environnement et des ports. Dans le fichier docker-compose.yml, vous pouvez également spécifier des paramètres de déploiement, comme les contraintes de positionnement, les limites de ressources et les noms DNS.

Le [modèle d’application Service Fabric](service-fabric-application-model.md) utilise des types de service et des types d’application. Il peut y avoir de nombreuses instances d’application du même type. Par exemple, vous pouvez avoir une seule instance d’application par client. Ce modèle basé sur le type prend en charge plusieurs versions du même type d’application inscrit auprès du runtime.

Par exemple, le client A peut utiliser une application instanciée avec le type 1.0 d’AppTypeA, tandis que le client B peut disposer d’une autre application instanciée avec le même type et la même version. Vous définissez les types d’applications dans les manifestes d’application, et vous spécifiez le nom de l’application et les paramètres de déploiement au moment où vous créez l’application.

Bien que ce modèle offre une certaine flexibilité, nous prévoyons également de prendre en charge un modèle de déploiement plus simple, basé sur les instances, dans lequel les types sont implicites à partir du fichier manifeste. Dans ce modèle, chaque application obtient son propre manifeste indépendant. Nous proposons cela en version préliminaire, en ajoutant la prise en charge du format docker-compose.yml, qui est un format de déploiement basé sur les instances.

## <a name="next-steps"></a>Étapes suivantes

* Lisez les informations sur le [Modèle d’application Service Fabric](service-fabric-application-model.md)
* [Bien démarrer avec l’interface CLI Service Fabric](service-fabric-cli.md)

