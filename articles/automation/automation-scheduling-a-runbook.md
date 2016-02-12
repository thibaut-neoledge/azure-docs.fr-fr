<properties 
   pageTitle="Planification d'un Runbook dans Azure Automation"
   description="Décrit comment créer une planification dans Azure Automation afin de pouvoir démarrer automatiquement un Runbook à un instant donné ou selon une planification périodique."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/03/2016"
   ms.author="bwren" />

# Planification d'un Runbook dans Azure Automation

Pour planifier le démarrage d'un Runbook dans Azure Automation à une heure spécifiée, liez-le à une ou plusieurs planifications. Une planification peut être configurée pour s'exécuter une seule fois ou de façon périodique selon un nombre spécifié d'heures ou de jours. Un Runbook peut être lié à plusieurs planifications et une planification peut avoir plusieurs Runbooks qui lui sont liés.

## Création d'une planification

Vous pouvez créer une planification avec le portail de gestion Azure ou avec Windows PowerShell. Vous avez également la possibilité de créer une planification lorsque vous liez un Runbook à une planification à l'aide du portail de gestion Azure.

### Pour créer une planification avec le portail de gestion Azure

1. Dans le portail de gestion Azure, sélectionnez Automation, puis cliquez sur le nom d'un compte Automation.
1. Sélectionnez l'onglet **Ressources**.
1. En bas de la fenêtre, cliquez sur **Ajouter un paramètre**.
1. Cliquez sur **Ajouter une planification**.
1. Entrez un **Nom** et, à titre facultatif, une **Description** pour la nouvelle planification.
1. Indiquez si la planification doit s'exécuter **Une fois**, **Toutes les heures** ou **Tous les jours**.
1. Spécifiez une **Heure de début** et d'autres options en fonction du type de planification que vous avez sélectionné.

### Pour créer une planification avec Windows PowerShell

Vous pouvez utiliser l'applet de commande [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) pour créer une planification dans Azure Automation. Vous devez spécifier l'heure de début de la planification et si elle doit s'exécuter une seule fois, toutes les heures ou tous les jours.

Les exemples de commandes suivants montrent comment créer une planification qui s'exécute chaque jour à 3h30 pm à partir du 20 janvier 2015.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime "1/20/2015 15:30:00" –DayInterval 1

## Liaison d'une planification à un Runbook

Un Runbook peut être lié à plusieurs planifications et une planification peut avoir plusieurs Runbooks qui lui sont liés. Si un Runbook possède des paramètres, vous pouvez leur fournir des valeurs. Vous devez fournir des valeurs pour tous les paramètres obligatoires et vous pouvez fournir des valeurs pour tous les paramètres facultatifs. Ces valeurs seront utilisées à chaque démarrage du Runbook par cette planification. Vous pouvez attacher le même Runbook à une autre planification et spécifier différentes valeurs de paramètre.

### Pour lier une planification à un Runbook avec le portail de gestion Azure

1. Dans le portail de gestion Azure, sélectionnez **Automation**, puis cliquez sur le nom d'un compte Automation.
1. Sélectionnez l'onglet **Runbooks**.
1. Cliquez sur le nom du Runbook à planifier.
1. Cliquez sur l'onglet **Planification**.
2. Si le Runbook n'est pas lié à une planification, vous avez la possibilité de le **Lier à une nouvelle planification** ou de le **Lier à une planification existante**. Si le Runbook n'est lié à une planification, cliquez sur **Lien** en bas de la fenêtre pour accéder à ces options.
1. Si le Runbook possède des paramètres, le système vous demande d'entrer leurs valeurs.  

### Pour lier une planification à un Runbook avec Windows PowerShell

Vous pouvez utiliser [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) pour lier une planification à un Runbook. Vous pouvez spécifier les valeurs des paramètres du Runbook avec le paramètre Parameters. Pour plus d'informations sur la spécification des valeurs des paramètres, consultez [Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md).

Les exemples de commandes suivants montrent comment lier une planification à un Runbook avec des paramètres.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Test-Runbook"
	$scheduleName = "Sample-DailySchedule"
	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## Désactivation d'une planification

Lorsque vous désactivez une planification, les Runbooks qui lui sont liés ne s'exécutent plus sur cette planification. Vous pouvez manuellement désactiver une planification ou définir un délai d'expiration pour les planifications Toutes les heures et Tous les jours lors de leur création. Lorsque le délai d'expiration est atteint, la planification est désactivée.

### Pour désactiver une planification avec le portail de gestion Azure

Vous pouvez désactiver une planification dans le portail de gestion Azure à partir de la page Détails de la planification.

1. Dans le portail de gestion Azure, sélectionnez Automation, puis cliquez sur le nom d'un compte Automation.
1. Sélectionnez l'onglet Ressources.
1. Cliquez sur le nom d'une planification pour ouvrir sa page Détails.
2. Remplacez **Activé** par **Non**.

### Pour désactiver une planification avec Windows PowerShell

Vous pouvez utiliser l'applet de commande [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) pour modifier les propriétés d'une planification existante. Pour désactiver la planification, spécifiez la valeur **false** pour le paramètre **IsEnabled**.

Les exemples de commandes suivants montrent comment désactiver une planification.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –IsEnabled $false

## Articles connexes

- [Planifier les ressources dans Azure Automation](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- [Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md) 

<!---HONumber=AcomDC_0204_2016-->