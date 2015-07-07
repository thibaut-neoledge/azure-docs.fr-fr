<properties
	pageTitle="Déployer et gérer une sauvegarde pour serveur/client Windows à l’aide de Windows PowerShell | Microsoft Azure"
	description="Déploiement et gestion d’Azure Backup à l’aide de Windows PowerShell"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="aashishr"/>


# Déployer et gérer une sauvegarde vers Azure pour serveur/client Windows à l’aide de Windows PowerShell
Cet article décrit l’utilisation de l’interface de ligne de commande Windows PowerShell® pour configurer Azure Backup sur Windows Server ou Windows Client ainsi que la gestion des sauvegardes et des restaurations.

## Configuration de l’environnement Windows PowerShell
Avant de pouvoir utiliser Windows PowerShell pour gérer Azure Backup, vous devez disposer de la bonne version de Windows PowerShell et avoir correctement configuré les variables d’environnement.

## Installation et inscription
Les tâches d’installation et d’inscription suivantes peuvent être automatisées à l’aide de Windows PowerShell - Installation de l’agent Azure Backup - Inscription auprès du service Azure Backup - Mise en réseau

### Installation de l'agent Azure Backup
Pour installer l'agent Azure Backup, vous devez avoir téléchargé le programme d’installation sur le serveur Windows. Vous pouvez obtenir la dernière version du programme d’installation sur le [Centre de téléchargement de Microsoft](aka.ms/azurebackup_agent). Enregistrez le programme d’installation dans un emplacement auquel vous pouvez accéder facilement, par exemple *C:\Téléchargements*. Pour installer l’agent, exécutez la commande suivante dans une console Windows PowerShell avec des privilèges élevés :

```
PS C:> MARSAgentInstaller.exe /q
```

Cette opération installera l’agent avec les options par défaut. L’installation s’effectue en arrière-plan et prend quelques minutes. Si vous ne spécifiez pas l’option **/nu**, la fenêtre **Windows Update** s’ouvrira à la fin de l’installation pour rechercher des mises à jour.

L’agent apparaîtra dans la liste des programmes installés. Pour afficher la liste des programmes installés, cliquez sur **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités**.

![Agent installé](./media/backup-client-automation/installed-agent-listing.png)

#### Options d’installation

Pour afficher toutes les options disponibles via la ligne de commande, utilisez la commande suivante :

```
PS C:> MARSAgentInstaller.exe /?
```

Les options disponibles incluent :

| Option | Détails | Default |
| ---- | ----- | ----- |
| /q | Installation silencieuse | - | | /p:"emplacement" | Chemin du dossier d’installation de l’agent Azure Backup. | C:\Program Files\Microsoft Azure Recovery Services Agent | | /s:"emplacement" | Chemin du dossier du cache de l’agent Azure Backup. | C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch | | /m | Abonnement à Microsoft Update | - | | /nu | Ne pas rechercher les mises à jour après l’installation | - | | /d | Désinstalle Microsoft Azure Recovery Services Agent | - | | /ph | Adresse de l’hôte proxy | - | | /po | Numéro de port de l’hôte proxy | - | | /pu | Nom d’utilisateur de l’hôte proxy | - | | /pw | Mot de passe du proxy | - |


### Inscription auprès du service Azure Backup
Avant de pouvoir vous inscrire auprès du service Azure Backup, vérifiez que les [conditions préalables](backup-try-azure-backup-in-10-mins.md) sont remplies : - Disposer d’un abonnement Azure valide - Créer un coffre de sauvegarde - Télécharger les informations d’identification du coffre et les stocker dans un emplacement pratique (par exemple *C:\Téléchargements*). Les informations d’identification du coffre peuvent également être renommées à votre convenance.

L’inscription de la machine auprès du coffre s’effectue l’aide de la cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) :

```
PS C:> Start-OBRegistration -VaultCredentials "C:\Downloads\register.vaultcredentials" -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : Australia East

Machine registration succeeded.
```

> [AZURE.IMPORTANT]N’utilisez pas de chemins relatifs pour spécifier le fichier des informations d’identification du coffre. Vous devez fournir un chemin absolu dans la cmdlet.


### Mise en réseau
Lorsque l’ordinateur Windows accède à Internet via un serveur proxy, les paramètres proxy peuvent également être fournis à l’agent. Dans cet exemple, il n’y a aucun serveur proxy. Nous effaçons donc explicitement toutes informations concernant un proxy.

L’utilisation de la bande passante peut également être contrôlée avec les options *bande passante d’heure travaillée* et *bande passante d’heure non travaillée*, certains jours de la semaine.

La définition des détails sur le proxy et la bande passante s’effectue à l’aide de la cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) :

```
PS C:> Set-OBMachineSetting -NoProxy
Server properties updated successfully.
```

```
PS C:> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

### Paramètres de chiffrement
Les données sauvegardées envoyées à Azure Backup sont chiffrées pour garantir leur confidentialité. Le mot de passe du chiffrement est le « mot de passe » permettant de déchiffrer les données lors de la restauration. Il est important de conserver ces informations en lieu sûr après les avoir définies.

```
PS C:> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

## Désinstallation de l’agent Azure Backup
La désinstallation de l’agent Azure Backup peut être effectuée à l’aide de la commande suivante :

```
PS C:> .\MARSAgentInstaller.exe /d /q
```

La désinstallation des fichiers binaires de l’ordinateur a d’autres conséquences : - elle supprime le filtre de fichier de l’ordinateur et interrompt le suivi des modifications. - Toutes les informations de stratégie sont supprimées de l’ordinateur mais restent stockées dans le service. - Toutes les planifications de sauvegarde sont supprimées et aucune autre sauvegarde n’est effectuée.

Cependant, les données stockées dans Azure sont conservées selon la stratégie de rétention que vous avez définie. Les points plus anciens deviennent automatiquement obsolètes.

## Gestion à distance
Toute la gestion concernant l’agent Azure Backup, les stratégies et les sources de données peut être effectuée à distance via Windows PowerShell. L’ordinateur qui sera géré à distance doit être correctement préparé.

Par défaut, le service WinRM est configuré pour un démarrage manuel. Le type de démarrage doit être défini sur *Automatique* et le service devrait être démarré. Pour vérifier que le service WinRM est exécuté, la valeur de la propriété État devrait être défini sur *En cours d’exécution*.

```
PS C:> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...

- Windows PowerShell should be configured for remoting.
```

```
PS C:> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:> Set-ExecutionPolicy unrestricted -force
```

L’ordinateur peut maintenant être géré à distance, en commençant par l’installation de l’agent. Par exemple, le script suivant copie et installe l’agent sur l’ordinateur distant.

```
PS C:> $dloc = "\REMOTESERVER01\c$\Windows\Temp"
PS C:> $agent = "\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:> $args = "/q"
PS C:> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```
## Étapes suivantes
Pour en savoir plus sur Azure Backup pour client/serveur Windows, consultez la rubrique [Présentation d’Azure Backup](backup-introduction-to-azure-backup.md)

<!---HONumber=62-->