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
	ms.date="12/18/2015" 
	ms.author="tomfitz"/>

# Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement

Cette rubrique vous indique comment déplacer des ressources d’un groupe de ressources vers un autre groupe de ressources. Il est également possible de déplacer les ressources vers un nouvel abonnement. Vous pouvez envisager de déplacer des ressources dans les cas de figure suivants :

1. À des fins de facturation, une ressource doit être placée au sein d’un abonnement distinct.
2. Une ressource ne partage plus le cycle de vie des ressources avec lesquelles elle était précédemment groupée. Vous souhaitez déplacer la ressource vers un nouveau groupe, afin d’être en mesure de la gérer séparément des autres ressources.
3. Une ressource partage désormais le cycle de vie d’autres ressources au sein d’un groupe de ressources différent. Vous souhaitez déplacer la ressource vers un nouveau groupe, afin d’être en mesure de procéder à une gestion partagée.

Plusieurs points sont à prendre en compte lors du déplacement d’une ressource :

1. Vous ne pouvez pas modifier l’emplacement de la ressource. Le déplacement d’une ressource consiste uniquement en sa translation vers un nouveau groupe de ressources. Le nouveau groupe de ressources peut présenter à un autre emplacement, mais l’emplacement de la ressource n’est aucunement modifié.
2. Le groupe de ressources de destination doit comporter uniquement des ressources qui partagent le cycle de vie des ressources que vous déplacez.
3. Si vous utilisez Azure PowerShell ou Azure CLI, assurez-vous de disposer de la version la plus récente. Pour mettre à jour votre version, exécutez Microsoft Web Platform Installer et vérifiez si une nouvelle version est disponible. Pour plus d’informations, consultez [Comment installer et configurer Azure PowerShell](powershell-install-configure.md) et [Installer Azure CLI](xplat-cli-install.md).
4. L’opération de déplacement peut prendre un certain temps. Durant cet intervalle, votre invite attend que l’opération se termine.
5. Lorsque vous déplacez des ressources, le groupe source et le groupe cible sont verrouillés pendant la durée de l'opération. Les opérations d’écriture et de suppression sont bloquées sur les groupes tant que le déplacement n’est pas terminé.

## Services pris en charge

Actuellement, tous les services ne permettent pas de déplacer les ressources.

Pour l’instant, les services à partir desquels il est possible de déplacer les ressources vers un nouveau groupe de ressources et un nouvel abonnement sont les suivants :

- API Management
- Automation
- Batch
- Data Factory
- DocumentDB
- Clusters HDInsight
- Key Vault
- Logic Apps
- Mobile Engagement
- Notification Hubs
- Operational Insights
- Cache Redis
- Search
- SQL Database
- Web Apps (certaines [limitations](app-service-web/app-service-move-resources.md) s’appliquent)

Les services qui prennent en charge le déplacement des ressources vers un nouveau groupe de ressources mais pas vers un nouvel abonnement sont les suivants :

- Machines virtuelles (classique)
- Storage (classique)

Les services qui ne prennent actuellement pas en charge le déplacement d’une ressource sont les suivants :

- Machines virtuelles
- Virtual Network
- Storage

Lorsque vous travaillez avec des applications web, vous ne pouvez pas déplacer uniquement un plan App Service. Pour déplacer des applications web, les options disponibles sont :

- Déplacer toutes les ressources d'un groupe de ressources à un autre, si le groupe de ressources de destination n'a pas déjà de ressources Microsoft.Web.
- Déplacer les applications web vers un autre groupe de ressources, mais conserver le plan App Service dans le groupe de ressources d'origine.

## Utilisation de PowerShell pour déplacer des ressources

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, utilisez la commande **Move-AzureRmResource**.

Le premier exemple vous indique comment déplacer une ressource vers un nouveau groupe de ressources.

    PS C:\> $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

Le second exemple vous indique comment déplacer plusieurs ressources vers un nouveau groupe de ressources.

    PS C:\> $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    PS C:\> $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId ($webapp.ResourceId, $plan.ResourceId)

Pour déplacer des ressources vers un nouvel abonnement, renseignez une valeur pour le paramètre **DestinationSubscriptionId**.

## Utilisation d’Azure CLI pour déplacer des ressources

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez la commande **azure resource move**. L’exemple suivant montre comment déplacer un cache Redis vers un nouveau groupe de ressources. Dans le paramètre **-i**, spécifiez une liste séparée par des virgules des ID des ressources à déplacer.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## Utilisation de l’API REST pour déplacer des ressources

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez :

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Dans le corps de la requête, vous indiquez le groupe de ressources cible et les ressources à déplacer. Pour plus d’informations sur l’opération REST de déplacement, consultez [Déplacer des ressources](https://msdn.microsoft.com/library/azure/mt218710.aspx).

## Étapes suivantes
- [Utilisation d’Azure PowerShell avec Azure Resource Manager](./powershell-azure-resource-manager.md)
- [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Manager](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Utilisation du portail Azure en version préliminaire pour gérer les ressources Azure](azure-portal/resource-group-portal.md)
- [Organisation des ressources Azure à l’aide de balises](./resource-group-using-tags.md)

<!---HONumber=AcomDC_1223_2015-->