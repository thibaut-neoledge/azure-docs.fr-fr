<properties 
   pageTitle="Contrôle d’accès en fonction du rôle dans Azure Automation | Microsoft Azure"
   description="Le contrôle d’accès en fonction du rôle (RBAC) permet de gérer les accès des ressources Azure. Cet article décrit la configuration de RBAC dans Azure Automation."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="sngun"/>

# Contrôle d’accès en fonction du rôle dans Azure Automation

## Contrôle d’accès en fonction du rôle

Le contrôle d’accès en fonction du rôle (RBAC) permet de gérer les accès des ressources Azure. Avec [RBAC](../active-directory/role-based-access-control-configure.md), vous pouvez séparer les tâches au sein de votre équipe et accorder aux utilisateurs, groupes et applications uniquement les accès nécessaires pour accomplir leur travail. L’accès en fonction du rôle peut être accordé aux utilisateurs à l’aide du portail Azure, des outils en ligne de commande Azure ou des API de gestion Azure.

## RBAC dans les comptes Automation

Dans Azure Automation, l’accès est octroyé en attribuant le rôle RBAC approprié aux utilisateurs, groupes et applications, dans l’étendue de compte Automation. Les rôles intégrés pris en charge par un compte Automation sont présentés ci-après :

|**Rôle** | **Description** |
|:--- |:---|
| Propriétaire | Le rôle de propriétaire autorise l’accès à l’ensemble des ressources et actions d’un compte Automation, y compris l’accès aux autres utilisateurs, groupes et applications pour gérer le compte Automation. |
| Collaborateur | Le rôle de collaborateur vous permet de gérer tous les éléments excepté la modification des autorisations d’accès des autres utilisateurs à un compte Automation. |
| Lecteur | Le rôle de lecteur vous permet d’afficher toutes les ressources d’un compte Automation, mais vous ne pouvez pas y apporter de modifications. |
| Opérateur Automation | Le rôle d’opérateur Automation vous permet d’effectuer des tâches opérationnelles ; vous pouvez notamment démarrer, arrêter, suspendre, reprendre et planifier des tâches. Ce rôle est utile si vous souhaitez protéger vos ressources de compte Automation telles que les ressources d’informations d’identification et les Runbooks et empêcher leur affichage ou leur modification, tout en autorisant les membres de votre organisation à exécuter ces Runbooks. L’article sur les [actions de l’opérateur Automation](../active-directory/role-based-access-built-in-roles.md#automation-operator) répertorie les actions prises en charge par le rôle d’opérateur Automation dans le compte Automation et dans ses ressources. |
| Administrateur de l’accès utilisateur | Le rôle d’administrateur de l’accès utilisateur vous permet de gérer l’accès utilisateur aux comptes Azure Automation. |

Dans cet article, nous allons vous guider dans la configuration de RBAC dans Azure Automation.

## Configurer RBAC pour votre compte Automation à l’aide du portail Azure

1.	Connectez-vous au [portail Azure](https://portal.azure.com/) et ouvrez votre compte Automation à partir du panneau Comptes Automation.  

2.	Cliquez sur le contrôle **Accès** dans l’angle supérieur droit. Cette opération ouvre le panneau **Utilisateurs** où vous pouvez ajouter de nouveaux utilisateurs, groupes et applications pour gérer votre compte Automation et afficher les rôles existants qui peuvent être configurés pour le compte Automation.

    ![Bouton Accéder](media/automation-role-based-access-control/automation-01-access-button.png)

>[AZURE.NOTE]  Le groupe **Administrateurs des abonnements** existe déjà en tant qu’utilisateur par défaut. Le groupe Active Directory Administrateurs des abonnements inclut les administrateurs et les coadministrateurs de services fédérés pour votre abonnement Azure. L’administrateur de services fédérés est le propriétaire de votre abonnement Azure et de ses ressources, et dispose du rôle de propriétaire hérité pour les comptes Automation. En d’autres termes, l’accès est **hérité** pour les **administrateurs et coadministrateurs de services fédérés** d’un abonnement et il est **affecté** à tous les autres utilisateurs. Cliquez sur **Administrateurs des abonnements** pour afficher plus de détails sur leurs autorisations.

### Ajouter un nouvel utilisateur et affecter un rôle

1.	Dans le panneau Utilisateurs, cliquez sur **Ajouter** pour ouvrir le panneau **Ajouter un accès** où vous pouvez ajouter un utilisateur, un groupe ou une application, et lui affecter un rôle.  

    ![Ajouter un utilisateur](media/automation-role-based-access-control/automation-02-add-user.png)

2.	Dans la liste des rôles disponibles, sélectionnez un rôle. Nous allons choisir le rôle **Lecteur**, mais vous pouvez choisir l’un des rôles intégrés disponibles pris en charge par un compte Automation ou un rôle personnalisé que vous avez peut-être défini.

    ![Sélectionner un rôle](media/automation-role-based-access-control/automation-03-select-role.png)

3.	Cliquez sur **Ajouter des utilisateurs** pour ouvrir le panneau **Ajouter des utilisateurs**. Si vous avez ajouté des utilisateurs, des groupes ou des applications pour gérer votre abonnement, ces utilisateurs sont répertoriés, et vous pouvez les sélectionner pour ajouter un accès. Si aucun utilisateur n’est répertorié ou si l’utilisateur que vous souhaitez ajouter n’est pas répertorié, cliquez sur **Inviter** pour ouvrir le panneau **Convier un invité** dans lequel vous pouvez inviter un utilisateur à l’aide d’une adresse de messagerie de compte Microsoft valide comme Outlook.com, OneDrive ou des identifiants Xbox Live. Une fois que vous avez entré l’adresse de messagerie de l’utilisateur, cliquez successivement sur **Sélectionner** pour ajouter l’utilisateur et sur **OK**.

    ![Ajouter des utilisateurs](media/automation-role-based-access-control/automation-04-add-users.png)
 
À présent, vous devez voir l’utilisateur ajouté au panneau **Utilisateurs** avec le rôle **Lecteur** qui lui est affecté.

![Répertorier les utilisateurs](media/automation-role-based-access-control/automation-05-list-users.png)

Vous pouvez également affecter un rôle à l’utilisateur à partir du panneau **Rôles**. À partir du panneau Utilisateurs, cliquez sur **Rôles** pour ouvrir le panneau **Rôles**. Dans ce panneau, vous pouvez voir le nom du rôle, ainsi que le nombre d’utilisateurs et de groupes affectés à ce rôle.

![Affecter un rôle à partir du panneau Utilisateurs](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)
   
>[AZURE.NOTE] Le contrôle d’accès en fonction du rôle ne peut être défini qu’au niveau du compte Automation et pas au niveau d’une ressource située au-dessous de ce compte.

Vous pouvez affecter plusieurs rôles à un utilisateur, à un groupe ou à une application. Par exemple, si nous ajoutons les rôles **Opérateur Automation** et **Lecteur** pour l’utilisateur, il pourra alors afficher toutes les ressources Automation et exécuter des tâches de Runbook. Vous pouvez développer la liste déroulante pour afficher la liste des rôles affectés à l’utilisateur.

![Afficher plusieurs rôles](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)
 
### Supprimer un utilisateur

Vous pouvez supprimer l’autorisation d’accès d’un utilisateur qui ne gère pas le compte Automation ou qui ne travaille plus pour votre organisation. Pour supprimer un utilisateur, procédez comme suit :

1.	Dans le panneau **Utilisateurs**, sélectionnez l’affectation de rôle à supprimer.

2.	Cliquez sur le bouton **Supprimer** dans le panneau des détails de l’affectation.

3.	Cliquez sur **Oui** pour confirmer la suppression.

    ![Supprimer des utilisateurs](media/automation-role-based-access-control/automation-08-remove-users.png)

## Utilisateur affecté à un rôle

Lorsqu’un utilisateur affecté à un rôle se connecte à son compte Automation, il peut alors voir le compte du propriétaire répertorié dans la liste des **répertoires par défaut**. Pour afficher le compte Automation auquel il a été ajouté, il doit basculer le répertoire par défaut sur le répertoire par défaut de l’utilisateur.

![Annuaire par défaut](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)

### Expérience utilisateur pour le rôle d’opérateur Automation

Lorsqu’un utilisateur, qui est affecté au rôle d’opérateur Automation, consulte le compte Automation auquel il est affecté, il peut afficher uniquement la liste des Runbooks, ainsi que les tâches et les planifications de Runbook créées dans le compte Automation, mais il ne peut pas afficher leur définition. Il peut démarrer, arrêter, suspendre, reprendre ou planifier la tâche de Runbook. L’utilisateur n’a pas accès aux autres ressources Automation telles que les configurations, les groupes de Workers hybrides ou les nœuds DSC.

![Pas d’accès aux ressources](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

Si l’utilisateur clique sur le Runbook, les commandes permettant d’afficher la source ou de modifier le Runbook ne sont pas fournies, car le rôle d’opérateur Automation ne permet pas d’y accéder.

![Pas d’accès pour modifier un Runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)

L’utilisateur peut afficher et créer des planifications, mais il n’a pas accès à d’autres types de ressource.

![Aucun accès aux ressources](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)

Cet utilisateur ne peut pas non plus accéder aux Webhooks associés à un Runbook.

![Pas d’accès aux Webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)

## Configurer RBAC pour votre compte Automation à l’aide d’Azure PowerShell

L’accès en fonction du rôle peut également être configuré pour un compte Automation à l’aide des [applets de commande Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md) ci-dessous.

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) répertorie tous les rôles qui sont disponibles dans RBAC d’Azure Active Directory. Vous pouvez utiliser cette commande avec la propriété **Name** pour dresser la liste de tous les utilisateurs dotés d’un rôle spécifique. **Exemple :** ![Obtenir la définition de rôle](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) répertorie les affectations de rôle RBAC d’Azure dans l’étendue spécifiée. Sans paramètres, cette commande renvoie toutes les affectations de rôle effectuées dans l’abonnement. Utilisez le paramètre **ExpandPrincipalGroups** pour répertorier les affectations d’accès d’un utilisateur spécifique ou de groupes dont l’utilisateur est membre. **Exemple **: utilisez la commande suivante pour dresser la liste de tous les utilisateurs et de leurs rôles dans un compte Automation.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Obtenir l’affectation de rôle](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• Utilisez [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) pour octroyer un accès à des utilisateurs, des groupes et des applications avec une étendue particulière. **Exemple **: utilisez la commande suivante pour créer un rôle « Opérateur Automation » pour un utilisateur au niveau du compte Automation.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Nouvelle affectation de rôle](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Utilisez [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) pour supprimer l’accès de l’utilisateur, du groupe ou de l’application spécifié au niveau d’une étendue particulière. **Exemple **: utilisez la commande suivante pour créer un rôle « Opérateur Automation » pour un utilisateur au niveau du compte Automation.

    Remove-AzureRmRoleAssignment -SignInName "<sign-in Id of a user you wish to remove>" -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

Dans les applets de commande indiquées ci-dessus, remplacez le nom de connexion, l’ID d’abonnement, le nom du groupe de ressources et le nom du compte Automation par les détails de votre compte. À l’invite, choisissez **yes** pour poursuivre la suppression des affectations de rôle.


## Étapes suivantes
-  Pour plus d’informations sur les différentes façons de configurer RBAC pour Azure Automation, reportez-vous à l’article [Gérer RBAC à l’aide d’Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
- Pour plus d’informations sur les différentes façons de démarrer un Runbook, consultez l’article [Démarrage d’un Runbook](automation-starting-a-runbook.md).
- Pour en savoir plus sur les différents types de Runbook, consultez l’article [Types de Runbook Azure Automation](automation-runbook-types.md).

<!---HONumber=AcomDC_0218_2016-->