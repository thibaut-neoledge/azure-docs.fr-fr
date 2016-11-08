---
title: Migration d’un compte et de ressources Automation | Microsoft Docs
description: Cet article décrit comment déplacer un compte Automation dans Azure Automation et les ressources associées d’un abonnement vers un autre.
services: automation
documentationcenter: ''
author: MGoedtel
manager: jwhit
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/07/2016
ms.author: magoedte

---
# Migration d’un compte et de ressources Automation
Pour les comptes Automation et ses ressources associées (actifs, Runbooks, modules, etc.) que vous avez créés dans le portail Azure et que vous souhaitez migrer d’un groupe de ressources vers un autre ou d’un abonnement vers un autre, vous pouvez le faire facilement grâce à la fonctionnalité [Déplacer des ressources](../resource-group-move-resources.md) disponible dans le portail Azure. Toutefois, avant d’exécuter cette opération, vous devez tout d’abord examiner cette [liste de vérification avant de déplacer des ressources](../resource-group-move-resources.md#Checklist-before-moving-resources) ainsi que la liste ci-dessous, spécifique à Automation.

1. Le groupe de ressources/abonnement de destination doit figurer dans la même région que la source. Cela signifie que les comptes Automation ne peuvent pas être déplacés entre des régions.
2. Lorsque vous déplacez des ressources (Runbooks, journaux, etc.), le groupe source et le groupe cible sont verrouillés pendant la durée de l'opération. Les opérations d’écriture et de suppression sont bloquées sur les groupes tant que le déplacement n’est pas terminé.
3. La totalité des Runbooks ou variables faisant référence à une ressource à un ID d’abonnement de l’abonnement existant devra être mise à jour une fois la migration terminée.

> [!NOTE]
> Cette fonctionnalité ne prend pas en charge les ressources Automation classiques.
> 
> 

## Pour déplacer le compte Automation à l’aide du portail
1. À partir de votre compte Automation, cliquez sur **Déplacer** en haut du panneau.<br> ![option Déplacer](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. Notez que le panneau **Déplacer des ressources** présente les ressources liées à la fois à votre compte Automation et à vos groupes de ressources. Sélectionnez l**’abonnement** et le **groupe de ressources** dans les listes déroulantes, ou choisissez l’option **Créer un groupe de ressources** et entrez un nouveau nom de groupe de ressources dans le champ fourni.
3. Vérifiez les informations et cochez la case pour confirmer que vous *comprenez que les outils et scripts devront être mis à jour pour utiliser les nouveaux ID de ressource une fois les ressources déplacées* puis cliquez sur **OK**.<br> ![panneau Déplacer des ressources](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>

Cette opération peut prendre plusieurs minutes. Le panneau **Notifications** affiche l’état de chaque action effectuée : validation, migration, puis confirmation que l’opération est terminée.

## Pour déplacer le compte Automation à l’aide de PowerShell
Pour déplacer des ressources Automation existantes vers un autre groupe de ressources ou abonnement, utilisez l’applet de commande **Get-AzureRmResource** afin d’obtenir le compte Automation spécifique, puis l’applet de commande **Move-AzureRmResource** pour effectuer le déplacement.

Le premier exemple indique comment déplacer un compte Automation vers un nouveau groupe de ressources.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ``` 

Une fois que vous exécutez l’exemple de code ci-dessus, vous devrez confirmer que vous souhaitez effectuer cette action. Une fois que vous cliquez sur **Oui** et autoriser l’exécution du script, vous ne recevrez plus d’autres notifications pendant la migration.

Pour déplacer des ressources vers un nouvel abonnement, renseignez une valeur pour le paramètre *DestinationSubscriptionId*.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ``` 

Comme dans l’exemple précédent, vous devrez confirmer le déplacement.

## Étapes suivantes
* Pour plus d’informations sur le déplacement de ressources vers un nouveau groupe de ressources ou un nouvel abonnement, consultez [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../resource-group-move-resources.md)
* Pour plus d’informations sur le contrôle d’accès en fonction du rôle dans Azure Automation, voir [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).
* Pour plus d’informations sur les applets de commande PowerShell permettant de gérer votre abonnement, consultez [Utilisation d’Azure PowerShell avec Resource Manager](../powershell-azure-resource-manager.md)
* Pour plus d’informations sur les fonctionnalités du portail permettant de gérer votre abonnement, consultez [Utilisation du Portail Azure pour gérer les ressources](../azure-portal/resource-group-portal.md).

<!---HONumber=AcomDC_0713_2016-->