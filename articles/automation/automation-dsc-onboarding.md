<properties 
   pageTitle="Intégrer des machines physiques et virtuelles pour en confier la gestion à Azure Automation DSC | Microsoft Azure" 
   description="Comment configurer des machines pour les gérer avec Azure Automation DSC" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="10/07/2015"
   ms.author="coreyp"/>

# Gestion de machines avec Azure Automation DSC

## Pourquoi gérer des machines avec Azure Automation DSC ?

Tout comme le service [Desired State Configuration de PowerShell](https://technet.microsoft.com/library/dn249912.aspx), le Desired State Configuration d’Azure Automation est un service de gestion de configuration simple et puissant pour les nœuds DSC (machines physiques et virtuelles) dans n’importe quel centre de données sur le cloud ou sur site. Il permet de faire évoluer des milliers d’ordinateurs rapidement et facilement à partir d’un emplacement central et sécurisé. Vous pouvez facilement intégrer des machines, leur affecter des configurations déclaratives et afficher des rapports montrant la conformité de chaque machine à l’état souhaité que vous avez spécifié. La couche de gestion Azure Automation DSC est au DSC ce que la couche de gestion Azure Automation est aux scripts PowerShell. En d’autres termes, de la même façon qu’Azure Automation vous permet de gérer des scripts PowerShell, il vous aide également à gérer les configurations DSC. Pour en savoir plus sur les avantages de l’utilisation d’Azure Automation DSC, consultez la [Vue d’ensemble d’Azure Automation DSC](automation-dsc-overview/).

Azure Automation DSC peut servir à gérer une grande diversité de machines :

*    Machines virtuelles Azure (classiques)
*    Machines virtuelles Azure
*    Machines physiques / virtuelles Windows sur site ou dans un cloud autre qu’Azure
*    Machines physiques / virtuelles Linux sur site, dans Azure, ou dans un cloud autre qu’Azure

Les sections suivantes décrivent la manière dont vous pouvez intégrer chaque type de machine à Azure Automation DSC.

## Machines virtuelles Azure (classiques)

Avec Azure Automation DSC, vous pouvez facilement intégrer des machines virtuelles Azure (classiques) pour une gestion de configuration via le portail Azure ou via PowerShell. En arrière-plan, et sans qu’aucun administrateur n’ait à contrôler la machine virtuelle à distance, l’extension Azure VM Desired State Configuration enregistre la machine virtuelle avec Azure Automation DSC. Étant donné que cette extension s’exécute de façon asynchrone, la section [Résolution des problèmes liés à l’intégration de machines virtuelles Azure](#Troubleshooting-Azure-virtual-machine-onboarding) ci-dessous décrit la procédure à suivre pour contrôler sa progression ou résoudre les problèmes.


### Machines virtuelles Azure

Dans la [version préliminaire du portail Azure](http://portal.azure.com/), cliquez sur **Browse** -> (Parcourir), puis sur **Virtual machines (classic)** (Machines virtuelles (classiques)). Sélectionnez la machine virtuelle Windows que vous souhaitez intégrer. Dans le panneau du tableau de bord de la machine virtuelle, cliquez sur **All setting** (Tous les paramètres) -> **Extensions** -> **Add** (Ajouter) -> **Azure Automation DSC** -> **Create** (Créer). Entrez les [valeurs du gestionnaire de configuration locale de PowerShell DSC](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) requises, la clé et l’URL d’enregistrement de votre compte Automation, et, éventuellement, une configuration de nœud à attribuer à la machine virtuelle.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)


Pour trouver l’URL et la clé d’enregistrement pour le compte Automation, consultez la section [Enregistrement sécurisé](#Secure-registration) ci-dessous.

### PowerShell

    # log in to both Azure Service Management and Azure Resource Manager
    Add-AzureAccount
    Login-AzureRmAccount
    
    # fill in correct values for your VM / Automation Account here
    $VMName = ""
    $ServiceName = ""
    $AutomationAccountName = ""
    $AutomationAccountResourceGroup = ""

    # fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
    $NodeConfigName = ""

    # get Azure Automation DSC registration info
    $Account = Get-AzureRMAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
    $RegistrationInfo = $Account | Get-AzureRMAutomationRegistrationInfo

    # use the DSC extension to onboard the VM for management with Azure Automation DSC
    $VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName
    
    $PublicConfiguration = ConvertTo-Json -Depth 8 @{
      SasToken = ""
      ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
      ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

    # update these DSC agent Local Configuration Manager defaults if they do not match your use case.
    # See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
       RegistrationKey = @{
         UserName = 'notused'
         Password = 'PrivateSettingsRef:RegistrationKey'
    }
      RegistrationUrl = $RegistrationInfo.Endpoint
      NodeConfigurationName = $NodeConfigName
      ConfigurationMode = "ApplyAndMonitor"
      ConfigurationModeFrequencyMins = 15
      RefreshFrequencyMins = 30
      RebootNodeIfNeeded = $False
      ActionAfterReboot = "ContinueConfiguration"
      AllowModuleOverwrite = $False
      }
    }

    $PrivateConfiguration = ConvertTo-Json -Depth 8 @{
      Items = @{
         RegistrationKey = $RegistrationInfo.PrimaryKey
      }
    }
    
    $VM = Set-AzureVMExtension
     -VM $vm
     -Publisher Microsoft.Powershell
     -ExtensionName DSC
     -Version 2.6
     -PublicConfiguration $PublicConfiguration
     -PrivateConfiguration $PrivateConfiguration
     -ForceUpdate

    $VM | Update-AzureVM

## Machines virtuelles Azure

Azure Automation DSC vous permet d’intégrer facilement des machines virtuelles Azure pour une gestion de configuration via le portail Azure, les modèles Azure Resource Manager ou PowerShell. En arrière-plan, et sans qu’aucun administrateur n’ait à contrôler la machine virtuelle à distance, l’extension Azure VM Desired State Configuration enregistre la machine virtuelle avec Azure Automation DSC. Étant donné que cette extension s’exécute de façon asynchrone, la section [Résolution des problèmes liés à l’intégration de machines virtuelles Azure](#Troubleshooting-Azure-virtual-machine-onboarding) ci-dessous décrit la procédure à suivre pour contrôler sa progression ou résoudre les problèmes.


### Portail Azure

Dans la [version préliminaire du portail Azure](http://portal.azure.com/), accédez au compte Azure Automation où vous souhaitez intégrer des machines virtuelles. Dans le tableau de bord du compte Automation, cliquez sur **DSC Nodes** (Nœuds DSC) -> **Add Azure VM** (Ajouter une machine virtuelle Azure).

Sous **Select virtual machines to onboard** (Sélectionner les machines virtuelles à intégrer), sélectionnez une ou plusieurs machines virtuelles Azure que vous souhaitez intégrer.

![](./media/automation-dsc-onboarding/DSC_Onboarding_2.png)


Sous **Configure registration data** (Configurer les données de l’enregistrement), entrez les [valeurs du gestionnaire de configuration locale de PowerShell DSC](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) requises et, éventuellement, une configuration de nœud à attribuer à la machine virtuelle.

![](./media/automation-dsc-onboarding/DSC_Onboarding_3.png)

 
### Modèles Microsoft Azure Resource Manager

Les machines virtuelles Azure peuvent être déployées et intégrées sur Azure Automation DSC via des modèles Azure Resource Manager. Voir [Configurer une machine virtuelle via l’extension DSC et Azure Automation DSC](http://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) pour un exemple de modèle intégrant une machine virtuelle existante dans Azure Automation DSC. Pour trouver la clé et l’URL d’enregistrement utilisées comme entrées dans ce modèle, consultez la section [Enregistrement sécurisé](#Secure-registration) ci-dessous.

### PowerShell

Vous pouvez utiliser l’applet de commande [Register-AzureAutomationDscNode](https://msdn.microsoft.com/library/mt244097.aspx?f=255&MSPPError=-2147217396) pour intégrer des machines virtuelles dans la version préliminaire du portail Azure via PowerShell.

#### Machines physiques / virtuelles Windows sur site ou dans un cloud autre qu’Azure

Les ordinateurs Windows en local et les ordinateurs Windows dans des clouds autres qu’Azure (comme Amazon Web Services) peuvent également être intégrés sur Azure Automation DSC à condition qu’ils disposent d’accès sortant à Internet. Leur intégration s’effectue très simplement, en quelques étapes :

1. Assurez-vous que la dernière version de [WMF 5](http://www.microsoft.com/fr-FR/download/details.aspx?id=48729) est installée sur les ordinateurs que vous souhaitez intégrer sur Azure Automation DSC.

2. Ouvrez la console PowerShell ou PowerShell ISE en tant qu’administrateur sur un ordinateur de votre environnement local. Cet ordinateur doit également disposer de la dernière version de WMF 5.

3. Connectez-vous à Azure Resource Manager à l’aide du module Azure PowerShell : `Login-AzureRMAccount`

4. Dans le compte Automation auquel vous souhaitez intégrer des nœuds, téléchargez les métaconfigurations PowerShell DSC pour les ordinateurs à intégrer :

	`Get-AzureRMAutomationDscOnboardingMetaconfig -ResourceGroupName MyResourceGroup -AutomationAccountName      		MyAutomationAccount -ComputerName MyServer1, MyServer2 -OutputFolder C:\Users\joe\Desktop`

5. Vous pouvez, si vous le souhaitez, afficher et mettre à jour les métaconfigurations dans le dossier de sortie pour les adapter aux [champs et valeurs du gestionnaire de configuration locale PowerShell DSC](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) souhaités, si les valeurs par défaut ne correspondent pas à votre cas de figure.

6. Appliquez à distance la métaconfiguration PowerShell DSC pour les machines que vous souhaitez intégrer :

	`Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2`

7. Si vous ne pouvez pas appliquer à distance les métaconfigurations PowerShell DSC, copiez le dossier de sortie de l’étape 4 sur chaque machine à intégrer. Appelez ensuite DscLocalConfigurationManager de jeu localement sur chaque machine à intégrer.

8. À l’aide du portail Azure ou des applets de commande, vérifiez que les machines à intégrer s’affichent bien en tant que nœuds DSC enregistrés dans votre compte Azure Automation.

#### Machines physiques / virtuelles Linux sur site, dans Azure, ou dans un cloud autre qu’Azure

Les ordinateurs Linux en local et les ordinateurs Linux dans des clouds autres qu’Azure peuvent également être intégrés sur Azure Automation DSC à condition qu’ils disposent d’accès sortant à Internet. Leur intégration s’effectue très simplement, en quelques étapes :

1. Assurez-vous que la dernière version de l’[agent DSC Linux](http://www.microsoft.com/fr-FR/download/details.aspx?id=49150) est installée sur les ordinateurs que vous souhaitez intégrer sur Azure Automation DSC.

2. Si les [valeurs par défaut du gestionnaire de configuration locale PowerShell DSC](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) correspondent à votre cas de figure :

*    Sur chaque ordinateur Linux que vous souhaitez intégrer sur Azure Automation DSC, utilisez Register.py pour effectuer l’intégration en utilisant les valeurs par défaut du gestionnaire de configuration locale PowerShell DSC :

	`/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

*    Pour trouver la clé et l’URL d’enregistrement pour votre compte Automation, consultez la section [Enregistrement sécurisé](#Secure-registration) ci-dessous.

	Si les valeurs par défaut du gestionnaire de configuration locale PowerShell DSC **ne** correspondent **pas** à votre cas de figure, suivez les étapes 3 à 9. Sinon, passez directement à l’étape 9.

3.  Ouvrez la console PowerShell ou PowerShell ISE en tant qu’administrateur sur un ordinateur Windows de votre environnement local. Cet ordinateur doit également disposer de la dernière version de [WMF 5](http://www.microsoft.com/fr-FR/download/details.aspx?id=48729).

4.  Connectez-vous à Azure Resource Manager à l’aide du module Azure PowerShell :

	`Login-AzureRMAccount`

5.  Dans le compte Automation auquel vous souhaitez intégrer des nœuds, téléchargez les métaconfigurations PowerShell DSC pour les ordinateurs à intégrer :
	
	`Get-AzureRMAutomationDscOnboardingMetaconfig -ResourceGroupName MyResourceGroup -AutomationAccountName MyAutomationAccount -ComputerName MyServer1, MyServer2 -OutputFolder C:\Users\joe\Desktop_`

6.  Vous pouvez, si vous le souhaitez, afficher et mettre à jour les métaconfigurations dans le dossier de sortie pour les adapter aux [champs et valeurs du gestionnaire de configuration locale PowerShell DSC](http://https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) souhaités, si les valeurs par défaut ne correspondent pas à votre cas de figure.

7.  Appliquez à distance la métaconfiguration PowerShell DSC pour les machines que vous souhaitez intégrer :
    	
    	$SecurePass = ConvertTo-SecureString -string "<root password>" -AsPlainText -Force
        $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurPass
        $Opt = New-CimSessionOption -UseSs1:$true -SkipCACheck:$true -SkipCNCheck:$true -SkipRevocationCheck:$true

        # need a CimSession for each Linux machine to onboard
        
        $Session = New-CimSession -Credential:$Cred -ComputerName:<your Linux machine> -Port:5986 -Authentication:basic -SessionOption:$Opt
    	
    	Set-DscLocalConfigurationManager -CimSession $Session –Path C:\Users\joe\Desktop\DscMetaConfigs

8.  Si vous ne pouvez pas appliquer à distance les métaconfigurations PowerShell DSC, pour chaque ordinateur Linux à intégrer, copiez la métaconfiguration correspondant à cet ordinateur à partir du dossier de l’étape 5 sur l’ordinateur Linux. Appelez ensuite `SetDscLocalConfigurationManager.py` localement sur chaque ordinateur Linux que vous souhaitez intégrer à Azure Automation DSC :

	`/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py –configurationmof <path to metaconfiguration file>`

9.  À l’aide du portail Azure ou des applets de commande, vérifiez que les machines à intégrer s’affichent bien en tant que nœuds DSC enregistrés dans votre compte Azure Automation.

## Enregistrement sécurisé

Les ordinateurs peuvent en toute sécurité s’intégrer à un compte Azure Automation via le protocole d’enregistrement WMF 5 DSC, qui permet à un nœud DSC de s’authentifier sur un serveur Pull ou Reporting PowerShell DSC V2 (y compris Azure Automation DSC). Le nœud s’enregistre sur le serveur à une **URL d’enregistrement** et s’authentifie à l’aide d’une **clé d’enregistrement**. Pendant l’enregistrement, le nœud DSC et le serveur Pull / Reporting DSC négocient un certificat unique pour ce nœud qui devra être utilisé pour l’authentification au serveur après l’enregistrement. Ce processus empêche les nœuds intégrés d’emprunter l’identité d’un autre nœud, par exemple si un nœud est compromis et agit à des fins malveillantes. Après l’enregistrement, la clé d’enregistrement n’est plus utilisée pour l’authentification et est supprimée du nœud.

Pour obtenir les informations requises pour le protocole d’enregistrement DSC, accédez au panneau **Gérer les clés** de la version préliminaire du portail Azure. Ouvrez ce panneau en cliquant sur l’icône de clé dans le panneau **Bases** du compte Automation.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

*    L’URL d’enregistrement correspond à la valeur du champ URL dans le panneau Gérer les clés.
*    La clé d’enregistrement correspond à la clé d’accès primaire ou à la clé d’accès secondaire dans le panneau Gérer les clés. Vous pouvez utiliser l’une de ces deux clés.

Pour renforcer la sécurité, les clés d’accès primaire et secondaire d’un compte Automation peuvent être régénérées à tout moment (à partir du panneau **Gérer les clés**) pour éviter que des nœuds s’enregistrent ultérieurement à l’aide de clés déjà utilisées.

## Résolution des problèmes liés à l’intégration de machines virtuelles Azure

Azure Automation DSC vous permet d’intégrer facilement des machines virtuelles Windows Azure à des fins de gestion de la configuration. En arrière-plan, l’extension Azure VM Desired State Configuration est utilisée pour enregistrer la machine virtuelle auprès d’Azure Automation DSC. Étant donné que cette extension s’exécute de façon asynchrone, il peut être très important d’en suivre la progression et de résoudre ses éventuels problèmes d’exécution.

>[AZURE.NOTE]Quelle que soit la méthode choisie pour intégrer une machine virtuelle Windows Azure sur Azure Automation DSC, l’enregistrement du nœud dans Azure Automation peut prendre jusqu’à une heure si l’extension Azure VM Desired State Configuration est utilisée. Cela est dû à l'installation de Windows Management Framework 5.0 sur la machine virtuelle par l'extension Azure VM DSC, nécessaire à l’intégration de la machine virtuelle dans Azure Automation DSC.

Pour résoudre les problèmes ou afficher l’extension Azure VM Desired State Configuration, rendez-vous dans la version préliminaire du portail Azure, accédez à la machine virtuelle en cours d’intégration, puis cliquez sur **All settings** (Tous les paramètres) -> **Extensions** -> **DSC**. Pour plus de détails, vous pouvez cliquer sur **Afficher l’état détaillé**.

[![](./media/automation-dsc-onboarding/DSC_Onboarding_5.png)](https://technet.microsoft.com/library/dn249912.aspx)

## Expiration du certificat et nouvel enregistrement

Une fois inscrit, chaque nœud négocie automatiquement un certificat unique pour l'authentification qui expire après un an. À ce stade, le protocole d’enregistrement PowerShell DSC ne peut pas renouveler automatiquement les certificats lorsqu’ils sont sur le point d’expirer. Vous devez donc renouveler l’enregistrement des nœuds après un an. Avant la réinscription, assurez-vous que chaque nœud exécute Windows Management Framework 5.0 RTM. Si le certificat d'authentification d'un nœud expire et si le nœud n'est pas réinscrit, le nœud ne pourra pas communiquer avec Azure Automation et sera marqué « Ne répond pas ». La réinscription s'effectue de la même façon que l'inscription initiale du nœud. La réinscription effectuée dans un délai de 90 jours ou moins à partir de l'heure d'expiration du certificat, ou à tout moment après le délai d'expiration du certificat, entraîne la génération et l'utilisation d'un nouveau certificat.

## Articles connexes
* [Vue d’ensemble d’Azure Automation DSC](automation-dsc-overview.md)
* [Applets de commande Azure Automation DSC](https://msdn.microsoft.com/library/mt244122.aspx)
* [Tarification d’Azure Automation DSC](http://azure.microsoft.com/pricing/details/automation/)

<!---HONumber=Oct15_HO3-->