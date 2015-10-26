<properties
	pageTitle="Contrôle d’accès en fonction du rôle Azure Active Directory | Microsoft Azure"
	description="Cet article décrit le contrôle d’accès en fonction du rôle dans Azure."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="10/12/2015"
	ms.author="inhenk"/>

# Contrôle d’accès en fonction du rôle Azure Active Directory

## Contrôle d’accès en fonction du rôle
Le contrôle d’accès en fonction du rôle (RBAC) Azure permet une gestion précise de l’accès pour Azure. Avec le contrôle d’accès en fonction du rôle, vous pouvez séparer les tâches au sein de votre équipe chargée des opérations de développement et accordez aux utilisateurs uniquement les accès nécessaires pour effectuer leurs tâches.

### Concepts de base de la gestion d’accès dans Azure
Chaque abonnement Azure est hébergé dans un annuaire Azure Active Directory. Seuls les utilisateurs, les groupes et les applications de ce répertoire peuvent être autorisés à gérer les ressources de l’abonnement Azure, à l’aide du portail de gestion Azure, des outils de ligne de commande Azure et des API de gestion Azure.

L’accès est octroyé en attribuant le rôle RBAC approprié aux utilisateurs, groupes et applications, dans l’étendue appropriée. Pour accorder l’accès à la totalité de l’abonnement, attribuez un rôle dans l’étendue de l’abonnement. Pour accorder l’accès à un groupe de ressources spécifique au sein d’un abonnement, attribuez un rôle dans l’étendue du groupe de ressources. Vous pouvez également attribuer des rôles à des ressources spécifiques, telles que des sites web, des machines virtuelles et des sous-réseaux, pour accorder l’accès à une seule ressource.

![](./media/role-based-access-control-configure/overview.png)

Le rôle RBAC que vous attribuez aux utilisateurs, groupes et applications détermine les ressources que l’utilisateur (ou l’application) peut gérer au sein de l’étendue.

### Rôles RBAC Azure intégrés
Le contrôle d’accès en fonction du rôle Azure comporte trois rôles de base qui s’appliquent à tous les types de ressources : Propriétaire, Contributeur et Lecteur. Le Propriétaire dispose d’un accès total à toutes les ressources, ainsi que le droit de déléguer l’accès à d’autres personnes. Le Contributeur peut créer et gérer tous types de ressources Azure, mais ne peut pas accorder l’accès à d’autres personnes. Le Lecteur peut uniquement afficher les ressources Azure existantes. Les autres rôles RBAC dans Azure permettent de gérer des ressources Azure spécifiques. Par exemple, le rôle de Contributeur de machine virtuelle autorise la création et la gestion de machines virtuelles, mais pas la gestion du réseau virtuel ou du sous-réseau auquel la machine virtuelle se connecte.

La rubrique [Rôles RBAC intégrés](role-based-access-built-in-roles.md) répertorie les rôles RBAC intégrés disponibles dans Azure. Elle indique les opérations auxquelles chaque rôle intégré accorde l’accès.

### Hiérarchie des ressources Azure et héritage d’accès
Dans Azure, chaque abonnement appartient à un seul répertoire, chaque groupe de ressources appartient à un seul abonnement, et chaque ressource appartient à un seul groupe de ressources. L’accès que vous accordez à des étendues parentes est hérité par les étendues enfant. Si vous attribuez le rôle de lecteur à un groupe Azure AD dans l’étendue de l’abonnement, les membres de ce groupe pourront voir tous les groupes de ressources et toutes les ressources de l’abonnement. Si vous attribuez le rôle de collaborateur à une application dans l’étendue du groupe de ressources, celle-ci pourra gérer les ressources de tous types dans ce groupe de ressources, mais pas dans les autres groupes ressource de l’abonnement.

### Contrôle d’accès en fonction du rôle Azure et Administrateur et coadministrateurs d’abonnement classiques
Les administrateur et coadministrateurs d’abonnement classiques ont un accès total à l’abonnement Azure. Ils peuvent gérer des ressources à l’aide au portail classique (https://manage.windowsazure.com), et les API du Gestionnaire de service Azure, ainsi que le nouveau portail de gestion (https://portal.azure.com), et les nouvelles API Azure Resource Manager. Dans le modèle RBAC, les administrateurs classiques se voient attribuer le rôle de propriétaire dans l’étendue de l’abonnement.

Le modèle d’autorisation plus précis (contrôle d’accès basé sur les rôles Azure) est pris en charge uniquement par le nouveau portail de gestion (https://portal.azure.com) et les API Azure Resource Manager. Les utilisateurs et les applications qui se voient attribuer des rôles RBAC (dans l’étendue abonnement/groupe de ressources/ressource) ne peuvent pas utiliser le portail de gestion classique (http://manage.windowsazure.com) et les API de gestion des services Azure.

### Autorisation pour les opérations de gestion et les opérations de données
Le modèle d’autorisation plus précis (contrôle d’accès basé sur les rôles Azure) est pris en charge uniquement pour les opérations de gestion des ressources Azure dans le portail Azure et les API Azure Resource Manager. Toutes les opérations de niveau de données pour les ressources Azure ne peuvent pas être autorisées via le RBAC. Par exemple, les opérations create/read/update/delete des comptes de stockage peuvent être contrôlées via le RBAC, mais le contrôle des opérations create/read/update/delete des objets blob ou des tables dans le compte de stockage n'est pas encore possible. De même, les opérations create/read/update/delete d'une base de données SQL peuvent être contrôlées via le RBAC, mais le contrôle des opérations create/read/update/delete des tables SQL dans la base de données n'est pas encore possible.

## Gérer l’accès à l’aide du portail de gestion Azure
### Voir l’accès
Sélectionnez les paramètres d’accès dans la section Essentials du panneau du groupe de ressources. Le panneau **Users** répertorie l’ensemble des utilisateurs, groupes et applications qui peuvent accéder au groupe de ressources. L’accès est attribué sur le groupe de ressources ou hérité d’une affectation sur l’abonnement parent.

![](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE]Les administrateurs et coadministrateurs d’abonnement classiques sont les propriétaires de l’abonnement dans le nouveau modèle RBAC.

### Ajout d’un accès
1. Cliquez sur l’icône **Add** (Ajouter) du panneau **Users** (Utilisateurs). ![](./media/role-based-access-control-configure/grant-access1.png)
2. Sélectionnez le rôle que vous voulez attribuer.
3. Recherchez et sélectionnez l’utilisateur, le groupe ou l’application auquel vous voulez accorder l’accès.
4. Recherchez des utilisateurs, groupes et applications dans l’annuaire à l’aide de noms complets, d’adresses de messagerie et d’identificateurs d’objet.![](./media/role-based-access-control-configure/grant-access2.png)

### Supprimer un accès
1. Dans le panneau **Users** (Utilisateurs), sélectionnez l’affectation de rôle à supprimer.
2. Cliquez sur l’icône **Remove** (Supprimer) dans le panneau des détails de l’affectation.
3. Cliquez sur **Yes** pour confirmer.

![](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE]Les affectations héritées ne peuvent pas être supprimées des étendues enfant. Vous devez accéder à l’étendue parente et supprimer ces affectations.


![](./media/role-based-access-control-configure/remove-access2.png)

## Gérer l’accès avec Azure PowerShell
L’accès peut être géré à l’aide des commandes du contrôle d’accès en fonction du rôle Azure dans les outils Azure PowerShell.

-	Utilisez `Get-AzureRoleDefinition` pour répertorier les rôles RBAC pouvant être affectés et inspecter les opérations auxquelles ils accordent l’accès.

-	Utilisez `Get-AzureRoleAssignment` pour répertorier les affectations d’accès RBAC valables au niveau de l’abonnement, du groupe de ressources ou de la ressource. Utilisez le paramètre `ExpandPrincipalGroups` pour répertorier les affectations d’accès d’un utilisateur spécifique ou de groupes dont l’utilisateur est membre. Utilisez le paramètre `IncludeClassicAdministrators` pour répertorier également les administrateurs et coadministrateurs d’abonnement classiques.

-	Utilisez `New-AzureRoleAssignment` pour accorder l’accès à des utilisateurs, des groupes et des applications.

-	Utilisez `Remove-AzureRoleAssignment` pour supprimer un accès.

Pour des exemples plus détaillés de la gestion de l’accès à l’aide d’Azure PowerShell, consultez la rubrique [Gérer l’accès avec Azure PowerShell](role-based-access-control-manage-access-powershell.md).

## Gérer l’accès à l’aide de l’interface de ligne de commande Azure
L’accès peut être géré à l’aide des commandes du contrôle d’accès en fonction du rôle Azure dans l’interface de ligne de commande Azure.

-	Utilisez `azure role list` pour répertorier les rôles RBAC pouvant être attribués. Utilisez azure role show pour examiner les opérations auxquelles ils donnent accès.

-	Utilisez `azure role assignment list` pour répertorier les affectations d’accès RBAC valables au niveau de l’abonnement, du groupe de ressources ou de la ressource. Utilisez l’option `expandPrincipalGroups` pour répertorier les affectations d’accès d’un utilisateur spécifique ou de groupes dont l’utilisateur est membre. Utilisez le paramètre `includeClassicAdministrators` pour répertorier également les administrateurs et coadministrateurs d’abonnement classiques.

-	Utilisez `azure role assignment create` pour accorder l’accès à des utilisateurs, des groupes et des applications.

-	Utilisez `azure role assignment delete` pour supprimer un accès.

Pour des exemples plus détaillés de la gestion de l’accès à l’aide de l’interface de ligne de commande Azure, consultez la rubrique [Gérer l’accès avec l’interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md).

## Utiliser le rapport d’historique des modifications d’accès
Toutes les modifications d’accès effectuées dans vos abonnements Azure sont enregistrées dans les événements Azure.

### Créer un rapport avec Azure PowerShell
Pour créer un rapport indiquant qui a accordé/révoqué tel type d’accès à telle personne sur telle étendue au sein de vos abonnements Azure, utilisez la commande PowerShell suivante :

    Get-AzureAuthorizationChangeLog

### Créer un rapport avec l’interface de ligne de commande Azure
Pour créer un rapport indiquant qui a accordé/révoqué tel type d’accès à telle personne sur telle étendue au sein de vos abonnements Azure, utilisez la commande de l’interface de ligne de commande suivante :

    azure authorization changelog

> [AZURE.NOTE]Les modifications d’accès peuvent être recherchées dans les 90 jours précédents (par lots de 15 jours).

L’exemple suivant répertorie toutes les modifications d’accès de l’abonnement au cours des 7 derniers jours.

![](./media/role-based-access-control-configure/access-change-history.png)

### Exporter les modifications d’accès vers une feuille de calcul
Pour examiner les modifications d’accès, il est plus pratique de les exporter dans une feuille de calcul.

![](./media/role-based-access-control-configure/change-history-spreadsheet.png)

<!---HONumber=Oct15_HO3-->