<properties 
   pageTitle="Runbooks enfants dans Azure Automation | Microsoft Azure"
   description="Décrit les différentes méthodes permettant le démarrage d’un Runbook à partir d’un autre Runbook dans Azure Automation et le partage d’informations entre eux."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/21/2016"
   ms.author="magoedte;bwren" />

# Runbooks enfants dans Azure Automation


Dans Azure Automation, il est recommandé d’écrire des Runbooks réutilisables et modulaires avec une fonction discrète qui peut être utilisée par d’autres Runbooks. Un Runbook parent appelle souvent un ou plusieurs Runbooks enfants pour exécuter la fonctionnalité requise. Il existe deux méthodes pour appeler un Runbook enfant. Vous devez comprendre leurs spécificités afin de déterminer celle répondant le mieux aux exigences de chacun de vos scénarios.

##  Appeler un Runbook enfant à l’aide de l’exécution en ligne

Pour appeler un Runbook en ligne à partir d’un autre Runbook, vous utilisez le nom du Runbook et définissez des valeurs pour ses paramètres de la même façon qu’avec une activité ou une applet de commande. Tous les Runbooks d’un même compte Automation peuvent être utilisés ainsi par les autres Runbooks. Le Runbook parent attend la fin de l’exécution du Runbook enfant avant de passer à la ligne suivante, et toute sortie est retournée directement au parent.

Lorsque vous appelez un Runbook en ligne, il est exécuté dans la même tâche que le Runbook parent. L’historique des tâches du Runbook enfant n’indique pas qu’il a été exécuté. Toutes les exceptions et sorties de flux issues du Runbook enfant seront associées au parent. Cela réduit le nombre de tâches et simplifie leur suivi, ainsi que la résolution des problèmes. En effet, toutes les exceptions lancées par le Runbook enfant et toute sortie de flux correspondante sont associées à la tâche du parent.

Lorsqu’un Runbook est publié, les Runbooks enfants qu’il appelle doivent déjà être publiés. En effet, Azure Automation crée une association avec tous les Runbooks enfants lorsqu’un Runbook est compilé. Si ce n’est pas le cas, la publication du Runbook parent semblera correcte, mais le Runbook générera une exception au démarrage. Dans ce cas, vous pouvez republier le Runbook parent pour référencer correctement les Runbooks enfants. Il est inutile de republier le Runbook parent si des Runbooks enfants sont modifiés, car l’association aura déjà été créée.

Les paramètres d’un Runbook enfant appelé en ligne peuvent correspondre à n’importe quel type de données, y compris des objets complexes. Aucune [sérialisation JSON](automation-starting-a-runbook.md#runbook-parameters) n’intervient, comme c’est le cas lorsque vous démarrez le Runbook à l’aide du Portail de gestion Azure ou de l’applet de commande Start-AzureRmAutomationRunbook.

### Types de runbook

Vous ne pouvez pas utiliser un [runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) ou un [runbook graphique](automation-runbook-types.md#graphical-runbooks) en tant qu’enfant d’un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) à l’aide d’une exécution incorporée. De même, vous ne pouvez pas utiliser un runbook PowerShell en tant qu’enfant avec une exécution incorporée dans un runbook PowerShell Workflow ou d’un runbook graphique. Les runbooks PowerShell peuvent utiliser un autre PowerShell uniquement en tant qu’enfant. Des runbooks graphiques et PowerShell Workflow peuvent se servir les uns des autres en tant que runbooks enfants.

Lorsque vous appelez un runbook graphique ou PowerShell Workflow enfant à l’aide d’une exécution incorporée, vous utilisez simplement le nom du runbook. Lorsque vous appelez un runbook enfant PowerShell, vous devez insérer *.\* devant son nom pour spécifier que le script se trouve dans le répertoire local.

### Exemple

Dans l’exemple suivant, on appelle un Runbook enfant de test qui accepte trois paramètres : un objet complexe, un entier et une valeur booléenne. La sortie du Runbook enfant est affectée à une variable. Dans ce cas, le runbook enfant est un runbook PowerShell Workflow

	$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

Voici le même exemple utilisant un runbook PowerShell en tant qu’enfant.

	$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true



##  Démarrage d’un Runbook enfant à l’aide d’une applet de commande

Vous pouvez utiliser l’applet de commande [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) pour démarrer un runbook, comme décrit dans [Pour démarrer un runbook avec Windows PowerShell](../automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Il existe deux modes d’utilisation pour cette applet de commande. Dans un mode, l’applet de commande renvoie l’ID de tâche dès que la tâche enfant est créée pour le runbook enfant. Dans l’autre mode, que vous activez en spécifiant le paramètre **-wait**, l’applet de commande attend que la tâche enfant se termine et renvoie la sortie du runbook enfant.

La tâche issue d’un Runbook enfant démarré avec une applet de commande est exécutée dans une tâche distincte du Runbook parent. Cela entraîne davantage de tâches que l’appel de runbook en ligne et rend leur suivi plus complexe. Le parent peut démarrer plusieurs Runbooks enfants de façon asynchrone, sans attendre la fin de leur exécution. Pour ce même type d’exécution en parallèle avec appel des runbooks enfants en ligne, le runbook parent doit utiliser le [mot clé parallèle](automation-powershell-workflow.md#parallel-processing).

Les paramètres d’un runbook enfant démarré avec une applet de commande sont fournis sous forme de table de hachage, comme décrit dans [Paramètres du runbook](automation-starting-a-runbook.md#runbook-parameters). Seuls les types de données simples peuvent être utilisés. Si le Runbook possède un paramètre avec un type de données complexe, il doit être appelé en ligne.

### Exemple

Dans l’exemple suivant, un Runbook enfant avec paramètres est démarré et exécuté avec le paramètre Start-AzureRmAutomationRunbook -wait. À l’issue de l’exécution du Runbook, sa sortie est collectée à partir du runbook enfant.

	$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResouceGroupName "LabRG" –Parameters $params –wait


## Comparaison des méthodes pour l’appel d’un Runbook enfant

Le tableau suivant résume les différences entre les deux méthodes applicables pour appeler un Runbook à partir d’un autre Runbook.

| | En ligne| Applet de commande|
|:---|:---|:---|
|Job|Les Runbooks enfants s’exécutent dans la même tâche que le parent.|Une tâche distincte est créée pour le Runbook enfant.|
|Exécution|Le Runbook parent attend la fin de l’exécution du Runbook enfant avant de se poursuivre.|Le runbook parent continue immédiatement après le démarrage du runbook enfant *ou* le runbook parent attend que la tâche enfant se termine.|
|Sortie|Le Runbook parent peut obtenir directement la sortie du Runbook enfant.|Le runbook parent doit récupérer la sortie à partir de la tâche du runbook enfant *ou* le runbook parent peut obtenir directement la sortie du runbook enfant.|
|Paramètres|Les valeurs des paramètres du Runbook enfant sont spécifiées séparément et peuvent utiliser n’importe quel type de données.|Les valeurs des paramètres du Runbook enfant doivent être combinées dans une table de hachage unique et peuvent inclure uniquement des types de données simples, de tableau et d’objet exploitant la sérialisation JSON.|
|Compte Automation|Le Runbook parent peut utiliser uniquement un Runbook enfant du même compte Automation.|Le Runbook parent peut utiliser un Runbook enfant de n’importe quel compte Automation du même abonnement Azure ou d’un autre abonnement si vous disposez de la connexion correspondante.|
|Publication|Le Runbook enfant doit être publié avant la publication du Runbook parent.|Le Runbook enfant doit être publié avant le démarrage du Runbook parent.|

## Étapes suivantes

- [Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md)
- [Sortie et messages de Runbook dans Azure Automation](automation-runbook-output-and-messages.md)

<!---HONumber=AcomDC_0518_2016-->