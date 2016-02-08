<properties 
	pageTitle="Modifier des runbooks textuels dans Azure Automation"
	description="Cet article fournit différentes procédures pour travailler avec des runbooks PowerShell et de workflow PowerShell dans Azure Automation à l’aide de l’éditeur de texte."
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
	ms.date="01/19/2016"
	ms.author="bwren" />

# Modifier des runbooks textuels dans Azure Automation

L’éditeur de texte dans Azure Automation peut être utilisé pour modifier des [runbooks PowerShell](automation-runbook-types.md#powershell-runbooks) et des [runbooks de workflow PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Il intègre les fonctionnalités standards d’autres éditeurs de code, par exemple IntelliSense et le codage en couleurs, avec des fonctionnalités spéciales supplémentaires pour vous aider à accéder aux ressources communes aux runbooks. Cet article fournit des instructions détaillées pour effectuer différentes fonctions avec cet éditeur.

L’éditeur de texte inclut une fonctionnalité permettant d’insérer du code pour les activités, les ressources et les runbooks enfants dans un runbook. Plutôt que de taper le code vous-même, vous pouvez sélectionner le code approprié dans la liste des ressources disponibles et l’insérer dans le runbook.

Dans Azure Automation, chaque runbook existe en deux versions : un brouillon et une version publiée. Vous devez modifier le brouillon du runbook, puis le publier afin qu’il puisse être exécuté. La version publiée ne peut pas être modifiée. Consultez [Publication d’un runbook](automation-creating-importing-runbook.md#publishing-a-runbook) pour plus d’informations.

Pour travailler avec des [runbooks graphiques](automation-runbook-types.md#graphical-runbooks), consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).

## Pour modifier un runbook avec le portail Azure en version préliminaire

Utilisez la procédure suivante pour ouvrir un runbook afin de le modifier dans l’éditeur de texte.

1. Dans le portail Azure, sélectionnez votre compte Automation.
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Cliquez sur le nom du runbook que vous souhaitez modifier, puis cliquez sur le bouton **Modifier**.
6. Effectuez la modification requise.
7. Cliquez sur **Enregistrer** lorsque vos modifications sont terminées.
8. Cliquez sur **Publier** si vous souhaitez que le dernier brouillon du runbook soit publié.

### Pour insérer une applet de commande dans un runbook

2. Dans le canevas de l’éditeur de texte, placez le curseur à l’emplacement où vous souhaitez placer l’applet de commande.
3. Développez le nœud **Applets de commande** dans le contrôle Bibliothèque. 
3. Développez le module contenant l’applet de commande que vous souhaitez utiliser.
4. Cliquez avec le bouton droit sur l’applet de commande à insérer, puis sélectionnez **Ajouter au canevas**. Si plusieurs jeux de paramètres sont définis pour l’applet de commande, le jeu par défaut est ajouté. Vous pouvez également développer l’applet de commande pour sélectionner un jeu de paramètres différent.
4. Le code de l’applet de commande est inséré avec sa liste entière de paramètres.
5. Indiquez une valeur appropriée à la place du type de données entouré par des accolades <> pour tous les paramètres requis. Supprimez les paramètres dont vous n’avez pas besoin.

### Pour insérer du code pour un runbook enfant dans un runbook

2. Dans le canevas de l’éditeur de texte, placez le curseur à l’emplacement où vous souhaitez placer le code pour le [runbook enfant](automation-child-runbooks.md).
3. Développez le nœud **Runbooks** dans le contrôle Bibliothèque. 
3. Cliquez avec le bouton droit sur le runbook à insérer, puis sélectionnez **Ajouter au canevas**.
4. Le code du runbook enfant est inséré avec des espaces réservés pour les paramètres des runbooks.
5. Remplacez les espaces réservés par des valeurs appropriées pour chaque paramètre.

### Pour insérer une ressource dans un runbook

2. Dans le canevas de l’éditeur de texte, placez le curseur à l’emplacement où vous souhaitez placer le code pour le runbook enfant.
3. Développez le nœud **Ressources** dans le contrôle Bibliothèque. 
4. Développez le nœud pour le type de ressources souhaité.
3. Cliquez avec le bouton droit sur la ressource à insérer, puis sélectionnez **Ajouter au canevas**. Pour les [ressources variables](automation-variables.md), sélectionnez **Ajouter « Obtenir la variable » au canevas** ou **Ajouter « Définir la variable » au canevas** selon que vous souhaitez obtenir ou définir la variable.
4. Le code de la ressource est inséré dans le runbook.



## Pour modifier un runbook avec le portail Azure

Utilisez la procédure suivante pour ouvrir un runbook afin de le modifier dans l’éditeur de texte.

1. Dans le portail Azure, sélectionnez **Automation**, puis cliquez sur le nom d'un compte Automation.
2. Sélectionnez l'onglet **Runbooks**.
3. Cliquez sur le nom du runbook que vous souhaitez modifier, puis sélectionnez l’onglet **Auteur**.
5. Cliquez sur le bouton **Modifier** au bas de l’écran.
6. Effectuez la modification requise.
7. Cliquez sur **Enregistrer** lorsque vos modifications sont terminées.
8. Cliquez sur **Publier** si vous souhaitez que le dernier brouillon du runbook soit publié.

### Pour insérer une activité dans un runbook

1. Dans le canevas de l’éditeur de texte, placez le curseur à l’emplacement où vous souhaitez placer l’activité.
1. En bas de l’écran, cliquez sur **Insérer**, puis sur **Activité**.
1. Dans la colonne **Module d’intégration**, sélectionnez le module qui contient l’activité.
1. Dans le volet **Activité**, sélectionnez une activité.
1. Dans la colonne **Description**, notez la description de l’activité. Si vous le souhaitez, vous pouvez cliquer sur Voir l’aide détaillée afin de lancer l’aide pour l’activité dans le navigateur.
1. Cliquez sur la flèche droite. Si l’activité possède des paramètres, ils sont répertoriés à titre d’information.
1. Cliquez sur le bouton représentant une coche. Le code permettant d’exécuter l’activité est inséré dans le runbook.
1. Si l’activité requiert des paramètres, indiquez une valeur appropriée à la place du type de données entouré par des accolades <>.

### Pour insérer du code pour un runbook enfant dans un runbook

1. Dans le canevas de l’éditeur de texte, placez le curseur à l’emplacement où vous souhaitez placer le [runbook enfant](automation-child-runbooks.md).
2. En bas de l’écran, cliquez sur **Insérer**, puis sur **Runbook**.
3. Sélectionnez le runbook à insérer à partir de la colonne centrale, puis cliquez sur la flèche droite.
4. Si le runbook possède des paramètres, ils sont répertoriés à titre d’information.
5. Cliquez sur le bouton représentant une coche. Le code permettant d’exécuter le runbook sélectionné est inséré dans le runbook en cours.
7. Si le runbook requiert des paramètres, indiquez une valeur appropriée à la place du type de données entouré par des accolades <>.

### Pour insérer une ressource dans un runbook

1. Dans le canevas de l’éditeur de texte, placez le curseur à l’emplacement où vous souhaitez placer l’activité pour récupérer la ressource.
1. En bas de l’écran, cliquez sur **Insérer**, puis sur **Paramètre**.
1. Dans la colonne **Action du paramètre**, sélectionnez l’action que vous souhaitez.
1. Faites votre choix parmi les ressources disponibles dans la colonne centrale.
1. Cliquez sur le bouton représentant une coche. Le code d’obtention ou de définition de la ressource est inséré dans le runbook.



## Pour modifier un runbook Azure Automation à l’aide de Windows PowerShell

Pour modifier un runbook avec Windows PowerShell, utilisez l’éditeur de votre choix et enregistrez-le dans un fichier .ps1. Vous pouvez utiliser l’applet de commande [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) pour récupérer le contenu du runbook, puis l’applet de commande [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) pour remplacer le brouillon du runbook existant par la version modifiée.

### Pour récupérer le contenu d’un runbook à l’aide de Windows PowerShell

Les exemples de commandes suivants montrent comment récupérer le script d’un runbook et l’enregistrer dans un fichier de script. Dans cet exemple, le brouillon est récupéré. Il est également possible de récupérer la version publiée du runbook, bien que cette version ne puisse pas être modifiée.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### Pour modifier le contenu d’un runbook à l’aide de Windows PowerShell

Les exemples de commandes suivants montrent comment remplacer le contenu existant d’un runbook par le contenu d’un fichier de script. Notez qu’il s’agit du même exemple de procédure que [Pour importer un runbook à partir d’un fichier de script avec Windows PowerShell](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS).

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## Articles connexes

- [Création ou importation d’un runbook dans Azure Automation](automation-creating-importing-runbook.md)
- [Apprentissage du workflow PowerShell](automation-powershell-workflow.md)
- [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md)
- [Certificats](automation-certificates.md)
- [Connexions](automation-connections.md)
- [Informations d'identification](automation-credentials.md)
- [Planifications](automation-schedules.md)
- [Variables](automation-variables.md)

<!---HONumber=AcomDC_0128_2016-->