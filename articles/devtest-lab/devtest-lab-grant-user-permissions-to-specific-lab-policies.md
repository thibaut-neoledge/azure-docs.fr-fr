    <properties
	pageTitle="Grant user permissions to specific DevTest Lab policies | Microsoft Azure"
	description="Learn how to grant user permissions to specific DevTest Lab policies based on each user's needs"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="tarcher"/>

# Accorder des autorisations utilisateur à des stratégies DevTest Lab spécifiques

## Vue d’ensemble

Cet article explique comment utiliser PowerShell pour accorder des autorisations utilisateur à une stratégie Azure DevTest Lab particulière. De cette façon, les autorisations peuvent être appliquées selon les besoins de chaque utilisateur. Par exemple, vous pouvez accorder à un utilisateur la possibilité de modifier les paramètres de stratégie d’une machine virtuelle, mais pas les stratégies de coût.

## Stratégies en tant que ressources

Comme expliqué dans l’article [Contrôle d’accès en fonction du rôle](/role-based-access-control-configure.md) RBAC permet une gestion précise de l’accès aux ressources pour Azure. Avec le contrôle d’accès en fonction du rôle, vous pouvez séparer les tâches au sein de votre équipe chargée des opérations de développement et accorder aux utilisateurs uniquement les accès nécessaires pour accomplir leur travail.

Dans DevTest Lab, une stratégie est un type de ressource qui permet l'action RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Chaque stratégie DevTest Lab est une ressource de type stratégie et peut être affectée comme une étendue à un rôle RBAC.

Par exemple, pour accorder à des utilisateurs une autorisation de lecture/écriture à une stratégie **Tailles de machine virtuelle autorisées**, vous devez créer un rôle personnalisé qui fonctionne avec l’action **Microsoft.DevTestLab/labs/policySets/policies/*** puis affectez les utilisateurs appropriés à ce rôle personnalisé dans la portée de **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Pour en savoir plus sur les rôles personnalisés dans RBAC, consultez la section [Rôles personnalisés dans Azure RBAC](/role-based-access-control-configure.md#custom-roles-in-azure-rbac) de l’article [Contrôle d’accès en fonction du rôle Azure](/role-based-access-control-configure.md).

##Création d'un rôle DevTest Lab personnalisé à l'aide de PowerShell
Pour commencer, vous devez lire l'article suivant, qui explique comment installer et configurer les cmdlets PowerShell Azure : [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Une fois que vous avez configuré les cmdlets PowerShell Azure, vous pouvez effectuer les tâches suivantes :

- Répertorier toutes les opérations/actions d’un fournisseur de ressources
- Répertorier les actions d’un rôle particulier :
- Créer un rôle personnalisé

Le script PowerShell suivant montre des exemples permettant d’effectuer ces tâches :

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

##Attribution d'autorisations à un utilisateur pour une stratégie spécifique à l'aide de rôles personnalisés
Une fois que vous avez défini vos rôles personnalisés, vous pouvez les attribuer aux utilisateurs. Pour attribuer un rôle personnalisé à un utilisateur, vous devez d'abord obtenir l’**ObjectId** représentant cet utilisateur. Pour cela, utilisez l’applet de commande **Get-AzureRmADUser**.

Dans l'exemple suivant, l’**ObjectId** de l’utilisateur *SomeUser* est 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Une fois que vous disposez de l’**ObjectId** de l'utilisateur et d’un nom de rôle personnalisé, vous pouvez attribuer ce rôle à l'utilisateur à l’aide de l’applet de commande **New-AzureRmRoleAssignment** :

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

Dans l'exemple précédent, la stratégie **AllowedVmSizesInLab** est utilisée. Vous pouvez utiliser une des stratégies suivantes :

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

## Étapes suivantes

Après avoir accordé aux utilisateurs les autorisations à des stratégies DevTest Lab spécifiques, voici les étapes à suivre :

- [Sécuriser l’accès à un laboratoire de test et développement](devtest-lab-add-devtest-user.md).

- [Définir des stratégies de laboratoire](devtest-lab-set-lab-policy.md).

- [Créer un modèle de laboratoire](devtest-lab-create-template.md).

- [Créer des artefacts personnalisés pour vos machines virtuelles](devtest-lab-artifact-author.md).

- [Ajouter une machine virtuelle avec des artefacts à un laboratoire de test et développement Azure](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0211_2016-->