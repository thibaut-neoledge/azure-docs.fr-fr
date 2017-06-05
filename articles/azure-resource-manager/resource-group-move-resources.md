---
title: "Déplacer des ressources Azure vers un nouveau groupe de ressources ou abonnement | Microsoft Docs"
description: "Utilisez Azure Resource Manager ou une API REST pour déplacer des ressources vers un nouveau groupe de ressources ou abonnement."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: f1b9beabfb0a92e5cc49d6af762693ae45a85e42
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017


---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement
Cette rubrique vous montre comment déplacer des ressources vers un nouvel abonnement ou un nouveau groupe de ressources dans le même abonnement. Vous pouvez utiliser le portail, PowerShell, Azure CLI ou l’API REST pour déplacer des ressources. Les opérations de déplacement de cette rubrique sont disponibles sans assistance du support technique Azure.

Lorsque vous déplacez des ressources, le groupe source et le groupe cible sont verrouillés pendant l’opération. Les opérations d’écriture et de suppression sont bloquées sur les groupes de ressources tant que le déplacement n’est pas terminé. Ce verrou signifie que vous ne pouvez pas ajouter, mettre à jour ou supprimer des ressources dans les groupes de ressources, mais il ne signifie pas que les ressources sont figées. Par exemple, si vous déplacez un serveur SQL Server et sa base de données vers un nouveau groupe de ressources, une application qui utilise la base de données ne rencontre aucune interruption de service. Elle peut toujours lire et écrire dans la base de données. 

Vous ne pouvez pas modifier l’emplacement de la ressource. Le déplacement d’une ressource consiste uniquement en sa translation vers un nouveau groupe de ressources. Le nouveau groupe de ressources peut présenter à un autre emplacement, mais l’emplacement de la ressource n’est aucunement modifié.

> [!NOTE]
> Cet article décrit le déplacement des ressources dans une offre de compte Azure. Si vous souhaitez en fait modifier votre offre de compte Azure (par exemple, en procédant à une mise à niveau d’une version par paiement à l’utilisation vers une version par prépaiement) tout en continuant à utiliser vos ressources existantes, consultez [Changer d’offre pour votre abonnement Azure](../billing/billing-how-to-switch-azure-offer.md). 
> 
> 

## <a name="checklist-before-moving-resources"></a>Liste de contrôle avant le déplacement de ressources
Plusieurs étapes importantes doivent être effectuées avant de déplacer une ressource. Vérifiez ces conditions pour prévenir d'éventuelles erreurs.

1. Les abonnements source et de destination doivent exister dans le même [client Azure Active Directory](../active-directory/active-directory-howto-tenant.md). Pour vérifier que les deux abonnements ont le même ID client, utilisez Azure PowerShell ou Azure CLI.

  Pour Azure PowerShell, utilisez :

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName "Example Subscription").TenantId
  ```

  Pour Azure CLI 2.0, utilisez :

  ```azurecli
  az account show --subscription "Example Subscription" --query tenantId
  ```

  Si les ID clients des abonnements source et de destination ne sont pas identiques, vous pouvez essayer de changer l’annuaire de l’abonnement. Toutefois, cette option est uniquement disponible pour les administrateurs de service qui sont connectés avec un compte Microsoft (pas un compte de société). Pour essayer de changer l’annuaire, connectez-vous au [portail classique](https://manage.windowsazure.com/) et sélectionnez **Paramètres**, puis l’abonnement. Si l’icône **Modifier l’annuaire** est disponible, sélectionnez-la pour modifier l’annuaire Azure Active Directory associé. 

  ![Modifier l’annuaire](./media/resource-group-move-resources/edit-directory.png) 

  Si cette icône n’est pas disponible, vous devez contacter le support pour déplacer les ressources vers un nouveau client.

2. Le service doit activer la possibilité de déplacer des ressources. Cette rubrique répertorie les services permettant de déplacer des ressources et les services qui ne le permettent pas.
3. L’abonnement de destination doit être inscrit pour le fournisseur de la ressource déplacée. Sinon, vous recevez une erreur indiquant que **l’abonnement n’est pas inscrit pour un type de ressource**. Vous pouvez rencontrer ce problème lors du déplacement d’une ressource vers un nouvel abonnement qui n’a jamais été utilisé avec ce type de ressource. Pour découvrir comment vérifier l’état d’inscription et inscrire des fournisseurs de ressources, consultez [Fournisseurs et types de ressources](resource-manager-supported-services.md#resource-providers-and-types).

## <a name="when-to-call-support"></a>Quand appeler le support technique
Vous pouvez déplacer la plupart des ressources via les opérations en libre-service présentées dans cette rubrique. Utilisez les opérations en libre-service pour :

* Déplacer des ressources Resource Manager.
* Déplacer des ressources classiques conformément aux [limitations du déploiement classique](#classic-deployment-limitations). 

Appelez le support technique quand vous devez :

* Déplacer vos ressources vers un nouveau compte Azure (et un locataire Azure Active Directory).
* Déplacer des ressources classiques, mais que vous rencontrez des problèmes avec les limitations.

## <a name="services-that-enable-move"></a>Services permettant le déplacement
Pour l’instant, les services qui permettent le déplacement vers un nouveau groupe de ressources et un nouvel abonnement sont les suivants :

* API Management
* Applications App Service (applications web) : consultez [Limitations d’App Service](#app-service-limitations)
* Application Insights
* Automatisation
* Batch
* Bing Maps
* CDN
* Cloud Services : consultez [Limitations relatives au déploiement Classic](#classic-deployment-limitations)
* Cognitive Services
* Content Moderator
* Data Catalog
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Azure Cosmos DB
* Event Hubs
* Clusters HDInsight - voir [Limitations de HDInsight](#hdinsight-limitations)
* IoT Hubs
* Key Vault 
* Équilibreurs de charge
* Logic Apps
* Apprentissage automatique
* Media Services
* Mobile Engagement
* Notification Hubs
* Operational Insights
* Operations Management
* Power BI
* Cache Redis
* Scheduler
* Search
* Gestion de serveur
* Service Bus
* Service Fabric
* Storage
* Storage (classique) : consultez [Limitations relatives au déploiement classique](#classic-deployment-limitations)
* Stream Analytics
* Serveur de base de données SQL : la base de données et le serveur doivent résider dans le même groupe de ressources. Lorsque vous déplacez un serveur SQL, toutes ses bases de données sont également déplacées.
* Traffic Manager
* Machines virtuelles : ne prend pas en charge le déplacement vers un nouvel abonnement lorsque ses certificats sont stockés dans un Key Vault
* Virtual Machines (classique) : consultez [Limitations relatives au déploiement classique](#classic-deployment-limitations)
* Jeux de mise à l’échelle de machine virtuelle
* Réseaux virtuels : actuellement, un réseau virtuel homologué ne peut pas être déplacé tant que l’homologation de réseau virtuel est activée. Une fois cette dernière désactivée, le réseau virtuel peut être déplacé correctement et l’homologation de réseau virtuel peut être activée.
* Passerelle VPN 

 
## <a name="services-that-do-not-enable-move"></a>Services qui ne permettent pas le déplacement
Les services qui ne permettent pas actuellement le déplacement d’une ressource sont les suivants :

* Service de contrôle d’intégrité hybride Active Directory
* Application Gateway
* Groupes à haute disponibilité comprenant des machines virtuelles avec des disques gérés
* BizTalk Services
* Service de conteneur
* ExpressRoute
* Laboratoires DevTest : le déplacement vers un nouveau groupe de ressources dans le même abonnement est activé, mais le déplacement entre abonnements n’est pas activé.
* Dynamics LCS
* Images créées à partir de disques gérés
* Managed Disks
* Applications gérées
* Coffre Recovery Services : par ailleurs, ne déplacez pas les ressources de calcul, de réseau et de stockage associées au coffre Recovery Services. Consultez [Limitations de Recovery Services](#recovery-services-limitations).
* Sécurité
* Instantanés créés à partir de disques gérés
* Machines virtuelles avec un certificat stocké dans Key Vault
* Machines virtuelles avec des disques managés
* Réseaux virtuels (classique) : consultez [Limitations relatives au déploiement classique](#classic-deployment-limitations)
* Les machines virtuelles créées à partir de ressources de la Place de marché ne peuvent pas être déplacées entre des abonnements. La ressource doit être déprovisionnée dans l’abonnement actuel et déployée à nouveau dans le nouvel abonnement.

## <a name="app-service-limitations"></a>limitations d’App Service
Lorsque vous travaillez avec des applications App Service, vous ne pouvez pas déplacer uniquement un plan App Service. Pour déplacer des applications App Service, les options disponibles sont :

* Déplacez le plan App Service et toutes les autres ressources d’App Service dans ce groupe de ressources vers un nouveau groupe de ressources qui ne dispose pas encore des ressources d’App Service. Cette exigence signifie que vous devez déplacer même les ressources d’App Service qui ne sont pas associées au plan App Service. 
* Déplacer les applications vers un autre groupe de ressources, mais conserver tous les plans App Service dans le groupe de ressources d'origine.

Le plan App Service ne doit pas forcément résider dans le même groupe de ressources que l’application pour que l’application fonctionne correctement.

Par exemple, si votre groupe de ressources contient :

* **web-a**, qui est associé à **plan-a**
* **web-b**, qui est associé à **plan-b**

Vos options sont :

* Déplacer **web-a**, **plan-a**, **web-b** et **plan-b**
* Déplacer **web-a** et **web-b**
* Déplacez **web-a**
* Déplacez **web-b**

Toutes les autres combinaisons impliquent l’abandon d’un type de ressource qui ne peut pas être abandonné lors du déplacement d’un plan App Service (n’importe quel type de ressource App Service).

Si votre application web réside dans un autre groupe de ressources que son plan App Service mais que vous souhaitez déplacer les deux dans un nouveau groupe de ressources, vous devez effectuer le déplacement en deux étapes. Par exemple :

* **web-a** se trouve dans **web-group**
* **plan-a** se trouve dans **plan-group**
* Vous voulez que **web-a** et **plan-a** se trouvent dans **combined-group**

Pour effectuer ce déplacement, effectuez deux opérations de déplacement distinctes dans l’ordre suivant :

1. Déplacez **web-a** vers **plan-group**
2. Déplacez **web-a** et **plan-a** vers **combined-group**.

Vous pouvez déplacer un certificat App Service vers un nouveau groupe de ressources ou abonnement sans aucun problème. Toutefois, si votre application web inclut un certificat SSL que vous avez acheté en externe et chargé sur l’application, vous devez supprimer le certificat avant de déplacer l’application web. Par exemple, vous pouvez effectuer les opérations suivantes :

1. Supprimer le certificat chargé de l’application web
2. Déplacer l’application web
3. Charger le certificat sur l’application web

## <a name="recovery-services-limitations"></a>Limitations de Recovery Services
Le déplacement n’est pas possible pour les ressources de stockage, de réseau ou de calcul utilisées pour configurer la récupération d’urgence avec Azure Site Recovery. 

Par exemple, supposons que vous avez configuré la réplication de vos machines locales vers un compte de stockage (Storage1) et que vous souhaitez que la machine protégée apparaisse après le basculement vers Azure comme une machine virtuelle (VM1) connectée à un réseau virtuel (Network1). Vous ne pouvez pas déplacer ces ressources Azure (Storage1, VM1 et Network1) sur différents groupes de ressources dans le même abonnement ou sur différents abonnements.

## <a name="hdinsight-limitations"></a>Limitations de HDInsight

Vous pouvez déplacer des clusters HDInsight vers un nouvel abonnement ou groupe de ressources. Toutefois, vous ne pouvez pas déplacer sur différents abonnements les ressources réseau liées au cluster HDInsight (par exemple le réseau virtuel, une carte réseau ou un équilibrage de charge). En outre, vous ne pouvez pas déplacer vers un nouveau groupe de ressources une carte réseau connectée à une machine virtuelle pour le cluster.

Lorsque vous déplacez un cluster HDInsight vers un nouvel abonnement, déplacez tout d’abord les autres ressources (le compte de stockage, par exemple). Puis, déplacez le cluster HDInsight par lui-même.

## <a name="classic-deployment-limitations"></a>Limitations relatives au déploiement Classic
Les options de déplacement des ressources déployées avec le modèle classique diffèrent selon que vous déplaciez les ressources au sein d’un abonnement ou vers un nouvel abonnement. 

### <a name="same-subscription"></a>Même abonnement
Lors du déplacement de ressources d’un groupe de ressources vers un autre au sein du même abonnement, les restrictions suivantes s’appliquent :

* Les réseaux virtuels (classiques) ne peuvent pas être déplacés.
* Les machines virtuelles (classiques) doivent être déplacées avec le service cloud. 
* Le service cloud ne peut être déplacé que lorsque le déplacement comprend toutes ses machines virtuelles.
* Un seul service cloud peut être déplacé à la fois.
* Un seul compte de stockage (classique) peut être déplacé à la fois.
* Vous ne pouvez pas déplacer un compte de stockage (classique) dans la même opération avec une machine virtuelle ou un service cloud.

Pour déplacer des ressources classiques vers un nouveau groupe de ressources dans le même abonnement, les opérations de déplacement standard via le [portail](#use-portal), [Azure PowerShell](#use-powershell), [l’interface CLI Azure](#use-azure-cli) ou [l’API REST](#use-rest-api). Vous utilisez les mêmes opérations que vous celles que vous utilisez pour déplacer des ressources Resource Manager.

### <a name="new-subscription"></a>Nouvel abonnement
Lors du déplacement de ressources vers un nouvel abonnement, les restrictions suivantes s’appliquent :

* Toutes les ressources classiques de l’abonnement doivent être déplacées au cours de la même opération.
* L’abonnement cible ne doit pas contenir d’autres ressources classiques.
* Le déplacement peut uniquement être demandé par le biais d’une API REST distincte pour les déplacements classiques. Les commandes de déplacement standard de Resource Manager ne fonctionnent pas lors du déplacement de ressources classiques vers un nouvel abonnement.

Pour déplacer des ressources classiques vers un nouvel abonnement, utilisez des opérations REST spécifiques aux ressources classiques. Pour utiliser REST, procédez comme suit :

1. Vérifiez si l’abonnement source peut participer à un déplacement entre abonnements. Utilisez l’opération suivante :

  ```HTTP   
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```
   
     Dans le corps de la demande, spécifiez :

  ```json 
  {
    "role": "source"
  }
  ```
  
     La réponse pour l’opération de validation est au format suivant :

  ```json 
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. Vérifiez si l’abonnement de destination peut participer à un déplacement entre abonnements. Utilisez l’opération suivante :

  ```HTTP 
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     Dans le corps de la demande, spécifiez :

  ```json 
  {
    "role": "target"
  }
  ```
   
     La réponse est dans le même format que la validation de l’abonnement source.
3. Si les deux abonnements sont validés, déplacez toutes les ressources classiques d’un abonnement à l’autre via l’opération suivante :

  ```HTTP 
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    Dans le corps de la demande, spécifiez :

  ```json 
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

Cette opération peut prendre plusieurs minutes. 

## <a name="use-portal"></a>Utilisation du portail
Pour déplacer des ressources, sélectionnez le groupe de ressources contenant ces ressources, puis sélectionnez le bouton **Déplacer**.

![Déplacer des ressources](./media/resource-group-move-resources/select-move.png)

Indiquez si vous déplacez les ressources vers un nouveau groupe de ressources ou vers un nouvel abonnement.

Sélectionnez les ressources à déplacer et le groupe de ressources de destination. Confirmez que vous devez mettre à jour les scripts de ces ressources et sélectionnez **OK**. Si vous avez sélectionné l’icône Modifier l’abonnement à l’étape précédente, vous devez également sélectionner l’abonnement de destination.

![Sélectionner la destination](./media/resource-group-move-resources/select-destination.png)

Dans **Notifications**, vous voyez que l’opération de déplacement est en cours d’exécution.

![afficher l’état du déplacement](./media/resource-group-move-resources/show-status.png)

Lorsqu’elle est terminée, vous êtes informé du résultat.

![afficher les résultats du déplacement](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Utiliser PowerShell
Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, utilisez la commande `Move-AzureRmResource`.

Le premier exemple vous indique comment déplacer une ressource vers un nouveau groupe de ressources.

```powershell
$resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId
```

Le second exemple vous indique comment déplacer plusieurs ressources vers un nouveau groupe de ressources.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Pour déplacer des ressources vers un nouvel abonnement, renseignez une valeur pour le paramètre `DestinationSubscriptionId`.

Vous devez confirmer que vous souhaitez déplacer les ressources spécifiées.

```powershell
Confirm
Are you sure you want to move these resources to the resource group
'/subscriptions/{guid}/resourceGroups/newRG' the resources:

/subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
/subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
```

## <a name="use-azure-cli-20"></a>Utiliser Azure CLI 2.0
Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, utilisez la commande `az resource move`. Fournissez les ID des ressources à déplacer. Vous pouvez obtenir les ID des ressources avec la commande suivante :

```azurecli
az resource show -g sourceGroup -n storagedemo --resource-type "Microsoft.Storage/storageAccounts" --query id
```

L’exemple suivant montre comment déplacer un compte de stockage vers un nouveau groupe de ressources. Dans le paramètre `--ids`, spécifiez une liste séparée par des espaces des ID des ressources à déplacer.

```azurecli
az resource move --destination-group newgroup --ids "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo"
```

Pour déplacer des ressources vers un nouvel abonnement, spécifiez le paramètre `--destination-subscription-id`.

## <a name="use-azure-cli-10"></a>Utiliser Azure CLI 1.0
Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, utilisez la commande `azure resource move`. Fournissez les ID des ressources à déplacer. Vous pouvez obtenir les ID des ressources avec la commande suivante :

```azurecli
azure resource list -g sourceGroup --json
```

Elle retourne les informations au format suivant :

```azurecli
[
  {
    "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
    "name": "storagedemo",
    "type": "Microsoft.Storage/storageAccounts",
    "location": "southcentralus",
    "tags": {},
    "kind": "Storage",
    "sku": {
      "name": "Standard_RAGRS",
      "tier": "Standard"
    }
  }
]
```

L’exemple suivant montre comment déplacer un compte de stockage vers un nouveau groupe de ressources. Dans le paramètre `-i`, spécifiez une liste séparée par des virgules des ID des ressources à déplacer.

```azurecli
azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
```

Vous devez confirmer que vous souhaitez déplacer la ressource spécifiée.

## <a name="use-rest-api"></a>Avec l’API REST
Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez :

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 
```

Dans le corps de la requête, vous indiquez le groupe de ressources cible et les ressources à déplacer. Pour plus d’informations sur l’opération REST de déplacement, consultez [Déplacer des ressources](https://msdn.microsoft.com/library/azure/mt218710.aspx).

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les applets de commande PowerShell permettant de gérer votre abonnement, consultez [Utilisation d’Azure PowerShell avec Resource Manager](powershell-azure-resource-manager.md).
* Pour plus d’informations sur les commandes de l’interface de ligne de commande Azure permettant de gérer votre abonnement, consultez [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Manager](xplat-cli-azure-resource-manager.md).
* Pour plus d’informations sur les fonctionnalités du portail permettant de gérer votre abonnement, consultez [Utilisation du Portail Azure pour gérer les ressources](resource-group-portal.md).
* Pour plus d’informations sur l’application d’une organisation logique à vos ressources, consultez [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).


