<properties
	pageTitle="Répliquer des machines virtuelles VMware sur Azure à l’aide d’Azure Site Recovery avec Azure Automation DSC | Microsoft Azure"
	description="Explique comment utiliser Azure Automation DSC pour déployer automatiquement le Service Mobilité ASR et l’agent Azure pour les machines virtuelles/physiques sur Azure."
	services="site-recovery"
	documentationCenter=""
	authors="krnese"
	manager="lorenr"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="krnese"/>

# Répliquer des machines virtuelles VMware sur Azure à l’aide de Site Recovery avec Azure Automation DSC

OMS vous fournit une solution complète de sauvegarde et de récupération d’urgence que vous pouvez exploiter dans le cadre de votre plan de continuité d’activité.

Nous avons commencé cette transition vers Hyper-V à l’aide d’un réplica Hyper-V, puis nous l’avons étendu pour prendre en charge une installation hétérogène. Nous sommes en effet bien conscients que les clients ont plusieurs hyperviseurs et plateformes dans leurs clouds.

Aujourd’hui, si vous exécutez des charges de travail VMware et/ou des serveurs physiques, c’est le serveur d’administration exécutant l’ensemble des composants Site Recovery exécutés dans votre environnement qui gère la communication et la réplication des données avec Azure (dans la mesure où Azure est votre destination).

## Déployer le Service Mobilité ASR à l’aide d’OMS Automation DSC
Commençons par analyser rapidement les opérations réellement effectuées par ce serveur d’administration :

Le serveur d’administration exécute plusieurs rôle du serveur, comme le rôle **configuration**, qui coordonne la communication et gère les processus de réplication et de récupération des données.

En outre, le rôle **processus** agit comme une passerelle de réplication qui reçoit les données de réplication à partir des machines sources protégées, les optimise avec le caching, la compression et le chiffrement, avant de les envoyer vers un compte de stockage Azure. L’une des fonctions du rôle processus est également de gérer l’installation Push du Service Mobilité sur les machines protégées et d’exécuter la détection automatique des machines virtuelles VMware.

Dans le cas d’une restauration à partir d’Azure, c’est le rôle **cible maître** qui est responsable. Il gère la réplication des données dans le cadre de cette opération.

Pour les machines protégées, nous nous fions au **Service Mobilité**, qui est un composant déployé sur chaque ordinateur (machine virtuelle VMware ou serveur physique) que vous voulez répliquer sur Azure. Son rôle est de capturer les écritures de données sur la machine et de les transférer vers le serveur d’administration (rôle processus).

Lorsque la continuité de l’activité est en jeu, vous devez absolument comprendre vos charges de travail, votre infrastructure et les composants impliqués pour satisfaire les exigences RTO et RPO. Dans ce contexte, le Service Mobilité est indispensable pour garantir que les charges de travail sont protégées comme attendu.

Alors, comment pouvons-nous nous assurer de manière optimisée que l’installation est protégée et fiable, à l’aide de certains composants OMS ?

Dans cet article, nous vous fournissons un exemple montrant comment tirer parti d’OMS Automation DSC et d’OMS Site Recovery pour garantir que le Service Mobilité et l’agent de machine virtuelle Azure sont déployés sur les ordinateurs Windows que vous voulez protéger, et constamment exécutés lorsqu’Azure est la cible de réplication.

## Composants requis

- Un référentiel pour stocker l’installation requise
- Un référentiel pour stocker la phrase secrète requise pour s’enregistrer avec le serveur d’administration
- Windows Management Framework 5.0 installé sur les ordinateurs que vous souhaitez protéger (obligatoire pour OMS Automation DSC)

Si vous souhaitez utiliser DSC avec vos ordinateurs Windows à l’aide de WMF 4.0, consultez la section « Utilisation de DSC dans les environnements déconnectés »

(Une phrase secrète unique est générée pour chaque serveur d’administration. Si vous envisagez de déployer plusieurs serveurs d’administration, vous devez vous assurer que la phrase secrète correcte est stockée dans un fichier passphrase.txt)

Le Service Mobilité peut être installé via la ligne de commande et accepte plusieurs arguments.

C’est la raison pour laquelle nous devons extraire les fichiers binaires (à partir de notre installation) et les stocker dans un emplacement où nous pourrons les récupérer à l’aide d’une configuration DSC.

## Étape 1 : Extraction des fichiers binaires

1. Pour extraire les fichiers binaires nécessaires à cette installation, accédez au répertoire suivant sur votre serveur d’administration : **\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository**

    Dans ce dossier, vous devriez voir un fichier MSI nommé :

    **Microsoft-ASR\_UA\_<version>_Windows\_GA_<date>\_Release.exe**

    Utilisez la commande suivante pour extraire le programme d’installation :

    .\\Microsoft-ASR\_UA\_9.1.0.0_Windows\_GA_02May2016\_release.exe /q /x:C:\\Users\\Administrator\\Desktop\\Mobility\_Service\\Extract

2. Sélectionnez tous les fichiers et envoyez-les vers un dossier compressé (zippé).

Et voilà ! Vous disposez maintenant des fichiers binaires dont vous avez besoin pour automatiser l’installation du Service Mobilité à l’aide d’OMS Automation DSC.

### Phrase secrète
Ensuite, vous devez déterminer où vous souhaitez placer ce dossier compressé. Vous pouvez utiliser un compte de stockage Azure, comme indiqué par la suite, pour stocker la phrase secrète dont vous avez besoin pour l’installation, afin que l’agent soit enregistré avec le serveur d’administration dans le cadre du processus.

La phrase secrète obtenue lors du déploiement du serveur d’administration peut être enregistrée dans un fichier txt en tant que passphrase.txt.

Placez le dossier compressé et la phrase secrète dans un conteneur dédié au sein du compte de stockage Azure.

![emplacement du dossier](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Si vous préférez conserver ces fichiers sur un partage de votre réseau, vous pouvez parfaitement le faire. Vous devez simplement vous assurer que la ressource qui sera utilisée ultérieurement peut accéder à l’installation et à la phrase secrète et les obtenir.

## Étape 2 - Création de la configuration DSC
L’installation dépend de WMF 5.0, ce qui signifie que WMF 5.0 doit être présent pour que l’ordinateur applique la configuration par le biais d’OMS Automation DSC.

L’exemple de configuration DSC suivant est utilisé dans l’environnement :

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Microsoft Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
La configuration effectue les opérations suivantes :

- Les variables indiquent à la configuration où obtenir les fichiers binaires pour le Service Mobilité et l’agent de machine virtuelle Azure, ainsi que la phrase secrète. Elles indiquent également où stocker la sortie.
- Importez la DscResource xPSDesiredStateConfiguration pour que nous puissions utiliser « xRemoteFile » pour télécharger les fichiers à partir du référentiel.
- Créez un répertoire dans lequel stocker les fichiers binaires.
- La ressource Archive extrait les fichiers à partir du dossier compressé.
- Le package « Install » de la ressource installe le Service Mobilité à partir du programme d’installation UNIFIEDAGENT avec les arguments spécifiques (les variables construisant les arguments doivent être modifiées pour refléter votre environnement).
- Le package « AzureAgent » installe l’agent de machine virtuelle Azure (recommandé pour chaque machine virtuelle s’exécutant dans Azure) et rend également possible l’ajout d’extensions à la machine virtuelle suite à un basculement.
- Les ressources Service garantissent que les services Mobilité connexes et les services Azure s’exécutent toujours.

Enregistrez la configuration en tant **qu’ASRMobilityService**.

(N’oubliez pas de remplacer le CSIP dans votre configuration pour qu’il reflète le serveur d’administration en cours de manière à ce que l’agent soit connecté correctement, en utilisant également la phrase secrète appropriée).

## Étape 3 - Téléchargement vers OMS Automation DSC

Dans la mesure où la configuration DSC créée importe un module de ressources DSC nécessaire (xPSDesiredStateConfiguration), vous devez importer ce module dans OMS Automation avant de charger la configuration DSC.

Connectez-vous à votre compte Automation et accédez à Actifs > Modules, puis cliquez sur Parcourir la galerie.

Ici, vous pouvez rechercher le module et l’importer dans votre compte.

![importer le module](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Une fois cette opération effectuée, accédez à l’ordinateur où les modules Azure RM sont installés et procédez à l’importation de la configuration DSC nouvellement créée.

### Importer les applets de commande

Dans PowerShell, connectez-vous à votre abonnement Azure et modifiez les applets de commande pour refléter votre environnement et capturer les informations de votre compte Automation dans une variable :
```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Commencez par charger la configuration vers OMS Automation DSC à l’aide de l’applet de commande suivante :

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

Ensuite, vous devez compiler la configuration dans OMS Automation DSC, afin de pouvoir commencer à y enregistrer des nœuds.

### Compiler la configuration dans OMS Automation DSC

Pour ce faire, exécutez l’applet de commande suivante :

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Cela peut prendre quelques minutes, car vous déployez la configuration vers le service Pull DSC hébergé.

Une fois terminé, vous pouvez récupérer les informations sur la tâche à l’aide de PowerShell (Get-AzureRmAutomationDscCompilationJob) ou utilisez portal.azure.com.

![Tâche de récupération](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

À présent, vous avez correctement publié et chargé notre configuration DSC sur OMS Automation DSC.

## Étape 4 - Intégration d’ordinateurs sur OMS Automation DSC
*L’une des conditions préalables à la réalisation de ce scénario est que les ordinateurs Windows soient mis à jour avec la dernière version de WMF. Vous pouvez télécharger et installer la version correcte pour votre plateforme via cette URL : https://www.microsoft.com/download/details.aspx?id=50395*

Vous allez maintenant créer une métaconfiguration de DSC que vous allez appliquer aux nœuds. Pour y parvenir, vous devez récupérer l’URL du point de terminaison et la clé primaire de votre compte Automation sélectionné dans Azure.

Ces valeurs peuvent être situées sous « Keys » (Clés) dans le panneau « All Settings » (Tous les paramètres) du compte Automation.

![Valeurs de clé](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

Dans cet exemple, nous avons un serveur physique Windows Server 2012 R2 que nous voulons protéger avec OMS Site Recovery.

Avant de commencer à associer le serveur au point de terminaison Automation DSC, il est recommandé de rechercher les opérations de changement de nom de fichier en attente dans le Registre, car cela peut empêcher la bonne exécution de l’installation en raison d’un redémarrage en attente.

### Rechercher les éventuelles opérations de changement de nom de fichier en attente dans le Registre

Exécutez l’applet de commande suivante pour vérifier qu’il n’y a aucun redémarrage en attente sur le serveur :

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Si rien n’apparaît, vous pouvez continuer. Sinon, vous devez remédier à cette situation en redémarrant le serveur pendant une fenêtre de maintenance.

Pour appliquer la configuration sur le serveur, démarrez PowerShell ISE et exécutez le script suivant. Il s’agit essentiellement d’une configuration du gestionnaire de configuration local de DSC qui force le moteur de ce dernier à s’inscrire auprès du service OMS Automation DSC et à récupérer la configuration spécifique (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }
        
        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
        
        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }
        
        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Cette configuration configure le gestionnaire de configuration local pour qu’il s’enregistre lui-même avec OMS Automation DSC et indique au moteur comment fonctionner, ce qu’il doit faire en cas de changement de configuration (ApplyAndAutoCorrect) et, en cas de redémarrage requis, de poursuivre ensuite la configuration.

Une fois cette configuration exécutée, le nœud doit commencer à s’enregistrer avec Automation DSC.

![Enregistrer le nœud](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Si vous revenez dans portal.azure.com, vous pouvez voir que le nœud qui vient d’être enregistré apparaît maintenant dans le portail.

![Enregistrer le nœud](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

Sur le serveur, vous pouvez exécuter l’applet de commande PowerShell suivante pour vérifier qu’il a été enregistré correctement :

```powershell
Get-DscLocalConfigurationManager
```

Une fois la configuration extraite et appliquée au serveur, vous pouvez vérifier cela en exécutant :

```powershell
Get-DscConfigurationStatus
```

La sortie montre que le serveur a correctement extrait sa configuration :

![Enregistrer le nœud](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

En outre, l’installation du Service Mobilité a son propre journal, consultable dans « <DisqueSystème>\\ProgramData\\ASRSetupLogs ».

Voilà, vous avez correctement déployé et enregistré le Service Mobilité sur l’ordinateur que vous souhaitez protéger avec Site Recovery, et vous savez qu’avec DSC les services nécessaires seront toujours exécutés.

![Enregistrer le nœud](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Une fois cela détecté par le serveur d’administration, vous pouvez passer à la configuration de la protection et activer la réplication sur l’ordinateur avec Site Recovery.

## Utilisation de DSC dans les environnements déconnectés

Si vos ordinateurs ne sont pas connectés à Internet, vous pouvez toujours vous fier à DSC pour déployer et configurer le Service Mobilité sur les charges de travail que vous souhaitez protéger.

Vous pouvez si vous le souhaitez instancier votre propre serveur Pull DSC dans votre environnement. Il fournira essentiellement les mêmes fonctionnalités que celles obtenues avec OMS Automation DSC, c’est-à-dire que les clients récupèrent la configuration une fois enregistrés sur le point de terminaison DSC. Toutefois, vous disposez d’une autre option qui consiste à utiliser la transmission de type Push, c’est-à-dire que vous transmettez manuellement la configuration DSC vers vos ordinateurs, en local ou à distance.

Notez que, dans cet exemple, un paramètre a été ajouté pour le nom de l’ordinateur (computername). Les fichiers à distance se trouvent désormais sur un partage distant auquel doivent pouvoir accéder les ordinateurs que vous souhaitez protéger. À la fin du script, ce dernier met en œuvre la configuration, puis commence à appliquer la configuration DSC à l’ordinateur cible.

### Conditions préalables

· Le module PowerShell xPSDesiredStateConfiguration doit être installé

Pour les ordinateurs Windows sur lesquels WMF 5.0 est installé, il vous suffit d’installer le module xPSDesiredStateConfiguration en exécutant l’applet de commande suivante sur les ordinateurs cibles :

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Vous pouvez également télécharger et enregistrer le module si vous devez le distribuer vers les ordinateurs Windows à l’aide de WMF 4.0, en exécutant cette applet de commande sur un ordinateur où PowerShellGet (WMF 5.0) est présent :

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Par ailleurs, pour WMF 4.0, vérifiez que la mise à jour suivante est installée sur les ordinateurs :

https://www.microsoft.com/download/details.aspx?id=40749

La configuration suivante peut être transmise vers les ordinateurs Windows WMF 5.0 et 4.0

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase
    
    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }
        
        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Microsoft Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Si vous souhaitez instancier votre propre serveur Pull DSC au sein de votre réseau d’entreprise, pour imiter les fonctionnalités que vous pouvez obtenir à partir d’OMS Automation DSC, consultez le guide suivant : https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396

## Facultatif : Déployer la configuration DSC à l’aide du modèle Azure Resource Manager

Cet article se concentre sur la création de votre propre configuration DSC pour déployer automatiquement le Service Mobilité et l’agent de machine virtuelle Azure, et s’assurer qu’ils s’exécutent sur les ordinateurs que vous souhaitez protéger. De plus, nous avons également utilisé un modèle Azure Resource Manager qui va déployer cette configuration DSC sur un compte Azure Automation nouveau ou existant, en créant des ressources automation destinées à contenir les variables de votre environnement via des paramètres d’entrée dans le modèle.

Après le déploiement, vous n’avez plus qu’à vous reporter à l’étape 4 de ce guide concernant l’intégration des ordinateurs.

Le modèle effectue les opérations suivantes :

· Utiliser un compte OMS Automation existant ou en créer un nouveau

· Récupérer les paramètres d’entrée pour :

	· ASRRemoteFile – the location where you have stored the Mobility Service setup
	· ASRPassphrase – the location where you have stored the passphrase.txt file
	· ASRCSEndpoint – the IP address of your Management server
	· Import xPSDesiredStateConfiguration PowerShell module
	· Create and compile the DSC configuration


Toutes les étapes ci-dessus sont effectuées dans le bon ordre pour que vous puissiez facilement commencer à intégrer les ordinateurs à protéger.

Le modèle comportant des instructions pour le déploiement se trouve à cet emplacement :

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC

Déployez à l’aide de PowerShell :

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## Étapes suivantes :

Après avoir déployé les agents du Service Mobilité, vous pouvez poursuivre en [activant la réplication](site-recovery-vmware-to-azure.md#step-6-replicate-applications) des machines virtuelles.

<!---HONumber=AcomDC_0824_2016-->