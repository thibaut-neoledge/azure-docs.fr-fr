<properties
   pageTitle="Sortie et messages de Runbook dans Azure Automation | Microsoft Azure"
   description="Décrit comment créer et récupérer la sortie et les messages d’erreur à partir des Runbooks dans Azure Automation."
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
   ms.date="03/03/2016"
   ms.author="magoedte;bwren" />

# Sortie et messages de Runbook dans Azure Automation

La plupart des Runbooks Azure Automation présentent une certaine forme de sortie. Il peut s’agir, par exemple, d’un message d’erreur à l’attention de l’utilisateur, ou encore d’un objet complexe destiné à être consommé par un autre flux de travail. Windows PowerShell fournit [plusieurs flux](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) pour l’envoi d’une sortie à partir d’un script ou d’un flux de travail. Azure Automation fonctionne différemment avec chacun de ces flux. Lorsque vous créez un Runbook, vous devez suivre les meilleures pratiques relatives à l’utilisation de chaque flux.

Le tableau suivant fournit une brève description de chacun des flux et de leur comportement dans le Portail de gestion Azure lors de l’exécution d’un Runbook publié et lors du [test d’un Runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx). Pour plus de détails sur chaque flux, reportez-vous aux sections suivantes.

| Stream | Description | Publié | Test|
|:---|:---|:---|:---|
|Sortie|Objets destinés à être consommés par d’autres Runbooks.|Consignation dans l’historique des tâches.|Affichage dans le volet de sortie du test.|
|Avertissement|Message d’avertissement destiné à l’utilisateur.|Consignation dans l’historique des tâches.|Affichage dans le volet de sortie du test.|
|Erreur|Message d’erreur destiné à l’utilisateur. Contrairement à une exception, le Runbook se poursuit après un message d’erreur par défaut.|Consignation dans l’historique des tâches.|Affichage dans le volet de sortie du test.|
|Détaillé|Messages fournissant des informations générales ou de débogage.|Consignation dans l’historique des tâches uniquement si la journalisation documentée est activée pour le Runbook.|Affichage dans le volet de sortie du test uniquement si la préférence $VerbosePreference a la valeur Continue dans le Runbook.|
|Progression|Informations générées automatiquement avant et après chaque activité dans le Runbook. Le Runbook ne doit pas tenter de créer ses propres informations de progression dans la mesure où elles sont destinées à un utilisateur interactif.|Consignation dans l’historique des tâches uniquement si l’enregistrement de la progression est activé pour le Runbook.|Pas d’affichage dans le volet de sortie du test.|
|Déboguer|Messages destinés à un utilisateur interactif. Utilisation proscrite dans les Runbooks.|Pas de consignation dans l’historique des tâches.|Pas d’affichage dans le volet de sortie du test.|

## Flux de sortie

Le flux de sortie est destiné à la sortie des objets créés par un script ou un flux de travail lorsqu’il s’exécute correctement. Dans Azure Automation, ce flux est principalement utilisé pour les objets destinés à être consommés par les [Runbooks parents qui appellent le Runbook actuel](automation-child-runbooks.md). Lorsque vous [appelez un Runbook en ligne](automation-child-runbooks.md/#InlineExecution) à partir d’un Runbook parent, il renvoie les données au parent à partir du flux de sortie. Vous devez utiliser le flux de sortie uniquement pour retourner des informations générales à l’utilisateur si vous savez que le Runbook ne sera jamais appelé par un autre Runbook. Cependant, nous vous recommandons d’utiliser le [flux des commentaires](#Verbose) pour communiquer des informations générales à l’utilisateur.

Vous pouvez écrire des données dans le flux de sortie à l’aide de [Write-Output](http://technet.microsoft.com/library/hh849921.aspx) ou en plaçant l’objet sur sa propre ligne dans le Runbook.

	#The following lines both write an object to the output stream.
	Write-Output –InputObject $object
	$object

### Sortie à partir d’une fonction

Lorsque vous écrivez dans le flux de sortie, dans une fonction qui est incluse dans votre Runbook, la sortie est renvoyée au Runbook. Si le Runbook affecte cette sortie à une variable, elle n’est pas écrite dans le flux de sortie. Toute écriture dans d’autres flux à partir de la fonction se fait dans le flux correspondant du Runbook.

Examinez l’exemple de Runbook suivant.

	Workflow Test-Runbook
	{
	   Write-Verbose "Verbose outside of function"
	   Write-Output "Output outside of function"
	   $functionOutput = Test-Function

	   Function Test-Function
	   {
	      Write-Verbose "Verbose inside of function"
	      Write-Output "Output inside of function"
	   }
	}

Le flux de sortie pour la tâche du Runbook serait :

	Output outside of function

Le flux des commentaires pour la tâche du Runbook serait :

	Verbose outside of function
	Verbose inside of function

### Déclaration du type de données de sortie

Un flux de travail peut spécifier le type de données de sa sortie à l’aide de l’[attribut OutputType](http://technet.microsoft.com/library/hh847785.aspx). Cet attribut n’a aucun effet lors de l’exécution, mais il fournit une indication à l’auteur du Runbook sur la sortie attendue du Runbook au moment de la conception. Alors que l’ensemble d’outils dédiés aux Runbooks continue d’évoluer, la déclaration des types de données de sortie au moment de la conception gagne en importance. Par conséquent, il est recommandé d’inclure cette déclaration dans tous les Runbooks que vous créez.

L’exemple de Runbook suivant génère un objet String et inclut une déclaration de son type de sortie. Si votre Runbook génère un tableau d’un certain type, vous devez toujours spécifier le type, par opposition à un tableau du type.

	Workflow Test-Runbook
	{
	   [OutputType([string])]

	   $output = "This is some string output."
	   Write-Output $output
	}

## Flux de messages

Contrairement au flux de sortie, les flux de messages sont destinés à la communication des informations à l’utilisateur. Il existe plusieurs flux de messages pour les différents types d’informations, et chacun d’eux est traité différemment par Azure Automation.

### Flux d’erreurs et d’avertissements

Les flux d’erreurs et d’avertissements sont conçus pour l’enregistrement des problèmes qui se produisent dans un Runbook. Ils sont consignés dans l’historique des tâches lorsqu’un Runbook est exécuté et sont inclus dans le volet de sortie du test du Portail de gestion Azure lorsqu’un Runbook est testé. Par défaut, l’exécution du Runbook se poursuit après un avertissement ou une erreur. Vous pouvez spécifier l’interruption du Runbook en cas d’avertissement ou d’erreur en définissant une [variable de préférence](#PreferenceVariables) dans le Runbook avant de créer le message. Par exemple, pour déclencher l’interruption du Runbook en cas d’erreur, comme dans le cas d’une exception, affectez la valeur Stop à **$ErrorActionPreference**.

Créez un message d’avertissement ou un message d’erreur à l’aide de l’applet de commande [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) ou [Write-Error](http://technet.microsoft.com/library/hh849962.aspx). Les activités peuvent également écrire dans ces flux.

	#The following lines create a warning message and then an error message that will suspend the runbook.

	$ErrorActionPreference = "Stop"
	Write-Warning –Message "This is a warning message."
	Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."

### Flux des commentaires

Ce flux est utilisé pour les informations générales sur le fonctionnement du Runbook. Dans la mesure où le [flux de débogage](#Debug) n’est pas disponible dans un Runbook, les messages documentés doivent être utilisés pour les informations de débogage. Par défaut, les messages documentés provenant de Runbooks publiés ne seront pas stockés dans l’historique des tâches. Pour stocker les messages documentés, configurez les Runbooks publiés de façon à enregistrer les informations de commentaires sous l’onglet de configuration du Runbook dans le Portail de gestion Azure. Pour des raisons de performances, il est généralement recommandé de conserver le paramètre par défaut (les informations de commentaires ne sont pas enregistrées pour le Runbook). Activez cette option uniquement pour résoudre les problèmes liés à un Runbook ou déboguer un Runbook.

Lors du [test d’un Runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx), les messages documentés ne s’affichent pas, même si le Runbook est configuré pour enregistrer les informations de commentaires. Pour afficher les messages documentés lors du [test d’un Runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx), vous devez affecter la valeur Continue à la variable $VerbosePreference. Dans ce cas, les messages documentés seront affichés dans le volet de sortie du test du Portail de gestion Azure.

Créez un message documenté à l’aide de l’applet de commande [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx).

	#The following line creates a verbose message.

	Write-Verbose –Message "This is a verbose message."

### Flux de débogage

Le flux de débogage est destiné à être utilisé avec un utilisateur interactif et ne doit pas être utilisé dans les Runbooks.

## Informations de progression

Si vous configurez un Runbook pour enregistrer les informations de progression (sous l’onglet de configuration du Runbook, dans le Portail de gestion Azure), un enregistrement est consigné dans l’historique des tâches avant et après l’exécution de chaque activité. Afin d’optimiser les performances, il est généralement recommandé de conserver le paramètre par défaut (les informations de progression ne sont pas enregistrées pour le Runbook). Activez cette option uniquement pour résoudre les problèmes liés à un Runbook ou déboguer un Runbook. Lors du test d’un Runbook, les messages de progression ne s’affichent pas, même si le Runbook est configuré pour enregistrer les informations de progression.

L’applet de commande [Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) n’est pas valide dans un Runbook, dans la mesure où les messages sont destinés à un utilisateur interactif.

## Variables de préférence

Windows PowerShell utilise des [variables de préférence](http://technet.microsoft.com/library/hh847796.aspx) pour déterminer comment répondre aux données envoyées aux différents flux de sortie. Vous pouvez définir ces variables dans un Runbook pour contrôler comment il répondra aux données envoyées dans différents flux.

Le tableau suivant répertorie les variables de préférence qui peuvent être utilisées dans des Runbooks, ainsi que leurs valeurs valides et valeurs par défaut. Notez que ce tableau inclut uniquement les valeurs valides dans un Runbook. D’autres valeurs sont valides pour les variables de préférence lorsqu’elles sont utilisées dans Windows PowerShell en dehors d’Azure Automation.

| Variable| Valeur par défaut| Valeurs valides|
|:---|:---|:---|
|WarningPreference|Continue|Stop<br>Continue<br>SilentlyContinue|
|ErrorActionPreference|Continue|Stop<br>Continue<br>SilentlyContinue|
|VerbosePreference|SilentlyContinue|Stop<br>Continue<br>SilentlyContinue|

Le tableau suivant indique le comportement associé aux valeurs des variables de préférence valides dans les Runbooks.

| Valeur| Comportement|
|:---|:---|
|Continue|Enregistre le message et poursuit l’exécution du Runbook.|
|SilentlyContinue|Poursuit l’exécution du Runbook sans enregistrer le message. Le message est alors ignoré.|
|Arrêter|Enregistre le message et interrompt le Runbook.|

## Récupération de la sortie et des messages de Runbook

### Portail de gestion Azure

Vous pouvez afficher les détails d’une tâche de Runbook dans le Portail de gestion Azure à partir de l’onglet Tâches d’un Runbook. Le résumé de la tâche affiche les paramètres d’entrée et le [flux de sortie](#Output) en plus des informations générales sur la tâche et des exceptions, le cas échéant. L’historique inclura les messages du [flux de sortie](#Output) et des [flux d’avertissements et d’erreurs](#WarningError) en plus du [flux des commentaires](#Verbose) et des [informations de progression](#Progress) si le Runbook est configuré pour enregistrer les informations de commentaires et de progression.

### Windows PowerShell

Dans Windows PowerShell, vous pouvez récupérer la sortie et les messages d’un Runbook à l’aide de l’applet de commande [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/dn690268.aspx). Cette applet de commande requiert l’ID de la tâche. Elle inclut le paramètre Stream qui vous permet de spécifier le flux à renvoyer. Vous pouvez spécifier la valeur Any pour renvoyer tous les flux de la tâche.

Dans l’exemple suivant, un exemple de Runbook est démarré et exécuté. À l’issue de l’exécution du Runbook, son flux de sortie est collecté à partir de la tâche.

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped")
	}

	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

### Création graphique

Pour les runbooks graphiques, une journalisation supplémentaire est disponible sous la forme de suivi au niveau de l’activité. Il existe deux niveaux de suivi : de base et détaillé. Dans le suivi de base, vous pouvez voir l’heure de début et de fin de chaque activité dans le runbook, ainsi que des informations relatives à toute nouvelle tentative de l’activité, comme le nombre de tentatives et l’heure de début de l’activité. Dans le suivi détaillé, vous obtenez le suivi de base, ainsi que des données d’entrée et de sortie pour chaque activité. Notez qu’actuellement les enregistrements de suivi sont écrits à l’aide du flux de commentaires détaillé. Vous devez activer la journalisation détaillée lorsque vous activez le suivi. Pour les runbooks graphiques avec le suivi activé, il est inutile de journaliser les informations de progression, étant donné que le suivi de base joue le même rôle et contient des informations plus détaillées.

![Vue du flux de travail de création graphique](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

La capture d’écran ci-dessus illustre le fait que lorsque vous activez la journalisation détaillée et le suivi pour les graphiques, vous obtenez beaucoup plus d’informations dans la vue du flux de travail de production. Ces informations supplémentaires peuvent être essentielles pour résoudre les problèmes de production pour un runbook. Il est donc conseillé de l’activer uniquement à cet effet et pas de manière générale. Les enregistrements de suivi peuvent être particulièrement nombreux. Avec le suivi de runbook graphique, vous pouvez obtenir entre deux et quatre enregistrements par activité selon que vous avez configuré le suivi de base ou le suivi détaillé. À moins que vous n’ayez besoin de ces informations pour suivre la progression d’un runbook à des fins de dépannage, vous pouvez choisir de désactiver le suivi.

**Pour activer le suivi au niveau de l’activité, procédez comme suit.**

 1. Dans le portail Azure, ouvrez votre compte Automation.

 2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.

 3. Dans le panneau Runbooks, cliquez sur un runbook graphique pour le sélectionner dans la liste des runbooks.

 4. Dans le panneau Paramètres du runbook sélectionné, cliquez sur **Journalisation et suivi**.

 5. Dans le panneau Journalisation et suivi, sous Journaliser les enregistrements détaillés, cliquez sur **Activé** pour activer la journalisation détaillée et sous Suivi au niveau de l’activité, modifiez le niveau de suivi sur **De base** ou **Détaillé** en fonction du niveau de suivi requis.<br>

    ![Panneau Journalisation et suivi de la création graphique](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

## Articles connexes

- [Suivre une tâche de Runbook](automation-runbook-execution.md)
- [Runbooks enfants](http://msdn.microsoft.com/library/azure/dn857355.aspx)

<!------HONumber=AcomDC_0309_2016-->