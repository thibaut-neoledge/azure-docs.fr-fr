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
	ms.date="01/04/2016"
	ms.author="inhenk"/>

# Contrôle d’accès en fonction du rôle Azure

## Contrôle d’accès en fonction du rôle
Le contrôle d’accès en fonction du rôle (RBAC) Azure permet une gestion précise de l’accès pour Azure. Avec le contrôle d’accès en fonction du rôle, vous pouvez séparer les tâches au sein de votre équipe chargée des opérations de développement et accorder aux utilisateurs uniquement les accès nécessaires pour accomplir leur travail.

### Concepts de base de la gestion d’accès dans Azure
Chaque abonnement Azure est associé à un annuaire Azure Active Directory. Seuls les utilisateurs, les groupes et les applications de ce répertoire peuvent être autorisés à gérer les ressources de l’abonnement Azure à l’aide du portail Azure, des outils de ligne de commande Azure et des API de gestion Azure.

L’accès est octroyé en attribuant le rôle RBAC approprié aux utilisateurs, groupes et applications, dans l’étendue appropriée. Pour accorder l’accès à la totalité de l’abonnement, attribuez un rôle dans l’étendue de l’abonnement. Pour accorder l’accès à un groupe de ressources spécifique au sein d’un abonnement, attribuez un rôle dans l’étendue du groupe de ressources. Vous pouvez également attribuer des rôles à des ressources spécifiques, telles que des sites web, des machines virtuelles et des sous-réseaux, pour accorder l’accès à une seule ressource.

![](./media/role-based-access-control-configure/overview.png)

Le rôle RBAC que vous attribuez aux utilisateurs, groupes et applications détermine les ressources que l’utilisateur (ou l’application) peut gérer au sein de l’étendue.

### Rôles RBAC Azure intégrés
Le contrôle d’accès en fonction du rôle Azure comporte trois rôles de base qui s’appliquent à tous les types de ressources : Propriétaire, Contributeur et Lecteur. Le Propriétaire dispose d’un accès total à toutes les ressources, ainsi que le droit de déléguer l’accès à d’autres personnes. Le Contributeur peut créer et gérer tous types de ressources Azure, mais ne peut pas accorder l’accès à d’autres personnes. Le Lecteur peut uniquement afficher les ressources Azure existantes. Les autres rôles RBAC dans Azure permettent de gérer des ressources Azure spécifiques. Par exemple, le rôle de Contributeur de machine virtuelle autorise la création et la gestion de machines virtuelles, mais pas la gestion du réseau virtuel ou du sous-réseau auquel la machine virtuelle se connecte.

La rubrique [Rôles RBAC intégrés](role-based-access-built-in-roles.md) répertorie les rôles RBAC intégrés disponibles dans Azure. Elle indique les opérations auxquelles chaque rôle intégré accorde l’accès.

### Hiérarchie des ressources Azure et héritage d’accès
Dans Azure, chaque abonnement appartient à un seul répertoire, chaque groupe de ressources appartient à un seul abonnement, et chaque ressource appartient à un seul groupe de ressources. L’accès que vous accordez à des étendues parentes est hérité par les étendues enfant. Si vous attribuez le rôle de lecteur à un groupe Azure AD dans l’étendue de l’abonnement, les membres de ce groupe pourront voir tous les groupes de ressources et toutes les ressources de l’abonnement. Si vous attribuez le rôle de collaborateur à une application dans l’étendue du groupe de ressources, celle-ci pourra gérer les ressources de tous types dans ce groupe de ressources, mais pas dans les autres groupes ressource de l’abonnement.

### Contrôle d’accès en fonction du rôle Azure et Administrateur et coadministrateurs d’abonnement classiques
Les administrateur et coadministrateurs d’abonnement classiques ont un accès total à l’abonnement Azure. Ils peuvent gérer des ressources à l’aide du portail Azure Classic (https://manage.windowsazure.com) et des API de gestion des services Azure, ainsi qu’à l’aide du portail Azure (https://portal.azure.com) et des nouvelles API Azure Resource Manager. Dans le modèle RBAC, les administrateurs classiques se voient attribuer le rôle de propriétaire dans l’étendue de l’abonnement.

Le modèle d’autorisation plus précis (contrôle d’accès basé sur les rôles Azure) est pris en charge uniquement par le portail Azure (https://portal.azure.com) et les API Azure Resource Manager. Les utilisateurs et les applications qui se voient attribuer des rôles RBAC (dans l’étendue abonnement/groupe de ressources/ressource) ne peuvent pas utiliser le portail de gestion classique (http://manage.windowsazure.com) et les API de gestion des services Azure.

### Autorisation pour les opérations de gestion et les opérations de données
Le modèle d’autorisation plus précis (contrôle d’accès basé sur les rôles Azure) est pris en charge uniquement pour les opérations de gestion des ressources Azure dans le portail Azure et les API Azure Resource Manager. Toutes les opérations de niveau de données pour les ressources Azure ne peuvent pas être autorisées via le RBAC. Par exemple, les opérations create/read/update/delete des comptes de stockage peuvent être contrôlées via le RBAC, mais le contrôle des opérations create/read/update/delete des objets blob ou des tables dans le compte de stockage n'est pas encore possible. De même, les opérations create/read/update/delete d'une base de données SQL peuvent être contrôlées via le RBAC, mais le contrôle des opérations create/read/update/delete des tables SQL dans la base de données n'est pas encore possible.

## Gérer l’accès à l’aide du portail Azure
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

-	Utilisez `Get-AzureRmRoleDefinition` pour répertorier les rôles RBAC pouvant être affectés et inspecter les opérations auxquelles ils accordent l’accès.

-	Utilisez `Get-AzureRmRoleAssignment` pour répertorier les affectations d’accès RBAC valables au niveau de l’abonnement, du groupe de ressources ou de la ressource. Utilisez le paramètre `ExpandPrincipalGroups` pour répertorier les affectations d’accès d’un utilisateur spécifique ou de groupes dont l’utilisateur est membre. Utilisez le paramètre `IncludeClassicAdministrators` pour répertorier également les administrateurs et coadministrateurs d’abonnement classiques.

-	Utilisez `New-AzureRmRoleAssignment` pour accorder l’accès à des utilisateurs, des groupes et des applications.

-	Utilisez `Remove-AzureRmRoleAssignment` pour supprimer un accès.

Pour des exemples plus détaillés de la gestion de l’accès à l’aide d’Azure PowerShell, consultez la rubrique [Gérer l’accès avec Azure PowerShell](role-based-access-control-manage-access-powershell.md).

## Gérer l’accès à l’aide de l’interface de ligne de commande Azure
L’accès peut être géré à l’aide des commandes du contrôle d’accès en fonction du rôle Azure dans l’interface de ligne de commande Azure.

-	Utilisez `azure role list` pour répertorier les rôles RBAC pouvant être attribués. Utilisez azure role show pour examiner les opérations auxquelles ils donnent accès.

-	Utilisez `azure role assignment list` pour répertorier les affectations d’accès RBAC valables au niveau de l’abonnement, du groupe de ressources ou de la ressource. Utilisez l’option `expandPrincipalGroups` pour répertorier les affectations d’accès d’un utilisateur spécifique ou de groupes dont l’utilisateur est membre. Utilisez le paramètre `includeClassicAdministrators` pour répertorier également les administrateurs et coadministrateurs d’abonnement classiques.

-	Utilisez `azure role assignment create` pour accorder l’accès à des utilisateurs, des groupes et des applications.

-	Utilisez `azure role assignment delete` pour supprimer un accès.

Pour des exemples plus détaillés de la gestion de l’accès à l’aide de l’interface de ligne de commande Azure, consultez la rubrique [Gérer l’accès avec l’interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md).

## Gestion de l'accès à l'aide de l'API REST
Consultez [Gestion du contrôle d'accès basé sur les rôles à l'aide de l'API REST](role-based-access-control-manage-access-rest.md) pour obtenir des exemples plus détaillés de gestion de l'accès avec l'API REST.

## Utiliser le rapport d’historique des modifications d’accès
Toutes les modifications d’accès effectuées dans vos abonnements Azure sont enregistrées dans les événements Azure.

### Créer un rapport avec Azure PowerShell
Pour créer un rapport indiquant qui a accordé/révoqué tel type d’accès à telle personne sur telle étendue au sein de vos abonnements Azure, utilisez la commande PowerShell suivante :

    `Get-AzureAuthorizationChangeLog`

### Créer un rapport avec l’interface de ligne de commande Azure
Pour créer un rapport indiquant qui a accordé/révoqué tel type d’accès à telle personne sur telle étendue au sein de vos abonnements Azure, utilisez la commande de l’interface de ligne de commande suivante :

    `azure authorization changelog`

> [AZURE.NOTE]Les modifications d’accès peuvent être recherchées dans les 90 jours précédents (par lots de 15 jours).

La capture d’écran suivante répertorie toutes les modifications d’accès de l’abonnement au cours des 7 derniers jours.

![](./media/role-based-access-control-configure/access-change-history.png)

### Exporter les modifications d’accès vers une feuille de calcul
Pour examiner les modifications d’accès, il est plus pratique de les exporter dans une feuille de calcul.

![](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## Rôles personnalisés dans le contrôle d’accès en fonction du rôle (RBAC) Azure
Créez un rôle personnalisé dans le RBAC Azure si aucun des rôles intégrés ne répond à vos besoins d’accès spécifiques. Il est possible de créer des rôles personnalisés à l’aide des outils en ligne de commande de RBAC dans Azure PowerShell et de l’interface de ligne de commande Azure. À l’instar des rôles intégrés, les rôles personnalisés peuvent être affectés à des utilisateurs, des groupes et des applications dans l’étendue d’abonnements, de groupes de ressources ou de ressources.

Voici un exemple de définition de rôle personnalisé permettant de surveiller et de redémarrer des machines virtuelles :

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
### Actions
La propriété Actions d’un rôle personnalisé spécifie les opérations Azure auxquelles le rôle accorde l’accès. Il s’agit d’un ensemble de chaînes d’opération qui identifient les opérations sécurisables des fournisseurs de ressources Azure. Les chaînes d’opération contenant des caractères génériques (*) accordent l’accès à toutes les opérations qui correspondent à la chaîne d’opération. Exemple :

-	`*/read` accorde l’accès aux opérations de lecture pour tous les types de ressources de l’ensemble des fournisseurs de ressources Azure.
-	`Microsoft.Network/*/read` accorde l’accès aux opérations de lecture pour tous les types de ressources dans le fournisseur de ressources Microsoft.Network d’Azure.
-	`Microsoft.Compute/virtualMachines/*` accorde l’accès à toutes les opérations des machines virtuelles et ses types de ressources enfants.
-	`Microsoft.Web/sites/restart/Action` autorise le redémarrage des sites web.

Utilisez la commande `Get-AzureRmProviderOperation` ou `azure provider operations show` pour répertorier les opérations des fournisseurs de ressources Azure. Vous pouvez également utiliser ces commandes pour vérifier qu’une chaîne d’opération est valide et pour développer les chaînes d’opération génériques.

![](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

![](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

### Non-actions
Si l’ensemble des opérations que vous souhaitez autoriser peut être plus facilement exprimé en excluant des opérations spécifiques qu’en incluant toutes les opérations à l’exception des opérations à exclure, utilisez la propriété **NotActions** d’un rôle personnalisé. L’accès effectif accordé par un rôle personnalisé est déterminé en excluant les opérations **NotActions** des opérations Actions.

> [AZURE.NOTE]Si un utilisateur se voit attribuer un rôle qui exclut une opération dans **NotActions** et un second rôle qui accorde l'accès à cette même opération, il sera autorisé à effectuer cette opération. **NotActions** n’est pas une règle de refus : il s’agit simplement d’un moyen pratique pour créer un ensemble d’opérations autorisées lorsque des opérations spécifiques doivent être exclues.

### AssignableScopes
La propriété **AssignableScopes** du rôle personnalisé spécifie les étendues (abonnements, groupes de ressources ou ressources) au sein desquelles le rôle personnalisé peut être affecté à des utilisateurs, des groupes et des applications. À l’aide de **AssignableScopes**, vous pouvez rendre le rôle personnalisé disponible uniquement dans les abonnements ou les groupes de ressources qui le nécessitent afin de ne pas surcharger l’expérience utilisateur pour le reste des abonnements ou des groupes de ressources.

> [AZURE.NOTE]Vous devez utiliser au moins un abonnement, un groupe de ressources ou ID de ressource.* La propriété **AssignableScopes** d'un rôle personnalisé contrôle également qui est autorisé à afficher, mettre à jour et supprimer le rôle. Voici quelques exemples d’étendues d’affectation valides :

-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”, “/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624” : le rôle peut être affecté dans deux abonnements.
-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e” : le rôle peut être affecté dans un seul abonnement.
-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network” : le rôle peut uniquement être affecté dans le groupe de ressources Network.

### Contrôle d’accès des rôles personnalisés
La propriété **AssignableScopes** du rôle personnalisé détermine les personnes autorisées à afficher, modifier et supprimer le rôle.

**Qui peut créer un rôle personnalisé ?** La création d’un rôle personnalisé n’aboutit que si l’utilisateur qui crée le rôle est autorisé à créer un rôle personnalisé pour l’ensemble des étendues **AssignableScopes** spécifiées. La création d’un rôle personnalisé n’aboutit que si l’utilisateur qui crée le rôle peut effectuer l’opération `Microsoft.Authorization/roleDefinition/write operation` dans l’ensemble des étendues **AssignableScopes** du rôle. Par conséquent, les propriétaires (et les administrateurs de l’accès utilisateur) d’abonnements, de groupes de ressources et de ressources peuvent créer des rôles personnalisés utilisables au sein de ces étendues.

**Qui peut modifier un rôle personnalisé ?** Les utilisateurs autorisés à mettre à jour des rôles personnalisés pour l’ensemble des étendues **AssignableScopes** d’un rôle peuvent modifier ce rôle personnalisé. Les utilisateurs qui peuvent effectuer l’opération `Microsoft.Authorization/roleDefinition/write` dans l’ensemble des étendues **AssignableScopes** d’un rôle personnalisé peuvent modifier ce rôle personnalisé. Par exemple, si un rôle personnalisé peut être affecté dans deux abonnements Azure (c’est-à-dire si sa propriété **AssignableScopes** comporte deux abonnements), un utilisateur doit être le propriétaire (ou l’administrateur de l’accès utilisateur) de ces deux abonnements pour pouvoir modifier le rôle personnalisé.

**Qui est autorisé à afficher les rôles personnalisés pouvant être affectés dans une étendue ?** Les utilisateurs qui peuvent effectuer l’opération `Microsoft.Authorization/roleDefinition/read` dans une étendue sont autorisés à afficher les rôles RBAC pouvant être affectés dans cette étendue. Tous les rôles intégrés dans le contrôle d’accès en fonction du rôle Azure permettent d’afficher les rôles pouvant être affectés.

<!---HONumber=AcomDC_0107_2016-->