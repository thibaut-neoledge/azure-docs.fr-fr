<properties 
	pageTitle="Utiliser le portail Azure pour gérer des ressources Azure | Microsoft Azure" 
	description="Regroupez plusieurs ressources dans un groupe logique qui devient la limite de cycle de vie pour les ressources qu’il contient." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/11/2016" 
	ms.author="tomfitz"/>


# Utilisation du portail Azure pour gérer vos ressources Azure

## Introduction

Azure Resource Manager vous permet de déployer et de gérer vos solutions via des groupes de ressources. Cette rubrique fournit un aperçu de la façon dont vous pouvez utiliser les groupes de ressources dans le portail Azure. En général, un groupe de ressources contient des ressources associées à une application spécifique. Par exemple, un groupe peut contenir une application web qui héberge votre site web public, une base de données SQL qui stocke les données relationnelles utilisées par le site et un compte de stockage qui stocke les ressources non relationnelles. Chaque ressource d’un groupe de ressources doit partager le même cycle de vie. Pour plus d’informations sur Resource Manager, consultez la page [Présentation d’Azure Resource Manager](../resource-group-overview.md).

Actuellement, certains services ne prennent pas en charge le portail ou Resource Manager. Pour ces services, vous devez utiliser le [portail Azure Classic](https://manage.windowsazure.com). Pour l’état de chaque service, voir [Graphique de la disponibilité du portail Azure](https://azure.microsoft.com/features/azure-portal/availability/)

Vous pouvez également gérer des ressources via Azure PowerShell et l’interface de ligne de commande Azure. Pour plus d’informations sur l’utilisation de ces interfaces, voir [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md) et [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](../xplat-cli-azure-resource-manager.md).


## Création d’un groupe de ressources et des ressources

Si vous devez créer un groupe de ressources vide, vous pouvez sélectionner **Nouveau**, **Gestion** et **Groupe de ressources**.

![créer un groupe de ressources vide](./media/resource-group-portal/create-empty-group.png)

Vous spécifiez son nom et son emplacement, puis, si nécessaire, sélectionnez un abonnement.

![définir les valeurs d’un groupe](./media/resource-group-portal/set-group-properties.png)

Lors du déploiement de vos ressources, vous pouvez choisir de les déployer sur le groupe de ressources que vous avez créé. L’illustration suivante montre comment créer une application web dans un groupe de ressources existant.

![Créer un groupe de ressources](./media/resource-group-portal/select-existing-group.png)

Vous pouvez également décider de créer un groupe de ressources lors du déploiement de vos ressources. Au lieu de sélectionner l’un des groupes de ressources existant dans votre abonnement, sélectionnez **Nouveau** et nommez le groupe de ressources.

![créer un groupe de ressources](./media/resource-group-portal/select-new-group.png)

## Parcourir les groupes de ressources

Vous pouvez parcourir tous les groupes de ressources en cliquant sur **Groupes de ressources**.

![parcourir les groupes de ressources](./media/resource-group-portal/browse-groups.png)

Lorsque vous sélectionnez un groupe de ressources particulier, un panneau vous fournit des informations sur ce groupe de ressources, notamment une liste de toutes les ressources qu’il contient.

![Résumé du groupe de ressources](./media/resource-group-portal/group-summary.png)

Il permet également d'obtenir une vue unifiée des informations de facturation et de surveillance pour toutes les ressources du groupe.

![surveillance et facturation](./media/resource-group-portal/monitoring-billing.png)

## Afficher votre abonnement et vos coûts

Vous pouvez afficher des informations sur votre abonnement et sur les coûts cumulés de toutes vos ressources. Sélectionnez **Abonnements** et l’abonnement que vous souhaitez voir. Il se peut que vous n’ayez qu’un seul abonnement à sélectionner.

![subscription](./media/resource-group-portal/select-subscription.png)

Dans le panneau de l’abonnement, vous voyez un taux d’avancement.

![taux d’avancement](./media/resource-group-portal/burn-rate.png)

Ainsi qu’une répartition des coûts par type de ressource.

![coût des ressources](./media/resource-group-portal/cost-by-resource.png)

## Personnalisation de l'interface

Pour accéder rapidement au résumé du groupe de ressources, vous pouvez épingler le panneau à votre tableau d'accueil.

![épingler un groupe de ressources](./media/resource-group-portal/pin-group.png)

Vous pouvez également épingler une section du panneau à votre tableau d'accueil en cliquant sur les points de suspension (...) situés au-dessus de la section. Vous pouvez aussi personnaliser la taille de la section dans le panneau ou supprimer complètement cette section. L’image suivante montre comment épingler, personnaliser ou supprimer la section Événements.

![épingler une section](./media/resource-group-portal/pin-section.png)

Après avoir épinglé la section Événements au tableau d'accueil, vous apercevrez un résumé des événements dans le tableau d'accueil.

![tableau d’accueil des événements](./media/resource-group-portal/events-startboard.png)

Vous pouvez le sélectionner immédiatement pour obtenir plus de détails sur les événements.

## Affichage des anciens déploiements

Le panneau du groupe de ressources indique la date et l'état du dernier déploiement de ce groupe de ressources. Vous pouvez sélectionner le lien pour afficher l'historique des déploiements du groupe.

![dernier déploiement](./media/resource-group-portal/last-deployment.png)

Si vous sélectionnez un déploiement dans l'historique, les détails de ce déploiement apparaissent.

![résumé du déploiement](./media/resource-group-portal/deployment-summary.png)

Vous pouvez consulter les opérations individuelles qui ont été exécutées pendant le déploiement. L’image suivante montre une opération qui a réussi et une autre qui a échoué.

![détails de l'opération](./media/resource-group-portal/operation-details.png)

Si vous sélectionnez une des opérations, les détails de cette opération apparaissent. Cela peut être particulièrement utile lorsqu'une opération a échoué, comme indiqué ci-dessous. Cela peut également vous aider à identifier l’origine de l'échec d'un déploiement. Dans l’image suivante, vous pouvez constater que le site web n'a pas été déployé car le nom n'est pas unique.

![message de l'opération](./media/resource-group-portal/operation-message.png)

## Affichage des journaux d'audit

Le journal d'audit contient non seulement les opérations de déploiement, mais aussi toutes les opérations de gestion effectuées sur les ressources dans le cadre de votre abonnement. Par exemple, les journaux d'audit vous indiquent quand un utilisateur de votre organisation a arrêté une application. Pour afficher les journaux d’audit, sélectionnez **Parcourir tout** puis **Journaux d’audit**.

![parcourir les journaux d’audit](./media/resource-group-portal/browse-audit-logs.png)

La section sur les opérations vous indique les opérations individuelles qui ont été effectuées dans le cadre de votre abonnement.

![afficher un journal d'audit](./media/resource-group-portal/view-audit-log.png)

Vous pouvez sélectionner une des opérations pour afficher des détails supplémentaires, notamment quel utilisateur a exécuté l'opération.

Vous pouvez filtrer les informations affichées dans le journal d'audit en sélectionnant **Filtrer**.

![filtrer un journal](./media/resource-group-portal/filter-logs.png)

Vous pouvez sélectionner le type d'opération à afficher, par exemple celles appartenant à un groupe de ressources ou à une ressource, celles survenues pendant une période précise, celles lancées par un appelant spécifique, ou les niveaux de l'opération.

![options de filtrage](./media/resource-group-portal/filter-options.png)

## Ajout de ressources à des groupes de ressources

Vous pouvez ajouter des ressources à un groupe de ressources au moyen de la commande **Ajouter** sur le panneau du groupe de ressources.

![ajouter une ressource](./media/resource-group-portal/add-resource.png)

Vous pouvez sélectionner la ressource de votre choix dans la liste proposée.

## Suppression de groupes de ressources

Comme les groupes de ressources permettent de gérer le cycle de vie de l’ensemble des ressources englobées, la suppression d’un groupe de ressources entraîne celle de toutes les ressources qu’il contient. Vous pouvez également supprimer des ressources individuelles d'un groupe de ressources. Soyez prudent lorsque vous supprimez un groupe de ressources, car d'autres ressources peuvent y être liées. Vous pouvez voir toutes les ressources liées dans la carte des ressources et prendre les mesures nécessaires pour éviter des conséquences fâcheuses lorsque vous supprimez des groupes de ressources. Les ressources liées ne seront pas supprimées, mais elles risquent de ne pas fonctionnent comme prévu.

![supprimer un groupe](./media/resource-group-portal/delete-group.png)

## Balisage des ressources

Vous pouvez appliquer des balises à des groupes de ressources pour organiser logiquement vos ressources. Pour plus d’informations sur l’utilisation des balises par le biais du portail, consultez [Organisation des ressources Azure à l’aide de balises](../resource-group-using-tags.md).

## Déploiement d'un modèle personnalisé

Si vous souhaitez effectuer un déploiement sans utiliser un des modèles de Marketplace, vous pouvez créer un modèle personnalisé qui définit l'infrastructure de votre solution. Pour en savoir plus sur les modèles, consultez [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).

Pour déployer un modèle personnalisé via le portail, sélectionnez **Nouveau**, et commencez à rechercher **Déploiement de modèle** jusqu’à ce que vous puissiez le sélectionner dans les options.

![rechercher un déploiement de modèle](./media/resource-group-portal/search-template.png)

Sélectionnez **Déploiement de modèle** à partir des ressources disponibles.

![sélectionner un déploiement de modèle](./media/resource-group-portal/select-template.png)

Après avoir lancé le déploiement du modèle, vous pouvez créer le modèle personnalisé et définir les valeurs pour le déploiement.

![créer un modèle](./media/resource-group-portal/show-custom-template.png)

## Étapes suivantes
Mise en route

- Pour une introduction aux concepts de Resource Manager, consultez [Vue d'ensemble d’Azure Resource Manager](../resource-group-overview.md).
- Pour plus d’informations sur l’utilisation d’Azure PowerShell lors du déploiement de ressources, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).
- Si vous n’avez jamais utilisé l’interface de ligne de commande Azure pour le déploiement de ressources, consultez [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

<!---HONumber=AcomDC_0218_2016-->