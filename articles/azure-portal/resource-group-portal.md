<properties 
	pageTitle="Utiliser le portail Azure pour gérer des ressources Azure | Microsoft Azure" 
	description="Utilisez le Portail Azure et Azure Resource Manager pour gérer vos ressources. Montre comment utiliser des tableaux de bord et des vignettes pour surveiller les ressources." 
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
	ms.date="06/10/2016" 
	ms.author="tomfitz"/>


# Gérer les ressources Azure sur le portail

> [AZURE.SELECTOR]
- [Portail](azure-portal/resource-group-portal.md)
- [Interface de ligne de commande Azure](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [Nœud](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

Cette rubrique montre comment utiliser le [portail Azure](https://portal.azure.com) avec [Azure Resource Manager](../resource-group-overview.md) pour gérer vos ressources Azure. Pour en savoir plus sur le déploiement des ressources avec le portail, consultez [Déployer des ressources avec des modèles Resource Manager et le portail Azure](../resource-group-template-deploy-portal.md).

Actuellement, certains services ne prennent pas en charge le portail ou Resource Manager. Pour ces services, vous devez utiliser le [portail Azure Classic](https://manage.windowsazure.com). Pour connaître l’état de chaque service, consultez [Graphique de la disponibilité du portail Azure](https://azure.microsoft.com/features/azure-portal/availability/).

<a id="access-control-for-azure-dashboards" />
## Personnaliser le tableau de bord pour surveiller les ressources

Le portail fournit un tableau de bord que vous pouvez utiliser pour surveiller et gérer vos ressources. Le tableau de bord est entièrement personnalisable, et vous pouvez créer plusieurs tableaux de bord afin de fournir facilement différents affichages de l’abonnement.

![dashboard](./media/resource-group-portal/dashboard.png)

> [AZURE.TIP] Le meilleur moyen d’en savoir plus sur l’utilisation des tableaux de bord est de regarder la vidéo [Créer des tableaux de bord personnalisés dans le portail Microsoft Azure](https://channel9.msdn.com/Blogs/trevor-cloud/azure-portal-dashboards).

### Partage de tableaux de bord Azure et contrôle d’accès
Après avoir configuré un tableau de bord, vous pouvez le publier et le partager avec d’autres utilisateurs de votre organisation. Le [Contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md) Azure régit l’accès aux informations affichées par les mosaïques dans le portail. Tous les tableaux de bord publiés sont implémentés en tant que ressources Azure. Du point de vue du contrôle d’accès, les tableaux de bord sont traités de la même manière qu’une machine virtuelle ou un compte de stockage.

Voici un exemple. Supposons que vous disposez d’un abonnement Azure et que les différents membres de votre équipe ont reçu les rôles de **propriétaire**, **contributeur** ou **lecteur** de l’abonnement. Les utilisateurs associés au rôle de propriétaire ou de contributeur peuvent répertorier, afficher, créer, modifier ou supprimer des tableaux de bord dans l’abonnement. Les utilisateurs ayant le rôle de lecteur peuvent répertorier et afficher des tableaux de bord, sans les modifier ou les supprimer. Les utilisateurs ayant un accès en lecture peuvent apporter des modifications locales à un tableau de bord publié (par exemple, lors de la résolution d’un problème), mais ne peuvent pas republier ces modifications sur le serveur. Ils pourront effectuer une copie privée du tableau de bord pour leur propre usage.

Les vignettes individuelles du tableau de bord appliquent leurs propres exigences en matière de contrôle d’accès, compte tenu des ressources qu’elles affichent. Par conséquent, vous pouvez concevoir un tableau de bord partagé à grande échelle tout en protégeant les données contenues dans chaque vignette.

## Gérer des groupes de ressources

1. Pour afficher tous les groupes de ressources de votre abonnement, sélectionnez **Groupes de ressources**.

    ![parcourir les groupes de ressources](./media/resource-group-portal/browse-groups.png)

2. Sélectionnez le groupe de ressources que vous souhaitez gérer. Un panneau vous fournit des informations sur ce groupe de ressources, notamment une liste de toutes les ressources qu’il contient.

    ![Résumé du groupe de ressources](./media/resource-group-portal/group-summary.png)

    Sélectionner une ressource vous donne des détails sur cette ressource.

3. Dans le panneau d’une ressource, vous pouvez ajouter des tables et graphiques supplémentaires en sélectionnant **Ajouter une section** sous le résumé.

    ![ajoutez une section](./media/resource-group-portal/add-section.png)

4. Une galerie de vignettes s’affiche pour vous permettre de sélectionner les informations à inclure dans le panneau. Les types de vignettes affichées sont filtrés par type de ressource. Si vous sélectionnez une autre ressource, vous obtiendrez des vignettes différentes.

    ![ajoutez une section](./media/resource-group-portal/tile-gallery.png)

5. Faites glisser la vignette de votre choix dans l’espace disponible.

    ![faire glisser une vignette](./media/resource-group-portal/drag-tile.png)

6. Sélectionnez **Terminé** en haut du portail. Votre nouvelle vue est maintenant intégrée au panneau.

    ![afficher une vignette](./media/resource-group-portal/show-lens.png)

7. Pour accéder rapidement à un groupe de ressources, vous pouvez épingler le panneau à votre tableau de bord.

    ![épingler un groupe de ressources](./media/resource-group-portal/pin-group.png)

    Vous pouvez également épingler une section du panneau à votre tableau de bord en cliquant sur les points de suspension (...) situés au-dessus de la section. Vous pouvez aussi personnaliser la taille de la section dans le panneau ou supprimer complètement cette section. L’illustration suivante montre comment épingler, personnaliser ou supprimer la section Processeur et mémoire.

    ![épingler une section](./media/resource-group-portal/pin-cpu-section.png)

8. Après avoir épinglé la section au tableau de bord, vous obtenez le résumé sur le tableau de bord.

    ![afficher le tableau de bord](./media/resource-group-portal/view-startboard.png)

Vous pouvez le sélectionner immédiatement pour obtenir plus de détails sur les données.

## Baliser des ressources

Vous pouvez appliquer des balises à des groupes de ressources pour organiser logiquement vos ressources. Pour plus d’informations sur l’utilisation des balises par le biais du portail, consultez [Organisation des ressources Azure à l’aide de balises](../resource-group-using-tags.md).

## Afficher votre abonnement et vos coûts

Vous pouvez afficher des informations sur votre abonnement et sur les coûts cumulés de toutes vos ressources. Sélectionnez **Abonnements** suivi de l’abonnement de votre choix. Il se peut que vous n’ayez qu’un seul abonnement à sélectionner.

![subscription](./media/resource-group-portal/select-subscription.png)

Dans le panneau de l’abonnement, vous voyez un taux d’avancement.

![taux d’avancement](./media/resource-group-portal/burn-rate.png)

Ainsi qu’une répartition des coûts par type de ressource.

![coût des ressources](./media/resource-group-portal/cost-by-resource.png)

## Exportation du modèle

Après avoir configuré votre groupe de ressources, vous pouvez éventuellement afficher le modèle Resource Manager pour le groupe de ressources. L’exportation du modèle offre deux avantages :

1. Vous pouvez facilement automatiser des déploiements futurs de la solution car l’ensemble de l’infrastructure est définie dans le modèle.

2. Vous pouvez vous familiariser avec la syntaxe de modèle en regardant dans la JSON (JavaScript Object Notation) qui représente votre solution.

Pour des instructions détaillées, consultez [Exporter un modèle Azure Resource Manager à partir de ressources existantes](../resource-manager-export-template.md).

## Supprimer des ressources ou un groupe de ressources

La suppression d’un groupe de ressources supprime toutes les ressources qu’il contient. Vous pouvez également supprimer des ressources individuelles d'un groupe de ressources. Soyez prudent lorsque vous supprimez un groupe de ressources car des ressources d'autres groupes de ressources peuvent y être liées. Les ressources liées ne seront pas supprimées, mais elles risquent de ne pas fonctionnent comme prévu.

![supprimer un groupe](./media/resource-group-portal/delete-group.png)


## Étapes suivantes

- Pour afficher les journaux d’audit, consultez [Opérations d’audit avec Resource Manager](../resource-group-audit.md).
- Pour résoudre des problèmes liés à un déploiement, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources avec le Portail Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Pour déployer des ressources avec le portail, consultez [Déployer des ressources avec des modèles Resource Manager et le portail Azure](../resource-group-template-deploy-portal.md).

<!---HONumber=AcomDC_0706_2016-->