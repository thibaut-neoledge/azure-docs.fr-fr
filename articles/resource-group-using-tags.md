<properties
	pageTitle="Organisation des ressources Azure à l’aide de balises | Microsoft Azure"
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
	ms.date="12/02/2015"
	ms.author="tomfitz"/>


# Organisation des ressources Azure à l'aide de balises

Resource Manager vous permet d'organiser logiquement les ressources en appliquant des balises. Les balises sont constituées de paires clé/valeur qui identifient les ressources avec les propriétés que vous définissez. Pour marquer des ressources comme appartenant à la même catégorie, appliquez la même balise à ces ressources.

Lorsque vous affichez des ressources avec une balise particulière, vous voyez les ressources de tous vos groupes de ressources. Vous n'êtes pas limité aux seules ressources d’un même groupe de ressources, ce qui vous permet d'organiser vos ressources de manière indépendante des relations de déploiement. Les balises peuvent être particulièrement utiles si vous devez organiser les ressources à des fins de facturation ou de gestion.

Chaque balise que vous ajoutez à une ressource ou à un groupe de ressources est automatiquement ajoutée à la classification sur l'ensemble de l'abonnement. Vous pouvez également préremplir cette taxinomie pour votre abonnement en saisissant des noms et des valeurs de balises que vous souhaitez utiliser pour le balisage de vos ressources.

Chaque ressource ou groupe de ressources peut inclure un maximum de 15 balises. Le nom de balise est limité à 512 caractères, et la valeur de balise à 256 caractères.

> [AZURE.NOTE] Vous ne pouvez appliquer des balises qu’à des ressources qui prennent en charge les opérations de Resource Manager. Si vous avez créé une machine virtuelle, un réseau virtuel ou un stockage par le biais du modèle de déploiement classique (telles que via le portail Azure ou l’[API Service Management](../services/api-management/)), vous ne pouvez pas appliquer de balise à cette ressource. Vous devez redéployer ces ressources via Resource Manager pour prendre en charge le balisage. Toutes les autres ressources prennent en charge le balisage.

## Balises dans les modèles

Il est très facile d'ajouter une balise dans une ressource pendant le déploiement. Ajoutez simplement l’élément **tags** dans la ressource que vous déployez et fournissez le nom et la valeur. Le nom et la valeur de la balise n’ont pas besoin d’exister préalablement dans votre abonnement. Vous pouvez fournir 15 balises au maximum pour chaque ressource.

L’exemple suivant illustre un compte de stockage avec une balise.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

## Balises dans le portail Azure

Il est facile de baliser des ressources et des groupes de ressources dans le portail. Utilisez le concentrateur Parcourir pour accéder à la ressource ou au groupe de ressources que vous souhaitez baliser, puis cliquez sur la partie Balises de la section Vue d'ensemble, située en haut du volet.

![Balises des panneaux de ressources et de groupe de ressources](./media/resource-group-using-tags/tag-icon.png)

Un volet s'ouvre. Il contient la liste des balises qui ont déjà été appliquées. S'il s'agit de votre première balise, la liste sera vide. Pour ajouter une balise, indiquez simplement un nom et une valeur, puis appuyez sur ENTRÉE. Après avoir ajouté quelques balises, vous remarquerez que les options de saisie semi-automatique basées sur les noms et les valeurs des balises existantes améliorent la cohérence de l'organisation de vos ressources, tout en évitant des erreurs courantes, comme les fautes d'orthographe.

![Baliser des ressources avec des paires nom/valeur](./media/resource-group-using-tags/tag-resources.png)

Pour afficher votre classification de balises dans le portail, utilisez le hub Parcourir pour afficher Tous les éléments, puis sélectionnez Balises.

![Rechercher des balises via le hub Parcourir](./media/resource-group-using-tags/browse-tags.png)

Épinglez les balises plus importantes à votre tableau d'accueil pour y accéder rapidement et vous serez prêt. Amusez-vous bien !

![Épingler des balises au tableau d'accueil](./media/resource-group-using-tags/pin-tags.png)

## Balisage avec PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

Les balises se trouvent directement sur les ressources et les groupes de ressources. Pour savoir quelles balises sont déjà appliquées, il suffit de récupérer une ressource ou un groupe de ressources avec **Get-AzureRmResource** ou **Get-AzureRmResourceGroup**. Commençons par un groupe de ressources.

    PS C:\> Get-AzureRmResourceGroup tag-demo

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


Cette cmdlet renvoie plusieurs octets de métadonnées sur le groupe de ressources, notamment les balises déjà appliquées, le cas échéant. Pour baliser un groupe de ressources, utilisez simplement la commande **Set-AzureRmResourceGroup**, puis indiquez un nom et une valeur de balise.

    PS C:\> Set-AzureRmResourceGroup tag-demo -Tag @( @{ Name="project"; Value="tags" }, @{ Name="env"; Value="demo"} )

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  =====
                    project  tags
                    env      demo

Les balises sont mises à jour en tant qu'ensemble, donc si vous ajoutez une balise à une ressource déjà balisée, vous devrez utiliser un tableau avec toutes les balises que vous souhaitez conserver. Pour ce faire, vous pouvez tout d'abord sélectionner les balises existantes et ajouter une nouvelle.

    PS C:\> $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureRmResourceGroup tag-demo -Tag $tags

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

Le processus est le même pour les ressources, sauf que vous utilisez les applets de commande **Get-AzureRmResource** et **Set-AzureRmResource**.

Pour récupérer des ressources ou des groupes de ressources avec une balise spécifique, utilisez l’applet de commande **Find-AzureRmResourceGroup** avec le paramètre **-Tag**.

    PS C:\> Find-AzureRmResourceGroup -Tag @{ Name="env"; Value="demo" } | %{ $_.ResourceGroupName }
    rbacdemo-group
    tag-demo

Pour les versions d’Azure PowerShell antérieures à la version 1.0, utilisez les commandes suivantes pour obtenir les ressources avec une balise particulière.

    PS C:\> Get-AzureResourceGroup -Tag @{ Name="env"; Value="demo" } | %{ $_.ResourceGroupName }
    rbacdemo-group
    tag-demo
    PS C:\> Get-AzureResource -Tag @{ Name="env"; Value="demo" } | %{ $_.Name }
    rbacdemo-web
    rbacdemo-docdb
    ...    

Pour obtenir une liste de toutes les balises d’un abonnement à l’aide de PowerShell, utilisez l’applet de commande **Get-AzureRmTag**.

    PS C:/> Get-AzureRmTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

Vous pouvez consulter les balises commençant par « masqué-» et « lien: ». Il s'agit de balises internes, que vous devez ignorer et éviter de modifier.

Pour ajouter des balises à la taxonomie, utilisez l’applet de commande **New-AzureRmTag**. Ces balises seront incluses dans la saisie semi-automatique, même si elles n'ont pas encore été appliquées à des ressources ou des groupes de ressources. Pour supprimer un nom ou une valeur de balise, commencez par supprimer la balise sur toutes les ressources où elle est appliquée, puis utilisez l’applet de commande **Remove-AzureRmTag** pour la supprimer de la taxonomie.

## Balisage avec une API REST

Le portail et PowerShell utilisent tous deux l'[API REST du Gestionnaire de ressources](https://msdn.microsoft.com/library/azure/dn848368.aspx) en arrière-plan. Si vous avez besoin intégrer le balisage dans un autre environnement, vous pouvez récupérer des balises avec une commande GET sur l'ID de ressource et mettre à jour l'ensemble des balises avec un appel PATCH.


## Balisage et facturation

Dans le cas des services pris en charge, vous pouvez utiliser des balises pour regrouper vos données de facturation. Par exemple, les [machines virtuelles intégrées à Azure Resource Manager](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md) vous permettent de définir et d’appliquer des balises pour organiser l’utilisation de la facturation pour les machines virtuelles. Si vous exécutez plusieurs machines virtuelles pour différentes organisations, vous pouvez recourir aux balises pour regrouper l’utilisation par centre de coûts. Vous pouvez également utiliser des balises pour catégoriser les coûts par environnement d’exécution ; par exemple, l’utilisation de la facturation pour les machines virtuelles en cours d’exécution dans l’environnement de production.

Vous pouvez récupérer des informations sur les balises par le biais des [API Resource Usage et RateCard](billing-usage-rate-card-overview.md) ou du fichier de valeurs séparées par des virgules (CSV) que vous pouvez télécharger à partir du [portail de comptes Azure](https://account.windowsazure.com/) ou du [portail EA](https://ea.azure.com). Pour plus d’informations sur l’accès par programme aux informations de facturation, consultez [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure](billing-usage-rate-card-overview.md). Pour plus d’informations sur les opérations de l’API REST, consultez [Informations de référence sur l’API REST Azure Billing](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Lorsque vous téléchargez le fichier CSV d’utilisation pour les services qui prennent en charge les balises avec la facturation, les balises s’affichent dans la colonne **Balises**. Pour plus d’informations, voir [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md).

![Voir les balises dans la facturation](./media/resource-group-using-tags/billing_csv.png)

## Étapes suivantes

- Vous pouvez appliquer des restrictions et des conventions sur votre abonnement avec des stratégies personnalisées. La stratégie que vous définissez peut exiger la définition d'une balise spécifique pour toutes les ressources. Pour plus d’informations, consultez [Utiliser le service Policy pour gérer les ressources et contrôler l’accès](resource-manager-policy.md).
- Pour plus d’informations sur l’utilisation d’Azure PowerShell lors du déploiement de ressources, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](./powershell-azure-resource-manager.md).
- Si vous n’avez jamais utilisé l’interface de ligne de commande Azure pour le déploiement de ressources, consultez [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](./xplat-cli-azure-resource-manager.md).
- Pour plus d’informations sur l’utilisation du portail, consultez [Utilisation du portail Azure pour gérer vos ressources Azure](./resource-group-portal.md).  

<!---HONumber=AcomDC_0128_2016-->