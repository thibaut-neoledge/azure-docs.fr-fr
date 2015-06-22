<properties 
   pageTitle="Ressources de variables dans Azure Automation"
   description="Les ressources de variables sont des valeurs disponibles pour tous les Runbooks d'Azure Automation. Cet article présente les variables de façon détaillée et comment les utiliser dans une création textuelle ou graphique."
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

# Ressources de variables dans Azure Automation

Les ressources de variables sont des valeurs disponibles pour tous les Runbooks de votre compte Automation. Elles peuvent être créées, modifiées et récupérées à partir du portail Azure, de Windows PowerShell ou d'un Runbook. Les variables Automation sont utiles pour les scénarios suivants :

- Partager une valeur entre plusieurs Runbooks.

- Partager une valeur entre plusieurs tâches du même Runbook.

- Gérer une valeur à partir du portail ou de la ligne de commande Windows PowerShell utilisée par les Runbooks.

Les variables Automation sont conservées afin de continuer à être disponibles même si le Runbook échoue. Cela permet aussi à une valeur d'être définie par un Runbook, qui est ensuite utilisé par un autre Runbook ou par le même Runbook lors de sa prochaine exécution.

Quand une variable est créée, vous pouvez spécifier qu'elle doit être stockée de manière chiffrée. Quand une variable est chiffrée, elle est stockée de manière sécurisée dans Azure Automation. Sa valeur ne peut pas être récupérée à partir de l'applet de commande [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx), fournie dans le cadre du module Azure PowerShell. Une valeur chiffrée ne peut être récupérée que d'une seule façon, à partir de l'activité **Get-AutomationVariable** d'un Runbook.

>[AZURE.NOTE]Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation à l'aide d'une clé unique, générée pour chaque compte Automation. Cette clé est chiffrée par un certificat principal et stockée dans Azure Automation. Avant de stocker une ressource sécurisée, la clé du compte Automation est déchiffrée à l'aide du certificat principal, puis utilisée pour chiffrer la ressource.

## Types de variables

Lorsque vous créez une variable avec le portail Azure, vous devez spécifier un type de données dans la liste déroulante afin que le portail puisse afficher le contrôle approprié pour l'entrée de la valeur de la variable. La variable n'est pas limitée à ce type de données, mais vous devez définir la variable à l'aide de Windows PowerShell si vous souhaitez spécifier une valeur d'un type différent. Si vous spécifiez **Non défini**, la variable est définie avec la valeur **$null**, et vous devez définir la valeur avec l'applet de commande [Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx) ou l'activité **Set-AutomationVariable**. Vous ne pouvez pas créer ou modifier la valeur d'un type de variable complexe dans le portail, mais vous pouvez fournir une valeur de tout type à l'aide de Windows PowerShell. Les types complexes sont retournés en tant que [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx).

Vous pouvez stocker plusieurs valeurs dans une seule variable en créant un tableau ou une table de hachage, et en l'enregistrant sur la variable.

## Applets de commande et activités de workflow

Les applets de commande du tableau suivant permettent de créer et de gérer les variables Automation avec Windows PowerShell. Elles sont fournies dans le cadre du [module Azure PowerShell](../powershell-install-configure.md), disponible pour être utilisé dans les Runbooks Automation.

|Applets de commande|Description|
|:---|:---|
|[Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx)|Récupère la valeur d'une variable existante.|
|[New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx)|Crée une variable et définit sa valeur.|
|[Remove-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913775.aspx)|Supprime une variable existante.|
|[Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx)|Définit la valeur d'une variable existante.|

Les activités de workflow du tableau suivant sont utilisées pour accéder aux variables Automation d'un Runbook. Elles sont uniquement disponibles pour être utilisées dans un Runbook et ne sont pas fournies dans le cadre du module Azure PowerShell.

|Activités de workflow|Description|
|:---|:---|
|Get-AutomationVariable|Récupère la valeur d'une variable existante.|
|Set-AutomationVariable|Définit la valeur d'une variable existante.|

>[AZURE.NOTE]Évitez d'utiliser des variables dans le paramètre – Name de **Get- AutomationVariable** dans un Runbook, car cela complique la découverte de dépendances entre les Runbooks et les variables Automation au moment de la conception.

## Création d'une variable Automation

### Création d'une variable avec le portail Azure

1. À partir de votre compte Automation, cliquez sur **Ressources** en haut de la fenêtre.
1. En bas de la fenêtre, cliquez sur **Ajouter un paramètre**.
1. Cliquez sur **Ajouter une variable**.
1. Terminez l'Assistant et cochez la case pour enregistrer la nouvelle variable.


### Pour créer une variable avec le portail Azure en version préliminaire

1. À partir de votre compte Automation, cliquez sur la partie **Ressources** afin d'ouvrir le panneau **Ressources**.
1. Cliquez sur la partie **Variables** afin d'ouvrir le panneau **Variables**.
1. Cliquez sur **Ajouter une variable** en haut du panneau.
1. Remplissez le formulaire, puis cliquez sur **Créer** pour enregistrer la nouvelle variable.


### Pour créer une variable avec Windows PowerShell

L'applet de commande [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx) crée une variable et définit sa valeur initiale. Vous pouvez récupérer la valeur en utilisant [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx). Si la valeur est un type simple, ce même type est retourné. S'il s'agit d'un type complexe, un **PSCustomObject** est retourné.

Les exemples de commandes suivants montrent comment créer une variable de type chaîne, puis retourner sa valeur.


	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' –Encrypted $false –Value 'My String'
	$string = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

Les exemples de commandes suivants montrent comment créer une variable de type complexe, puis retourner ses propriétés. Dans ce cas, une machine virtuelle à partir de **Get-AzureVM** est utilisée.

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
	
	$vmValue = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
	$vmName = $ vmValue.Name
	$vmIpAddress = $ vmValue.IpAddress



## Utilisation d'une variable dans un Runbook

Utilisez l'activité **Set-AutomationVariable** activité qui permet de définir la valeur d'une variable Automation dans un Runbook et l'activité **Get-AutomationVariable** pour la récupérer. Vous ne devez pas utiliser les applets de commande **Set-AzureAutomationVariable** ou **Get-AzureAutomationVariable** dans un Runbook, car elles sont moins efficaces que les activités de workflow. Vous ne pouvez pas non plus récupérer la valeur de variables sécurisées avec **Get-AzureAutomationVariable**. La seule façon de créer une variable à partir d'un Runbook consiste à utiliser l'applet de commande [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx).


### Exemple de Runbooks textuels

#### Définition et récupération d'une valeur simple à partir d'une variable

Les exemples de commandes suivants montrent comment définir et récupérer une variable dans un Runbook textuel. Dans cet exemple, il est supposé que les variables de type entier nommées *NumberOfIterations* et *NumberOfRunnings* et une variable de type chaîne nommée *SampleMessage* ont déjà été créées.

	$NumberOfIterations = Get-AutomationVariable -Name 'NumberOfIterations'
	$NumberOfRunnings = Get-AutomationVariable -Name 'NumberOfRunnings'
	$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
	
	Write-Output "Runbook has been run $NumberOfRunnings times."
	
	for ($i = 1; $i -le $NumberOfIterations; $i++) {
	   Write-Output "$i`: $SampleMessage"
	}
	Set-AutomationVariable –Name NumberOfRunnings –Value (NumberOfRunngs += 1)


#### Définition et récupération d'un objet complexe dans une variable

L'exemple de code suivant montre comment mettre à jour une variable avec une valeur complexe dans un Runbook textuel. Dans cet exemple, une machine virtuelle Azure est récupérée avec **Get-AzureVM** et enregistrée dans une variable Automation existante. Comme expliqué dans [Types de variables](#variable-types), il est stocké comme PSCustomObject.

	$vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
	Set-AutomationVariable -Name "MyComplexVariable" -Value $vm


Dans le code suivant, la valeur est extraite de la variable et utilisée pour démarrer la machine virtuelle.

	$vmObject = Get-AutomationVariable -Name "MyComplexVariable"
	if ($vmObject.PowerState -eq 'Stopped') {
	   Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
	}


#### Définition et récupération d'une collection dans une variable

L'exemple de code suivant montre comment utiliser une variable avec une collection de valeurs complexes dans un Runbook textuel. Dans cet exemple, plusieurs machines virtuelles Azure sont récupérées avec **Get-AzureVM** et enregistrées dans une variable Automation existante. Comme expliqué dans [Types de variables](#variable-types), elles sont stockées comme collection de PSCustomObject.

	$vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

Dans le code suivant, la valeur est extraite de la variable et utilisée pour démarrer chaque machine virtuelle.

	$vmValues = Get-AutomationVariable -Name "MyComplexVariable"
	ForEach ($vmValue in $vmValues)
	{
	   if ($vmValue.PowerState -eq 'Stopped') {
	      Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
	   }
	}

### Exemples de Runbook graphiques

Dans un Runbook graphique, vous ajoutez l'activité **Get-AutomationVariable** ou **Set-AutomationVariable** en cliquant sur la variable dans le volet Bibliothèque de l'éditeur graphique et en sélectionnant l'activité souhaitée.

![Ajouter une variable à la zone de dessin](media/automation-variables/variable-add-canvas.png)

#### Définition de valeurs dans une variable

L'image suivante montre des exemples d'activité pour mettre à jour une variable avec une valeur simple dans un Runbook graphique. Dans cet exemple, une seule machine virtuelle Azure est récupérée avec **Get-AzureVM** et le nom d'ordinateur est enregistré dans une variable Automation existante avec le type String. Peu importe si les [lien est un pipeline ou une séquence](automation-graphical-authoring-intro.md#links-and-workflow), car nous attendons uniquement un objet unique dans la sortie.

![Définir une variable simple](media/automation-variables/set-simple-variable.png)

L'image suivante montre les activités utilisées pour mettre à jour une variable avec une valeur complexe dans un Runbook graphique. La seule modification par rapport à l'exemple précédent est la non-spécification d'un **chemin d'accès au champ** pour la**sortie d'activité** de l'activité **Set-AutomationVariable**, de telle sorte que c'est l'objet qui est stocké, et non une simple propriété de l'objet. Comme expliqué dans [Types de variables](#variable-types), il est stocké comme PSCustomObject.

![Définir une variable complexe](media/automation-variables/set-complex-variable.png)

L'illustration suivante montre une fonctionnalité similaire à celle de l'exemple précédent, avec plusieurs machines virtuelles enregistrées dans la variable. Un [lien séquence](automation-graphical-authoring-intro.md#links-and-workflow) doit être utilisé ici afin que l'activité **Set-AutomationVariable** reçoive l'ensemble complet des machines virtuelles comme une seule collection. Si un [lien pipeline](automation-graphical-authoring-intro.md#links-and-workflow) a été utilisé, l'activité **Set-AutomationVariable** sera exécutée séparément pour chaque objet et, en conséquence, seule la dernière machine virtuelle de la collection sera enregistrée. Comme expliqué dans [Types de variables](#variable-types), les machines virtuelles sont stockées comme collection de PSCustomObject.

![Définir une variable de collection complexe](media/automation-variables/set-complex-variable-collection.png)

#### Extraction des valeurs d'une variable

L'image suivante montre des exemples d'activité qui récupèrent une variable et l'utilisent dans un Runbook graphique. La première activité récupère les machines virtuelles qui ont été enregistrées dans la variable de l'exemple précédent. Le lien doit être un [pipeline](automation-graphical-authoring-intro.md#links-and-workflow) afin que l'activité **Start-AzureVM** s'exécute une fois pour chaque objet envoyé à partir de l'activité **Get-AutomationVariable**. Le fonctionnement est le même si un seul objet ou plusieurs objets sont stockés dans la variable. L'activité **Start-AzureVM** utilise des propriétés du PSCustomObject qui représente chaque machine virtuelle.

![Obtenir une variable complexe](media/automation-variables/get-complex-variable.png)

L'illustration suivante montre comment filtrer les objets qui sont stockés dans une variable d'un Runbook graphique. Une [condition](automation-graphical-authoring-intro.md#links-and-workflow) est ajoutée au lien de l'exemple précédent pour filtrer uniquement les machines virtuelles qui ont été arrêtées lorsque la variable a été définie.

![Obtenir une variable complexe filtrée](media/automation-variables/get-complex-variable-filter.png)


## Articles connexes

- [Liens de création graphique](automation-graphical-authoring-intro.md#links-and-workflow)

<!---HONumber=58--> 