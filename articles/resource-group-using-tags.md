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
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>


# Organisation des ressources Azure à l'aide de balises

Le portail Azure et le Gestionnaire de ressources sous-jacentes vont organiser vos ressources et personnaliser votre expérience exactement selon vos besoins.

Dans le portail Azure classique, les abonnements sont le seul moyen de classer et regrouper vos ressources. Avec le portail Azure, [nous avons introduit des groupes de ressources](./resource-group-portal.md) qui vous permettent de regrouper les entités associées. Cet avantage s'est accentué lorsque [nous avons introduit l'accès basé sur les rôles](./role-based-access-control-configure.md). À présent, dans cette même optique, vous pouvez baliser vos ressources avec des paires de clé/valeur pour classer et afficher plus précisément vos ressources entre les différents groupes de ressources et, dans le portail, entre les différents abonnements.

Regroupez vos ressources en fonction de vos équipes, de vos projets ou même de vos environnements, pour vous concentrer sur ce que vous voulez afficher, lorsque vous en avez besoin.


## Balises dans le portail Azure

Il est facile de baliser des ressources et des groupes de ressources dans le portail. Utilisez le concentrateur Parcourir pour accéder à la ressource ou au groupe de ressources que vous souhaitez baliser, puis cliquez sur la partie Balises de la section Vue d'ensemble, située en haut du volet.

![Balises des panneaux de ressources et de groupe de ressources](./media/resource-group-using-tags/rgblade.png)

Un volet s'ouvre. Il contient la liste des balises qui ont déjà été appliquées. S'il s'agit de votre première balise, la liste sera vide. Pour ajouter une balise, indiquez simplement un nom et une valeur, puis appuyez sur ENTRÉE. Après avoir ajouté quelques balises, vous remarquerez que les options de saisie semi-automatique basées sur les noms et les valeurs des balises existantes améliorent la cohérence de l'organisation de vos ressources, tout en évitant des erreurs courantes, comme les fautes d'orthographe.

![Baliser des ressources avec des paires nom/valeur](./media/resource-group-using-tags/tag-resources.png)

À ce stade, vous pouvez cliquer sur chaque balise pour afficher une liste de toutes les ressources associées à la même balise. Bien sûr, si c'est votre première balise, cette liste ne sera pas très intéressante. Pour l'instant, passons à PowerShell pour baliser tous nos ressources rapidement.


## Balisage avec PowerShell

Tout d'abord, récupérez le tout dernier [module Azure PowerShell](./install-configure-powershell.md). Si vous utilisez le module Azure PowerShell pour la première fois, [consultez la documentation](./install-configure-powershell.md) pour apprendre à l'utiliser. Cet article part du principe que vous avez déjà ajouté un compte et sélectionné un abonnement avec les ressources que vous souhaitez baliser.

Le balisage est uniquement disponible pour les ressources et les groupes de ressources disponibles dans le [Gestionnaire de ressources](http://msdn.microsoft.com/library/azure/dn790568.aspx). La prochaine chose à faire consiste à passer au Gestionnaire de ressources. Pour plus d'informations, consultez [Utilisation d'Azure PowerShell avec le Gestionnaire de ressources Azure](powershell-azure-resource-manager.md).

    Switch-AzureMode AzureResourceManager

Les balises se trouvent directement sur les ressources et les groupes de ressources. Pour savoir quelles balises sont déjà appliqués, il suffit de récupérer une ressource ou un groupe de ressources avec `Get-AzureResource` ou `Get-AzureResourceGroup`, le cas échéant. Commençons par un groupe de ressources.

![Obtention de balises avec Get-AzureResourceGroup dans PowerShell](./media/resource-group-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

Cette cmdlet renvoie plusieurs octets de métadonnées sur le groupe de ressources, notamment les balises déjà appliquées, le cas échéant. Pour baliser un groupe de ressources, utilisez simplement `Set-AzureResourceGroup` avant d'indiquer un nom et une valeur de balise.

![Définition de balises avec Set-AzureResourceGroup dans PowerShell](./media/resource-group-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

N'oubliez pas que les balises sont mises à jour en tant qu'ensemble, et que si vous ajoutez une balise à une ressource déjà balisée, vous devrez donc utiliser un tableau avec toutes les balises que vous souhaitez conserver. Pour supprimer une balise, enregistrez simplement le tableau sans la balise que vous souhaitez supprimer.

Le processus est le même pour les ressources, sauf que vous allez utiliser les applets de commande `Get-AzureResource` et `Set-AzureResource`. Pour récupérer des ressources ou des groupes de ressources avec une balise spécifique, utilisez l'applet de commande `Get-AzureResource` ou `Get-AzureResourceGroup` avec le paramètre `-Tag`.

![Obtention de ressources et de groupes de ressources balisés avec Get-AzureResource et Get-AzureResourceGroup dans PowerShell](./media/resource-group-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## Balisage avec le Gestionnaire de ressources

Le portail et PowerShell utilisent tous deux l'[API REST du Gestionnaire de ressources](http://msdn.microsoft.com/library/azure/dn790568.aspx) en arrière-plan. Si vous avez besoin intégrer le balisage dans un autre environnement, vous pouvez récupérer des balises avec une commande GET sur l'ID de ressource et mettre à jour l'ensemble des balises avec un appel PATCH.


## Gestion de votre classification

Nous avons vu précédemment comment la saisie semi-automatique vous aide à assurer la cohérence et éviter les erreurs. La saisie semi-automatique s'applique selon la classification des balises disponibles configurée pour l'abonnement. Chaque balise que vous ajoutez à une ressource ou à un groupe de ressources est automatiquement ajoutée à la classification sur l'ensemble de l'abonnement. Cependant, vous pouvez également préremplir cette classification en saisissant des noms et des valeurs de balises que vous souhaitez utiliser pour le balisage de vos ressources.

Pour obtenir une liste de toutes les balises d'un abonnement à l'aide de PowerShell, utilisez l'applet de commande `Get-AzureTag`.

![Get-AzureTag dans PowerShell](./media/resource-group-using-tags/Get-AzureTag-in-PowerShell.png)


Vous pouvez consulter les balises commençant par « masqué-» et « lien: ». Il s'agit de balises internes, que vous devez ignorer et éviter de modifier.

Utilisez l'applet de commande `New-AzureTag` pour ajouter des balises à la classification. Ces balises seront incluses dans la saisie semi-automatique, même si elles n'ont pas encore été appliquées à des ressources ou des groupes de ressources. Pour supprimer un nom ou une valeur de balise, commencez par supprimer la balise sur toutes les ressources où elle est appliquée, puis utilisez l'applet de commande `Remove-AzureTag` pour la supprimer de la classification.

Pour afficher votre classification de balises dans le portail, utilisez le concentrateur Parcourir pour afficher Tous les éléments, puis sélectionnez Balises.

![Rechercher des balises via le concentrateur Parcourir](./media/resource-group-using-tags/browse-tags.png)

Épinglez les balises plus importantes à votre tableau d'accueil pour y accéder rapidement et vous serez prêt. Amusez-vous bien !

![Épingler des balises au tableau d'accueil](./media/resource-group-using-tags/pin-tags.png)

## Balisage et facturation

Dans le cas des services pris en charge, vous pouvez utiliser des balises pour regrouper vos données de facturation. Par exemple, les [machines virtuelles intégrées à Azure Resource Manager](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md) vous permettent de définir et d’appliquer des balises pour organiser l’utilisation de la facturation pour les machines virtuelles. Si vous exécutez plusieurs machines virtuelles pour différentes organisations, vous pouvez recourir aux balises pour regrouper l’utilisation par centre de coûts. Vous pouvez également utiliser des balises pour catégoriser les coûts par environnement d’exécution ; par exemple, l’utilisation de la facturation pour les machines virtuelles en cours d’exécution dans l’environnement de production.

Vous pouvez récupérer des informations sur les balises par le biais de l’[API Usage](billing-usage-rate-card-overview.md) ou du fichier de valeurs séparées par des virgules (CSV) que vous pouvez télécharger à partir du [portail de comptes Azure](https://account.windowsazure.com/) ou du [portail EA](https://ea.azure.com).

Lorsque vous téléchargez le fichier CSV d’utilisation pour les services qui prennent en charge les balises avec la facturation, les balises s’affichent dans la colonne **Balises**. Pour plus d’informations, voir [Comprendre votre facture Microsoft Azure](billing-understand-your-bill.md).

![Voir les balises dans la facturation](./media/resource-group-using-tags/billing_csv.png)

## Étapes suivantes
Mise en route

- [Présentation d’Azure Resource Manager](./resource-group-overview.md)  
- [Utilisation d’Azure PowerShell avec Azure Resource Manager](./powershell-azure-resource-manager.md)
- [Utilisation de l’interface de ligne de commande Azure pour Mac, Linux et Windows avec le Gestionnaire des ressources Azure](./xplat-cli-azure-resource-manager.md)  
- [Utilisation du portail Azure pour gérer vos ressources Azure](./resource-group-portal.md)  
  
Création et déploiement d’applications
  
- [Création de modèles Azure Resource Manager](./resource-group-authoring-templates.md)  
- [Déploiement d’une application avec un modèle Azure Resource Manager](./resource-group-template-deploy.md)  
- [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](./resource-group-deploy-debug.md)  
- [Fonctions des modèles Azure Resource Manager](./resource-group-template-functions.md)  
- [Opérations de modèle avancées](./resource-group-advanced-template.md)  
  
Gestion et audit de l’accès
  
- [Gestion et audit d’accès aux ressources](./resource-group-rbac.md)  
- [Authentification d’un principal du service à l’aide d’Azure Resource Manager](./resource-group-authenticate-service-principal.md)  
- [Création d'un nouveau principal du service Azure à l’aide du portail Azure classique](./resource-group-create-service-principal-portal.md)  
  

<!---HONumber=July15_HO5-->