<properties 
   pageTitle="Affichage de l'état d'une tâche du Runbook dans Azure Automation"
   description="Lorsque vous démarrez un Runbook dans Azure Automation, une tâche est créée. Cet article fournit des informations sur la façon d'effectuer le suivi de chaque tâche et d'afficher son état actuel."
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
   ms.date="03/30/2015"
   ms.author="bwren" />

# Affichage de l'état d'une tâche du Runbook dans Azure Automation


Lorsque vous démarrez un Runbook dans Azure Automation, une tâche est créée. Une tâche est une instance d'exécution unique d'un Runbook. Un Runbook unique peut avoir plusieurs tâches, chacune avec son propre jeu de valeurs pour les paramètres du Runbook. Il existe plusieurs façons de vérifier l'état d'une tâche particulière et toutes les tâches d'un ou de plusieurs Runbooks.

## États des tâches

Le tableau suivant décrit les différents statuts possibles pour une tâche.

| Statut| Description|
|:---|:---|
|Completed|La tâche s'est terminée avec succès.|
|Failed|La tâche s'est terminée avec une erreur.|
|Échec, en attente de ressources|La tâche a échoué, car elle a atteint la limite de [répartition de charge équilibrée](http://msdn.microsoft.com/library/azure/3179b655-ab39-407a-9169-33571f958325#fairshare) trois fois et a démarré à partir du même point de contrôle ou à partir du début du Runbook à chaque fois.|
|Mis en file d'attente.|La tâche attend que les ressources d'un travail Automation deviennent disponibles afin de pouvoir être démarrée.|
|Starting|La tâche a été attribuée à un travail et le système est en train de le démarrer.|
|Reprise|Le système est en cours de reprise de la tâche après qu'elle a été suspendue.|
|Exécution|La tâche est en cours d'exécution.|
|En cours d'exécution, en attente de ressources|La tâche a été déchargée, car elle a atteint la limite de [répartition de charge équilibrée](http://msdn.microsoft.com/library/azure/3179b655-ab39-407a-9169-33571f958325#fairshare). Elle va bientôt reprendre depuis son dernier point de contrôle.|
|Arrêté|La tâche a été arrêtée par l'utilisateur avant qu'elle n'ait été terminée.|
|En cours d’arrêt|Le système est en cours d'arrêt de la tâche.|
|Interrompu|La tâche a été suspendue par l'utilisateur, le système ou une commande du Runbook. Une tâche qui est interrompue peut être démarrée à nouveau et reprend à partir de son dernier point de contrôle ou à partir du début du Runbook s'il n'a aucun point de contrôle. Le Runbook est uniquement interrompu par le système en cas d'exception. Par défaut, ErrorActionPreference est définie sur **Continuer**, ce qui signifie que la tâche se poursuivra en cas d'erreur. Si cette préférence est définie sur **Arrêter**,la tâche s'interrompt en cas d'erreur.|
|Suspension|Le système tente de suspendre la tâche à la demande de l'utilisateur. Le Runbook doit atteindre son prochain point de contrôle avant de pouvoir être suspendu. S'il a déjà passé le dernier point de contrôle, il se termine avant d'être suspendu.|

## Affichage de l'état des tâches à l'aide du portail de gestion Azure

### Tableau de bord Automation

Le tableau de bord Automation affiche un résumé de tous les Runbooks d'un compte Automation particulier. Il inclut également une présentation de l'utilisation du compte. Le graphique récapitulatif affiche le nombre total de tâches de tous les Runbooks qui sont passées par chaque état sur un nombre donné de jours ou d'heures. Vous pouvez sélectionner la période dans le coin supérieur droit du graphique. L'axe temporel du graphique change en fonction du type de période que vous sélectionnez. Vous pouvez choisir d'afficher la ligne d'un état particulier en cliquant dessus, en haut de l'écran.

Vous pouvez utiliser les étapes suivantes pour afficher le tableau de bord Automation.

1. Dans le portail de gestion Azure, sélectionnez **Automation**, puis cliquez sur le nom d'un compte Automation.
1. Sélectionnez l'onglet **Tableau de bord**.

### Tableau de bord du Runbook

Le tableau de bord du Runbook affiche le résumé d'un seul Runbook. Le graphique récapitulatif affiche le nombre total de tâches du Runbook qui sont passées par chaque état sur un nombre donné de jours ou d'heures. Vous pouvez sélectionner la période dans le coin supérieur droit du graphique. L'axe temporel du graphique change en fonction du type de période que vous sélectionnez. Vous pouvez choisir d'afficher la ligne d'un état particulier en cliquant dessus, en haut de l'écran.

Vous pouvez utiliser les étapes suivantes pour afficher le tableau de bord du Runbook.

1. Dans le portail de gestion Azure, sélectionnez **Automation**, puis cliquez sur le nom d'un compte Automation.
1. Cliquez sur le nom d'un Runbook.
1. Sélectionnez l'onglet **Tableau de bord**.

### Résumé des tâches

Vous pouvez afficher la liste de toutes les tâches qui ont été créées pour un Runbook donné et leur état le plus récent. Vous pouvez filtrer cette liste par état de la tâche et par plage de dates de la dernière modification de la tâche. Cliquez sur le nom d'une tâche pour afficher des informations détaillées et sa sortie. La vue détaillée de la tâche inclut les valeurs des paramètres du Runbook qui ont été fournies à cette tâche.

Vous pouvez utiliser les étapes suivantes pour afficher les tâches d'un Runbook.

1. Dans le portail de gestion Azure, sélectionnez **Automation**, puis cliquez sur le nom d'un compte Automation.
1. Cliquez sur le nom d'un Runbook.
1. Sélectionnez l'onglet **Tâches**.
1. Cliquez sur la colonne **Tâche créée** d'une tâche pour afficher ses détails et sa sortie.

## Récupération de l'état d'une tâche à l'aide de Windows PowerShell

Vous pouvez utiliser [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) pour récupérer les tâches créées pour un Runbook et les détails d'une tâche particulière. Si vous démarrez un Runbook avec Windows PowerShell à l'aide de [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx), il retourne la tâche résultante. Utilisez [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) pour obtenir la sortie d'une tâche.

Les exemples de commandes suivants récupèrent la dernière tâche d'un exemple de Runbook et affichent son état, les valeurs fournies aux paramètres du Runbook et la sortie de la tâche.

	$job = (Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" | sort LastModifiedDate –desc)[0]
	$job.Status
	$job.JobParameters
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## Articles connexes

- [Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md) 

<!---HONumber=July15_HO3-->