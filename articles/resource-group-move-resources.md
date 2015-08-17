<properties 
	pageTitle="Déplacer les ressources vers un nouveau groupe de ressources" 
	description="Utilisez Azure PowerShell ou une API REST pour déplacer des ressources vers un nouveau groupe de ressources pour Azure Resource Manager." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="tomfitz"/>

# Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement

Cette rubrique vous indique comment déplacer des ressources d’un groupe de ressources vers un autre groupe de ressources. Il est également possible de déplacer les ressources vers un nouvel abonnement. Vous pouvez envisager de déplacer des ressources dans les cas de figure suivants :

1. À des fins de facturation, une ressource doit être placée au sein d’un abonnement distinct.
2. Une ressource ne partage plus le cycle de vie des ressources avec lesquelles elle était précédemment groupée. Vous souhaitez déplacer la ressource vers un nouveau groupe, afin d’être en mesure de la gérer séparément des autres ressources.
3. Une ressource partage désormais le cycle de vie d’autres ressources au sein d’un groupe de ressources différent. Vous souhaitez déplacer la ressource vers un nouveau groupe, afin d’être en mesure de procéder à une gestion partagée.

Plusieurs points sont à prendre en compte lors du déplacement d’une ressource :

1. Vous ne pouvez pas modifier l’emplacement de la ressource. Le déplacement d’une ressource consiste uniquement en sa translation vers un nouveau groupe de ressources. Le nouveau groupe de ressources peut présenter à un autre emplacement, mais l’emplacement de la ressource n’est aucunement modifié.
2. Le groupe de ressources de destination doit comporter uniquement des ressources qui partagent le cycle de vie des ressources que vous déplacez.
3. Si vous utilisez Azure PowerShell, assurez-vous de disposer de la version la plus récente. La commande **Move-AzureResource** est fréquemment mise à jour. Pour mettre à jour votre version, exécutez Microsoft Web Platform Installer et vérifiez si une nouvelle version est disponible. Pour plus d’informations, consultez la rubrique [Comment installer et configurer Azure PowerShell](powershell-install-configure.md).
4. L’opération de déplacement peut prendre un certain temps. Durant cet intervalle, votre invite PowerShell est mise en attente.

## Services pris en charge

Actuellement, tous les services ne permettent pas de déplacer les ressources.

Pour l’instant, les services à partir desquels il est possible de déplacer les ressources vers un nouveau groupe de ressources et un nouvel abonnement sont les suivants :

- Gestion des API
- Azure Search
- Data Factory
- Key Vault
- Mobile Engagement
- Operational Insights
- Cache Redis
- Azure Web Apps (certaines [limitations](app-service-web/app-service-move-resources.md) s'appliquent)

Les services qui prennent en charge le déplacement des ressources vers un nouveau groupe de ressources mais pas vers un nouvel abonnement sont les suivants :

- Calcul (classique)
- Storage (classique)

Les services qui ne prennent actuellement pas en charge le déplacement d’une ressource sont les suivants :

- Réseaux virtuels

## Utilisation de PowerShell pour déplacer des ressources

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez la commande **Move-AzureResource**.

Le premier exemple vous indique comment déplacer une ressource vers un nouveau groupe de ressources.

    PS C:\> Move-AzureResource -DestinationResourceGroupName TestRG -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OtherExample/providers/Microsoft.ClassicStorage/storageAccounts/examplestorage

Le second exemple vous indique comment déplacer plusieurs ressources vers un nouveau groupe de ressources.

    PS C:\> $webapp = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExampleSite -ResourceType Microsoft.Web/sites
    PS C:\> $plan = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExamplePlan -ResourceType Microsoft.Web/serverFarms
    PS C:\> Move-AzureResource -DestinationResourceGroupName NewRG -ResourceId ($webapp.ResourceId, $plan.ResourceId)

Pour déplacer les ressources vers un nouvel abonnement, renseignez une valeur pour le paramètre **DestinationSubscriptionId**.

## Utilisation de l’API REST pour déplacer des ressources

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez :

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Remplacez **{source-subscription-id}** et **{source-resource-group-name}** par l'abonnement et le groupe de ressource qui comportent actuellement les ressources que vous souhaitez déplacer. Utilisez **2015-01-01** pour {api-version}.

Dans la requête, incluez un objet JSON qui définit le groupe de ressources cible et les ressources que vous souhaitez déplacer.

    {
        "targetResourceGroup": "/subscriptions/{target-subscription-id}/resourceGroups/{target-resource-group-name}", "resources": [
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}"
        ]
    }

## Étapes suivantes
- [Utilisation d’Azure PowerShell avec Azure Resource Manager](./powershell-azure-resource-manager.md)
- [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Manager](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Utilisation du portail Azure en version préliminaire pour gérer les ressources Azure](azure-portal/resource-group-portal.md)
- [Organisation des ressources Azure à l’aide de balises](./resource-group-using-tags.md)

<!---HONumber=August15_HO6-->