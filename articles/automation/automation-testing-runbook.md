<properties 
	pageTitle="Test d’un runbook dans Azure Automation"
	description="Avant de publier un runbook dans Azure Automation, vous pouvez le tester pour vous assurer qu’il fonctionne comme prévu. Cet article décrit la procédure de test d’un runbook et de l’affichage de son résultat."
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
	ms.date="09/23/2015"
	ms.author="bwren" />

# Test d’un runbook dans Azure Automation
Lorsque vous testez un runbook, la [version Brouillon](automation-creating-importing-runbook.md#publishing-a-runbook) est exécutée et toutes les actions qu’il effectue sont finalisées. Aucun historique des tâches n’est créé, mais les flux [Résultat](automation-runbook-output-and-messages.md#output-stream) et [Avertissement et Erreur](automation-runbook-output-and-messages.md#message-streams) s’affichent dans le panneau de sortie du test. Les messages dirigés vers le [flux de messages](automation-runbook-output-and-messages.md#message-streams) s’affiche dans le panneau de résultat uniquement si la variable [$VerbosePreference variable](automation-runbook-output-and-messages.md#preference-variables) a pour valeur Continue.

Bien que la version brouillon soit exécutée, le runbook exécute toujours le flux de travail normalement et effectue des actions sur des ressources dans l’environnement. Pour cette raison, vous devez tester les runbooks uniquement sur des ressources hors production.

La procédure de chaque [type de runbook](automation-runbook-types.md) est identique et il n’y a aucune différence de test entre l’éditeur de texte et l’éditeur graphique dans le portail Azure en version préliminaire.


## Pour tester le runbook dans le portail Azure en version préliminaire

Vous pouvez utiliser n’importe quel [type de runbook](automation-runbook-types.md) dans le portail Azure en version préliminaire.

1. Ouvrez la version Brouillon du runbook dans l’[éditeur de texte](automation-editing-a-runbook#Portal) ou l’[éditeur graphique](automation-graphical-authoring-intro.md).
2. Cliquez sur le bouton **Tester** pour ouvrir le panneau de test.
3. Si le runbook possède des paramètres, ils figureront dans le volet gauche dans lequel vous pourrez fournir des valeurs à utiliser pour le test.
4. Si vous souhaitez exécuter le test sur [Runbook Worker hybride](automation-hybrid), définissez les **Paramètres d’exécution** sur **Worker hybride** et sélectionnez le nom du groupe cible. Dans le cas contraire, conservez la valeur par défaut **Azure** pour exécuter le test dans le cloud.
5. Cliquez sur le bouton **Démarrer** pour démarrer le test.
6. Si le runbook est [PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) ou [Graphique](automation-runbook-types.md#graphical-runbooks), vous pouvez l’arrêter ou le suspendre tandis qu’il est en cours de test en vous aidant des boutons situés sous le panneau de résultat. Lorsque vous interrompez le runbook, il termine l’activité en cours avant de s’interrompre. Lorsque le runbook est suspendu, vous pouvez l’arrêter ou le redémarrer.
7. Inspectez la sortie du runbook dans le panneau de résultat.



## Pour tester le runbook dans le portail Azure

Vous pouvez uniquement travailler avec des [runbooks de workflow PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) dans le portail Azure.


1. [Ouvrez la version Brouillon du runbook](automation-edit-textual-runbook.md#to-edit-a-runbook-with-the-azure-portal).
2. Cliquez sur le bouton **Tester** pour démarrer le test. Si le runbook possède des paramètres, une boîte de dialogue vous invitera à fournir des valeurs à utiliser pour le test.
6. Vous pouvez arrêter ou suspendre le runbook qui est en cours de test en vous aidant des boutons situés sous le panneau de résultat. Lorsque vous interrompez le runbook, il termine l’activité en cours avant de s’interrompre. Lorsque le runbook est suspendu, vous pouvez l’arrêter ou le redémarrer.
7. Inspectez la sortie du runbook dans le panneau de résultat.


## Articles connexes

- [Création ou importation d’un runbook dans Azure Automation](automation-creating-importing-runbook.md)
- [Runbooks graphiques dans Azure Automation](automation-graphical-authoring-intro.md)
- [Modification des runbooks textuels dans Azure Automation](automation-edit-textual-runbook.md)
- [Sortie et messages de Runbook dans Azure Automation](automation-runbook-output-and-messages.md)

<!---HONumber=Oct15_HO1-->