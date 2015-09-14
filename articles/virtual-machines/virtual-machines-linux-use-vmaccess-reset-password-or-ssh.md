<properties
	pageTitle="Utilisation de VMAccess pour les machines virtuelles Linux"
	description="Utilisation du portail Microsoft Azure en version préliminaire ou de l’extension VMAccess pour Linux pour réinitialiser les mots de passe et les clés SSH, réinitialiser les configurations SSH et supprimer les utilisateurs Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="kathydav"/>

# Réinitialisation d’un mot de passe ou de SSH pour les machines virtuelles Linux #

Si vous ne pouvez pas vous connecter à une machine virtuelle Linux en raison d’un mot de passe oublié, d’une clé SSH (Secure Shell) incorrecte ou d’un problème lié à la configuration SSH, utilisez le portail Microsoft Azure en version préliminaire pour réinitialiser le mot de passe ou la clé SSH, ou corriger la configuration SSH. Notez que cet article s’applique aux machines virtuelles créées à l’aide du modèle de déploiement **Classique**.

## Version préliminaire du portail Azure

Pour réinitialiser la configuration SSH dans le [portail Microsoft Azure en version préliminaire](https://portal.azure.com), cliquez sur **Parcourir** > **Virtual machines** > *votre machine virtuelle Linux* > **Réinitialiser l’accès à distance**. Voici un exemple.

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-RDP-Reset-Linux.png)

Pour réinitialiser le nom et le mot de passe du compte d’utilisateur avec des privilèges sudo ou la clé publique SSH dans le [portail Microsoft Azure en version préliminaire](https://portal.azure.com), cliquez sur **Parcourir** > **Virtual machines** > *votre machine virtuelle Linux* > **Tous les paramètres** > **Réinitialisation du mot de passe**. Voici un exemple.

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-PW-Reset-Linux.png)


## Interface de ligne de commande Microsoft Azure et PowerShell

Vous devez disposer des éléments suivants :

- Agent Microsoft Azure Linux version 2.0.5 ou ultérieure. La plupart des images Linux dans la galerie de machines virtuelles incluent la version 2.0.5. Pour connaître la version installée, exécutez **waagent -version**. Pour mettre à jour l’agent, suivez les instructions du [Guide de l’utilisateur de l’agent Linux Azure].
- Interface de ligne de commande Microsoft Azure Pour plus d’informations sur la configuration de l’interface de ligne de commande Microsoft Azure, consultez la page [Installation et configuration de l’interface de ligne de commande Microsoft Azure](../xplat-cli.md).
- Azure PowerShell. Vous allez utiliser les commandes de la cmdlet Set-AzureVMExtension pour charger et configurer automatiquement l'extension VMAccessForLinux. Pour plus d’informations sur la configuration de Microsoft Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell].
- Un nouveau mot de passe ou des clés SSH, si vous souhaitez réinitialiser l’un des deux. Vous n’avez pas besoin de ces derniers si vous souhaitez corriger la configuration SSH.

### Aucune installation nécessaire

VMAccess ne doit être installé avant que vous puissiez l’utiliser. Tant que l’agent Linux est installé sur la machine virtuelle, l’extension est chargée automatiquement lorsque vous exécutez une commande Azure PowerShell qui utilise l’applet de commande **Set-AzureVMExtension**.

## Utilisation de l’interface de ligne de commande Microsoft Azure

Grâce à l’interface de ligne de commande Microsoft Azure, vous pouvez exécuter la commande **azure** à partir de votre interface de ligne de commande (interpréteur de commandes, terminal, invite de commandes) pour accéder aux commandes associées. Exécutez **azure vm extension set –help** pour solliciter l’extension détaillée.

L’interface de ligne de commande Microsoft Azure vous permet d’effectuer les tâches suivantes :

+ [Réinitialisation du mot de passe](#pwresetcli)
+ [Réinitialisation de la clé SSH](#sshkeyresetcli)
+ [Réinitialisation du mot de passe et de la clé SSH](#resetbothcli)
+ [Création d’un nouveau compte utilisateur sudo](#createnewsudocli)
+ [Réinitialisation de la configuration SSH](#sshconfigresetcli)
+ [Suppression d’un utilisateur](#deletecli)
+ [Affichage de l’état de l’extension VMAccess](#statuscli)

### <a name="pwresetcli"></a>Réinitialisation du mot de passe

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments, en lieu et place des valeurs des espaces réservés.

	{
	"username":"currentusername",
	"password":"newpassword",
	"expiration":"2016-01-01",
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

### <a name="sshkeyresetcli"></a>Réinitialisation de la clé SSH

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments, en lieu et place des valeurs des espaces réservés.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="resetbothcli"></a>Réinitialisation du mot de passe et de la clé SSH

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments, en lieu et place des valeurs des espaces réservés.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	"password":"newpassword",
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="createnewsudocli"></a>Création d’un nouveau compte utilisateur sudo

Si vous avez oublié votre nom utilisateur, utilisez VMAccess pour en créer un nouveau, avec autorité sudo. Le cas échéant, le nom d’utilisateur et le mot de passe existants ne sont pas modifiés.

Pour créer un nouvel utilisateur sudo avec accès par mot de passe, utilisez le script de [Réinitialisation du mot de passe](#pwresetcli), puis spécifiez le nouveau nom d’utilisateur.

Pour créer un nouvel utilisateur sudo avec accès par clé SSH, utilisez le script de [Réinitialisation de la clé SSH](#sshkeyresetcli), puis spécifiez le nouveau nom d’utilisateur.

Vous pouvez également utiliser [Réinitialisation du mot de passe et de la clé SSH](#resetbothcli) pour créer un nouvel utilisateur avec accès par mot de passe et clé SSH.

### <a name="sshconfigresetcli"></a>Réinitialisation de la configuration SSH

Si la configuration SSH se trouve dans un état non souhaité, vous pouvez également perdre l’accès à la machine virtuelle. Pour redéfinir l’état par défaut de la configuration, utilisez l’extension VMAccess. Pour ce faire, réinitialisez la clé reset\_ssh sur la valeur True. L’extension redémarre le serveur SSH, ouvre le port SSH sur votre machine virtuelle et rétablit la configuration SSH par défaut. Le compte d’utilisateur (nom, mot de passe ou clés SSH) ne sera pas modifié.

> [AZURE.NOTE]Le fichier de configuration SSH réinitialisé se trouve dans /etc/ssh/sshd\_config.

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments.

	{
	"reset_ssh":"True",
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="deletecli"></a>Suppression d’un utilisateur

Si vous souhaitez supprimer un compte utilisateur sans vous connecter directement à la machine virtuelle, vous pouvez utiliser ce script.

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments, en lieu et place de la valeur d’espace réservé.

	{
	"remove_user":"usernametoremove",
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="statuscli"></a>Affichage de l’état de l’extension VMAccess

Pour afficher l’état de l’extension VMAccess, exécutez cette commande.

	azure vm extension get


## Utilisation d'Azure PowerShell

Utilisez l’applet de commande **Set-AzureVMExtension** pour effectuer les modifications autorisées par VMAccess. Dans tous les cas, démarrez en utilisant le nom du service cloud et le nom de la machine virtuelle pour obtenir l’objet de machine de virtuelle et le stocker dans une variable.

Renseignez le nom du service cloud et le nom de la machine virtuelle, puis exécutez les commandes suivantes à une invite de commandes de niveau administrateur Azure PowerShell. Remplacez tous les éléments entre guillemets, y compris les caractères < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

Si vous ne connaissez pas le service cloud et le nom de la machine virtuelle, exécutez **Get-AzureVM** pour afficher ces informations pour toutes les machines virtuelles dans votre abonnement actuel.


> [AZURE.NOTE]Les lignes de commande qui commencent par $ définissent des variables PowerShell qui sont ensuite utilisées dans les commandes PowerShell.

Si vous créez la machine virtuelle à l’aide du portail de gestion Azure, exécutez la commande supplémentaire suivante :

	$vm.GetInstance().ProvisionGuestAgent = $true

Cette commande empêche l’erreur « L’agent invité d’approvisionnement doit être activé sur l’objet VM avant de définir l’extension d’accès à la machine virtuelle IaaS » de se produire lors de l’exécution de la commande Set-AzureVMExtension dans les sections suivantes.

Exécutez ensuite les tâches suivantes :

+ [Réinitialisation du mot de passe](#password)
+ [Réinitialisation d’une clé SSH](#SSHkey)
+ [Réinitialisation du mot de passe et de la clé SSH](#both)
+ [Réinitialisation de la configuration SSH](#config)
+ [Suppression d’un utilisateur](#delete)
+ [Affichage de l’état de l’extension VMAccess](#status)

### <a name="password"></a>Réinitialisation du mot de passe

Renseignez le nom d'utilisateur Linux actuel et le nouveau mot de passe, puis exécutez ces commandes.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE]Si vous souhaitez réinitialiser le mot de passe ou la clé SSH pour un compte d’utilisateur, veillez à taper le nom d’utilisateur exact. Si vous tapez un nom différent, l’extension VMAccess crée un nouveau compte d’utilisateur et affecte le mot de passe à ce compte.


### <a name="SSHKey"></a>Réinitialisation d’une clé SSH

Renseignez le nom d'utilisateur Linux actuel et le chemin d'accès au certificat contenant les clés SSH, puis exécutez ces commandes.

	$UserName = "<current Linux user name>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>Réinitialisation du mot de passe et de la clé SSH

Renseignez le nom d'utilisateur Linux actuel, le nouveau mot de passe ainsi que le chemin d'accès au certificat contenant les clés SSH, puis exécutez ces commandes.

	$UserName = "<current Linux user name>"
	$Password = "<new password>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>Réinitialisation de la configuration SSH

Les erreurs de configuration SSH peuvent vous empêcher d’accéder à la machine virtuelle. Vous pouvez résoudre ce problème en rétablissant la configuration SSH par défaut. Cette action supprime tous les nouveaux paramètres d'accès dans la configuration, tels que le nom d'utilisateur, le mot de passe et la clé SSH, mais ne modifie pas le mot de passe ou les clés SSH du compte d'utilisateur. L’extension redémarre le serveur SSH, ouvre le port SSH sur votre machine virtuelle et rétablit la configuration SSH par défaut.

Exécutez ces commandes.

	$PrivateConfig = '{"reset_ssh": "True"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE]Le fichier de configuration SSH se trouve dans /etc/ssh/sshd\_config.

### <a name="delete"></a> Suppression d’un utilisateur

Renseignez le nom d'utilisateur Linux à supprimer, puis exécutez ces commandes.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="status"></a>Affichage de l’état de l’extension VMAccess

Pour afficher l’état de l’extension VMAccess, exécutez cette commande.

	$vm.GuestAgentStatus


## Ressources supplémentaires

[Fonctionnalités et extensions de machine virtuelle Azure][]

[Connexion à une machine virtuelle Microsoft Azure avec RDP ou SSH][]


<!--Link references-->
[Guide de l’utilisateur de l’agent Linux Azure]: virtual-machines-linux-agent-user-guide.md
[Installation et configuration d’Azure PowerShell]: ../install-configure-powershell.md
[Fonctionnalités et extensions de machine virtuelle Azure]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Connexion à une machine virtuelle Microsoft Azure avec RDP ou SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx

<!---HONumber=September15_HO1-->