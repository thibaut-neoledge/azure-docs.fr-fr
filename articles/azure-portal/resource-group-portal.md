<properties 
	pageTitle="Utiliser le portail Azure pour gérer des ressources Azure | Microsoft Azure" 
	description="Utilisez le Portail Azure et Azure Resource Manager pour gérer vos ressources. Montre comment utiliser des tableaux de bord pour surveiller les ressources." 
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
	ms.date="08/23/2016" 
	ms.author="tomfitz"/>

# Gérer les ressources Azure sur le portail

> [AZURE.SELECTOR]
- [Azure PowerShell](../powershell-azure-resource-manager.md)
- [Interface de ligne de commande Azure](../xplat-cli-azure-resource-manager.md)
- [Portail](resource-group-portal.md)
- [API REST](../resource-manager-rest-api.md)

Cette rubrique montre comment utiliser le [portail Azure](https://portal.azure.com) avec [Azure Resource Manager](../resource-group-overview.md) pour gérer vos ressources Azure. Pour en savoir plus sur le déploiement des ressources avec le portail, voir [Déployer des ressources avec des modèles Resource Manager et le portail Azure](../resource-group-template-deploy-portal.md).

Actuellement, certains services ne prennent pas en charge le portail ou Resource Manager. Pour ces services, vous devez utiliser le [Portail Azure Classic](https://manage.windowsazure.com). Pour connaître l’état de chaque service, voir [Graphique de la disponibilité du portail Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## Gérer des groupes de ressources

1. Pour afficher tous les groupes de ressources de votre abonnement, sélectionnez **Groupes de ressources**.

    ![parcourir les groupes de ressources](./media/resource-group-portal/browse-groups.png)

1. Pour créer un groupe de ressources vide, sélectionnez **Ajouter**.

    ![ajouter un groupe de ressources](./media/resource-group-portal/add-resource-group.png)

1. Fournissez un nom et un emplacement pour le nouveau groupe de ressources. Sélectionnez **Créer**.

    ![Créer un groupe de ressources](./media/resource-group-portal/create-empty-group.png)

1. Vous pourrez avoir besoin de sélectionner **Actualiser** pour afficher le groupe de ressources que vous venez de créer.

    ![actualiser un groupe de ressources](./media/resource-group-portal/refresh-resource-groups.png)

1. Pour personnaliser les informations affichées pour vos groupes de ressources, sélectionnez **Colonnes**.

    ![personnaliser des colonnes](./media/resource-group-portal/select-columns.png)

1. Sélectionnez les colonnes à ajouter, puis sélectionnez **Mettre à jour**.

    ![ajouter des colonnes](./media/resource-group-portal/add-columns.png)

1. Pour plus d’informations sur le déploiement de ressources dans votre nouveau groupe de ressources, voir [Déployer des ressources à l’aide de modèles Resource Manager et du Portail Azure](../resource-group-template-deploy-portal.md).

1. Pour accéder rapidement à un groupe de ressources, vous pouvez épingler le panneau à votre tableau de bord.

    ![épingler un groupe de ressources](./media/resource-group-portal/pin-group.png)

1. Le tableau de bord affiche le groupe de ressources et ses ressources. Vous pouvez sélectionner les groupes de ressources ou l’une de leurs ressources pour accéder à l’élément correspondant.

    ![épingler un groupe de ressources](./media/resource-group-portal/show-resource-group-dashboard.png)

## Baliser des ressources

Vous pouvez appliquer des balises à des groupes de ressources pour organiser logiquement vos ressources. Pour plus d’informations sur l’utilisation des balises, voir [Organisation des ressources Azure à l’aide de balises](../resource-group-using-tags.md).

[AZURE.INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## Surveiller les ressources

Lorsque vous sélectionnez une ressource, le panneau de ressources présente les graphiques et tables par défaut pour la surveillance de ce type de ressource.

1. Sélectionnez une ressource, puis observez la section **Surveillance**. Elle contient des graphiques pertinents pour le type de ressource. L’illustration suivante montre les données de surveillance par défaut pour un compte de stockage.

    ![afficher la surveillance](./media/resource-group-portal/show-monitoring.png)

1. Vous pouvez épingler une section du panneau à votre tableau de bord en sélectionnant les points de suspension (...) situés au-dessus de la section. Vous pouvez aussi personnaliser la taille de la section dans le panneau ou supprimer complètement cette section. L’illustration suivante montre comment épingler, personnaliser ou supprimer la section Processeur et mémoire.

    ![épingler une section](./media/resource-group-portal/pin-cpu-section.png)

1. Après avoir épinglé la section au tableau de bord, vous obtenez le résumé sur le tableau de bord. Vous pouvez le sélectionner immédiatement pour obtenir plus de détails sur les données.

    ![afficher le tableau de bord](./media/resource-group-portal/view-startboard.png)

1. Pour personnaliser entièrement les données que vous surveillez via le portail, accédez à votre tableau de bord par défaut, puis sélectionnez **Nouveau tableau de bord**.

    ![dashboard](./media/resource-group-portal/dashboard.png)

1. Donnez un nom à votre tableau de bord et faites glisser les mosaïques dessus. Les mosaïques sont filtrées par différentes options.

    ![dashboard](./media/resource-group-portal/create-dashboard.png)

     Pour plus d’informations sur l’utilisation des tableaux de bord, visionnez la vidéo [Build Custom Dashboards in the Azure Portal](https://channel9.msdn.com/Blogs/trevor-cloud/azure-portal-dashboards) (Créer des tableaux de bord personnalisés dans le portail Azure). Pour plus d’informations sur le partage de l’accès à un tableau de bord publié, voir [Partage des tableaux de bord Azure](azure-portal-dashboard-share-access.md).

## Gestion des ressources

Le panneau d’une ressource montre les options de gestion de cette ressource. Le portail présente les options de gestion pour ce type de ressource particulier. Les commandes de gestion se trouvent dans la partie supérieure et sur le côté gauche du panneau de la ressource.

![gérer les ressources](./media/resource-group-portal/manage-resources.png)

À partir de ces options, vous pouvez effectuer des opérations telles que le démarrage et l’arrêt d’une machine virtuelle, ou la reconfiguration des propriétés de la machine virtuelle.

## Déplacer des ressources

Si vous souhaitez déplacer des ressources vers un autre groupe de ressources ou un autre abonnement, voir [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../resource-group-move-resources.md).

## Verrouiller des ressources

Vous pouvez verrouiller un abonnement, un groupe de ressources ou une ressource afin d’empêcher d’autres utilisateurs de votre organisation de supprimer ou modifier accidentellement des ressources cruciales. Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](../resource-group-lock-resources.md).

[AZURE.INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## Afficher votre abonnement et vos coûts

Vous pouvez afficher des informations sur votre abonnement et les coûts cumulés de toutes vos ressources. Sélectionnez **Abonnements**, puis l’abonnement de votre choix. Il se peut que vous n’ayez qu’un seul abonnement à sélectionner.

![abonnement](./media/resource-group-portal/select-subscription.png)

Le panneau de l’abonnement vous présente un taux d’avancement.

![taux d’avancement](./media/resource-group-portal/burn-rate.png)

Ainsi qu’une répartition des coûts par type de ressource.

![coût des ressources](./media/resource-group-portal/cost-by-resource.png)

## Exportation du modèle

Après avoir configuré votre groupe de ressources, vous pouvez éventuellement afficher le modèle Resource Manager pour le groupe de ressources. L’exportation du modèle offre deux avantages :

1. Vous pouvez facilement automatiser les futurs déploiements de la solution, car le modèle contient la totalité de l’infrastructure.

2. Vous pouvez vous familiariser avec la syntaxe de modèle en regardant dans la JSON (JavaScript Object Notation) qui représente votre solution.

Pour obtenir des instructions détaillées, voir [Exporter un modèle Azure Resource Manager à partir de ressources existantes](../resource-manager-export-template.md).

## Supprimer des ressources ou un groupe de ressources

La suppression d’un groupe de ressources supprime toutes les ressources qu’il contient. Vous pouvez également supprimer des ressources individuelles d'un groupe de ressources. Faites preuve de prudence lorsque vous supprimez un groupe de ressources, car des ressources d’autres groupes de ressources peuvent y être liées. Resource Manager ne supprime pas les ressources liées, mais ces dernières risquent de ne pas fonctionner correctement sans les ressources attendues.

![supprimer un groupe](./media/resource-group-portal/delete-group.png)

## Étapes suivantes

- Pour visualiser les journaux d’audit, voir [Opérations d’audit avec Resource Manager](../resource-group-audit.md).
- Pour résoudre les erreurs de déploiement, voir [Résolution des problèmes liés aux déploiements de groupes de ressources avec le Portail Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Pour déployer des ressources via le portail, voir [Déployer des ressources à l’aide de modèles Resource Manager et du Portail Azure](../resource-group-template-deploy-portal.md).
- Pour gérer l’accès aux ressources, voir [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](../active-directory/role-based-access-control-configure.md).

<!---HONumber=AcomDC_0824_2016-->