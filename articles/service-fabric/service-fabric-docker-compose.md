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
ms.date: 07/06/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 91b6e98df5df98bb557d7fac0475354322d95640
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017

---
# <a name="compose-application-support-in-service-fabric-preview"></a>Application Compose dans Service Fabric (version préliminaire)

Docker utilise le fichier [docker-compose.yml](https://docs.docker.com/compose) pour la définition d’applications à conteneurs multiples.
Pour permettre aux clients familiarisés avec Docker d’orchestrer facilement des applications de conteneur existantes sur Service Fabric, nous avons inclus la prise en charge en version préliminaire de Docker Compose, en mode natif dans la plateforme. Service Fabric peut accepter des fichiers `docker-compose.yml` version 3(+). Comme cette prise en charge est disponible en version préliminaire, seul un sous-ensemble des directives de Compose est pris en charge. Par exemple, les mises à niveau d’application ne sont pas prises en charge. Toutefois, vous pouvez toujours supprimer et déployer des applications au lieu de les mettre à niveau.

Pour utiliser cette version préliminaire, vous devez créer votre cluster avec la version préliminaire du kit de développement logiciel SDK (version 255.255.x.x) via le portail. 

> [!NOTE]
> Cette fonctionnalité est disponible en version préliminaire et n’est pas prise en charge.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Déployer un fichier Docker Compose sur Service Fabric

Ces commandes suivantes créent une application Service Fabric (nommée `fabric:/TestContainerApp` dans l’exemple précédent) qui peut être surveillée et gérée de la même manière que n’importe quelle autre application Service Fabric. Le nom de l’application spécifiée peut être utilisé pour des requêtes d’intégrité.

### <a name="using-powershell"></a>Utiliser PowerShell

Créez une application Service Fabric à partir d’un fichier docker-compose.yml en exécutant la commande suivante dans PowerShell :

```powershell
New-ServiceFabricComposeApplication -ApplicationName fabric:/TestContainerApp -Compose docker-compose.yml [-RepositoryUserName <>] [-RepositoryPassword <>] [-PasswordEnctypted]
```

`RepositoryUserName`et `RepoistoryPassword` font référence au nom d’utilisateur et au mot de passe du registre de conteneurs. Une fois l’application terminée, vous pouvez utiliser la commande suivante pour vérifier l’état de l’application :

```powershell
Get-ServiceFabricComposeApplicationStatus -ApplicationName fabric:/TestContainerApp -GetAllPages
```

Pour supprimer l’application Compose, utilisez la commande suivante dans PowerShell :

```powershell
Remove-ServiceFabricComposeApplication  -ApplicationName fabric:/TestContainerApp
```

### <a name="using-azure-cli-20"></a>Avec Azure CLI 2.0

Vous pouvez également exécutez la commande Azure CLI suivante :

```azurecli
az sf compose create --application-id fabric:/TestContainerApp --compose-file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```

Une fois l’application créée, vous pouvez vérifier son état à l’aide de la commande suivante :

```azurecli
az sf compose status --application-id TestContainerApp [ --timeout ]
```

Pour supprimer l’application Compose, utilisez la commande suivante :

```azurecli
az sf compose remove  --application-id TestContainerApp [ --timeout ]
```

## <a name="supported-compose-directives"></a>Directives Compose prises en charge

Un sous-ensemble des options de configuration du format Compose V3 est pris en charge dans cette version préliminaire. Les types de primitives suivants sont pris en charge :

* Services -> Déploiement -> Réplicas
* Services -> Déploiement -> Placement -> Contraintes
* Services -> Déploiement -> Placement -> Limites
*         -cpu-shares
*         -memory
*         -memory-swap
* Services -> Commandes
* Services -> Environnement
* Services -> Ports
* Services -> image
* Services -> Isolation (uniquement pour Windows)
* Services -> Journalisation -> Pilote
* Services -> Journalisation -> Pilote -> Options
* Volume & déploiement -> Volume

Le cluster doit être configuré pour appliquer des limites de ressources, comme décrit dans [Gouvernance des ressources de Fabric Service](service-fabric-resource-governance.md). Toutes les autres directives Docker Compose ne sont pas prises en charge pour cette version préliminaire.

## <a name="servicednsname-computation"></a>Calcul de ServiceDnsName

Si le nom de service spécifié dans le fichier Compose est un nom de domaine complet (autrement dit, s’il contient un point '.'), alors le nom DNS enregistré par Service Fabric est `<ServiceName>`, y compris le point. Si ce n’est pas le cas, chaque segment de chemin d’accès dans ApplicationName devient une étiquette de domaine dans le nom DNS du service, le premier segment de chemin devenant l’étiquette du domaine de niveau supérieur.

Par exemple, si le nom de l’application spécifié est `fabric:/SampleApp/MyComposeApp`, alors `<ServiceName>.MyComposeApp.SampleApp` est le nom DNS enregistré.

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Différences entre le Compose (définition d’instance) et le modèle d’application Service Fabric (définition de type)

Le fichier docker-compose.yml décrit un ensemble déployable de conteneurs, y compris leurs propriétés et leurs configurations.
Par exemple, le fichier peut contenir des variables d’environnement et des ports. Les paramètres de déploiement, comme les contraintes de positionnement, les limites de ressources et les noms DNS, sont également spécifiés dans le fichier docker-compose.yml.

Le [modèle d’application Service Fabric](service-fabric-application-model.md) utilise des types de service et des types d’application. Il peut y avoir de nombreuses instances d’application du même type. Par exemple, vous pouvez avoir une seule instance d’application par client. Ce modèle basé sur le type prend en charge plusieurs versions du même type d’application enregistré avec le runtime.
Par exemple, le client A peut avoir une application instanciée avec un type 1.0 de AppTypeA et le client B peut disposer d’une autre application instanciée avec le même type et la même version. Les types d’application sont définis dans les manifestes d’application, et le nom d’application et les paramètres de déploiement sont spécifiés au moment de la création d’applications.

Bien que ce modèle offre une certaine flexibilité, nous comptons également prendre en charge un modèle de déploiement plus simple, basé sur les instances, dans lequel les types sont implicites à partir du fichier manifeste. Dans ce modèle, chaque application obtient son propre manifeste indépendant. Nous proposons cela en version préliminaire, en ajoutant la prise en charge du format docker-compose.yml, qui est un format de déploiement basé sur les instances.

## <a name="next-steps"></a>Étapes suivantes

* Lisez les informations sur le [Modèle d’application Service Fabric](service-fabric-application-model.md).

## <a name="related-articles"></a>Articles connexes

* [Prise en main de Service Fabric et d’Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Getting started with Service Fabric XPlat CLI](service-fabric-azure-cli.md) (Prise en main de l’interface de ligne de commande Service Fabric XPlat)

