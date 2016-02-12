<properties 
	pageTitle="Démarrage et arrêt des machines virtuelles avec Azure Automation - Workflow PowerShell | Microsoft Azure"
	description="Version graphique de la solution Azure Automation incluant des runbooks pour démarrer et arrêter des machines virtuelles classiques."
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
	ms.date="01/27/2016"
	ms.author="bwren" />

# Solution Azure Automation : démarrage et arrêt de machines virtuelles

Cette solution Azure Automation inclut des runbooks pour démarrer et arrêter des machines virtuelles classiques. Vous pouvez utiliser cette solution dans les cas suivants :

- Utiliser les runbooks sans modification dans votre propre environnement. 
- Modifier les runbooks pour exécuter une fonctionnalité personnalisée.  
- Appeler les runbooks à partir d’un autre runbook dans le cadre d’une solution globale. 
- Utiliser les runbooks comme didacticiels pour apprendre les concepts de création de runbook. 

> [AZURE.SELECTOR]
- [Graphical](automation-solution-startstopvm-graphical.md)
- [PowerShell Workflow](automation-solution-startstopvm-psworkflow.md)

Il s’agit de la version de runbook du Workflow PowerShell de cette solution. Elle est également disponible à l’aide des [runbooks graphiques](automation-solution-startstopvm-graphical.md).

## Obtention de la solution

Cette solution se compose de deux runbooks Workflow PowerShell que vous pouvez télécharger à partir des liens suivants. Consultez la [version graphique](automation-solution-startstopvm-graphical.md) de cette solution pour des liens vers les runbooks graphiques.

| Runbook | Lien | Type | Description |
|:---|:---|:---|:---|
| Start-AzureVMs | [Démarrer les machines virtuelles Azure classiques](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | Workflow PowerShell | Démarre toutes les machines virtuelles classiques d’un abonnement Azure ou toutes les machines virtuelles portant un nom de service particulier. |
| Stop-AzureVMs | [Arrêter les machines virtuelles Azure classiques](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | Workflow PowerShell | Arrête toutes les machines virtuelles d’un compte Automation ou toutes les machines virtuelles portant un nom de service particulier. |


## Installation de la solution

### 1\. Installation des runbooks

Après avoir téléchargé les runbooks, vous pouvez les importer à l’aide de la procédure décrite dans [Importation d’un runbook](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook).

### 2\. Examen de la description et des conditions requises
Les runbooks incluent du texte d’aide commenté qui comprend une description et les ressources requises. Vous pouvez également obtenir les mêmes informations à partir de cet article.

### 3\. Configuration des ressources
Les runbooks requièrent les ressources suivantes que vous devez créer et remplir avec les valeurs appropriées.

| Type de ressource | Nom de la ressource | Description |
|:---|:---|:---|:---|
| Informations d'identification | AzureCredential | Contient des informations d’identification d’un compte ayant les autorisations pour démarrer et arrêter des machines virtuelles dans l’abonnement Azure. Vous pouvez également spécifier une autre ressource d’informations d’identification dans le paramètre **Credential** de l’activité **Add-AzureAccount**. |
| Variable | AzureSubscriptionId | Contient l’ID d’abonnement de votre abonnement Azure. |

## Utilisation de la solution

### Paramètres

Les runbooks ont tous les paramètres suivants. Vous devez fournir des valeurs pour tous les paramètres obligatoires et pouvez éventuellement fournir des valeurs pour les autres paramètres, selon vos besoins.

| Paramètre | Type | Obligatoire | Description |
|:---|:---|:---|:---|
| ServiceName | string | Non | Si une valeur est fournie, toutes les machines virtuelles portant ce nom de service sont démarrées ou arrêtées. Si aucune valeur n’est fournie, toutes les machines virtuelles classiques dans l’abonnement Azure sont démarrées ou arrêtées. |
| AzureSubscriptionIdAssetName | string | Non | Contient le nom de la [ressource variable](#installing-the-solution) qui contient l’ID d’abonnement de votre abonnement Azure. Si vous ne spécifiez aucune valeur, la valeur *AzureSubscriptionId* est utilisée. |
| AzureCredentialAssetName | string | Non | Contient le nom de la [ressource d’informations d’identification](#installing-the-solution) qui contient les informations d’identification pour le runbook à utiliser. Si vous ne spécifiez aucune valeur, la valeur *AzureCredential* est utilisée. |

### Démarrage des runbooks

Vous pouvez utiliser n’importe quelle méthode proposée dans [Démarrage d’un runbook dans Azure Automation](automation-starting-a-runbook.md) pour démarrer le runbook de votre choix dans cette solution.

Les exemples de commandes suivants utilisent Windows PowerShell pour exécuter **StartAzureVMs** afin de démarrer toutes les machines virtuelles portant le nom de service *MyVMService*.

	$params = @{"ServiceName"="MyVMService"}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### Sortie

Les Runbooks [généreront un message](automation-runbook-output-and-messages.md) pour chaque machine virtuelle, indiquant si l'instruction de démarrage ou d'arrêt a été correctement envoyée. Vous pouvez rechercher une chaîne spécifique dans la sortie afin de déterminer le résultat pour chaque runbook. Les chaînes de sortie possibles sont répertoriées dans le tableau suivant.

| Runbook | Condition | Message |
|:---|:---|:---|
| Start-AzureVMs | Machine virtuelle déjà en cours d’exécution | MyVM déjà en cours d’exécution |
| Start-AzureVMs | Demande de démarrage de la machine virtuelle envoyée avec succès | MyVM démarrée |
| Start-AzureVMs | Échec de la demande de démarrage de la machine virtuelle | MyVM n’a pas pu démarrer |
| Stop-AzureVMs | Machine virtuelle déjà en cours d’exécution | MyVM déjà arrêtée |
| Stop-AzureVMs | Demande de démarrage de la machine virtuelle envoyée avec succès | MyVM démarrée |
| Stop-AzureVMs | Échec de la demande de démarrage de la machine virtuelle | MyVM n’a pas pu démarrer |

Par exemple, l’extrait de code suivant d’un runbook tente de démarrer toutes les machines virtuelles portant le nom de service *MyServiceName*. Si l’une des demandes de démarrage échoue, des mesures peuvent être prises.

	$results = Start-AzureVMs -ServiceName "MyServiceName"
	foreach ($result in $results) {
		if ($result -like "* has been started" ) {
			# Action to take in case of success.
		}
		else {
			# Action to take in case of error.
		}
	}


## Analyse détaillée

Voici une analyse détaillée des runbooks de cette solution. Vous pouvez utiliser ces informations pour personnaliser les runbooks ou simplement pour en tirer des connaissances.

### Paramètres

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

Le flux de travail démarre en obtenant les valeurs des [paramètres d’entrée](#using-the-solution). Si les noms de ressources ne sont pas fournis, les noms par défaut sont utilisés.

### Sortie

    # Returns strings with status messages
    [OutputType([String])]

Cette ligne déclare que la sortie du runbook est une chaîne. Cette pratique n’est pas obligatoire mais elle est conseillée dans les situations où le runbook est utilisé comme un [runbook enfant](automation-child-runbooks.md) afin qu’un runbook parent connaisse le type de sortie attendu.

### Authentification

	# Connect to Azure and select the subscription to work against
	$Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
	$null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
	$SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

Les lignes suivantes définissent les [informations d’identification](automation-configuring.md#configuring-authentication-to-azure-resources) et l’abonnement Azure qui seront utilisés pour le reste du runbook. Tout d’abord, nous utilisons **Get-AutomationPSCredential** pour obtenir la ressource qui détient les informations d’identification avec accès pour démarrer et arrêter des machines virtuelles dans l’abonnement Azure. **Add-AzureAccount** utilise ensuite cette ressource pour définir les informations d’identification. La sortie est affectée à une variable factice de sorte qu’elle n’est pas incluse dans la sortie de runbook.

La ressource variable avec l’ID d’abonnement est ensuite récupérée avec **Get-AutomationVariable** et l’abonnement est défini avec **Select-AzureSubscription**.

### Obtention de machines virtuelles

	# If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
	{ 
		$VMs = Get-AzureVM -ServiceName $ServiceName
	}
    else 
	{ 
		$VMs = Get-AzureVM
	}

La commande **Get-AzureVM** est utilisée pour récupérer les machines virtuelles avec lesquelles le runbook va fonctionner. Si une valeur est fournie dans la variable d’entrée **ServiceName**, alors seules les machines virtuelles portant ce nom de service sont récupérées. Si **ServiceName** est vide, toutes les machines virtuelles sont récupérées.

### Démarrage/arrêt des machines virtuelles et envoi de sorties

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
		if ($VM.PowerState -eq "Started")
		{
			# The VM is already started, so send notice
			Write-Output ($VM.InstanceName + " is already running")
		}
		else
		{
			# The VM needs to be started
        	$StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

	        if ($StartRtn.OperationStatus -ne 'Succeeded')
	        {
				# The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
	        }
			else
			{
				# The VM started, so send notice
				Write-Output ($VM.InstanceName + " has been started")
			}
		}
    }

Les lignes suivantes passent en revue chaque machine virtuelle. Premièrement, l’état d’alimentation **PowerState** de chaque machine virtuelle est vérifié pour voir si elle est déjà en cours d’exécution ou arrêtée, selon le runbook. Si elle est déjà dans l’état cible, un message est envoyé à la sortie et le runbook s’arrête. Si ce n’est pas le cas, les commandes **Start-AzureVM** ou **Stop-AzureVM** sont utilisées pour tenter de démarrer ou d’arrêter la machine virtuelle avec le résultat de la demande stocké dans une variable. Ensuite, un message est envoyé à la sortie indiquant si la demande de démarrage ou d’arrêter a été envoyée avec succès.


## Articles connexes

- [Runbooks enfants dans Azure Automation](automation-child-runbooks.md) 
- [Sortie et messages de Runbook dans Azure Automation](automation-runbook-output-and-messages.md)

<!---HONumber=AcomDC_0204_2016-->