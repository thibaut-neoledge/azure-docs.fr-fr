---
title: "Utiliser une application gérée Azure | Microsoft Docs"
description: "Décrit comment un client crée une application gérée Azure à partir de fichiers publiés."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: ed8fbaf2a4546c8e31eeced11cd0b5627fd62c0c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2017
---
# <a name="consume-an-internal-managed-application"></a>Utiliser une application managée interne

Vous pouvez utiliser des [applications managées](managed-application-overview.md) Azure pour les besoins des membres de votre organisation. Par exemple, vous pouvez sélectionner des applications managées dans votre service informatique qui garantissent la conformité par rapport aux normes de l’organisation. Ces applications managées sont disponibles via le catalogue de services, et non la Place de marché Azure.

Avant d’aller plus loin dans la lecture de cet article, vérifiez qu’une application managée est disponible dans le catalogue de services de votre abonnement. Si personne au sein de votre organisation n’a encore créé une application managée, consultez [Publier une application managée pour une utilisation interne](managed-application-publishing.md).

Actuellement, vous pouvez utiliser Azure CLI ou le portail Azure pour utiliser une application gérée.

## <a name="create-the-managed-application-by-using-the-portal"></a>Créer l’application gérée à l’aide du portail

Pour déployer une application managée via le portail, suivez ces étapes :

1. Accédez au portail Azure. Recherchez **Application managée du catalogue de services**.

   ![Application gérée du catalogue de services](./media/managed-application-consumption/create-service-catalog-managed-application.png)

1. Sélectionnez l’application managée que vous voulez créer dans la liste des solutions disponibles. Sélectionnez **Créer**.

   ![Sélection d’application gérée](./media/managed-application-consumption/select-offer.png)

1. Fournissez les valeurs des paramètres nécessaires à l’approvisionnement des ressources. Sélectionnez **Ouest-Centre des États-Unis** comme emplacement. Sélectionnez **OK**.

   ![Paramètres de l’application gérée](./media/managed-application-consumption/input-parameters.png)

1. Le modèle valide les valeurs que vous avez fournies. Si la validation aboutit, sélectionnez **OK** pour commencer le déploiement.

   ![Validation de l’application managée](./media/managed-application-consumption/validation.png)

Une fois le déploiement terminé, les ressources appropriées définies dans le modèle sont approvisionnées dans le groupe de ressources gérées fourni.

## <a name="create-the-managed-application-by-using-azure-cli"></a>Créer l’application gérée à l’aide de l’interface CLI Azure

Il existe deux manières de créer une application gérée à l’aide d’Azure CLI :

* Utilisez la commande permettant de créer des applications managées.
* Utilisez la commande de déploiement de modèle standard.

### <a name="use-the-template-deployment-command"></a>Utiliser la commande de déploiement de modèle

Déployez le fichier applianceMainTemplate.json créé par le fournisseur.

Puis, créez deux groupes de ressources. Le premier groupe de ressources se trouve là où la ressource d’application managée est créée : Microsoft.Solutions/appliances. Le deuxième groupe de ressources contient toutes les ressources définies dans le fichier mainTemplate.json. Ce groupe de ressources est géré par l’éditeur de logiciels indépendants.

```azurecli
az group create --name mainResourceGroup --location westcentralus
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> Utilisez `westcentralus` comme emplacement du groupe de ressources.
>

Utilisez la commande suivante pour déployer le fichier applianceMainTemplate.json dans mainResourceGroup :

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri
```

Après l’exécution du modèle précédent, il vous demande les valeurs des paramètres qui sont définis dans le modèle. Outre les paramètres requis pour l’approvisionnement des ressources dans un modèle, vous avez besoin de deux valeurs de paramètre essentielles :

- **managedResourceGroupId** : ID du groupe de ressources contenant les ressources définies dans applianceMainTemplate.json. L’ID est au format `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`. Dans l’exemple précédent, il s’agit de l’ID de `managedResourceGroup`.
- **applianceDefinitionId** : ID de la ressource de la définition de l’application gérée. Cette valeur est fournie par l’éditeur de logiciels indépendants.

> [!NOTE]
> L’éditeur doit accorder l’accès au groupe de ressources qui contient la définition d’application managée. La ressource de définition est créée dans l’abonnement de l’éditeur. Par conséquent, un utilisateur, un groupe d’utilisateurs ou une application du locataire client a besoin d’accéder en lecture à cette ressource.

À l’issue du déploiement, vous pouvez constater que l’application managée est créée dans mainResourceGroup. La ressource storageAccount est créée dans managedResourceGroup.

### <a name="use-the-create-command"></a>Utiliser la commande create

Vous pouvez utiliser la commande `az managedapp create` pour créer une application gérée à partir de la définition d’application gérée.

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus"
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401"
    --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401"
    --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401"
    --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}"
    --debug
```

* **appliance-definition-Id** : ID de ressource de la définition de l’application managée créée à l’étape précédente. Pour obtenir cet ID, exécutez la commande suivante :

  ```azurecli
  az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
  ```

  Cette commande retourne la définition de l’application managée. Vous avez besoin de la valeur de la propriété ID.

* **managed-rg-id** : nom du groupe de ressources contenant les ressources définies dans applianceMainTemplate.json. Ce groupe de ressources est le groupe de ressources gérées. Il est géré par l’éditeur. S’il n’existe pas, il est créé pour vous.
* **resource-group** : groupe de ressources dans lequel la ressource d’application managée est créée. La ressource Microsoft.Solutions/appliance se trouve dans ce groupe de ressources.
* **parameters** : paramètres qui sont nécessaires aux ressources définies dans le fichier applianceMainTemplate.json.

## <a name="known-issues"></a>Problèmes connus

Cette version préliminaire comprend les problèmes suivants :

* Une erreur de serveur interne 500 apparaît pendant la création de l’application managée. Si vous rencontrez ce problème, il est sans doute intermittent. Retentez l’opération.
* Un nouveau groupe de ressources est nécessaire pour le groupe de ressources gérées. L’utilisation d’un groupe de ressources existant provoque l’échec du déploiement.
* Le groupe de ressources qui contient la ressource Microsoft.Solutions/appliances doit être créé à l’emplacement **westcentralus**.

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées](managed-application-overview.md).
* Pour plus d’informations sur la publication d’une application gérée de catalogue de services, consultez l’article [Créer et publier une application gérée de catalogue de services](managed-application-publishing.md).
* Pour plus d’informations sur la publication d’applications gérées sur la Place de marché, consultez l’article [Applications gérées sur la Place de marché](managed-application-author-marketplace.md).
* Pour plus d’informations sur l’utilisation d’applications gérées de la Place de marché, consultez l’article [Utilisation des applications gérées de la Place de marché Azure](managed-application-consume-marketplace.md).
