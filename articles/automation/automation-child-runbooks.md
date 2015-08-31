<properties 
   pageTitle="Runbooks enfants dans Azure Automation | Microsoft Azure"
	description="Décrit les différentes méthodes permettant le démarrage d’un Runbook à partir d’un autre Runbook dans Azure Automation et le partage d’informations entre eux."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn"/>
<tags 
   ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/17/2015"
	ms.author="bwren"/>

# Runbooks enfants dans Azure Automation


Dans Azure Automation, il est recommandé d’écrire des Runbooks réutilisables et modulaires avec une fonction discrète qui peut être utilisée par d’autres Runbooks. Un Runbook parent appelle souvent un ou plusieurs Runbooks enfants pour exécuter la fonctionnalité requise. Il existe deux méthodes pour appeler un Runbook enfant. Vous devez comprendre leurs spécificités afin de déterminer celle répondant le mieux aux exigences de chacun de vos scénarios.

##  Appeler un Runbook enfant à l’aide de l’exécution en ligne

Pour appeler un Runbook en ligne à partir d’un autre Runbook, vous utilisez le nom du Runbook et définissez des valeurs pour ses paramètres de la même façon qu’avec une activité ou une applet de commande. Tous les Runbooks d’un même compte Automation peuvent être utilisés ainsi par les autres Runbooks. Le Runbook parent attend la fin de l’exécution du Runbook enfant avant de passer à la ligne suivante, et toute sortie est retournée directement au parent.

Lorsque vous appelez un Runbook en ligne, il est exécuté dans la même tâche que le Runbook parent. L’historique des tâches du Runbook enfant n’indique pas qu’il a été exécuté. Toutes les exceptions et sorties de flux issues du Runbook enfant seront associées au parent. Cela réduit le nombre de tâches et simplifie leur suivi, ainsi que la résolution des problèmes. En effet, toutes les exceptions lancées par le Runbook enfant et toute sortie de flux correspondante sont associées à la tâche du parent.

Lorsqu’un Runbook est publié, les Runbooks enfants qu’il appelle doivent déjà être publiés. En effet, Azure Automation crée une association avec tous les Runbooks enfants lorsqu’un Runbook est compilé. Si ce n’est pas le cas, la publication du Runbook parent semblera correcte, mais le Runbook générera une exception au démarrage. Dans ce cas, vous pouvez republier le Runbook parent pour référencer correctement les Runbooks enfants. Il est inutile de republier le Runbook parent si des Runbooks enfants sont modifiés, car l’association aura déjà été créée.

Les paramètres d’un Runbook enfant appelé en ligne peuvent correspondre à n’importe quel type de données, y compris des objets complexes. Aucune [sérialisation JSON](automation-starting-a-runbook.md#runbook-parameters) n’intervient, comme c’est le cas lorsque vous démarrez le Runbook à l’aide du Portail de gestion Azure ou de l’applet de commande Start-AzureAutomationRunbook.

Dans l’exemple suivant, on appelle un Runbook enfant de test qui accepte trois paramètres : un objet complexe, un entier et une valeur booléenne. La sortie du Runbook enfant est affectée à une variable.

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	$output = Test-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

##  Démarrage d’un Runbook enfant à l’aide d’une applet de commande

Vous pouvez utiliser l’applet de commande [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/dn690259.aspx) pour démarrer un Runbook, comme décrit dans la section [Démarrer un Runbook avec Windows PowerShell](../automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Lorsque vous démarrez un Runbook enfant à partir d’une applet de commande, le Runbook parent passe à la ligne suivante dès que la tâche est créée pour le Runbook enfant. Si vous devez récupérer des sorties du Runbook, vous devez accéder à la tâche à l’aide de [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/dn690268.aspx).

La tâche issue d’un Runbook enfant démarré avec une applet de commande est exécutée dans une tâche distincte du Runbook parent. Cela entraîne davantage de tâches que l’appel de script en ligne et rend leur suivi plus complexe. Le parent peut démarrer plusieurs Runbooks enfants sans attendre la fin de leur exécution. Pour ce même type d’exécution en parallèle avec appel des Runbooks enfants en ligne, le Runbook parent doit utiliser le [mot-clé parallèle](automation-powershell-workflow.md#parallel-processing).

Les paramètres d’un Runbook enfant démarré avec une applet de commande sont fournis sous forme de table de hachage, comme décrit dans la section [Paramètres du Runbook](automation-starting-a-runbook.md#runbook-parameters). Seuls les types de données simples peuvent être utilisés. Si le Runbook possède un paramètre avec un type de données complexe, il doit être appelé en ligne.

Dans l’exemple suivant, un Runbook enfant avec paramètres est démarré et exécuté. À l’issue de l’exécution du Runbook, sa sortie est collectée à partir de la tâche par le Runbook parent.

	$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test- ChildRunbook" –Parameters $params
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

[Start-ChildRunbook](http://gallery.technet.microsoft.com/scriptcenter/Start-Azure-Automation-1ac858a9) est un Runbook d’assistance disponible dans la Galerie TechNet, pour le démarrage d’un Runbook à partir d’une applet de commande. Cela permet d’attendre la fin de l’exécution du Runbook enfant et de récupérer sa sortie. Vous pouvez utiliser ce Runbook dans votre propre environnement Azure Automation, mais également en tant que référence pour l’utilisation de Runbooks et de tâches avec des applets de commande. Le Runbook d’assistance doit être appelé en ligne, car il requiert un paramètre de table de hachage pour accepter les valeurs de paramètre du Runbook enfant.


## Comparaison des méthodes pour l’appel d’un Runbook enfant

Le tableau suivant résume les différences entre les deux méthodes applicables pour appeler un Runbook à partir d’un autre Runbook.

| | En ligne| Applet de commande|
|:---|:---|:---|
|Job|Les Runbooks enfants s’exécutent dans la même tâche que le parent.|Une tâche distincte est créée pour le Runbook enfant.|
|Exécution|Le Runbook parent attend la fin de l’exécution du Runbook enfant avant de se poursuivre.|Le Runbook parent se poursuit dès le démarrage du Runbook enfant.|
|Sortie|Le Runbook parent peut obtenir directement la sortie du Runbook enfant.|Le Runbook parent doit récupérer la sortie à partir de la tâche du Runbook enfant.|
|Paramètres|Les valeurs des paramètres du Runbook enfant sont spécifiées séparément et peuvent utiliser n’importe quel type de données.|Les valeurs des paramètres du Runbook enfant doivent être combinées dans une table de hachage unique et peuvent inclure uniquement des types de données simples, de tableau et d’objet exploitant la sérialisation JSON.|
|Compte Automation|Le Runbook parent peut utiliser uniquement un Runbook enfant du même compte Automation.|Le Runbook parent peut utiliser un Runbook enfant de n’importe quel compte Automation du même abonnement Azure ou d’un autre abonnement si vous disposez de la connexion correspondante.|
|Publication|Le Runbook enfant doit être publié avant la publication du Runbook parent.|Le Runbook enfant doit être publié avant le démarrage du Runbook parent.|

## Articles connexes

- [Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md)
- [Sortie et messages de Runbook dans Azure Automation](automation-runbook-output-and-messages.md)

<!---HONumber=August15_HO8-->