<properties
	pageTitle="Organisation des ressources Azure à l'aide de balises"
	description="Indique comment appliquer des balises afin d’organiser des ressources dédiées à la facturation et à la gestion."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="AzurePortal"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/04/2015"
	ms.author="tomfitz"/>


# Organisation des ressources Azure à l'aide de balises

Resource Manager vous permet d'organiser logiquement les ressources en appliquant des balises. Les balises sont constituées de paires clé/valeur qui identifient les ressources avec les propriétés que vous définissez. Pour marquer des ressources comme appartenant à la même catégorie, appliquez la même balise à ces ressources.

Lorsque vous affichez des ressources avec une balise particulière, vous voyez les ressources de tous vos groupes de ressources. Vous n'êtes pas limité aux seules ressources d’un même groupe de ressources, ce qui vous permet d'organiser vos ressources de manière indépendante des relations de déploiement. Les balises peuvent être particulièrement utiles si vous devez organiser les ressources à des fins de facturation ou de gestion.

Chaque balise que vous ajoutez à une ressource ou à un groupe de ressources est automatiquement ajoutée à la classification sur l'ensemble de l'abonnement. Vous pouvez également préremplir cette taxinomie pour votre abonnement en saisissant des noms et des valeurs de balises que vous souhaitez utiliser pour le balisage de vos ressources.

> [AZURE.NOTE]Vous ne pouvez appliquer des balises qu’à des ressources qui prennent en charge les opérations de Resource Manager. Si vous avez créé une machine virtuelle, un réseau virtuel ou un stockage par le biais du modèle de déploiement classique (par exemple, via le portail Azure ou l'[API Service Management](../services/api-management/)), vous ne pouvez pas appliquer de balise à cette ressource. Vous devez redéployer ces ressources via Resource Manager pour prendre en charge le balisage. Toutes les autres ressources prennent en charge le balisage.


## Balises de la version préliminaire du portail

Il est facile de baliser des ressources et des groupes de ressources dans la version préliminaire du portail. Utilisez le hub Parcourir pour accéder à la ressource ou au groupe de ressources que vous souhaitez baliser, puis cliquez sur la partie Balises de la section Vue d'ensemble, située en haut du volet.

![Balises des panneaux de ressources et de groupe de ressources](./media/resource-group-using-tags/tag-icon.png)

Un volet s'ouvre. Il contient la liste des balises qui ont déjà été appliquées. S'il s'agit de votre première balise, la liste sera vide. Pour ajouter une balise, indiquez simplement un nom et une valeur, puis appuyez sur ENTRÉE. Après avoir ajouté quelques balises, vous remarquerez que les options de saisie semi-automatique basées sur les noms et les valeurs des balises existantes améliorent la cohérence de l'organisation de vos ressources, tout en évitant des erreurs courantes, comme les fautes d'orthographe.

![Baliser des ressources avec des paires nom/valeur](./media/resource-group-using-tags/tag-resources.png)

Pour afficher votre classification de balises dans le portail, utilisez le hub Parcourir pour afficher Tous les éléments, puis sélectionnez Balises.

![Rechercher des balises via le hub Parcourir](./media/resource-group-using-tags/browse-tags.png)

Épinglez les balises plus importantes à votre tableau d'accueil pour y accéder rapidement et vous serez prêt. Amusez-vous bien !

![Épingler des balises au tableau d'accueil](./media/resource-group-using-tags/pin-tags.png)

## Balisage avec PowerShell

Si vous n’avez pas déjà utilisé Azure PowerShell avec Resource Manager, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md). Cet article part du principe que vous avez déjà ajouté un compte et sélectionné un abonnement avec les ressources que vous souhaitez baliser.

Le balisage est uniquement disponible pour les ressources et les groupes de ressources disponibles dans le [Gestionnaire de ressources](http://msdn.microsoft.com/library/azure/dn790568.aspx). La prochaine chose à faire consiste à passer au Gestionnaire de ressources.

    Switch-AzureMode AzureResourceManager

Les balises se trouvent directement sur les ressources et les groupes de ressources. Pour savoir quelles balises sont déjà appliquées, il suffit de récupérer une ressource ou un groupe de ressources avec `Get-AzureResource` ou `Get-AzureResourceGroup`, le cas échéant. Commençons par un groupe de ressources.

    PS C:\> Get-AzureResourceGroup tag-demo

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    Resources         :
                    Name                             Type                                  Location
                    ===============================  ====================================  ==============
                    CPUHigh ExamplePlan              microsoft.insights/alertrules         eastus
                    ForbiddenRequests tag-demo-site  microsoft.insights/alertrules         eastus
                    LongHttpQueue ExamplePlan        microsoft.insights/alertrules         eastus
                    ServerErrors tag-demo-site       microsoft.insights/alertrules         eastus
                    ExamplePlan-tag-demo             microsoft.insights/autoscalesettings  eastus
                    tag-demo-site                    microsoft.insights/components         centralus
                    ExamplePlan                      Microsoft.Web/serverFarms             southcentralus
                    tag-demo-site                    Microsoft.Web/sites                   southcentralus


Cette cmdlet renvoie plusieurs octets de métadonnées sur le groupe de ressources, notamment les balises déjà appliquées, le cas échéant. Pour baliser un groupe de ressources, utilisez simplement la commande `Set-AzureResourceGroup` avant d'indiquer un nom et une valeur de balise.

    PS C:\> Set-AzureResourceGroup tag-demo -Tag @( @{ Name="project"; Value="tags" }, @{ Name="env"; Value="demo"} )

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  =====
                    project  tags
                    env      demo

Les balises sont mises à jour en tant qu'ensemble, donc si vous ajoutez une balise à une ressource déjà balisée, vous devrez utiliser un tableau avec toutes les balises que vous souhaitez conserver. Pour ce faire, vous pouvez tout d'abord sélectionner les balises existantes et ajouter une nouvelle.

    PS C:\> $tags = (Get-AzureResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureResourceGroup tag-demo -Tag $tags

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  ========
                    project  tags
                    env      demo
                    status   approved


Pour supprimer une ou plusieurs balises, enregistrez simplement le tableau sans les balises que vous souhaitez supprimer.

Le processus est le même pour les ressources, sauf que vous allez utiliser les applets de commande `Get-AzureResource` et `Set-AzureResource`. Pour récupérer des ressources ou des groupes de ressources avec une balise spécifique, utilisez l'applet de commande `Get-AzureResource` ou `Get-AzureResourceGroup` avec le paramètre `-Tag`.

    PS C:\> Get-AzureResourceGroup -Tag @{ Name="env"; Value="demo" } | %{ $_.ResourceGroupName }
    rbacdemo-group
    tag-demo
    PS C:\> Get-AzureResource -Tag @{ Name="env"; Value="demo" } | %{ $_.Name }
    rbacdemo-web
    rbacdemo-docdb
    ...

Pour obtenir une liste de toutes les balises d'un abonnement à l'aide de PowerShell, utilisez l'applet de commande `Get-AzureTag`.

    PS C:/> Get-AzureTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

Vous pouvez consulter les balises commençant par « masqué-» et « lien: ». Il s'agit de balises internes, que vous devez ignorer et éviter de modifier.

Utilisez l'applet de commande `New-AzureTag` pour ajouter des balises à la classification. Ces balises seront incluses dans la saisie semi-automatique, même si elles n'ont pas encore été appliquées à des ressources ou des groupes de ressources. Pour supprimer un nom ou une valeur de balise, commencez par supprimer la balise sur toutes les ressources où elle est appliquée, puis utilisez l'applet de commande `Remove-AzureTag` pour la supprimer de la classification.

## Balisage avec une API REST

Le portail et PowerShell utilisent tous deux l'[API REST du Gestionnaire de ressources](http://msdn.microsoft.com/library/azure/dn790568.aspx) en arrière-plan. Si vous avez besoin intégrer le balisage dans un autre environnement, vous pouvez récupérer des balises avec une commande GET sur l'ID de ressource et mettre à jour l'ensemble des balises avec un appel PATCH.


## Balisage et facturation

Dans le cas des services pris en charge, vous pouvez utiliser des balises pour regrouper vos données de facturation. Par exemple, les [machines virtuelles intégrées à Azure Resource Manager](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md) vous permettent de définir et d’appliquer des balises pour organiser l’utilisation de la facturation pour les machines virtuelles. Si vous exécutez plusieurs machines virtuelles pour différentes organisations, vous pouvez recourir aux balises pour regrouper l’utilisation par centre de coûts. Vous pouvez également utiliser des balises pour catégoriser les coûts par environnement d’exécution ; par exemple, l’utilisation de la facturation pour les machines virtuelles en cours d’exécution dans l’environnement de production.

Vous pouvez récupérer des informations sur les balises par le biais des [API Resource Usage et RateCard](billing-usage-rate-card-overview.md) ou du fichier de valeurs séparées par des virgules (CSV) que vous pouvez télécharger à partir du [portail de comptes Azure](https://account.windowsazure.com/) ou du [portail EA](https://ea.azure.com). Pour plus d'informations sur l'accès par programme aux informations de facturation, consultez [Obtenir une vue d'ensemble de votre consommation des ressources Microsoft Azure](billing-usage-rate-card-overview.md). Pour plus d’informations sur les opérations de l’API REST, consultez [Informations de référence sur l’API REST Azure Billing](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Lorsque vous téléchargez le fichier CSV d’utilisation pour les services qui prennent en charge les balises avec la facturation, les balises s’affichent dans la colonne **Balises**. Pour plus d’informations, voir [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md).

![Voir les balises dans la facturation](./media/resource-group-using-tags/billing_csv.png)

## Étapes suivantes

- Pour plus d’informations sur l’utilisation d’Azure PowerShell pendant le déploiement de ressources, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](./powershell-azure-resource-manager.md).
- Si vous n’avez jamais utilisé Azure CLI pour le déploiement de ressources, consultez [Utilisation d’Azure CLI pour Mac, Linux et Windows avec Azure Resource Management](./xplat-cli-azure-resource-manager.md).
- Pour plus d’informations sur l’utilisation du portail en version préliminaire, consultez [Utilisation du portail Azure en version préliminaire pour gérer vos ressources Azure](./resource-group-portal.md).  

<!---HONumber=Oct15_HO1-->