<properties 
	pageTitle="Utiliser le portail Azure pour gérer des ressources Azure | Microsoft Azure" 
	description="Utilisez le portail Azure et Azure Resource Manager pour déployer et gérer vos ressources. Cet article explique comment baliser des ressources et afficher des journaux d’audit." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="03/29/2016" 
	ms.author="tomfitz"/>


# Utilisation du portail Azure pour déployer et gérer vos ressources Azure

## Introduction

Cette rubrique montre comment utiliser le [portail Azure](https://portal.azure.com) avec [Azure Resource Manager](../resource-group-overview.md) pour déployer et gérer vos ressources Azure.

Actuellement, certains services ne prennent pas en charge le portail ou Resource Manager. Pour ces services, vous devez utiliser le [portail Azure Classic](https://manage.windowsazure.com). Pour connaître l’état de chaque service, voir [Graphique de la disponibilité du portail Azure](https://azure.microsoft.com/features/azure-portal/availability/)

Vous pouvez également gérer des ressources via Azure PowerShell et l’interface de ligne de commande Azure. Pour plus d’informations sur l’utilisation de ces interfaces, voir [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md) et [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](../xplat-cli-azure-resource-manager.md). Pour plus d’informations sur le déploiement de solutions avec Visual Studio, voir [Création et déploiement des groupes de ressources Azure via Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Créer et gérer des groupes de ressources

Pour créer un groupe de ressources vide, sélectionnez **Nouveau**, **Gestion** et **Groupe de ressources**.

![créer un groupe de ressources vide](./media/resource-group-portal/create-empty-group.png)

Vous spécifiez son nom et son emplacement, puis, si nécessaire, sélectionnez un abonnement.

![définir les valeurs d’un groupe](./media/resource-group-portal/set-group-properties.png)

Une fois votre groupe de ressources créé, vous pouvez y déployer des ressources. Pour commencer le déploiement, sélectionnez simplement **Nouveau** et choisissez le type de ressource que vous souhaitez déployer.

![déployer des ressources](./media/resource-group-portal/deploy-resource.png)

Si vous ne voyez pas le type de ressource que vous souhaitez déployer, vous pouvez lancer une recherche sur le Marketplace.

![effectuer une recherche sur le marketplace](./media/resource-group-portal/search-resource.png)

Selon le type de ressource que vous avez sélectionné, vous devrez définir une collection de propriétés pertinentes avant le déploiement. Ces options ne sont pas présentées ici, car elles varient selon le type de ressource. Pour tous les types, vous devez sélectionner un groupe de ressources de destination. L’illustration suivante montre comment créer une application web et la déployer dans le groupe de ressources que vous venez de créer.

![Créer un groupe de ressources](./media/resource-group-portal/select-existing-group.png)

Vous pouvez également décider de créer un groupe de ressources lors du déploiement de vos ressources. Au lieu de sélectionner l’un des groupes de ressources existants dans votre abonnement, sélectionnez **Nouveau** et nommez le groupe de ressources.

![créer un groupe de ressources](./media/resource-group-portal/select-new-group.png)

Votre déploiement commence. Cette opération peut prendre quelques minutes. Vous recevez une notification une fois le déploiement terminé.

![afficher une notification](./media/resource-group-portal/view-notification.png)

### Ajouter des ressources à un groupe de ressources existant

Vous pouvez ajouter des ressources à un groupe de ressources au moyen de la commande **Ajouter** sur le panneau du groupe de ressources.

![ajouter une ressource](./media/resource-group-portal/add-resource.png)

Vous pouvez sélectionner la ressource de votre choix dans la liste proposée.

### Parcourir les groupes de ressources

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

### Supprimer un groupe de ressources

Comme les groupes de ressources permettent de gérer le cycle de vie de l’ensemble des ressources englobées, la suppression d’un groupe de ressources entraîne celle de toutes les ressources qu’il contient. Vous pouvez également supprimer des ressources individuelles d'un groupe de ressources. Soyez prudent lorsque vous supprimez un groupe de ressources, car d'autres ressources peuvent y être liées. Vous pouvez voir toutes les ressources liées dans la carte des ressources et prendre les mesures nécessaires pour éviter des conséquences fâcheuses lorsque vous supprimez des groupes de ressources. Les ressources liées ne seront pas supprimées, mais elles risquent de ne pas fonctionnent comme prévu.

![supprimer un groupe](./media/resource-group-portal/delete-group.png)


## Afficher les anciens déploiements

Le panneau du groupe de ressources indique la date et l'état du dernier déploiement de ce groupe de ressources. Vous pouvez sélectionner le lien pour afficher l'historique des déploiements du groupe.

![dernier déploiement](./media/resource-group-portal/last-deployment.png)

Si vous sélectionnez un déploiement dans l'historique, les détails de ce déploiement apparaissent.

![résumé du déploiement](./media/resource-group-portal/deployment-summary.png)

Vous pouvez consulter les opérations individuelles qui ont été exécutées pendant le déploiement. L’image suivante montre une opération qui a réussi et une autre qui a échoué.

![détails de l'opération](./media/resource-group-portal/operation-details.png)

Pour plus d’informations sur la résolution des problèmes liés à un déploiement, consultez l’article [Troubleshooting resource group deployments with Azure Portal](../resource-manager-troubleshoot-deployments-portal.md).

Vous pouvez récupérer le modèle utilisé pour le déploiement en sélectionnant **Exporter le modèle**.

![exporter le modèle](./media/resource-group-portal/export-template.png)

Vous obtenez alors le modèle précisément utilisé pour ce déploiement.

![afficher le modèle](./media/resource-group-portal/show-template.png)

Ce modèle ne représente pas le groupe de ressources de manière exhaustive ; si vous avez ajouté ou supprimé des ressources en dehors de ce déploiement, ces actions ne sont pas répercutées dans le modèle. Le panneau inclut le modèle, un fichier de paramètres à utiliser avec le modèle et un script PowerShell permettant de déployer le modèle. Vous pouvez télécharger ces 3 fichiers en sélectionnant **Enregistrer dans le fichier**.

## Afficher les journaux d’audit

Le journal d'audit contient non seulement les opérations de déploiement, mais aussi toutes les opérations de gestion effectuées sur les ressources dans le cadre de votre abonnement. Par exemple, les journaux d'audit vous indiquent quand un utilisateur de votre organisation a arrêté une application. Pour afficher les journaux d’audit, sélectionnez **Parcourir tout** puis **Journaux d’audit**.

![parcourir les journaux d’audit](./media/resource-group-portal/browse-audit-logs.png)

La section sur les opérations vous indique les opérations individuelles qui ont été effectuées dans le cadre de votre abonnement.

![afficher un journal d'audit](./media/resource-group-portal/view-audit-log.png)

Vous pouvez sélectionner une des opérations pour afficher des détails supplémentaires, notamment quel utilisateur a exécuté l'opération.

Pour plus d’informations sur l’affichage des journaux d’audit, consultez [Opérations d’audit avec Resource Manager](../resource-group-audit.md).

## Baliser des ressources

Vous pouvez appliquer des balises à des groupes de ressources pour organiser logiquement vos ressources. Pour plus d’informations sur l’utilisation des balises par le biais du portail, consultez [Organisation des ressources Azure à l’aide de balises](../resource-group-using-tags.md).

## Déployer un modèle personnalisé

Si vous souhaitez effectuer un déploiement sans utiliser un des modèles de Marketplace, vous pouvez créer un modèle personnalisé qui définit l'infrastructure de votre solution. Pour en savoir plus sur les modèles, consultez [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).

Pour déployer un modèle personnalisé via le portail, sélectionnez **Nouveau** et commencez à rechercher **Déploiement de modèle** jusqu’à ce que vous puissiez le sélectionner dans les options.

![rechercher un déploiement de modèle](./media/resource-group-portal/search-template.png)

Sélectionnez **Déploiement de modèle** à partir des ressources disponibles.

![sélectionner un déploiement de modèle](./media/resource-group-portal/select-template.png)

Après avoir lancé le déploiement du modèle, vous pouvez créer le modèle personnalisé et définir les valeurs pour le déploiement.

![créer un modèle](./media/resource-group-portal/show-custom-template.png)

## Afficher votre abonnement et vos coûts

Vous pouvez afficher des informations sur votre abonnement et sur les coûts cumulés de toutes vos ressources. Sélectionnez **Abonnements** suivi de l’abonnement de votre choix. Il se peut que vous n’ayez qu’un seul abonnement à sélectionner.

![subscription](./media/resource-group-portal/select-subscription.png)

Dans le panneau de l’abonnement, vous voyez un taux d’avancement.

![taux d’avancement](./media/resource-group-portal/burn-rate.png)

Ainsi qu’une répartition des coûts par type de ressource.

![coût des ressources](./media/resource-group-portal/cost-by-resource.png)

## Contrôle d’accès pour les tableaux de bord Azure

L’accès aux informations affichées par la plupart des vignettes dans le portail est régi par le [contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md) (RBAC) Azure. Pour intégrer des tableaux de bord en toute transparence dans l’écosystème, tous les tableaux de bord publiés sont implémentés en tant que ressources Azure. Du point de vue du contrôle d’accès, les tableaux de bord sont traités de la même manière qu’une machine virtuelle ou un compte de stockage.

Voici un exemple. Supposons que vous disposez d’un abonnement Azure et que les différents membres de votre équipe ont reçu les rôles de **propriétaire**, **contributeur** ou **lecteur** de l’abonnement. Les utilisateurs associés au rôle de propriétaire ou de contributeur seront en mesure de répertorier, afficher, créer, modifier ou supprimer des tableaux de bord dans l’abonnement. Les utilisateurs ayant le rôle de lecteur pourront répertorier et afficher des tableaux de bord, sans les modifier ou les supprimer. Les utilisateurs ayant un accès en lecture seront en mesure d’apporter des modifications locales à un tableau de bord publié (par exemple, lors de la résolution d’un problème), mais n’auront pas la possibilité de republier ces modifications sur le serveur. Ils pourront effectuer une copie privée du tableau de bord pour leur propre usage.

Notez que les vignettes individuelles du tableau de bord appliquent leurs propres exigences en matière de contrôle d’accès, compte tenu des ressources dont ils affichent les données. Autrement dit, vous pouvez concevoir un tableau de bord que vous pouvez partager à plus grande échelle tout en protégeant les données contenues dans chaque vignette.

## Étapes suivantes

- Pour une introduction aux concepts de Resource Manager, consultez [Vue d'ensemble d’Azure Resource Manager](../resource-group-overview.md).
- Pour plus d’informations sur l’utilisation d’Azure PowerShell lors du déploiement de ressources, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).
- Si vous n’avez jamais utilisé l’interface de ligne de commande Azure pour le déploiement de ressources, consultez [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

<!---HONumber=AcomDC_0330_2016-->