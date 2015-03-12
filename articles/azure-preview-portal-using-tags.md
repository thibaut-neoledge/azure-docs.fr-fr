<properties 
	pageTitle="Organisation des ressources Azure à l'aide de balises" 
	description="" 
	services="" 
	documentationCenter="" 
	authors="flanakin" 
	writer="" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="micflan"/>


# Organisation des ressources Azure à l'aide de balises

La version préliminaire du portail Azure et le Gestionnaire de ressources sous-jacentes vont organiser vos ressources et personnaliser votre expérience exactement selon vos besoins. 

Dans l'ensemble du portail Azure, les abonnements sont le seul moyen de classer et regrouper vos ressources. Avec la version préliminaire du portail, [nous avons introduit des groupes de ressources](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups) qui vous permettent de regrouper les entités associées. Cet avantage s'est accentué lorsque [nous avons introduit l'accès basé sur les rôles](http://azure.microsoft.com/documentation/articles/role-based-access-control-configure). À présent, dans cette même optique, vous pouvez baliser vos ressources avec des paires de clé/valeur pour classer et afficher plus précisément vos ressources entre les différents groupes de ressources et, dans le portail, entre les différents abonnements.

Regroupez vos ressources en fonction de vos équipes, de vos projets ou même de vos environnements, pour vous concentrer sur ce que vous voulez afficher, lorsque vous en avez besoin. 


## Balises de la version préliminaire du portail Azure

Il est facile de baliser des ressources et des groupes de ressources dans la version préliminaire du portail. Utilisez le concentrateur Parcourir pour accéder à la ressource ou au groupe de ressources que vous souhaitez baliser, puis cliquez sur la partie Balises de la section Vue d'ensemble, située en haut du volet. 

![Tags part on resource and resource group blades](./media/azure-preview-portal-using-tags/rgblade.png)

Un volet s'ouvre. Il contient la liste des balises qui ont déjà été appliquées. S'il s'agit de votre première balise, la liste sera vide. Pour ajouter une balise, indiquez simplement un nom et une valeur, puis appuyez sur ENTRÉE. Après avoir ajouté quelques balises, vous remarquerez que les options de saisie semi-automatique basées sur les noms et les valeurs des balises existantes améliorent la cohérence de l'organisation de vos ressources, tout en évitant des erreurs courantes, comme les fautes d'orthographe.

![Tag resources with name/value pairs](./media/azure-preview-portal-using-tags/tag-resources.png)

À ce stade, vous pouvez cliquer sur chaque balise pour afficher une liste de toutes les ressources associées à la même balise. Bien sûr, si c'est votre première balise, cette liste ne sera pas très intéressante. Pour l'instant, passons à PowerShell pour baliser tous nos ressources rapidement.


## Balisage avec PowerShell

Tout d'abord, récupérez le tout dernier [module Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Si vous utilisez le module Azure PowerShell pour la première fois, [consultez la documentation](http://azure.microsoft.com/documentation/articles/install-configure-powershell) pour apprendre à l'utiliser. Cet article part du principe que vous avez déjà ajouté un compte et sélectionné un abonnement avec les ressources que vous souhaitez baliser.

Le balisage est uniquement disponible pour les ressources et les groupes de ressources disponibles dans le [Gestionnaire de ressources](http://msdn.microsoft.com/library/azure/dn790568.aspx). La prochaine chose à faire consiste à passer au Gestionnaire de ressources. Pour plus d'informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/).

  Switch-AzureMode AzureResourceManager

Les balises se trouvent directement sur les ressources et les groupes de ressources. Pour savoir quelles balises sont déjà appliqués, il suffit de récupérer une ressource ou un groupe de ressources avec `Get-AzureResource` ou `Get-AzureResourceGroup`, le cas échéant. Commençons par un groupe de ressources.

![Getting tags with Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

Cette cmdlet renvoie plusieurs octets de métadonnées sur le groupe de ressources, notamment les balises déjà appliquées, le cas échéant. Pour baliser  un groupe de ressources, utilisez simplement `Set-AzureResourceGroup` avant d'indiquer un nom et une valeur de balise.

![Setting tags with Set-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

N'oubliez pas que les balises sont mises à jour en tant qu'ensemble, et si vous ajoutez une balise à une ressource déjà balisée, vous devrez donc l'enregistrer en utilisant un tableau contenant toutes les balises que vous souhaitez conserver. Pour supprimer une balise, enregistrez simplement le tableau sans la balise que vous souhaitez supprimer. 

Le processus est le même pour les ressources, sauf que vous allez utiliser les cmdlets `Get-AzureResource` et `Set-AzureResource`. Pour récupérer des ressources ou des groupes de ressources avec une balise spécifique, utilisez la cmdlet `Get-AzureResource` ou `Get-AzureResourceGroup` avec le paramètre `-Tag`.

![Getting tagged resources and resource groups with Get-AzureResource and Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## Balisage avec le Gestionnaire de ressources

La version préliminaire du portail et PowerShell utilisent tous deux [l'API REST du Gestionnaire de ressources](http://msdn.microsoft.com/library/azure/dn790568.aspx) en arrière-plan. Si vous avez besoin intégrer le balisage dans un autre environnement, vous pouvez récupérer des balises avec une commande GET sur l'ID de ressource et mettre à jour l'ensemble des balises avec un appel PATCH.


## Gestion de votre classification

Nous avons vu précédemment comment la saisie semi-automatique vous aide à assurer la cohérence et éviter les erreurs. La saisie semi-automatique s'applique selon la classification des balises disponibles configurée pour l'abonnement. Chaque balise que vous ajoutez à une ressource ou à un groupe de ressources est automatiquement ajoutée à la classification sur l'ensemble de l'abonnement. Cependant, vous pouvez également préremplir cette classification en saisissant des noms et des valeurs de balises que vous souhaitez utiliser pour le balisage de vos ressources.

Pour obtenir une liste de toutes les balises d'un abonnement à l'aide de PowerShell, utilisez la cmdlet `Get-AzureTag`.

![Get-AzureTag in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureTag-in-PowerShell.png)


Vous pouvez consulter les balises commençant par " masqué-" et " lien: ". Il s'agit de balises internes, que vous devez ignorer et éviter de modifier. 

Utilisez la cmdlet `New-AzureTag` pour ajouter des balises à la classification. Ces balises seront incluses dans la saisie semi-automatique, même si elles n'ont pas encore été appliquées à des ressources ou des groupes de ressources. Pour supprimer un nom ou une valeur de balise, commencez par supprimer la balise sur toutes les ressources où elle est appliquée, puis utilisez la cmdlet `Remove-AzureTag` pour la supprimer de la classification.

Pour afficher votre classification de balises dans le portail, utilisez le concentrateur Parcourir pour afficher Tous les éléments, puis sélectionnez Balises.

![Find tags via the Browse hub](./media/azure-preview-portal-using-tags/browse-tags.png)

Épinglez les balises plus importantes à votre tableau d'accueil pour y accéder rapidement et vous serez prêt. Amusez-vous bien !

![Pin tags to the Startboard](./media/azure-preview-portal-using-tags/pin-tags.png)


<!--HONumber=46--> 
