---
title: "Ressources de variables dans Azure Automation | Microsoft Docs"
description: "Les ressources de variables sont des valeurs disponibles pour tous les Runbooks et configurations DSC d’Azure Automation.  Cet article présente les variables de façon détaillée et comment les utiliser dans une création textuelle ou graphique."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: b880c15f-46f5-4881-8e98-e034cc5a66ec
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 28896becae631eb316775e0904930f750bafa607
ms.lasthandoff: 04/27/2017


---
# <a name="variable-assets-in-azure-automation"></a>Ressources de variables dans Azure Automation

Les ressources de variables sont des valeurs disponibles pour tous les Runbooks et configurations DSC de votre compte Automation. Elles peuvent être créées, modifiées et récupérées à partir du portail Azure, de Windows PowerShell ou bien d’un Runbook ou d’une configuration DSC. Les variables Automation sont utiles pour les scénarios suivants :

- Partager une valeur entre plusieurs Runbooks ou configurations DSC.

- Partager une valeur entre plusieurs tâches du même Runbook ou de la même configuration DSC.

- Gérer une valeur du portail ou de la ligne de commande Windows PowerShell, qui est utilisée par les Runbooks ou les configurations DSC, par exemple un ensemble d’éléments de configuration communs comme une liste spécifique de noms de machine virtuelle, un groupe de ressources particulier ou un nom de domaine Active Directory.  

Les variables Automation sont conservées afin qu’elles demeurent disponibles même si le Runbook ou la configuration DSC échoue.  Cela permet aussi à une valeur d’être définie par un Runbook ou une configuration DSC, puis utilisée par un autre Runbook ou configuration DSC, ou bien par le même Runbook ou configuration DSC à sa prochaine exécution.     

Quand une variable est créée, vous pouvez spécifier qu'elle doit être stockée de manière chiffrée.  Quand une variable est chiffrée, elle est stockée de manière sécurisée dans Azure Automation. Sa valeur ne peut pas être récupérée à partir de l’applet de commande [Get-AzureRmAutomationVariable](/powershell/module/azurerm.automation/get-azurermautomationvariable), fournie dans le cadre du module Azure PowerShell.  Une valeur chiffrée ne peut être récupérée que d’une seule façon, à partir de l’activité **Get-AutomationVariable** d’un Runbook ou d’une configuration DSC.

> [!NOTE]
> Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation à l'aide d'une clé unique, générée pour chaque compte Automation. Cette clé est chiffrée par un certificat principal et stockée dans Azure Automation. Avant de stocker une ressource sécurisée, la clé du compte Automation est déchiffrée à l'aide du certificat principal, puis utilisée pour chiffrer la ressource.

## <a name="variable-types"></a>Types de variables

Lorsque vous créez une variable avec le portail Azure, vous devez spécifier un type de données dans la liste déroulante afin que le portail puisse afficher le contrôle approprié pour l’entrée de la valeur de la variable. La variable n'est pas limitée à ce type de données, mais vous devez définir la variable à l'aide de Windows PowerShell si vous souhaitez spécifier une valeur d'un type différent. Si vous spécifiez **Non défini**, la variable est définie avec la valeur **$null**, et vous devez définir la valeur avec la cmdlet [Set-AzureAutomationVariable](/powershell/module/azurerm.automation/set-azurermautomationvariable) ou l’activité **Set-AutomationVariable**.  Vous ne pouvez pas créer ou modifier la valeur d'un type de variable complexe dans le portail, mais vous pouvez fournir une valeur de tout type à l'aide de Windows PowerShell. Les types complexes sont retournés en tant que [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx).

Vous pouvez stocker plusieurs valeurs dans une seule variable en créant un tableau ou une table de hachage, et en l'enregistrant sur la variable.

Voici la liste des types de variable disponibles dans Automation :

* String
* Entier 
* DateTime
* Booléen
* Null

>[!NOTE]
>Les ressources de variables sont limitées à 1 024 caractères. 

## <a name="cmdlets-and-workflow-activities"></a>Applets de commande et activités de workflow

Les applets de commande du tableau suivant permettent de créer et de gérer les variables Automation avec Windows PowerShell. Elles sont fournies dans le cadre du [module Azure PowerShell](/powershell/azure/overview), utilisable dans les Runbooks Automation et la configuration DSC.

|Applets de commande|Description|
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/azurerm.automation/get-azurermautomationvariable)|Récupère la valeur d'une variable existante.|
|[New-AzureRmAutomationVariable](/powershell/module/azurerm.automation/new-azurermautomationvariable)|Crée une variable et définit sa valeur.|
|[Remove-AzureRmAutomationVariable](/powershell/module/azurerm.automation/remove-azurermautomationvariable)|Supprime une variable existante.|
|[Set-AzureRmAutomationVariable](/powershell/module/azurerm.automation/set-azurermautomationvariable)|Définit la valeur d'une variable existante.|

Les activités de workflow du tableau suivant sont utilisées pour accéder aux variables Automation d'un Runbook. Elles sont uniquement disponibles pour être utilisées dans un Runbook ou une configuration DSC et ne sont pas fournies dans le cadre du module Azure PowerShell.

|Activités de workflow|Description|
|:---|:---|
|Get-AutomationVariable|Récupère la valeur d'une variable existante.|
|Set-AutomationVariable|Définit la valeur d'une variable existante.|

> [!NOTE] 
> Évitez d’utiliser des variables dans le paramètre –Name de **Get-AutomationVariable** dans un Runbook ou une configuration DSC, car cela peut compliquer la découverte de dépendances entre les Runbooks ou la configuration DSC et les variables Automation au moment de la conception.

## <a name="creating-an-automation-variable"></a>Création d’une variable Automation

### <a name="to-create-a-variable-with-the-azure-portal"></a>Pour créer une variable avec le portail Azure

1. À partir de votre compte Automation, cliquez sur le panneau **Ressources** afin d’ouvrir le panneau **Ressources**.
1. Cliquez sur la mosaïque **Variables** afin d’ouvrir le panneau **Variables**.
1. Sélectionnez **Ajouter une variable** en haut du panneau.
1. Remplissez le formulaire, puis cliquez sur **Créer** pour enregistrer la nouvelle variable.


### <a name="to-create-a-variable-with-windows-powershell"></a>Pour créer une variable avec Windows PowerShell

La cmdlet [New-AzureRmAutomationVariable](/powershell/module/azurerm.automation/new-azurermautomationvariable) crée une variable et définit sa valeur initiale. Vous pouvez récupérer la valeur en utilisant [Get-AzureRmAutomationVariable](/powershell/module/azurerm.automation/get-azurermautomationvariable). Si la valeur est un type simple, ce même type est retourné. S'il s'agit d'un type complexe, un **PSCustomObject** est retourné.

Les exemples de commandes suivants montrent comment créer une variable de type chaîne, puis retourner sa valeur.

    New-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" 
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
    –Encrypted $false –Value 'My String'
    $string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

Les exemples de commandes suivants montrent comment créer une variable de type complexe, puis retourner ses propriétés. Dans ce cas, une machine virtuelle à partir de **Get-AzureRmVm** est utilisée.

    $vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
    New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
    
    $vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
    $vmName = $vmValue.Name
    $vmIpAddress = $vmValue.IpAddress


## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Utilisation d’une variable dans un Runbook ou une configuration DSC

Utilisez l’activité **Set-AutomationVariable** qui permet de définir la valeur d’une variable Automation dans un Runbook ou dans une configuration DSC, et l’activité **Get-AutomationVariable** pour la récupérer.  Vous ne devez pas utiliser les applets de commande **Set-AzureAutomationVariable** ou **Get-AzureAutomationVariable** dans un Runbook ou dans une configuration DSC, car elles sont moins efficaces que les activités de flux de travail.  Vous ne pouvez pas non plus récupérer la valeur de variables sécurisées avec **Get-AzureAutomationVariable**.  La seule façon de créer une variable à partir d’un Runbook ou d’une configuration DSC consiste à utiliser la cmdlet [New-AzureAutomationVariable](/powershell/module/azure/new-azureautomationvariable?view=azuresmps-3.7.0).


### <a name="textual-runbook-samples"></a>Exemple de Runbooks textuels

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Définition et récupération d'une valeur simple à partir d'une variable

Les exemples de commandes suivants montrent comment définir et récupérer une variable dans un Runbook textuel. Dans cet exemple, il est supposé que les variables de type entier nommées *NumberOfIterations* et *NumberOfRunnings* et une variable de type chaîne nommée *SampleMessage* ont déjà été créées.

    $NumberOfIterations = Get-AutomationVariable -Name 'NumberOfIterations'
    $NumberOfRunnings = Get-AutomationVariable -Name 'NumberOfRunnings'
    $SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
    
    Write-Output "Runbook has been run $NumberOfRunnings times."
    
    for ($i = 1; $i -le $NumberOfIterations; $i++) {
       Write-Output "$i`: $SampleMessage"
    }
    Set-AutomationVariable –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>Définition et récupération d'un objet complexe dans une variable

L'exemple de code suivant montre comment mettre à jour une variable avec une valeur complexe dans un Runbook textuel. Dans cet exemple, une machine virtuelle Azure est récupérée avec **Get-AzureVM** et enregistrée dans une variable Automation existante.  Comme expliqué dans [Types de variables](#variable-types), il est stocké comme PSCustomObject.

    $vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
    Set-AutomationVariable -Name "MyComplexVariable" -Value $vm


Dans le code suivant, la valeur est extraite de la variable et utilisée pour démarrer la machine virtuelle.

    $vmObject = Get-AutomationVariable -Name "MyComplexVariable"
    if ($vmObject.PowerState -eq 'Stopped') {
       Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
    }


#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>Définition et récupération d'une collection dans une variable

L'exemple de code suivant montre comment utiliser une variable avec une collection de valeurs complexes dans un Runbook textuel. Dans cet exemple, plusieurs machines virtuelles Azure sont récupérées avec **Get-AzureVM** et enregistrées dans une variable Automation existante.  Comme expliqué dans [Types de variables](#variable-types), elles sont stockées comme collection de PSCustomObject.

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

#### <a name="setting-and-retrieving-a-secure-string"></a>Définition et récupération d’une chaîne sécurisée

Si vous avez besoin de transmettre une chaîne sécurisée ou des informations d’identification, vous devez d’abord créer cette ressource en tant qu’information d’identification ou en tant que variable sécurisée. 

    $securecredential = get-credential

    New-AzureRmAutomationCredential -ResourceGroupName contoso `
    -AutomationAccountName contosoaccount -Name ContosoCredentialAsset -Value $securecredential

Vous pouvez ensuite transmettre le nom de cette ressource au Runbook en tant que paramètre et utiliser les activités intégrées à récupérer et à utiliser dans votre script, comme illustré dans l’exemple de code suivant :  

    ExampleScript
    Param

      (
         $ContosoCredentialAssetName
      )

    $ContosoCred = Get-AutomationPSCredential -Name $ContosoCredentialAssetName

L’exemple suivant montre comment appeler votre Runbook :  

    $RunbookParams = @{"ContosoCredentialAssetName"="ContosoCredentialAsset"}

    Start-AzureRMAutomationRunbook -ResourceGroupName contoso `
    -AutomationAccountName contosoaccount -Name ExampleScript -Parameters $RunbookParams

### <a name="graphical-runbook-samples"></a>Exemples de Runbook graphiques

Dans un Runbook graphique, ajoutez l’activité **Get-AutomationVariable** ou **Set-AutomationVariable** en cliquant avec le bouton droit sur la variable dans le volet Bibliothèque de l’éditeur graphique et en sélectionnant l’activité souhaitée.

![Ajouter une variable à la zone de dessin](media/automation-variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Définition de valeurs dans une variable
L'image suivante montre des exemples d'activité pour mettre à jour une variable avec une valeur simple dans un Runbook graphique. Dans cet exemple, une seule machine virtuelle Azure est récupérée avec **Get-AzureRmVM**, et le nom d’ordinateur est enregistré dans une variable Automation existante avec le type String.  Peu importe si les [lien est un pipeline ou une séquence](automation-graphical-authoring-intro.md#links-and-workflow) , car nous attendons uniquement un objet unique dans la sortie.

![Définir une variable simple](media/automation-variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la connexion d’activités lors de la création graphique, consultez [Liens lors de la création graphique](automation-graphical-authoring-intro.md#links-and-workflow)
* Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md) 


