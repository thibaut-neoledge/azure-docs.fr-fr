<properties
	pageTitle="Gestion du Contrôle d'accès en fonction du rôle avec Azure PowerShell | Microsoft Azure"
	description="Comment gérer le contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell, notamment le référencement des rôles, l’attribution de rôles et la suppression d’attributions de rôle."
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
	ms.date="04/12/2016"
	ms.author="kgremban"/>

# Gestion du Contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)


Le contrôle d'accès en fonction du rôle (RBAC) dans l'API du portail Azure et l'API de gestion de ressources Azure permet une gestion très fine de l'accès à votre abonnement. Cette fonctionnalité vous permet d’accorder l’accès aux utilisateurs, groupes et principaux du service Active Directory en leur affectant certains rôles avec une étendue spécifique.

Avant d’utiliser PowerShell pour gérer le contrôle d’accès en fonction du rôle (RBAC), vous devez disposer des composants suivants :

- Azure PowerShell, version 0.8.8 ou ultérieure. Pour installer la dernière version et l’associer à votre abonnement Azure, consultez la page [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

- Applets de commande Azure Resource Manager. Installez les [applets de commande Azure Resource Manager](https://msdn.microsoft.com/library/mt125356.aspx) dans PowerShell.

## Répertorier les rôles

### Répertorier tous les rôles disponibles
Pour répertorier les rôles RBAC pouvant être affectés et inspecter les opérations auxquelles ils accordent l'accès, utilisez :

		Get-AzureRmRoleDefinition

![RBAC PowerShell - Get-AzureRmRoleDefinition - capture d’écran](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Répertorier les actions d'un rôle
Pour répertorier les actions pour un rôle spécifique, utilisez :

    Get-AzureRmRoleDefinition <role name>

![RBAC PowerShell - Get-AzureRmRoleDefinition pour un rôle spécifique - capture d’écran](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Voir quel utilisateur a des autorisations d’accès
Pour répertorier les attributions d’accès RBAC, utilisez :

    Get-AzureRmRoleAssignment

###	Répertorier les attributions de rôle dans une étendue spécifique
Vous pouvez voir toutes les attributions d’accès pour un abonnement, un groupe de ressources ou une ressource. Par exemple, pour voir toutes les attributions actives pour un groupe de ressources, utilisez :

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![RBAC PowerShell - Get-AzureRmRoleAssignment pour un groupe de ressource - capture d’écran](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Répertorier les rôles attribués à un utilisateur
Pour répertorier tous les rôles attribués à un utilisateur spécifique, y compris les rôles attribués aux groupes auquel il appartient, utilisez :

    Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups

![RBAC PowerShell - Get-AzureRmRoleAssignment pour un utilisateur - capture d’écran](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Répertorier les affectations classiques des rôles Administrateur de service et Coadministrateur
Pour répertorier les affectations d'accès de liste pour les administrateurs d'abonnement et les coadministrateurs classiques, utilisez :

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## Accorder l'accès
### Rechercher des ID d’objet
Pour attribuer un rôle, vous devez identifier l’objet (utilisateur, groupe ou application) et l’étendue.

Si vous ne connaissez pas l’ID d’abonnement, vous pouvez le trouver dans le panneau **Abonnements** dans le portail Azure. Découvrez également comment le rechercher avec [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) sur MSDN.

Pour obtenir l’ID d’objet pour un groupe Azure AD, utilisez :

    Get-AzureRmADGroup -SearchString <group name in quotes>

Pour obtenir l’ID d’objet pour un principal du service Azure AD ou une application, utilisez :

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### Affectation d'un rôle à une application pour l'abonnement
Pour accorder l'accès à une application pour l'abonnement, utilisez :

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name in quotes> -Scope <subscription id>

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

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

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

## Voir aussi
- [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md)[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0413_2016-->