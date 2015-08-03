<properties 
   pageTitle="Planifications dans Azure Automation"
   description="Les planifications Automation permettent de planifier les Runbooks dans Azure pour qu'ils démarrent automatiquement. Cet article décrit comment créer les planifications."
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
   ms.date="05/21/2015"
   ms.author="bwren" />

# Planifications dans Azure Automation

Les planifications Automation permettent de planifier les Runbooks pour qu'ils s'exécutent automatiquement. Il peut s'agir d'une seule date et heure pour que le Runbook s'exécute une seule fois. Ou il peut s'agir d'une planification récurrente pour démarrer le Runbook plusieurs fois. Les planifications ne sont généralement pas accessibles à partir des Runbooks.

## Applets de commande Windows PowerShell

Les applets de commande du tableau suivant permettent de créer et de gérer les variables avec Windows PowerShell dans Azure Automation. Elles sont fournies dans le cadre du [module Azure PowerShell](../powershell-install-configure.md).

|Applets de commande|Description|
|:---|:---|
|[Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx)|Récupère une planification.|
|[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx)|Crée une planification.|
|[Remove-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx)|Supprime une planification.|
|[Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx)|Définit les propriétés d'une planification existante.|
|[Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx)|Récupère les Runbooks planifiés.|
|[Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx)|Associe un Runbook à une planification.|
|[Unregister-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690273.aspx)|Dissocie un Runbook d'une planification.|

## Création d'une planification

### Pour créer une planification avec le portail Azure


1. À partir de votre compte Automation, cliquez sur **Ressources** en haut de la fenêtre.
1. En bas de la fenêtre, cliquez sur **Ajouter un paramètre**.
1. Cliquez sur **Ajouter une planification**.
1. Terminez l'Assistant et cochez la case pour enregistrer la nouvelle variable.

### Pour créer une planification avec le portail Azure en version préliminaire

1. À partir de votre compte Automation, cliquez sur la partie **Ressources** afin d'ouvrir le panneau **Ressources**.
1. Cliquez sur la partie **Planifications** afin d'ouvrir le panneau **Planifications**.
1. Cliquez sur **Ajouter une planification** en haut du panneau.
1. Remplissez le formulaire, puis cliquez sur **Créer** pour enregistrer la nouvelle planification.

### Pour créer une planification avec Windows PowerShell

L'applet de commande [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx) crée une planification et définit la valeur d'une planification existante. Les exemples de commandes Windows PowerShell suivants créent une planification nommée My Daily Schedule, qui débute le lendemain à midi et se déclenche chaque jour de l'année :

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "My Daily Schedule"
	$startTime = (Get-Date).Date.AddDays(1).AddHours(12)
	$expiryTime = $startTime.AddYears(1)
	
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime $startTime –ExpiryTime $expiryTime –DayInterval 1


## Voir aussi
- [Planification d'un Runbook dans Azure Automation](automation-scheduling-a-runbook.md)
 

<!---HONumber=July15_HO4-->