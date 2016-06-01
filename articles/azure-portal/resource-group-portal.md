<properties 
	pageTitle="Utiliser le portail Azure pour gérer des ressources Azure | Microsoft Azure" 
	description="Utilisez le portail Azure et Azure Resource Manager pour déployer et gérer vos ressources. Cet article explique comment baliser des ressources et afficher des journaux d’audit." 
	services="azure-resource-manager,azure-portal" 
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
	ms.date="05/16/2016" 
	ms.author="tomfitz"/>


# Utilisation du portail Azure pour déployer et gérer vos ressources Azure

## Introduction

Cette rubrique montre comment utiliser le [portail Azure](https://portal.azure.com) avec [Azure Resource Manager](../resource-group-overview.md) pour déployer et gérer vos ressources Azure.

Actuellement, certains services ne prennent pas en charge le portail ou Resource Manager. Pour ces services, vous devez utiliser le [portail Azure Classic](https://manage.windowsazure.com). Pour connaître l’état de chaque service, voir [Graphique de la disponibilité du portail Azure](https://azure.microsoft.com/features/azure-portal/availability/)

Vous pouvez également gérer des ressources via Azure PowerShell et l’interface de ligne de commande Azure. Pour plus d’informations sur l’utilisation de ces interfaces, voir [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md) et [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](../xplat-cli-azure-resource-manager.md). Pour plus d’informations sur le déploiement de solutions avec Visual Studio, voir [Création et déploiement des groupes de ressources Azure via Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Créer un groupe de ressources

Pour créer un groupe de ressources vide, sélectionnez **Nouveau**, **Gestion** et **Groupe de ressources**.

![créer un groupe de ressources vide](./media/resource-group-portal/create-empty-group.png)

Spécifiez un nom et un emplacement, puis, si nécessaire, sélectionnez un abonnement.

![définir les valeurs d’un groupe](./media/resource-group-portal/set-group-properties.png)

## Déploiement de ressources

Une fois votre groupe de ressources créé, vous pouvez y déployer des ressources. Pour commencer un déploiement, sélectionnez simplement **Nouveau** et choisissez le type de ressource que vous souhaitez déployer.

![déployer des ressources](./media/resource-group-portal/deploy-resource.png)

Si vous ne voyez pas le type de ressource que vous souhaitez déployer, vous pouvez lancer une recherche sur le Marketplace.

![effectuer une recherche sur le marketplace](./media/resource-group-portal/search-resource.png)

Selon le type de ressource que vous avez sélectionné, vous devrez définir une collection de propriétés pertinentes avant le déploiement. Ces options ne sont pas présentées ici, car elles varient selon le type de ressource. Pour tous les types, vous devez sélectionner un groupe de ressources de destination. L’illustration suivante montre comment créer une application web et la déployer dans le groupe de ressources que vous venez de créer.

![Créer un groupe de ressources](./media/resource-group-portal/select-existing-group.png)

Vous pouvez également décider de créer un groupe de ressources lors du déploiement de vos ressources. Au lieu de sélectionner l’un des groupes de ressources existants dans votre abonnement, sélectionnez **Nouveau** et donnez un nom au groupe de ressources.

![créer un groupe de ressources](./media/resource-group-portal/select-new-group.png)

Votre déploiement commence. Cette opération peut prendre quelques minutes. Vous recevez une notification une fois le déploiement terminé.

![afficher une notification](./media/resource-group-portal/view-notification.png)

Après avoir déployé vos ressources, vous pouvez décider d’ajouter des ressources au groupe. Vous pouvez ajouter des ressources à un groupe de ressources au moyen de la commande **Ajouter** sur le panneau du groupe de ressources.

![ajouter une ressource](./media/resource-group-portal/add-resource.png)

## Exportation du modèle

Après avoir configuré votre groupe de ressources, vous pouvez éventuellement afficher le modèle Resource Manager pour le groupe de ressources. L’exportation du modèle offre deux avantages :

1. Vous pouvez facilement automatiser des déploiements futurs de la solution car l’ensemble de l’infrastructure est définie dans le modèle.

2. Vous pouvez vous familiariser avec la syntaxe de modèle en regardant dans la JSON (JavaScript Object Notation) qui représente votre solution.

> [AZURE.NOTE] La fonctionnalité Modèle d’exportation est en version préliminaire. Tous les types de ressources ne la prennent pas actuellement en charge. Lorsque vous tentez d’exporter un modèle, une erreur indiquant que certaines ressources n’ont pas été exportées peut s’afficher. Le cas échéant, vous pouvez définir manuellement ces ressources dans votre modèle après l’avoir téléchargé.

Pour des instructions détaillées, consultez [Exporter un modèle Azure Resource Manager à partir de ressources existantes](../resource-manager-export-template/).

## Gestion d'un groupe de ressources

Vous pouvez parcourir tous les groupes de ressources en cliquant sur **Groupes de ressources**.

![parcourir les groupes de ressources](./media/resource-group-portal/browse-groups.png)

Lorsque vous sélectionnez un groupe de ressources particulier, un panneau vous fournit des informations sur ce groupe de ressources, notamment une liste de toutes les ressources qu’il contient.

![Résumé du groupe de ressources](./media/resource-group-portal/group-summary.png)

Vous pouvez ajouter des tables et graphiques supplémentaires dans le panneau Groupe de ressources en sélectionnant **Ajouter une section** sous le résumé.

![ajoutez une section](./media/resource-group-portal/add-section.png)

Une galerie de vignettes s’affiche pour vous permettre de sélectionner les informations à inclure dans le panneau. Les types de vignettes affichées sont filtrés par type de ressource. Si vous sélectionnez une autre ressource, vous obtiendrez des vignettes différentes.

![ajoutez une section](./media/resource-group-portal/tile-gallery.png)

Faites glisser la vignette de votre choix dans l’espace disponible.

![faire glisser une vignette](./media/resource-group-portal/drag-tile.png)

Sélectionnez **Terminé** en haut du portail. Votre nouvelle vue est maintenant intégrée au panneau.

![afficher une vignette](./media/resource-group-portal/show-lens.png)

Pour accéder rapidement à un groupe de ressources, vous pouvez épingler le panneau à votre tableau de bord.

![épingler un groupe de ressources](./media/resource-group-portal/pin-group.png)

Vous pouvez également épingler une section du panneau à votre tableau de bord en cliquant sur les points de suspension (...) situés au-dessus de la section. Vous pouvez aussi personnaliser la taille de la section dans le panneau ou supprimer complètement cette section. L’illustration suivante montre comment épingler, personnaliser ou supprimer la section Processeur et mémoire.

![épingler une section](./media/resource-group-portal/pin-cpu-section.png)

Après avoir épinglé la section au tableau de bord, vous obtenez le résumé sur le tableau de bord.

![afficher le tableau de bord](./media/resource-group-portal/view-startboard.png)

Vous pouvez le sélectionner immédiatement pour obtenir plus de détails sur les données.

Comme les groupes de ressources permettent de gérer le cycle de vie de l’ensemble des ressources englobées, la suppression d’un groupe de ressources entraîne celle de toutes les ressources qu’il contient. Vous pouvez également supprimer des ressources individuelles d'un groupe de ressources. Soyez prudent lorsque vous supprimez un groupe de ressources car des ressources d'autres groupes de ressources peuvent y être liées. Les ressources liées ne seront pas supprimées, mais elles risquent de ne pas fonctionnent comme prévu.

![supprimer un groupe](./media/resource-group-portal/delete-group.png)

## Baliser des ressources

Vous pouvez appliquer des balises à des groupes de ressources pour organiser logiquement vos ressources. Pour plus d’informations sur l’utilisation des balises par le biais du portail, consultez [Organisation des ressources Azure à l’aide de balises](../resource-group-using-tags.md).

## Déploiement d’un modèle enregistré

Si vous avez enregistré un modèle dans votre compte, vous pouvez l’afficher ultérieurement grâce aux commandes **Parcourir** et **Modèles**.

![parcourir les modèles](./media/resource-group-portal/browse-templates.png)

Votre propre collection de modèles s’affiche.

![afficher la collection de modèles](./media/resource-group-portal/show-template-collection.png)


## Déploiement d’un modèle personnalisé

Si vous souhaitez effectuer un déploiement sans utiliser un des modèles de Marketplace, vous pouvez créer un modèle personnalisé qui définit l'infrastructure de votre solution. Pour en savoir plus sur les modèles, consultez [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).

Pour déployer un modèle personnalisé à travers le portail, sélectionnez **Nouveau**, puis recherchez le **Déploiement de modèle** jusqu’à ce que vous puissiez le sélectionner dans les options.

![rechercher un déploiement de modèle](./media/resource-group-portal/search-template.png)

Sélectionnez **Déploiement de modèle** à partir des ressources disponibles.

![sélectionner un déploiement de modèle](./media/resource-group-portal/select-template.png)

Après avoir lancé le déploiement du modèle, vous pouvez créer le modèle personnalisé et définir les valeurs pour le déploiement.

![créer un modèle](./media/resource-group-portal/show-custom-template.png)

Vous pouvez également sélectionner un modèle existant à partir des [modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/). Ces modèles sont fournis par la communauté. Ils couvrent de nombreux scénarios courants, et il est donc possible que quelqu’un ait ajouté un modèle semblable à celui que vous tentez de déployer. Vous pouvez rechercher parmi les modèles pour trouver un élément qui correspond à votre scénario.

![sélectionner un modèle de démarrage rapide](./media/resource-group-portal/select-quickstart-template.png)

Une fois un modèle sélectionné, il est chargé dans l’éditeur.

## Afficher votre abonnement et vos coûts

Vous pouvez afficher des informations sur votre abonnement et sur les coûts cumulés de toutes vos ressources. Sélectionnez **Abonnements** suivi de l’abonnement de votre choix. Il se peut que vous n’ayez qu’un seul abonnement à sélectionner.

![subscription](./media/resource-group-portal/select-subscription.png)

Dans le panneau de l’abonnement, vous voyez un taux d’avancement.

![taux d’avancement](./media/resource-group-portal/burn-rate.png)

Ainsi qu’une répartition des coûts par type de ressource.

![coût des ressources](./media/resource-group-portal/cost-by-resource.png)

## Contrôle d’accès pour les tableaux de bord Azure

L’accès aux informations affichées par la plupart des mosaïques dans le portail est régi par le [contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md) (RBAC) Azure. Pour intégrer des tableaux de bord en toute transparence dans l’écosystème, tous les tableaux de bord publiés sont implémentés en tant que ressources Azure. Du point de vue du contrôle d’accès, les tableaux de bord sont traités de la même manière qu’une machine virtuelle ou un compte de stockage.

Voici un exemple. Supposons que vous disposez d’un abonnement Azure et que les différents membres de votre équipe ont reçu les rôles de **propriétaire**, **contributeur** ou **lecteur** de l’abonnement. Les utilisateurs associés au rôle de propriétaire ou de contributeur seront en mesure de répertorier, afficher, créer, modifier ou supprimer des tableaux de bord dans l’abonnement. Les utilisateurs ayant le rôle de lecteur pourront répertorier et afficher des tableaux de bord, sans les modifier ou les supprimer. Les utilisateurs ayant un accès en lecture seront en mesure d’apporter des modifications locales à un tableau de bord publié (par exemple, lors de la résolution d’un problème), mais n’auront pas la possibilité de republier ces modifications sur le serveur. Ils pourront effectuer une copie privée du tableau de bord pour leur propre usage.

Notez que les vignettes individuelles du tableau de bord appliquent leurs propres exigences en matière de contrôle d’accès, compte tenu des ressources dont ils affichent les données. Autrement dit, vous pouvez concevoir un tableau de bord que vous pouvez partager à plus grande échelle tout en protégeant les données contenues dans chaque vignette.

## Étapes suivantes

- Pour afficher les journaux d’audit, consultez l’article [Opérations d’audit avec Resource Manager](../resource-group-audit.md).
- Pour résoudre des problèmes liés à un déploiement, consultez l’article [Résolution des problèmes liés aux déploiements de groupes de ressources avec le portail Azure](../resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0518_2016-->