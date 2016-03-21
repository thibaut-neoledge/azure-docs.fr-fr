<properties 
	pageTitle="Déplacer les ressources vers un nouveau groupe de ressources" 
	description="Utilisez Azure PowerShell ou une API REST pour déplacer des ressources vers un nouveau groupe de ressources pour Azure Resource Manager." 
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
	ms.date="03/04/2016" 
	ms.author="tomfitz"/>

# Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement

Cette rubrique vous indique comment déplacer des ressources d’un groupe de ressources vers un autre groupe de ressources. Vous pouvez également déplacer des ressources vers un nouvel abonnement (cependant, l’abonnement doit se trouver dans le même [client](./active-directory/active-directory-howto-tenant.md)). Vous pouvez envisager de déplacer des ressources dans les cas de figure suivants :

1. À des fins de facturation, une ressource doit être placée au sein d’un abonnement distinct.
2. Une ressource ne partage plus le cycle de vie des ressources avec lesquelles elle était précédemment groupée. Vous souhaitez déplacer la ressource vers un nouveau groupe, afin d’être en mesure de la gérer séparément des autres ressources.
3. Une ressource partage désormais le cycle de vie d’autres ressources au sein d’un groupe de ressources différent. Vous souhaitez déplacer la ressource vers un nouveau groupe, afin d’être en mesure de procéder à une gestion partagée.

## Éléments à prendre en considération avant le déplacement de ressources

Plusieurs problèmes importants doivent être pris en considération avant de déplacer une ressource :

1. Vous ne pouvez pas modifier l’emplacement de la ressource. Le déplacement d’une ressource consiste uniquement en sa translation vers un nouveau groupe de ressources. Le nouveau groupe de ressources peut présenter à un autre emplacement, mais l’emplacement de la ressource n’est aucunement modifié.
2. Le fournisseur de la ressource déplacée doit être inscrit dans l’abonnement de destination. Vous pouvez rencontrer ce problème lors du déplacement d’une ressource vers un nouvel abonnement qui n’a jamais été utilisé avec ce type de ressource. Par exemple, si vous déplacez une instance du service Gestion des API vers un abonnement dans lequel le fournisseur de ressources **Microsoft.ApiManagement** n’a pas été inscrit, le déplacement échouera. Pour savoir comment vérifier l’état d’inscription et inscrire des fournisseurs de ressources, consultez [Fournisseurs et types de ressources](../resource-manager-supported-services/#resource-providers-and-types).
2. Le groupe de ressources de destination doit comporter uniquement des ressources qui partagent le cycle de vie des ressources que vous déplacez.
3. Si vous utilisez Azure PowerShell ou Azure CLI, assurez-vous de disposer de la version la plus récente. Pour mettre à jour votre version, exécutez Microsoft Web Platform Installer et vérifiez si une nouvelle version est disponible. Pour plus d’informations, consultez [Comment installer et configurer Azure PowerShell](powershell-install-configure.md) et [Installer Azure CLI](xplat-cli-install.md).
4. L’opération de déplacement peut prendre un certain temps. Durant cet intervalle, votre invite attend que l’opération se termine.
5. Lorsque vous déplacez des ressources, le groupe source et le groupe cible sont verrouillés pendant la durée de l'opération. Les opérations d’écriture et de suppression sont bloquées sur les groupes tant que le déplacement n’est pas terminé.

## Services pris en charge

Actuellement, tous les services ne permettent pas de déplacer les ressources.

Pour l’instant, les services à partir desquels il est possible de déplacer les ressources vers un nouveau groupe de ressources et un nouvel abonnement sont les suivants :

- API Management
- Applications App Service (consultez la section [Limitations d’App Service](#app-service-limitations) ci-après)
- Automation
- Batch
- Data Factory
- DocumentDB
- Clusters HDInsight
- Key Vault
- Mobile Engagement
- Notification Hubs
- Operational Insights
- Cache Redis
- Search
- Serveur de base de données SQL (consultez la section [Limitations des bases de données SQL](#sql-database-limitations) ci-après)

Les services qui prennent en charge le déplacement des ressources vers un nouveau groupe de ressources mais pas vers un nouvel abonnement sont les suivants :

- Machines virtuelles (classique)
- Storage (classique)
- Virtual Network
- Microsoft Azure

Les services qui ne prennent actuellement pas en charge le déplacement d’une ressource sont les suivants :

- Machines virtuelles
- Storage
- ExpressRoute

## Limitations d’App Service

Lorsque vous travaillez avec des applications App Service, vous ne pouvez pas déplacer uniquement un plan App Service. Pour déplacer des applications App Service, les options disponibles sont :

- Déplacer toutes les ressources d'un groupe de ressources à un autre, si le groupe de ressources de destination n'a pas déjà de ressources Microsoft.Web.
- Déplacer les applications web vers un autre groupe de ressources, mais conserver le plan App Service dans le groupe de ressources d'origine.

## Limitations des bases de données SQL

Vous ne pouvez pas déplacer une base de données SQL séparément de son serveur. La base de données et le serveur doivent résider dans le même groupe de ressources. Lorsque vous déplacez un serveur SQL, toutes ses bases de données sont également déplacées.

## Utilisation de PowerShell pour déplacer des ressources

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, utilisez la commande **Move-AzureRmResource**.

Le premier exemple vous indique comment déplacer une ressource vers un nouveau groupe de ressources.

    PS C:\> $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

Le second exemple vous indique comment déplacer plusieurs ressources vers un nouveau groupe de ressources.

    PS C:\> $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    PS C:\> $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Pour déplacer des ressources vers un nouvel abonnement, renseignez une valeur pour le paramètre **DestinationSubscriptionId**.

## Utilisation d’Azure CLI pour déplacer des ressources

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez la commande **azure resource move**. L’exemple suivant montre comment déplacer un cache Redis vers un nouveau groupe de ressources. Dans le paramètre **-i**, spécifiez une liste séparée par des virgules des ID des ressources à déplacer.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## Utilisation de l’API REST pour déplacer des ressources

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez :

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Dans le corps de la requête, vous indiquez le groupe de ressources cible et les ressources à déplacer. Pour plus d’informations sur l’opération REST de déplacement, consultez [Déplacer des ressources](https://msdn.microsoft.com/library/azure/mt218710.aspx).

## Utilisation du portail pour déplacer des ressources

Vous pouvez déplacer des ressources via le portail ; cependant, tous les fournisseurs de ressources qui prennent en charge l’opération de déplacement ne fournissent pas cette fonctionnalité via le portail.

Pour déplacer une ressource, sélectionnez la ressource, puis cliquez sur le bouton **Déplacer**.

![Déplacer la ressource](./media/resource-group-move-resources/move-resources.png)

Spécifiez l’emplacement de destination de la ressource. Si d’autres ressources doivent être déplacées avec la ressource, celles-ci sont répertoriées.

![Sélectionner la destination](./media/resource-group-move-resources/select-destination.png)

## Étapes suivantes
- [Utilisation d’Azure PowerShell avec Azure Resource Manager](./powershell-azure-resource-manager.md)
- [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Manager](./xplat-cli-azure-resource-manager.md)
- [Utilisation du portail Azure en version préliminaire pour gérer les ressources Azure](azure-portal/resource-group-portal.md)
- [Organisation des ressources Azure à l’aide de balises](./resource-group-using-tags.md)

<!---------HONumber=AcomDC_0309_2016-->