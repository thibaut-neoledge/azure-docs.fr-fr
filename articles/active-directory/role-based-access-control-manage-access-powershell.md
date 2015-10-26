<properties
	pageTitle="Gestion du Contrôle d'accès en fonction du rôle avec Azure PowerShell | Microsoft Azure"
	description="Comment gérer le Contrôle d'accès en fonction du rôle Azure PowerShell, notamment le référencement des rôles, l'affectation de rôles et la suppression d'attributions de rôles."
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

# Gestion du Contrôle d'accès en fonction du rôle avec Azure PowerShell
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## Répertorier les rôles RBAC
### Répertorier tous les rôles disponibles
Pour répertorier les rôles RBAC pouvant être affectés et inspecter les opérations auxquelles ils accordent l'accès, utilisez :

		Get-AzureRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Répertorier les actions d'un rôle
Pour répertorier les actions pour un rôle spécifique, utilisez :

    Get-AzureRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Répertorier les accès
### Répertorier toutes les affectations de rôles dans l'abonnement sélectionné
Pour répertorier les affectations d'accès RBAC valables au niveau de l'abonnement, d'une ressource ou d'un groupe de ressources, utilisez :

    Get-AzureRoleAssignment

###	Répertorier les affectations de rôles valables dans un groupe de ressources
Pour répertorier les affectations d'accès pour un groupe de ressources, utilisez :

    Get-AzureRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Répertorier les affectations d'un utilisateur, notamment les rôles affectés à des groupes d'utilisateurs
Pour répertorier les affectations d'accès d'un utilisateur spécifique ou de groupes dont l'utilisateur est membre, utilisez :

    Get-AzureRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Répertorier les affectations classiques des rôles Administrateur de service et Coadministrateur
Pour répertorier les affectations d'accès de liste pour les administrateurs d'abonnement et les coadministrateurs classiques, utilisez :

    Get-AzureRoleAssignment -IncludeClassicAdministrators

## Accorder l'accès
### Rechercher des ID d'objet
Pour utiliser les séquences de commande suivantes, vous devez rechercher tout d'abord les ID d'objet. Nous partons du principe que vous connaissez déjà l'ID d'abonnement que vous utilisez. Sinon, consulter [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) sur MSDN.

#### Rechercher l'ID d'objet pour un groupe Azure AD
Pour obtenir l'ID d'objet pour un groupe Azure AD, utilisez :

    Get-AzureADGroup -SearchString <group name in quotes>

#### Rechercher l'ID d'objet pour un principal du service Azure AD
Pour obtenir l'ID d'objet pour un principal du service Azure AD, utilisez : Get-AzureADServicePrincipal -SearchString <service name in quotes>

### Affectation d'un rôle à un groupe pour l'abonnement
Pour accorder l'accès à un groupe pour l'abonnement, utilisez :

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### Affectation d'un rôle à une application pour l'abonnement
Pour accorder l'accès à une application pour l'abonnement, utilisez :

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Affectation d'un rôle à un utilisateur pour le groupe de ressources
Pour accorder l'accès à un utilisateur pour le groupe :

    New-AzureRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Affectation d'un rôle à un groupe au niveau des ressources
Pour accorder l'accès à un groupe au niveau des ressources, utilisez :

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Suppression d'accès
Pour supprimer l'accès pour des utilisateurs, des groupes et des applications, utilisez :

    Remove-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Rubriques RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->