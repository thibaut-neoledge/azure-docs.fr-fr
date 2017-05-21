---
title: "Utiliser une application gérée Azure | Microsoft Docs"
description: "Décrit comment un client crée une application gérée Azure à partir des fichiers publiés."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 2763da60fe25f2ca55603ecfcbbcefe3e368c25d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# <a name="consume-an-azure-managed-application"></a>Utiliser une application gérée Azure

Comme décrit dans l’article [Managed Application overview](managed-application-overview.md) (Vue d’ensemble de l’application gérée), il existe deux scénarios dans l’expérience de bout en bout. D’un côté, il y a l’éditeur ou l’éditeur de logiciels indépendant désireux de créer une application gérée destinée à être utilisée par les clients. De l’autre, le client final ou l’utilisateur de l’application gérée. Cet article décrit le deuxième scénario et explique comment un client final peut utiliser une application gérée fournie par un éditeur de logiciels indépendants.

Actuellement, vous pouvez utiliser Azure CLI ou le portail Azure pour utiliser une application gérée. 

## <a name="create-the-managed-application-using-cli"></a>Créer l’application gérée à l’aide de l’interface de ligne de commande 

Vous devez obtenir l’ID de définition d’appliance pour l’appliance que vous souhaitez utiliser.

Il existe deux manières de créer une application gérée à l’aide d’Azure CLI. L’une consiste à utiliser la commande de déploiement de modèle normale, et l’autre à utiliser une nouvelle commande fournie uniquement à cet effet.

### <a name="create-using-template-deployment-command"></a>Créer à l’aide de la commande de déploiement de modèle

Vous déployez le fichier applianceMainTemplate.json créé par le fournisseur.

Vous créez deux groupes de ressources. Le premier groupe de ressources se trouve où la ressource d’appliance est créée (Microsoft.Solutions/appliances). Le deuxième groupe de ressources contient toutes les ressources définies dans le fichier mainTemplate.json. Ce groupe de ressources est géré par l’éditeur de logiciels indépendants.

```azurecli
az group create --name mainResourceGroup --location westcentralus    
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> Utilisez `westcentralus` comme emplacement du groupe de ressources.
>


Ensuite, utilisez la commande suivante pour déployer le fichier applianceMainTemplate.json dans mainResourceGroup :

```azurecli
az group deployment --name managedAppDeployment --resourceGroup mainResourceGroup 
      --templateUri  
```

Lorsque le modèle précédent s’exécute, il vous demande les valeurs des paramètres qui sont définis dans le modèle. Outre les paramètres requis pour l’approvisionnement des ressources dans un modèle, vous avez besoin de deux valeurs de paramètre essentielles :

- managedResourceGroupId : ID du groupe de ressources dans lequel les ressources définies dans le fichier applianceMainTemplate.json sont créées. L’ID est au format `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`. Dans l’exemple précédent, il s’agit de l’id de `managedResourceGroup`.
- applianceDefinitionId : ID de la ressource de la définition de l’application gérée. Cette valeur est fournie par l’éditeur de logiciels indépendants. 

> [!NOTE] 
> L’ISV doit accorder l’accès au groupe de ressources où la ressource de définition d’applicance est créée. La ressource de définition d’appliance est créée dans l’abonnement ISV. Par conséquent, un utilisateur, un groupe d’utilisateurs ou une application du locataire client a besoin d’accéder en lecture à cette ressource. 

Une fois le déploiement réussi, la ressource d’appliance créée dans **mainResourceGroup** s’affiche. La ressource storageAccount est créée dans **managedResourceGroup**.

### <a name="create-the-managed-application-using-create-command"></a>Créer l’application gérée à l’aide de la commande créer

Vous pouvez utiliser la commande `az managedapp create` pour créer une application gérée à partir de la définition d’application gérée. 

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus" 
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401" 
       --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401" 
       --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401" 
       --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}" 
       --debug
```

**appliance-definition-Id** : ID de ressource de la définition d’applicance créée à l’étape précédente. Pour obtenir cet ID, exécutez la commande suivante :

```azurecli
az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
```

Cette commande renvoie la définition d’appliance. Vous avez besoin de la valeur de la propriété **Id**.

**managedResourceGroupId** : nom du groupe de ressources dans lequel les ressources définies dans le fichier applianceMainTemplate.json sont créées. Ce groupe de ressources est le groupe de ressources gérées qui est géré par l’éditeur. S’il n’existe pas, il est créé pour vous.

**resource-group** : groupe de ressources où la ressource d’appliance est créée. La ressource Microsoft.Solutions/appliance se trouve dans ce groupe de ressources. 

**parameters** : paramètres qui sont nécessaires aux ressources définies dans le fichier applianceMainTemplate.json.

## <a name="create-the-managed-application-using-portal"></a>Créer l’application gérée à l’aide du portail

Le portail comporte également le support permettant d’utiliser les applications gérées publiées par les éditeurs de logiciels. Vous pouvez procéder comme suit :

Sélectionnez l’application gérée dans le panneau Créer du portail Azure.

![](./media/managed-application-consumption/create-blade.png)

Ensuite, la liste des offres issues des différents éditeurs de logiciels indépendants/partenaires s’affiche. Sélectionnez celle que vous souhaitez créer et cliquez sur « Créer ».

![](./media/managed-application-consumption/select-offer.png)

Après avoir cliqué sur Créer, indiquez les paramètres requis pour approvisionner les ressources dans le panneau qui s’ouvre. 

![](./media/managed-application-consumption/input-parameters.png)

Une fois les valeurs indiquées, cliquez sur OK. Le modèle est validé en fonction des saisies effectuées. En cas de réussite de la validation, le déploiement du modèle démarre. Une fois le déploiement terminé, les ressources appropriées définies dans le modèle sont approvisionnées dans le groupe de ressources géré fourni.

## <a name="known-issues"></a>Problèmes connus

Cette version préliminaire comprend les problèmes suivants :

* Si vous obtenez une erreur 500 Erreur interne du serveur lors de la création de l’appliance, il s’agit probablement d’un problème intermittent. Recommencez l’opération si vous rencontrez ce problème.
* Un nouveau groupe de ressources est nécessaire pour le groupe de ressources gérées. L’utilisation d’un groupe de ressources existant provoque l’échec du déploiement.
* Le groupe de ressources qui contient la ressource Microsoft.Solutions/appliances doit être créé à l’emplacement **westcentralus**.

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](managed-application-overview.md).
* Pour comprendre l’expérience client, consultez [Créer et publier une application gérée Azure](managed-application-publishing.md).
