<properties
	pageTitle="Réinitialisation des mots de passe de machine virtuelle Linux et ajout d'utilisateurs à partir de l'interface CLI Azure | Microsoft Azure"
	description="Utilisation de l'extension VMAccess à partir du portail Azure ou de la CLI pour réinitialiser les mots de passe et les clés SSH, les configurations SSH de la machine virtuelle Linux, ajouter et supprimer des comptes d'utilisateurs, et vérifier la cohérence des disques."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="cynthn"/>

# Réinitialisation de l'accès, gestion des utilisateurs et vérification des disques avec l'extension Azure VMAccess Extension pour Linux#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle de gestionnaire de ressources.


Si vous ne pouvez pas vous connecter à une machine virtuelle Linux sur Azure en raison d'un mot de passe oublié, d'une clé SSH (Secure Shell) incorrecte ou d'un problème lié à la configuration SSH, utilisez le portail Azure ou l'extension VMAccessForLinux avec l'interface de ligne de commande Azure pour réinitialiser le mot de passe ou la clé SSH, corriger la configuration SSH et vérifier la cohérence des disques.

## Portail Azure

Pour réinitialiser la configuration SSH dans le [portail Azure](https://portal.azure.com), cliquez sur **Parcourir** > **Machines virtuelles** > *votre machine virtuelle Linux* > **Réinitialiser l’accès à distance**. Voici un exemple.

![](./media/virtual-machines-linux-classic-reset-access/Portal-RDP-Reset-Linux.png)

Pour réinitialiser le nom et le mot de passe du compte d’utilisateur avec des privilèges sudo ou la clé publique SSH dans le [portail Azure](https://portal.azure.com), cliquez sur **Parcourir** > **Machines virtuelles** > *votre machine virtuelle Linux* > **Tous les paramètres** > **Réinitialisation du mot de passe**. Voici un exemple.

![](./media/virtual-machines-linux-classic-reset-access/Portal-PW-Reset-Linux.png)


## Interface de ligne de commande Microsoft Azure et PowerShell

Vous devez disposer des éléments suivants :

- Agent Microsoft Azure Linux version 2.0.5 ou ultérieure. La plupart des images Linux dans la galerie de machines virtuelles incluent la version 2.0.5. Pour connaître la version installée, exécutez **waagent -version**. Pour mettre à jour l’agent, suivez les instructions du [Guide de l’utilisateur de l’agent Linux Azure].
- Interface de ligne de commande Microsoft Azure Pour plus d’informations sur la configuration de l’interface de ligne de commande Microsoft Azure, voir la page [Installation et configuration de l’interface de ligne de commande Microsoft Azure](../xplat-cli-install.md).
- Azure PowerShell. Vous allez utiliser les commandes de la cmdlet Set-AzureVMExtension pour charger et configurer automatiquement l’extension VMAccessForLinux. Pour plus d’informations sur la configuration de Microsoft Azure PowerShell, voir la page [Installation et configuration d’Azure PowerShell].
- Un nouveau mot de passe ou des clés SSH, si vous souhaitez réinitialiser l’un des deux. Vous n’avez pas besoin de ces derniers si vous souhaitez corriger la configuration SSH.

### Aucune installation nécessaire

VMAccess ne doit être installé avant que vous puissiez l’utiliser. Tant que l’agent Linux est installé sur la machine virtuelle, l’extension est chargée automatiquement lorsque vous exécutez une commande Azure PowerShell qui utilise l’applet de commande **Set-AzureVMExtension**.

## Utilisation de l’interface de ligne de commande Microsoft Azure

Grâce à l’interface de ligne de commande Microsoft Azure, vous pouvez exécuter la commande **azure** à partir de votre interface de ligne de commande (interpréteur de commandes, terminal, invite de commandes) pour accéder aux commandes associées. Exécutez **azure vm extension set –help** pour solliciter l’extension détaillée.

L’interface de ligne de commande Microsoft Azure vous permet d’effectuer les tâches suivantes :

+ [Réinitialisation du mot de passe](#pwresetcli)
+ [Réinitialisation de la clé SSH](#sshkeyresetcli)
+ [Réinitialisation du mot de passe et de la clé SSH](#resetbothcli)
+ [Création d’un nouveau compte utilisateur sudo](#createnewsudocli)
+ [Réinitialisation de la configuration SSH](#sshconfigresetcli)
+ [Suppression d’un utilisateur](#deletecli)
+ [Affichage de l’état de l’extension VMAccess](#statuscli)
+ [Vérification de la cohérence des disques ajoutés](#checkdisk)
+ [Réparation des disques ajoutées sur votre VM Linux](#repairdisk)

### <a name="pwresetcli"></a>Réinitialisation du mot de passe

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments, en lieu et place des valeurs des espaces réservés.

	{
	"username":"currentusername",
	"password":"newpassword",
	"expiration":"2016-01-01"
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

### <a name="sshkeyresetcli"></a>Réinitialisation de la clé SSH

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments, en lieu et place des valeurs des espaces réservés.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey"
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="resetbothcli"></a>Réinitialisation du mot de passe et de la clé SSH

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments, en lieu et place des valeurs des espaces réservés.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	"password":"newpassword"
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="createnewsudocli"></a>Création d’un nouveau compte utilisateur sudo

Si vous avez oublié votre nom utilisateur, utilisez VMAccess pour en créer un nouveau, avec autorité sudo. Le cas échéant, le nom d’utilisateur et le mot de passe existants ne sont pas modifiés.

Pour créer un nouvel utilisateur sudo avec accès par mot de passe, utilisez le script de [Réinitialisation du mot de passe](#pwresetcli), puis spécifiez le nouveau nom d’utilisateur.

Pour créer un nouvel utilisateur sudo avec accès par clé SSH, utilisez le script de [Réinitialisation de la clé SSH](#sshkeyresetcli), puis spécifiez le nouveau nom d’utilisateur.

Vous pouvez également utiliser [Réinitialisation du mot de passe et de la clé SSH](#resetbothcli) pour créer un nouvel utilisateur avec accès par mot de passe et clé SSH.

### <a name="sshconfigresetcli"></a>Réinitialisation de la configuration SSH

Si la configuration SSH se trouve dans un état non souhaité, vous pouvez également perdre l’accès à la machine virtuelle. Pour redéfinir l’état par défaut de la configuration, utilisez l’extension VMAccess. Pour ce faire, réinitialisez la clé reset\_ssh sur la valeur True. L’extension redémarre le serveur SSH, ouvre le port SSH sur votre machine virtuelle et rétablit la configuration SSH par défaut. Le compte d’utilisateur (nom, mot de passe ou clés SSH) ne sera pas modifié.

> [AZURE.NOTE] Le fichier de configuration SSH réinitialisé se trouve dans /etc/ssh/sshd\_config.

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments.

	{
	"reset_ssh":"True"
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="deletecli"></a>Suppression d’un utilisateur

Si vous souhaitez supprimer un compte utilisateur sans vous connecter directement à la machine virtuelle, vous pouvez utiliser ce script.

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments, en lieu et place de la valeur d’espace réservé.

	{
	"remove_user":"usernametoremove"
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="statuscli"></a>Affichage de l’état de l’extension VMAccess

Pour afficher l’état de l’extension VMAccess, exécutez cette commande.

	azure vm extension get

### <a name='checkdisk'<</a>Vérification de la cohérence des disques ajoutés

Pour exécuter la commande fsck sur tous les disques de votre machine virtuelle Linux, vous devez effectuer les opérations suivantes :

Étape 1 : Créez un fichier nommé PublicConf.json avec ces éléments. Vérifiez que le disque prend une valeur booléenne pour vérifier les disques attachés à votre machine virtuelle ou non.

    {   
    "check_disk": "true"
    }

Étape 2 : Exécutez cette commande, en remplaçant les valeurs d'espace réservé.

   azure vm extension set vm-name VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json

### <a name='repairdisk'></a>Réparation des disques ajoutées sur votre machine virtuelle Linux

Pour réparer les disques qui ne se montent pas ou dont la configuration de montage présente des erreurs, utilisez l'extension VMAccess pour réinitialiser la configuration de montage sur votre machine virtuelle Linux.

Étape 1 : Créez un fichier nommé PublicConf.json avec ces éléments.

    {
    "repair_disk":"true",
    "disk_name":"yourdisk"
    }

Étape 2 : Exécutez cette commande, en remplaçant les valeurs d'espace réservé.

    azure vm extension set vm-name VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json

## Utilisation d'Azure PowerShell

Utilisez l’applet de commande **Set-AzureVMExtension** pour effectuer les modifications autorisées par VMAccess. Dans tous les cas, démarrez en utilisant le nom du service cloud et le nom de la machine virtuelle pour obtenir l’objet de machine de virtuelle et le stocker dans une variable.

Renseignez le nom du service cloud et le nom de la machine virtuelle, puis exécutez les commandes suivantes à une invite de commandes de niveau administrateur Azure PowerShell. Remplacez tous les éléments entre guillemets, y compris les caractères < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

Si vous ne connaissez pas le service cloud et le nom de la machine virtuelle, exécutez **Get-AzureVM** pour afficher ces informations pour toutes les machines virtuelles dans votre abonnement actuel.


> [AZURE.NOTE] Les lignes de commande qui commencent par $ définissent des variables PowerShell qui sont ensuite utilisées dans les commandes PowerShell.

Si vous avez créé la machine virtuelle avec le portail Azure Classic, exécutez la commande supplémentaire suivante :

	$vm.GetInstance().ProvisionGuestAgent = $true

Cette commande empêche l’erreur « L’agent invité d’approvisionnement doit être activé sur l’objet VM avant de définir l’extension d’accès à la machine virtuelle IaaS » de se produire lors de l’exécution de la commande Set-AzureVMExtension dans les sections suivantes.

Exécutez ensuite les tâches suivantes :

+ [Réinitialisation du mot de passe](#password)
+ [Réinitialisation d’une clé SSH](#SSHkey)
+ [Réinitialisation du mot de passe et de la clé SSH](#both)
+ [Réinitialisation de la configuration SSH](#config)
+ [Suppression d’un utilisateur](#delete)
+ [Affichage de l’état de l’extension VMAccess](#status)
+ [Vérification de la cohérence des disques ajoutés](#checkdisk)
+ [Réparation des disques ajoutées sur votre VM Linux](#repairdisk)

### <a name="password"></a>Réinitialisation du mot de passe

Renseignez le nom d’utilisateur Linux actuel et le nouveau mot de passe, puis exécutez ces commandes.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Si vous souhaitez réinitialiser le mot de passe ou la clé SSH pour un compte d’utilisateur, veillez à taper le nom d’utilisateur exact. Si vous tapez un nom différent, l’extension VMAccess crée un nouveau compte d’utilisateur et affecte le mot de passe à ce compte.


### <a name="SSHKey"></a>Réinitialisation d’une clé SSH

Renseignez le nom d’utilisateur Linux actuel et le chemin d’accès au certificat contenant les clés SSH, puis exécutez ces commandes.

	$UserName = "<current Linux user name>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>Réinitialisation du mot de passe et de la clé SSH

Renseignez le nom d’utilisateur Linux actuel, le nouveau mot de passe ainsi que le chemin d’accès au certificat contenant les clés SSH, puis exécutez ces commandes.

	$UserName = "<current Linux user name>"
	$Password = "<new password>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>Réinitialisation de la configuration SSH

Les erreurs de configuration SSH peuvent vous empêcher d’accéder à la machine virtuelle. Vous pouvez résoudre ce problème en rétablissant la configuration SSH par défaut. Cette action supprime tous les nouveaux paramètres d’accès dans la configuration, tels que le nom d’utilisateur, le mot de passe et la clé SSH, mais ne modifie pas le mot de passe ou les clés SSH du compte d’utilisateur. L’extension redémarre le serveur SSH, ouvre le port SSH sur votre machine virtuelle et rétablit la configuration SSH par défaut.

Exécutez ces commandes.

	$PrivateConfig = '{"reset_ssh": "True"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Le fichier de configuration SSH se trouve dans /etc/ssh/sshd\_config.

### <a name="delete"></a> Suppression d’un utilisateur

Renseignez le nom d’utilisateur Linux à supprimer, puis exécutez ces commandes.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="status"></a>Affichage de l’état de l’extension VMAccess

Pour afficher l’état de l’extension VMAccess, exécutez cette commande.

	$vm.GuestAgentStatus

### <a name="checkdisk"<</a>Vérification de la cohérence des disques ajoutés

Pour vérifier la cohérence de vos disques avec l'utilitaire fsck, exécutez les commandes suivantes.

	$PublicConfig = "{"check_disk": "true"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PublicConfiguration $PublicConfig | Update-AzureVM

### <a name="checkdisk"<</a>Réparation des disques ajoutées sur votre VM Linux

Pour réparer des disques avec l'utilitaire fsck, exécutez les commandes suivantes.

	$PublicConfig = "{"repair_disk": "true", "disk_name": "my_disk"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PublicConfiguration $PublicConfig | Update-AzureVM

## Ressources supplémentaires

[Fonctionnalités et extensions de machine virtuelle Azure][]

[Connexion à une machine virtuelle Microsoft Azure avec RDP ou SSH][]


<!--Link references-->
[Guide de l’utilisateur de l’agent Linux Azure]: virtual-machines-linux-agent-user-guide.md
[Installation et configuration d’Azure PowerShell]: ../install-configure-powershell.md
[Fonctionnalités et extensions de machine virtuelle Azure]: virtual-machines-windows-extensions-features.md
[Connexion à une machine virtuelle Microsoft Azure avec RDP ou SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx

<!---HONumber=AcomDC_0323_2016-->