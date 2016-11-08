---
title: Accorder des autorisations à des utilisateurs sur des stratégies de laboratoire spécifiques | Microsoft Docs
description: Découvrez comment accorder des autorisations aux utilisateurs sur des stratégies de laboratoire spécifique dans DevTest Labs selon les besoins de chaque utilisateur
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: ''

ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: tarcher

---
# Accorder des autorisations à des utilisateurs sur des stratégies de laboratoire spécifiques
## Vue d'ensemble
Cet article explique comment utiliser PowerShell pour accorder à des utilisateurs des autorisations sur une stratégie de laboratoire particulière. De cette façon, les autorisations peuvent être appliquées selon les besoins de chaque utilisateur. Par exemple, vous pouvez accorder à un utilisateur la possibilité de modifier les paramètres de stratégie d’une machine virtuelle, mais pas les stratégies de coût.

## Stratégies en tant que ressources
Comme expliqué dans l’article [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md), RBAC permet une gestion précise de l’accès aux ressources pour Azure. Avec le contrôle d’accès en fonction du rôle, vous pouvez séparer les tâches au sein de votre équipe chargée des opérations de développement et accorder aux utilisateurs uniquement les accès nécessaires pour accomplir leur travail.

Dans DevTest Labs, une stratégie est un type de ressource qui active l’action RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Chaque stratégie de laboratoire est une ressource de type stratégie et peut être affectée comme étendue à un rôle RBAC.

Par exemple, pour accorder à des utilisateurs une autorisation de lecture/écriture sur une stratégie **Tailles de machine virtuelle autorisées**, vous créez un rôle personnalisé qui fonctionne avec l’action **Microsoft.DevTestLab/labs/policySets/policies/*** puis vous affectez les utilisateurs appropriés à ce rôle personnalisé dans l’étendue de **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Pour en savoir plus sur les rôles personnalisés dans RBAC, consultez la section [Rôles personnalisés dans le contrôle d’accès en fonction du rôle (RBAC) Azure](../active-directory/role-based-access-control-configure.md#custom-roles-in-azure-rbac) de l’article [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).

## Création d’un rôle personnalisé de laboratoire en utilisant PowerShell
Pour commencer, vous devez lire l’article suivant, qui explique comment installer et configurer les applets de commande Azure PowerShell : [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Une fois que vous avez configuré les applets de commande Azure PowerShell, vous pouvez effectuer les tâches suivantes :

* Répertorier toutes les opérations/actions d’un fournisseur de ressources
* Répertorier les actions d’un rôle particulier :
* Créer un rôle personnalisé

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

## Attribution d'autorisations à un utilisateur pour une stratégie spécifique à l'aide de rôles personnalisés
Une fois que vous avez défini vos rôles personnalisés, vous pouvez les attribuer aux utilisateurs. Pour affecter un rôle personnalisé à un utilisateur, vous devez d’abord obtenir **l’ObjectId** représentant cet utilisateur. Pour cela, utilisez l’applet de commande **Get-AzureRmADUser**.

Dans l’exemple suivant, **l’ObjectId** de l’utilisateur *SomeUser* est 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Une fois que vous disposez de **l’ObjectId** de l’utilisateur et d’un nom de rôle personnalisé, vous pouvez affecter ce rôle à l’utilisateur avec l’applet de commande **New-AzureRmRoleAssignment** :

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

Dans l’exemple précédent, la stratégie **AllowedVmSizesInLab** est utilisée. Vous pouvez utiliser une des stratégies suivantes :

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Étapes suivantes
Après avoir accordé aux utilisateurs des autorisations sur des stratégies de laboratoire spécifiques, voici des étapes à prendre en compte :

* [Sécuriser l’accès à un laboratoire](devtest-lab-add-devtest-user.md).
* [Définir des stratégies de laboratoire](devtest-lab-set-lab-policy.md).
* [Créer un modèle de laboratoire](devtest-lab-create-template.md).
* [Créer des artefacts personnalisés pour vos machines virtuelles](devtest-lab-artifact-author.md).
* [Ajouter une machine virtuelle avec des artefacts à un laboratoire](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0831_2016-->