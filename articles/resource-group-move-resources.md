<properties 
	pageTitle="Déplacer des ressources vers un nouveau groupe de ressources | Microsoft Azure" 
	description="Utilisez Azure Resource Manager ou une API REST pour déplacer des ressources vers un nouveau groupe de ressources ou abonnement." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2016" 
	ms.author="tomfitz"/>

# Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement

Cette rubrique vous indique comment déplacer des ressources d’un groupe de ressources vers un autre groupe de ressources. Vous pouvez également déplacer des ressources vers un nouvel abonnement (cependant, l’abonnement doit se trouver dans le même [client](./active-directory/active-directory-howto-tenant.md)). Vous pouvez envisager de déplacer des ressources dans les cas de figure suivants :

1. À des fins de facturation, une ressource doit être placée au sein d’un abonnement distinct.
2. Une ressource ne partage plus le cycle de vie des ressources avec lesquelles elle était précédemment groupée. Vous souhaitez déplacer la ressource vers un nouveau groupe, afin d’être en mesure de la gérer séparément des autres ressources.

Lorsque vous déplacez des ressources, le groupe source et le groupe cible sont verrouillés pendant la durée de l'opération. Les opérations d’écriture et de suppression sont bloquées sur les groupes tant que le déplacement n’est pas terminé.

Vous ne pouvez pas modifier l’emplacement de la ressource. Le déplacement d’une ressource consiste uniquement en sa translation vers un nouveau groupe de ressources. Le nouveau groupe de ressources peut présenter à un autre emplacement, mais l’emplacement de la ressource n’est aucunement modifié.

## Liste de contrôle avant le déplacement de ressources

Plusieurs étapes importantes doivent être effectuées avant de déplacer une ressource. Vérifiez ces conditions pour prévenir d'éventuelles erreurs.

1. Le service doit prendre en charge le déplacement de ressources. Consultez la liste ci-dessous pour plus d’informations sur les [services qui prennent en charge le déplacement des ressources](#services-that-support-move).
2. L’abonnement de destination doit être inscrit pour le fournisseur de la ressource déplacée. Sinon, vous recevrez une erreur indiquant que l'**abonnement n'est pas inscrit pour un type de ressource**. Vous pouvez rencontrer ce problème lors du déplacement d’une ressource vers un nouvel abonnement qui n’a jamais été utilisé avec ce type de ressource. Pour savoir comment vérifier l’état d’inscription et inscrire des fournisseurs de ressources, consultez [Fournisseurs et types de ressources](../resource-manager-supported-services/#resource-providers-and-types).
3. Si vous utilisez Azure PowerShell ou Azure CLI, utilisez la version la plus récente. Pour mettre à jour votre version, exécutez Microsoft Web Platform Installer et vérifiez si une nouvelle version est disponible. Pour plus d’informations, consultez [Comment installer et configurer Azure PowerShell](powershell-install-configure.md) et [Installer Azure CLI](xplat-cli-install.md).
4. Si vous déplacez une application App Service, vous avez lu attentivement les [limitations App Service](#app-service-limitations).
5. Si vous déplacez des ressources déployées via le modèle classique, vous avez consulté les [Limitations relatives au déploiement classique](#classic-deployment-limitations).

## Services qui prennent en charge le déplacement

Pour l’instant, les services à partir desquels il est possible de déplacer les ressources vers un nouveau groupe de ressources et un nouvel abonnement sont les suivants :

- API Management
- Applications App Service : consultez [Limitations d’App Service](#app-service-limitations)
- Automation
- Batch
- CDN
- Cloud Services : consultez [Limitations relatives au déploiement classique](#classic-deployment-limitations)
- Data Factory
- DocumentDB
- Clusters HDInsight
- Key Vault
- Mobile Engagement
- Notification Hubs
- Operational Insights
- Cache Redis
- Scheduler
- Search
- Storage (classique) : consultez [Limitations relatives au déploiement classique](#classic-deployment-limitations)
- Serveur de base de données SQL : la base de données et le serveur doivent résider dans le même groupe de ressources. Lorsque vous déplacez un serveur SQL, toutes ses bases de données sont également déplacées.
- Virtual Machines (classique) : consultez [Limitations relatives au déploiement classique](#classic-deployment-limitations)

## Services qui prennent partiellement en charge le déplacement

Les services qui prennent en charge le déplacement des ressources vers un nouveau groupe de ressources mais pas vers un nouvel abonnement sont les suivants :

- Réseaux virtuels

## Services qui ne prennent pas en charge le déplacement

Les services qui ne prennent actuellement pas en charge le déplacement d’une ressource sont les suivants :

- Application Insights
- ExpressRoute
- Storage
- Machines virtuelles
- Réseaux virtuels (classique) : consultez [Limitations relatives au déploiement classique](#classic-deployment-limitations)

## Limitations d’App Service

Lorsque vous travaillez avec des applications App Service, vous ne pouvez pas déplacer uniquement un plan App Service. Pour déplacer des applications App Service, les options disponibles sont :

- Déplacez le plan App Service et toutes les autres ressources d’App Service dans ce groupe de ressources vers un nouveau groupe de ressources qui ne dispose pas encore des ressources d’App Service. Cela signifie que vous devez déplacer même les ressources d’App Service qui ne sont pas associées au plan App Service. 
- Déplacer les applications vers un autre groupe de ressources, mais conserver tous les plans App Service dans le groupe de ressources d'origine.

Si votre groupe de ressources d’origine inclut également une ressource Application Insights, vous ne pouvez pas déplacer cette ressource, car Application Insights ne prend pas en charge l’opération de déplacement. Si vous incluez la ressource Application Insights lors du déplacement d’applications App Service, l’opération de déplacement entière échoue. Toutefois, Application Insights et le plan App Service n’ont pas à résider dans le même groupe de ressources pour que l’application fonctionne correctement.

Par exemple, si votre groupe de ressources contient :

- **web-a**, qui est associé à **plan-a** et **app-insights-a**
- **web-b**, qui est associé à **plan-b** et **app-insights-b**

Vos options sont :

- Déplacez **web-a**, **plan-a**, **web-b** et **plan-b**
- Déplacez **web-a** et **web-b**
- Déplacez **web-a**
- Déplacez **web-b**

Toutes les autres combinaisons impliquant le déplacement d’une ressource qui ne peut pas l’être (Application Insights) ou l’abandon d’un type de ressource qui ne peut pas l’être lors du déplacement d’un plan App Service (n’importe quel type de ressource App Service).

Si votre application web réside dans un autre groupe de ressources que son plan App Service mais que vous souhaitez déplacer les deux dans un nouveau groupe de ressources, vous devez effectuer le déplacement en deux étapes. Par exemple :

- **web-a** réside dans **web-group**
- **plan-a** réside dans **plan-group**
- Vous souhaitez que **web-a** et **plan-a** résident dans **combined-group**

Pour effectuer ce déplacement, effectuez deux opérations de déplacement distinctes dans l’ordre suivant :

1. Déplacez **web-a** vers **plan-group**
2. Déplacez **web-a** et **plan-a** vers **combined-group**.

## Limitations relatives au déploiement classique

Les options de déplacement des ressources déployées avec le modèle classique diffèrent selon que vous déplaciez les ressources au sein d’un abonnement ou vers un nouvel abonnement.

Lors du déplacement de ressources d’un groupe de ressources vers un autre **au sein du même abonnement**, les restrictions suivantes s’appliquent :

- Les réseaux virtuels (classiques) ne peuvent pas être déplacés.
- Les machines virtuelles (classiques) doivent être déplacées avec le service cloud. 
- Le service cloud peut uniquement être déplacé lorsque le déplacement comprend toutes ses machines virtuelles.
- Un seul service cloud peut être déplacé à la fois.
- Un seul compte de stockage (classique) peut être déplacé à la fois.
- Vous ne pouvez pas déplacer un compte de stockage (classique) dans la même opération avec une machine virtuelle ou un service cloud.

Lors du déplacement de ressources pour un **nouvel abonnement**, les restrictions suivantes s’appliquent :

- Toutes les ressources classiques de l’abonnement doivent être déplacées au cours de la même opération.
- Le déplacement peut uniquement être demandé par le biais du portail ou via une API REST distincte pour les déplacements classiques. Les commandes de déplacement standard de Resource Manager ne fonctionnent pas lors du déplacement de ressources classiques vers un nouvel abonnement. Les étapes d’utilisation du portail ou de l’API REST sont décrites dans les sections ci-dessous.

## Utilisation du portail pour déplacer des ressources

Vous pouvez déplacer des ressources via le portail ; cependant, tous les fournisseurs de ressources qui prennent en charge l’opération de déplacement ne fournissent pas cette fonctionnalité via le portail.

Pour déplacer une ressource, sélectionnez la ressource, puis cliquez sur le bouton **Déplacer**.

![Déplacer la ressource](./media/resource-group-move-resources/move-resources.png)

Vous spécifiez l’abonnement de destination et le groupe de ressources lors du déplacement de la ressource. Si d’autres ressources doivent être déplacées avec la ressource, celles-ci sont répertoriées.

![Sélectionner la destination](./media/resource-group-move-resources/select-destination.png)

Dans **Notifications**, vous verrez que l’opération de déplacement est en cours d’exécution.

![afficher l’état du déplacement](./media/resource-group-move-resources/show-status.png)

Lorsqu’elle est terminée, vous êtes informé du résultat.

![afficher les résultats du déplacement](./media/resource-group-move-resources/show-result.png)

Pour une autre option de déplacement des ressources vers un nouveau groupe de ressources (mais pas d’abonnement), sélectionnez la ressource que vous souhaitez déplacer.

![sélectionner la ressource à déplacer](./media/resource-group-move-resources/select-resource.png)

Sélectionnez ses **Propriétés**.

![sélectionner les propriétés](./media/resource-group-move-resources/select-properties.png)

Si elle est disponible pour ce type de ressource, sélectionnez **Modifier le groupe de ressources**.

![modifier le groupe de ressources](./media/resource-group-move-resources/change-resource-group.png)

Vous pouvez sélectionner les ressources à déplacer ainsi que le groupe de ressources vers lequel les déplacer.

![déplacer des ressources](./media/resource-group-move-resources/select-group.png)

Lorsque vous déplacez des ressources déployées via le modèle classique vers un nouveau groupe de ressources, vous pouvez utiliser l’icône Modifier à côté du nom du groupe de ressources.

![déplacer des ressources classiques](./media/resource-group-move-resources/edit-rg-icon.png)

Sélectionnez les ressources à déplacer tout en gardant à l’esprit les [Limitations relatives au déploiement classique](#classic-deployment-limitations). Sélectionnez **OK** pour lancer le déplacement.

 ![sélectionner des ressources classiques](./media/resource-group-move-resources/select-classic-resources.png)
 
 Lorsque vous déplacez des ressources déployées via le modèle classique vers un nouvel abonnement, utilisez l’icône Modifier à côté de l’abonnement.
 
 ![déplacer vers un nouvel abonnement](./media/resource-group-move-resources/edit-subscription-icon.png)
 
 Toutes les ressources classiques sont automatiquement sélectionnées pour le déplacement.

## Utilisation de PowerShell pour déplacer des ressources

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, utilisez la commande **Move-AzureRmResource**.

Le premier exemple vous indique comment déplacer une ressource vers un nouveau groupe de ressources.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

Le second exemple vous indique comment déplacer plusieurs ressources vers un nouveau groupe de ressources.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Pour déplacer des ressources vers un nouvel abonnement, renseignez une valeur pour le paramètre **DestinationSubscriptionId**.

Vous devrez confirmer que vous souhaitez déplacer les ressources spécifiées.

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## Utilisation d’Azure CLI pour déplacer des ressources

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez la commande **azure resource move**. L’exemple suivant montre comment déplacer un cache Redis vers un nouveau groupe de ressources. Dans le paramètre **-i**, spécifiez une liste séparée par des virgules des ID des ressources à déplacer.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
	
Vous devrez confirmer que vous souhaitez déplacer la ressource spécifiée.
	
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## Utilisation de l’API REST pour déplacer des ressources

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez :

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Dans le corps de la requête, vous indiquez le groupe de ressources cible et les ressources à déplacer. Pour plus d’informations sur l’opération REST de déplacement, consultez [Déplacer des ressources](https://msdn.microsoft.com/library/azure/mt218710.aspx).

Toutefois, pour déplacer **des ressources classiques vers un nouvel abonnement**, vous devez utiliser des opérations REST différentes. Pour vérifier si un abonnement peut participer en tant qu’abonnement source ou cible dans un déplacement de ressources classiques entre abonnements, utilisez l’opération suivante :

    POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
Pour l’abonnement source, utilisez le corps de requête :

    {
        "role": "source"
    }

Pour l’abonnement cible, utilisez le corps de requête :

    {
        "role": "target"
    }

La réponse pour les deux opérations de validation est :

    {
        "status": "{status}",
        "reasons": [
            "reason1",
            "reason2"
        ]
    }

Pour déplacer toutes les ressources classiques d’un abonnement à un autre, utilisez l’opération suivante :

    POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

Avec le corps de requête :

    {
        "target": "/subscriptions/{target-subscription-id}"
    }



## Étapes suivantes
- Pour plus d'informations sur les applets de commande PowerShell afin de gérer votre abonnement, consultez la rubrique [Utilisation d'Azure PowerShell avec Resource Manager](powershell-azure-resource-manager.md).
- Pour plus d'informations sur les commandes Azure CLU afin de gérer votre abonnement, consultez la rubrique [Utilisation d'Azure CLI avec Resource Manager](xplat-cli-azure-resource-manager.md).
- Pour plus d'informations sur les fonctionnalités du portail afin de gérer votre abonnement, consultez la rubrique [Utilisation du portail Azure pour gérer les ressources](./azure-portal/resource-group-portal.md).
- Pour plus d'informations sur l'application d'une organisation logique à vos ressources, consultez la rubrique [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).

<!---HONumber=AcomDC_0518_2016-->