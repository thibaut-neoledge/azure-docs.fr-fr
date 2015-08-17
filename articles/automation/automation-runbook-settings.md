<properties 
   pageTitle="Paramètres du Runbook"
   description="Décrit les paramètres de configuration d'un Runbook dans Azure Automation et explique comment les modifier à l'aide du portail de gestion Azure et de Windows PowerShell."
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
   ms.date="07/22/2015"
   ms.author="bwren" />

# Paramètres du Runbook

Chaque Runbook dans Azure Automation a plusieurs paramètres qui lui permettent d'être identifié et de modifier son comportement de journalisation. Chacun de ces paramètres est décrit ci-dessous, suivi des procédures sur la façon de les modifier.

## Paramètres

### Nom et description

Vous ne pouvez pas modifier le nom d'un Runbook une fois qu'il a été créé. La description est facultative et peut comporter jusqu'à 512 caractères.

### Balises

Les balises permettent d'attribuer des mots et des expressions distincts pour permettre d'identifier un Runbook. Par exemple, lorsque vous envoyez un Runbook à la [Galerie des Runbooks](https://msdn.microsoft.com/library/dn781422.aspx), vous spécifiez certaines balises pour identifier les catégories dans lesquelles le Runbook doit être répertorié. Vous pouvez spécifier plusieurs balises pour un Runbook en les séparant par des virgules.

### Journalisation

Par défaut, les informations de commentaires et de progression ne sont pas écrites dans l'historique des tâches. Vous pouvez modifier les paramètres d'un Runbook donné pour enregistrer ces informations. Pour en savoir plus sur ces informations, consultez [Sortie et messages de runbook](https://msdn.microsoft.com/library/dn879148.aspx).

## Modification des paramètres du Runbook

### Modification des paramètres du Runbook avec le portail de gestion Azure

Vous pouvez modifier les paramètres d'un Runbook dans le portail de gestion Azure à partir de la page **Configurer** du Runbook.

1. Dans le portail de gestion Azure, sélectionnez **Automation**, puis cliquez sur le nom d'un compte Automation.
1. Sélectionnez l'onglet **Runbooks**.
1. Cliquez sur le nom d'un Runbook.
1. Sélectionnez l'onglet **Configurer**.

### Modification des paramètres du Runbook avec Windows PowerShell

Vous pouvez utiliser l'applet de commande [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) pour modifier les paramètres d'un Runbook. Si vous souhaitez spécifier plusieurs balises, vous pouvez fournir au paramètre Balises un tableau ou une chaîne unique avec les valeurs délimitées par des virgules. Vous pouvez obtenir les balises actives avec [Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx).

Les exemples de commandes suivants montrent comment définir les propriétés d'un Runbook. Cet exemple ajoute trois balises aux balises existantes et spécifie que les informations de commentaires doivent être enregistrées.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Sample-TestRunbook"
	$tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
	$tags += "Tag1,Tag2,Tag3"
	Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## Articles connexes
- [Sortie et messages de runbook](../automation-runbook-output-and-messages) 
- [Création ou importation d'un runbook](https://msdn.microsoft.com/library/dn643637.aspx) 

<!---HONumber=August15_HO6-->