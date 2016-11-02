<properties 
    pageTitle="Démarrage et arrêt des machines virtuelles avec Azure Automation - Workflow PowerShell | Microsoft Azure"
    description="Version graphique du scénario Azure Automation incluant des runbooks pour démarrer et arrêter des machines virtuelles classiques."
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
    ms.date="07/06/2016"
    ms.author="bwren" />


# <a name="azure-automation-scenario---starting-and-stopping-virtual-machines"></a>Scénario Azure Automation : démarrage et arrêt de machines virtuelles

Ce scénario Azure Automation inclut des runbooks pour démarrer et arrêter des machines virtuelles classiques.  Vous pouvez utiliser ce scénario dans les cas suivants :  

- Utiliser les runbooks sans modification dans votre propre environnement. 
- Modifier les runbooks pour exécuter une fonctionnalité personnalisée.  
- Appeler les runbooks à partir d’un autre runbook dans le cadre d’une solution globale. 
- Utiliser les runbooks comme didacticiels pour apprendre les concepts de création de runbook. 

> [AZURE.SELECTOR]
- [Graphique](automation-solution-startstopvm-graphical.md)
- [Workflow PowerShell](automation-solution-startstopvm-psworkflow.md)

Il s’agit de la version de runbook du Workflow PowerShell de ce scénario. Elle est également disponible à l’aide des [runbooks graphiques](automation-solution-startstopvm-graphical.md).

## <a name="getting-the-scenario"></a>Obtention du scénario

Ce scénario se compose de deux runbooks Workflow PowerShell que vous pouvez télécharger à partir des liens suivants.  Consultez la [version graphique](automation-solution-startstopvm-graphical.md) de ce scénario pour des liens vers les runbooks graphiques.

| Runbook | Lien | Type | Description |
|:---|:---|:---|:---|
| Start-AzureVMs | [Démarrer les machines virtuelles Azure classiques](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | Workflow PowerShell | Démarre toutes les machines virtuelles classiques d’un abonnement Azure ou toutes les machines virtuelles portant un nom de service particulier. |
| Stop-AzureVMs | [Arrêter les machines virtuelles Azure classiques](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | Workflow PowerShell | Arrête toutes les machines virtuelles d’un compte Automation ou toutes les machines virtuelles portant un nom de service particulier.  |


## <a name="installing-and-configuring-the-scenario"></a>Installation et configuration du scénario

### <a name="1.-install-the-runbooks"></a>1. Installation des runbooks

Après avoir téléchargé les runbooks, vous pouvez les importer à l’aide de la procédure décrite dans [Importation d’un runbook](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook).

### <a name="2.-review-the-description-and-requirements"></a>2. Examen de la description et des conditions requises
Les runbooks incluent du texte d’aide commenté qui comprend une description et les ressources requises.  Vous pouvez également obtenir les mêmes informations à partir de cet article. 

### <a name="3.-configure-assets"></a>3. Configuration des ressources
Les runbooks requièrent les ressources suivantes que vous devez créer et remplir avec les valeurs appropriées.

| Type de ressource | Nom de la ressource | Description |
|:---|:---|:---|:---|
| Informations d'identification | AzureCredential | Contient des informations d’identification d’un compte ayant les autorisations pour démarrer et arrêter des machines virtuelles dans l’abonnement Azure.  Vous pouvez également spécifier une autre ressource d’informations d’identification dans le paramètre **Credential** de l’activité **Add-AzureAccount**. |
| Variable | AzureSubscriptionId | Contient l’ID d’abonnement de votre abonnement Azure. |

## <a name="using-the-scenario"></a>Utilisation du scénario

### <a name="parameters"></a>Paramètres

Les runbooks ont tous les paramètres suivants.  Vous devez fournir des valeurs pour tous les paramètres obligatoires et pouvez éventuellement fournir des valeurs pour les autres paramètres, selon vos besoins.

| Paramètre | Type | Obligatoire | Description |
|:---|:---|:---|:---|
| ServiceName | string | Non | Si une valeur est fournie, toutes les machines virtuelles portant ce nom de service sont démarrées ou arrêtées.  Si aucune valeur n’est fournie, toutes les machines virtuelles classiques dans l’abonnement Azure sont démarrées ou arrêtées. |
| AzureSubscriptionIdAssetName | string | Non | Contient le nom de la [ressource variable](#installing-and-configuring-the-scenario) qui contient l’ID de votre abonnement Azure.  Si vous ne spécifiez aucune valeur, la valeur *AzureSubscriptionId* est utilisée.  |
| AzureCredentialAssetName | string | Non | Contient le nom de la [ressource d’informations d’identification](#installing-and-configuring-the-scenario) qui contient les informations d’identification pour le runbook à utiliser.  Si vous ne spécifiez aucune valeur, la valeur *AzureCredential* est utilisée.  |

### <a name="starting-the-runbooks"></a>Démarrage des runbooks

Vous pouvez utiliser n’importe quelle méthode proposée dans [Démarrage d’un runbook dans Azure Automation](automation-starting-a-runbook.md) pour démarrer le runbook de votre choix dans ce scénario.

Les exemples de commandes suivants utilisent Windows PowerShell pour exécuter **StartAzureVMs** afin de démarrer toutes les machines virtuelles portant le nom de service *MyVMService*.

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### <a name="output"></a>Sortie

Les Runbooks [généreront un message](automation-runbook-output-and-messages.md) pour chaque machine virtuelle, indiquant si l'instruction de démarrage ou d'arrêt a été correctement envoyée.  Vous pouvez rechercher une chaîne spécifique dans la sortie afin de déterminer le résultat pour chaque runbook.  Les chaînes de sortie possibles sont répertoriées dans le tableau suivant.

| Runbook | Condition | Message |
|:---|:---|:---|
| Start-AzureVMs | Machine virtuelle déjà en cours d’exécution  | MyVM déjà en cours d’exécution |
| Start-AzureVMs | Demande de démarrage de la machine virtuelle envoyée avec succès | MyVM démarrée |
| Start-AzureVMs | Échec de la demande de démarrage de la machine virtuelle  | MyVM n’a pas pu démarrer |
| Stop-AzureVMs | Machine virtuelle déjà arrêtée  | MyVM déjà arrêtée |
| Stop-AzureVMs | Demande d’arrêt de la machine virtuelle envoyée avec succès | MyVM arrêtée |
| Stop-AzureVMs | Échec de la demande d’arrêt de la machine virtuelle  | Échec de l’arrêt de MyVM |

Par exemple, l’extrait de code suivant d’un runbook tente de démarrer toutes les machines virtuelles portant le nom de service *MyServiceName*.  Si l’une des demandes de démarrage échoue, des mesures peuvent être prises. 

    $results = Start-AzureVMs -ServiceName "MyServiceName"
    foreach ($result in $results) {
        if ($result -like "* has been started" ) {
            # Action to take in case of success.
        }
        else {
            # Action to take in case of error.
        }
    }


## <a name="detailed-breakdown"></a>Analyse détaillée

Voici une analyse détaillée des runbooks de ce scénario.  Vous pouvez utiliser ces informations pour personnaliser les Runbooks ou simplement pour obtenir des informations à partir de ceux-ci afin de créer vos propres scénarios d’automatisation.

### <a name="parameters"></a>Paramètres

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

Le flux de travail démarre en obtenant les valeurs des [paramètres d’entrée](#using-the-scenario).  Si les noms de ressources ne sont pas fournis, les noms par défaut sont utilisés.

### <a name="output"></a>Sortie

    # Returns strings with status messages
    [OutputType([String])]

Cette ligne déclare que la sortie du runbook est une chaîne.  Cette pratique n’est pas obligatoire mais elle est conseillée dans les situations où le runbook est utilisé comme un [runbook enfant](automation-child-runbooks.md) afin qu’un runbook parent connaisse le type de sortie attendu.

### <a name="authentication"></a>Authentification

    # Connect to Azure and select the subscription to work against
    $Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
    $null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
    $SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

Les lignes suivantes définissent les [informations d’identification](automation-configuring.md#configuring-authentication-to-azure-resources) et l’abonnement Azure qui seront utilisés pour le reste du runbook.
Tout d’abord, nous utilisons **Get-AutomationPSCredential** pour obtenir la ressource qui détient les informations d’identification avec accès pour démarrer et arrêter des machines virtuelles dans l’abonnement Azure. **Add-AzureAccount** utilise ensuite cette ressource pour définir les informations d’identification.  La sortie est affectée à une variable factice de sorte qu’elle n’est pas incluse dans la sortie de runbook.  

La ressource variable avec l’ID d’abonnement est ensuite récupérée avec **Get-AutomationVariable** et l’abonnement est défini avec **Select-AzureSubscription**.

### <a name="get-vms"></a>Obtention de machines virtuelles

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

**Get-AzureVM** est utilisée pour récupérer les machines virtuelles avec lesquelles le runbook va fonctionner.  Si une valeur est fournie dans la variable d’entrée **ServiceName** , alors seules les machines virtuelles portant ce nom de service sont récupérées.  Si **ServiceName** est vide, toutes les machines virtuelles sont récupérées.

### <a name="start/stop-virtual-machines-and-send-output"></a>Démarrage/arrêt des machines virtuelles et envoi de sorties

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

Les lignes suivantes passent en revue chaque machine virtuelle.  Premièrement, l’état d’alimentation **PowerState** de chaque machine virtuelle est vérifié pour voir si elle est déjà en cours d’exécution ou arrêtée, selon le runbook.  Si elle est déjà dans l’état cible, un message est envoyé à la sortie et le runbook s’arrête.  Si ce n’est pas le cas, les commandes **Start-AzureVM** ou **Stop-AzureVM** sont utilisées pour tenter de démarrer ou d’arrêter la machine virtuelle avec le résultat de la demande stocké dans une variable.  Ensuite, un message est envoyé à la sortie indiquant si la demande de démarrage ou d’arrêter a été envoyée avec succès.


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’utilisation de runbooks enfants, consultez la rubrique [Runbooks enfants dans Azure Automation](automation-child-runbooks.md) 
- Pour en savoir plus sur les messages de sortie pendant l’exécution d’un runbook et la journalisation pour le dépannage, consultez la rubrique [Sortie et messages de Runbook dans Azure Automation](automation-runbook-output-and-messages.md)



<!--HONumber=Oct16_HO2-->


