---
title: "Gérer les applications Service Fabric à l’aide d’Azure CLI 2.0"
description: "Décrit le processus de déploiement et de suppression des applications dans un cluster Service Fabric à l’aide d’Azure CLI 2.0."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: a99819f6a1c0ef31e14c95b6bd47138feb05053f
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017

---
# <a name="manage-service-fabric-application-using-azure-cli-20"></a>Gérer une application Service Fabric à l’aide d’Azure CLI 2.0

Suivez cette documentation pour créer et supprimer des applications qui s’exécutent dans un cluster Service Fabric.

## <a name="prerequisites"></a>Composants requis

Veillez à installer Azure CLI 2.0 et à sélectionner votre cluster Service Fabric. Vous trouverez plus d’informations dans la [documentation sur la mise en route d’Azure CLI 2.0](service-fabric-azure-cli-2-0.md).

Vous devez également avoir un package d’application Service Fabric prêt à être déployé. Vous trouverez plus d’informations sur la création et le packaging d’une application dans la [documentation sur le modèle application](service-fabric-application-model.md).

## <a name="overview"></a>Vue d'ensemble

Le déploiement d’une application consiste en quatre étapes :

1. Télécharger un package d’application dans le magasin d’image Service Fabric
1. Approvisionner un type d’application
1. Spécifier et créer une application
1. Spécifier et créer des services

La suppression d’une application existante requiert trois étapes :

1. Supprimer l’application
1. Annuler l’approvisionnement du type d’application associé
1. Supprimer le contenu du magasin d’images

## <a name="deploy-a-new-application"></a>Déployer une nouvelle application

Pour déployer une nouvelle application, suivez les étapes ci-dessous.

### <a name="upload-a-new-application-package-to-the-image-store"></a>Charger un nouveau package d’application dans le magasin d’images

Avant de créer une application, vous devez charger le package d’application dans le magasin d’images Service Fabric.
Supposons que votre package d’application existe dans le répertoire `app_package_dir`. Utilisez les commandes suivantes pour charger le répertoire :

```azurecli
az sf application upload --path ~/app_package_dir
```

Pour les packages d’application volumineux, vous pouvez spécifier l’option `--show-progress` pour afficher la progression du chargement.

### <a name="provision-application-type"></a>Approvisionner un type d’application

Une fois le chargement terminé, l’application doit être approvisionnée. Pour approvisionner l’application, utilisez la commande suivante :

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

Le chemin `application-type-build-path` correspond au nom du répertoire contenant le package d’application chargé précédemment.

### <a name="create-application-from-application-type"></a>Créer une application à partir du type d’application

Une fois que l’application a été approvisionnée, vous pouvez nommer et créer votre application à l’aide de la commande suivante :

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

Ici, `app-name` est le nom que vous souhaitez donner à l’instance de l’application. Les autres paramètres se trouvent dans le manifeste d’application approvisionné précédemment.

Le nom de l’application doit commencer par le préfixe `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Créer des services pour la nouvelle application

Après avoir créé une application, vous pouvez créer des services à partir de l’application. Pour cet exemple, nous créer un service sans état à partir de notre application. Les services que vous pouvez créer à partir d’une application sont définis dans un manifeste de service placé dans le package d’application précédemment approvisionné.

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-creation-and-health"></a>Vérifier la création et l’intégrité de l’application

Pour vous assurer qu'une application et un service ont été déployés, vous pouvez vérifier que l’application et le service sont répertoriés à l’aide des commandes suivantes :

```azurecli
az sf application list
az sf service list --application-list TestApp
```

Pour vérifier l’intégrité du service, utilisez des commandes similaires pour récupérer des informations sur l’intégrité du service et de l’application.

```azurecli
az sf application health --application-id TestApp
az sf service health --service-id TestApp/TestSvc
```

Les applications et services intègres doivent avoir une valeur `HealthState` = `Ok`.

## <a name="remove-an-existing-application"></a>Supprimer une application existante

Pour supprimer une application, suivez les étapes ci-dessous.

### <a name="delete-the-application"></a>Supprimer l’application

Supprimez l’application en exécutant la commande suivante :

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Annuler l’approvisionnement de l’application

Une fois que l’application est supprimée, l’approvisionnement du type d’application peut être annulé s’il n’est plus nécessaire. Utilisez la commande suivante pour annuler l’approvisionnement du type d’application.

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

Ici, le nom et la version du type doivent correspondre au nom et à la version du manifeste d’application précédemment approvisionné.

### <a name="delete-application-package"></a>Supprimer le package d’application

Une fois l’approvisionnement du type d’application annulé, le package d’application peut supprimé du magasin de l’image si vous n’en avez plus besoin. La suppression des packages d’application permet de récupérer de l’espace sur le disque. Utilisez la commande suivante pour supprimer le package d’application du magasin d’images :

```azurecli
az sf application package-delete --content-path app_package_dir
```

Ici, le chemin `content-path` doit correspondre au nom du répertoire initialement chargé lors de la création de l’application.

## <a name="related-articles"></a>Articles connexes

* [Prise en main de Service Fabric et d’Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Getting started with Service Fabric XPlat CLI](service-fabric-azure-cli.md) (Prise en main de l’interface de ligne de commande Service Fabric XPlat)

