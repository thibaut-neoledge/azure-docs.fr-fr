<properties
	pageTitle="Gestion du Contrôle d'accès en fonction du rôle avec Azure PowerShell | Microsoft Azure"
	description="Comment gérer le Contrôle d'accès en fonction du rôle Azure PowerShell, notamment le référencement des rôles, l'affectation de rôles et la suppression d'attributions de rôles."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="01/22/2016"
	ms.author="kgremban"/>

# Gestion du Contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## Répertorier les rôles pour le contrôle d’accès basé sur les rôles (RBAC)
### Répertorier tous les rôles disponibles
Pour répertorier les rôles RBAC pouvant être affectés et inspecter les opérations auxquelles ils accordent l'accès, utilisez :

		Get-AzureRmRoleDefinition

![RBAC PowerShell - Get-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Répertorier les actions d'un rôle
Pour répertorier les actions pour un rôle spécifique, utilisez :

    Get-AzureRmRoleDefinition <role name>

![RBAC PowerShell - Get-AzureRmRoleDefinition pour un rôle spécifique - capture d’écran](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Répertorier les accès
### Répertorier toutes les affectations de rôles dans l'abonnement sélectionné
Pour répertorier les affectations d'accès RBAC valables au niveau de l'abonnement, d'une ressource ou d'un groupe de ressources, utilisez :

    Get-AzureRmRoleAssignment

###	Répertorier les affectations de rôles valables dans un groupe de ressources
Pour répertorier les affectations d'accès pour un groupe de ressources, utilisez :

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![RBAC PowerShell - Get-AzureRmRoleAssignment pour un groupe de ressource - capture d’écran](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Répertorier les affectations d'un utilisateur, notamment les rôles affectés à des groupes d'utilisateurs
Pour répertorier les affectations d'accès d'un utilisateur spécifique ou de groupes dont l'utilisateur est membre, utilisez :

    Get-AzureRmRoleAssignment -ExpandPrincipalGroups

![RBAC PowerShell - Get-AzureRmRoleAssignment pour un utilisateur - capture d’écran](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Répertorier les affectations classiques des rôles Administrateur de service et Coadministrateur
Pour répertorier les affectations d'accès de liste pour les administrateurs d'abonnement et les coadministrateurs classiques, utilisez :

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## Accorder l'accès
### Rechercher des ID d’objet
Pour utiliser les séquences de commande suivantes, vous devez d’abord rechercher les ID d’objet. Nous partons du principe que vous connaissez déjà l’ID d’abonnement que vous utilisez. Sinon, consultez [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) sur MSDN.

#### Rechercher l’ID d’objet pour un groupe Azure AD
Pour obtenir l’ID d’objet pour un groupe Azure AD, utilisez :

    Get-AzureRmADGroup -SearchString <group name in quotes>

#### Rechercher l’ID d’objet pour un principal du service Azure AD
Pour obtenir l’ID d’objet pour un principal du service Azure AD, utilisez :

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### Affectation d'un rôle à un groupe pour l'abonnement
Pour accorder l'accès à un groupe pour l'abonnement, utilisez :

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - capture d’écran](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### Affectation d'un rôle à une application pour l'abonnement
Pour accorder l'accès à une application pour l'abonnement, utilisez :

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - capture d’écran](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Affectation d'un rôle à un utilisateur pour le groupe de ressources
Pour accorder l'accès à un utilisateur pour le groupe :

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - capture d’écran](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Affectation d'un rôle à un groupe au niveau des ressources
Pour accorder l'accès à un groupe au niveau des ressources, utilisez :

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - capture d’écran](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Suppression d'accès
Pour supprimer l'accès pour des utilisateurs, des groupes et des applications, utilisez :

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - capture d’écran](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Créer un rôle personnalisé
Pour créer un rôle personnalisé, utilisez la commande `New-AzureRmRoleDefinition`.

L’exemple suivant crée un rôle personnalisé appelé *Opérateur de machine virtuelle* qui accorde l’accès à toutes les opérations de lecture des fournisseurs de ressources *Microsoft.Compute*, *Microsoft.Storage* et *Microsoft.Network* ainsi que l’accès pour démarrer, redémarrer et analyser des machines virtuelles. Le rôle personnalisé peut être utilisé dans deux abonnements.

![RBAC PowerShell - Get-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## Modifier un rôle personnalisé
Pour modifier un rôle personnalisé, utilisez d’abord la commande `Get-AzureRmRoleDefinition` pour récupérer la définition de rôle. Apportez ensuite les modifications souhaitées à la définition de rôle. Enfin, utilisez la commande `Set-AzureRmRoleDefinition` pour enregistrer la définition de rôle modifiée.

L’exemple suivant ajoute l’opération `Microsoft.Insights/diagnosticSettings/*` au rôle personnalisé *Opérateur de machine virtuelle*.

![RBAC PowerShell - Set-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

L’exemple suivant ajoute un abonnement Azure aux étendues attribuables du rôle personnalisé Opérateur de machine virtuelle.

![RBAC PowerShell - Set-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## Supprimer un rôle personnalisé

Pour supprimer un rôle personnalisé, utilisez la commande `Remove-AzureRmRoleDefinition`.

L’exemple suivant supprime le rôle personnalisé *Opérateur de machine virtuelle*.

![RBAC PowerShell - Remove-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## Répertorier les rôles personnalisés
Pour répertorier les rôles pouvant être affectés dans une étendue, utilisez la commande `Get-AzureRmRoleDefinition`.

L’exemple suivant répertorie tous les rôles pouvant être affectés dans l’abonnement sélectionné.

![RBAC PowerShell - Get-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

Dans l’exemple suivant, le rôle personnalisé *Opérateur de machine virtuelle* n’est pas disponible dans l’abonnement *Production4*, car cet abonnement ne figure pas dans l’élément **AssignableScopes** du rôle.

![RBAC PowerShell - Get-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## Rubriques RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0204_2016-->