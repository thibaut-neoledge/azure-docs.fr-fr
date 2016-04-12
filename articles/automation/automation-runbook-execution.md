<properties
   pageTitle="Exécution d'un Runbook dans Azure Automation"
   description="Décrit les détails du traitement d'un Runbook dans Azure Automation."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/21/2016"
   ms.author="bwren" />

# Exécution d'un Runbook dans Azure Automation


Lorsque vous démarrez un Runbook dans Azure Automation, une tâche est créée. Une tâche est une instance d'exécution unique d'un Runbook. Un travail Azure Automation est assigné pour exécuter chaque tâche. Même si les travaux sont partagés par plusieurs comptes Azure, les tâches des différents comptes Automation sont isolées les unes des autres. Vous n'avez pas le contrôle du travail qui traitera la demande de votre tâche. Un même Runbook peut avoir plusieurs tâches s'exécutant à la fois. Lorsque vous affichez la liste des Runbooks du portail Azure, vous voyez l'état de la dernière tâche démarrée pour chaque Runbook. Vous pouvez afficher la liste des tâches de chaque Runbook pour en assurer le suivi de l'état. Pour obtenir une description des différents états des tâches, consultez [États des tâches](#job-statuses).

Le diagramme suivant illustre le cycle de vie d'une tâche de Runbook pour des [Runbooks graphiques](automation-runbook-types.md#graphical-runbooks) et des [Runbooks de workflow PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![États des tâches - Workflow PowerShell](./media/automation-runbook-execution/job-statuses.png)

Le diagramme suivant illustre le cycle de vie d'une tâche de Runbook pour des [Runbooks PowerShell](automation-runbook-types.md#powershell-runbooks).

![États des tâches - Script PowerShell](./media/automation-runbook-execution/job-statuses-script.png)


Vos tâches auront accès à vos ressources Azure en créant une connexion à votre abonnement Azure. Ils auront uniquement accès aux ressources de votre centre de données si ces ressources sont accessibles depuis le cloud public.

## États des tâches

Le tableau suivant décrit les différents statuts possibles pour une tâche.

| Statut| Description|
|:---|:---|
|Completed|La tâche s'est terminée avec succès.|
|Échec| Pour des [Runbooks graphiques et de workflow PowerShell](automation-runbook-types.md), le Runbook n’a pas pu être compilé. Pour des [Runbooks de script PowerShell](automation-runbook-types.md), le Runbook n'a pas pu démarrer ou la tâche a rencontré une exception. |
|Échec, en attente de ressources|La tâche a échoué, car elle a atteint la limite de [répartition de charge équilibrée](#fairshare) trois fois et a démarré à partir du même point de contrôle ou à partir du début du Runbook à chaque fois.|
|Mis en file d'attente.|La tâche attend que les ressources d'un travail Automation deviennent disponibles afin de pouvoir être démarrée.|
|Starting|La tâche a été attribuée à un travail et le système est en train de le démarrer.|
|Reprise|Le système est en cours de reprise de la tâche après qu'elle a été suspendue.|
|Exécution|La tâche est en cours d'exécution.|
|En cours d'exécution, en attente de ressources|La tâche a été déchargée, car elle a atteint la limite de [répartition de charge équilibrée](#fairshare). Elle va bientôt reprendre depuis son dernier point de contrôle.|
|Arrêté|La tâche a été arrêtée par l'utilisateur avant qu'elle n'ait été terminée.|
|En cours d’arrêt|Le système est en cours d'arrêt de la tâche.|
|Interrompu|La tâche a été suspendue par l'utilisateur, le système ou une commande du Runbook. Une tâche qui est interrompue peut être démarrée à nouveau et reprend à partir de son dernier point de contrôle ou à partir du début du Runbook s'il n'a aucun point de contrôle. Le Runbook est uniquement interrompu par le système en cas d'exception. Par défaut, ErrorActionPreference est définie sur **Continuer**, ce qui signifie que la tâche se poursuivra en cas d'erreur. Si cette préférence est définie sur **Arrêter**,la tâche s'interrompt en cas d'erreur. S'applique aux [Runbooks graphiques et de workflow PowerShell](automation-runbook-types.md) uniquement.|
|Suspension|Le système tente de suspendre la tâche à la demande de l'utilisateur. Le Runbook doit atteindre son prochain point de contrôle avant de pouvoir être suspendu. S'il a déjà passé le dernier point de contrôle, il se termine avant d'être suspendu. S'applique aux [Runbooks graphiques et de workflow PowerShell](automation-runbook-types.md) uniquement.|

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

## Répartition de charge équilibrée

Afin de partager les ressources entre tous les Runbooks du cloud, Azure Automation décharge temporairement toute tâche après qu'elle a été exécutée pendant 3 heures. Les Runbooks [graphiques](automation-runbook-types.md#graphical-runbooks) et de [workflow PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) seront repris à partir de leur dernier [point de contrôle](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints). Pendant ce temps, la tâche affiche l'état En cours d'exécution, en attente de ressources. Si le Runbook n'a aucun point de contrôle ou que la tâche n'a pas atteint le premier point de contrôle avant d'être déchargée, il redémarre à partir du début. Les Runbooks [PowerShell](automation-runbook-types.md#powershell-runbooks) sont toujours redémarrés depuis le début puisqu’ils ne prennent pas en charge les points de contrôle.

>[AZURE.NOTE] La limite de répartition de charge n’est pas applicable à l’exécution de Runbook Workers hybrides.

Si le Runbook redémarre à partir du même point de contrôle ou du début du Runbook trois fois de suite, il se termine avec l'état Échec, en attente de ressources. L'objectif est d'empêcher que les Runbooks ne s'exécutent indéfiniment sans s'achever, car ils ne sont pas en mesure d'accéder au point de contrôle suivant sans être à nouveau déchargés. Dans ce cas, vous recevez l'exception suivante avec l'échec.

*La tâche ne peut pas continuer, car elle a été exclue à plusieurs reprises du même point de contrôle. Assurez-vous que votre Runbook n'effectue pas des opérations de longue durée sans conserver son état.*

Lorsque vous créez un Runbook, vous devez vous assurer que la durée d'exécution de toute activité entre deux points de contrôle ne dépasse pas 3 heures. Vous devrez peut-être ajouter des points de contrôle à votre Runbook pour vous assurer qu'il n'excède pas cette limite de 3 heures ou n’interrompt pas les opérations de longue durée. Par exemple, votre Runbook peut effectuer une réindexation sur une base de données SQL volumineuse. Si cette opération unique ne se termine pas dans la limite de la répartition de charge équilibrée, la tâche est déchargée et redémarrée depuis le début. Dans ce cas, vous devez décomposer l'opération de réindexation en plusieurs étapes, comme la réindexation d'une table à la fois, puis insérer un point de contrôle après chaque opération, afin que la tâche puisse reprendre après que la dernière opération s'est terminée.



## Étapes suivantes

- [Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md)

<!---HONumber=AcomDC_0323_2016-->