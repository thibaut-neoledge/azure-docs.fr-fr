<properties
   pageTitle="Explorateur de ressources Azure | Microsoft Azure"
   description="Décrit l’Explorateur de ressources Azure et comment l’utiliser pour afficher et mettre à jour des déploiements par le biais d’Azure Resource Manager"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="stuartle;tomfitz"/>

# Affichage et modification des ressources à l’aide de l’Explorateur de ressources Azure
L’[Explorateur de ressources Azure](https://resources.azure.com) est un excellent outil pour consulter des ressources que vous avez déjà créées dans votre abonnement. Cet outil vous permet de comprendre comment les ressources sont structurées et de voir les propriétés affectées à chaque ressource. Vous pouvez en savoir plus sur les opérations de l’API REST et les applets de commande PowerShell disponibles pour un type de ressource. Vous pouvez également émettre des commandes par le biais de l’interface. L’Explorateur de ressources peut s’avérer particulièrement utile lorsque vous créez des modèles Resource Manager, car il permet d’afficher les propriétés des ressources existantes.

Le code source de l’outil Explorateur de ressources est disponible sur [github](https://github.com/projectkudu/ARMExplorer), qui fournit une référence précieuse si vous devez implémenter un comportement similaire dans vos propres applications.

## Afficher les ressources
Accédez à [https://resources.azure.com](https://resources.azure.com) et connectez-vous avec les informations d’identification que vous utilisez pour le [portail Azure](https://portal.azure.com).

Une fois chargée, l’arborescence de gauche vous permet d’accéder aux informations détaillées sur vos abonnements et groupes de ressources :

![arborescence](./media/resource-manager-resource-explorer/are-01-treeview.png)

Lorsque vous accédez à un groupe de ressources, les fournisseurs pour lesquels il existe des ressources dans ce groupe s’affichent :

![fournisseurs](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

À partir de là, vous pouvez commencer l’exploration des instances de la ressource. Dans la capture d’écran ci-dessous, vous pouvez voir l’instance SQL Server `sltest` dans l’arborescence. Sur le côté droit, vous pouvez voir des informations sur les demandes d’API REST que vous pouvez utiliser avec cette ressource. En accédant au nœud correspondant à une ressource, l’Explorateur de ressources effectue automatiquement la requête GET pour extraire des informations sur la ressource. Dans la zone de texte située sous l’URL, la réponse de l’API s’affiche.

Une fois que vous serez familiarisé avec les modèles Resource Manager, le contenu du corps vous paraîtra familier. La section **properties** de la réponse correspond aux valeurs que vous pouvez fournir dans la section **properties** de votre modèle.

![serveur SQL](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

L’Explorateur de ressources vous permet d’explorer en détail les ressources enfants. Dans le cas de la base de données SQL Server, il existe des ressources enfants pour des éléments tels que des bases de données et règles de pare-feu.

L’exploration d’une base de données nous montre les propriétés de cette base de données. Dans la capture d’écran ci-dessous, nous pouvons voir que l’`edition` de la base de données est `Standard` et que le niveau de base de données `serviceLevelObjective` est `S1`.

![base de données SQL](./media/resource-manager-resource-explorer/are-04-database-get.png)

## Modifier les ressources

Une fois que vous avez accédé à une ressource, vous pouvez sélectionner le bouton Modifier pour rendre le contenu JSON modifiable. Vous pouvez ensuite utiliser l’Explorateur de ressources pour modifier le script JSON et envoyer une demande PUT pour modifier la ressource. Par exemple, l’image ci-dessous indique que le niveau de base de données est désormais défini sur `S0` :

![base de données - demande PUT](./media/resource-manager-resource-explorer/are-05-database-put.png)

En sélectionnant **PUT**, vous envoyez la demande.

Une fois que la demande a été soumise, l’Explorateur de ressources émet à nouveau la demande GET pour actualiser l’état. Dans ce cas, nous pouvons voir que l’élément `requestedServiceObjectiveId` a été mis à jour et est différent de `currentServiceObjectiveId` qui indique qu’une opération de mise à l’échelle est en cours. Vous pouvez cliquer sur le bouton GET pour actualiser l’état manuellement.

![base de données - demande PUT 2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## Exécution d’actions sur les ressources

L’onglet **Actions** vous permet de voir et d’exécuter d’autres opérations REST. Par exemple, lorsque vous avez sélectionné une ressource de site web, l’onglet Actions présente une longue liste d’opérations disponibles, dont certaines sont présentées ci-dessous.

![web - demande POST](./media/resource-manager-resource-explorer/are-web-post.png)

## Appel de l’API au moyen de PowerShell
L’onglet PowerShell de l’Explorateur de ressources affiche les applets de commande à utiliser pour interagir avec la ressource que vous explorez. Selon le type de ressource sélectionné, le script PowerShell affiché peut utiliser des applets de commande simples (telles que `Get-AzureRmResource` et `Set-AzureRmResource`) ou des applets de commande plus complexes (telles que la permutation des emplacements sur un site web).

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Pour plus d’informations sur les applets de commande Azure PowerShell, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](powershell-azure-resource-manager.md).

## Résumé
Lorsque vous travaillez avec Resource Manager, l’Explorateur de ressources peut être un outil extrêmement utile. Il est idéal pour trouver des moyens d’utiliser PowerShell pour effectuer des requêtes et apporter des modifications. Si vous utilisez l’API REST, c’est un excellent moyen de commencer à tester rapidement les appels d’API avant de commencer à écrire du code. En outre, si vous écrivez des modèles, cela peut être un excellent moyen de comprendre la hiérarchie des ressources et de déterminer où placer la configuration. Vous pouvez apporter une modification dans le portail, puis rechercher les entrées correspondantes dans l’Explorateur de ressources.

Pour plus d’informations, consultez la [vidéo Channel 9 avec Scott Hanselman et David Ebbo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)

<!---HONumber=AcomDC_0803_2016-->