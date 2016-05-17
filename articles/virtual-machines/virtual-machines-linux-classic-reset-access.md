<properties
	pageTitle="Réinitialisation du mot de passe de machine virtuelle Linux et de la clé SSH à partir de l’interface CLI | Microsoft Azure"
	description="Comment utiliser l’extension VMAccess à partir de l’Interface de ligne de commande (CLI) Azure pour réinitialiser un mot de passe de machine virtuelle Linux ou une clé SSH, corriger la configuration SSH et vérifier la cohérence des disques"
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
	ms.date="04/20/2016"
	ms.author="cynthn"/>

# Réinitialisation de l'accès, gestion des utilisateurs et vérification des disques avec l'extension Azure VMAccess pour Linux


Si vous ne pouvez pas vous connecter à une machine virtuelle Linux sur Azure en raison d'un mot de passe oublié, d'une clé SSH (Secure Shell) incorrecte ou d'un problème lié à la configuration SSH, utilisez l'extension VMAccessForLinux avec l'interface de ligne de commande Azure pour réinitialiser le mot de passe ou la clé SSH, corriger la configuration SSH et vérifier la cohérence des disques.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).




## Interface de ligne de commande Azure

Vous devez disposer des éléments suivants :

- Interface de ligne de commande Microsoft Azure Vous devrez [installer l’interface de ligne de commande Azure](../xplat-cli-install.md), et vous [connecter à votre abonnement](../xplat-cli-connect.md) pour utiliser des ressources Azure associées à votre compte.
- Un nouveau mot de passe ou des clés SSH, si vous souhaitez réinitialiser l’un des deux. Vous n’avez pas besoin de ces derniers si vous souhaitez corriger la configuration SSH.


## Utilisation de la commande azure vm extension set

Grâce à l’interface de ligne de commande Microsoft Azure, vous pouvez utiliser la commande **azure vm extension set** à partir de votre interface de ligne de commande (interpréteur de commandes (Bash), terminal, invite de commandes) pour accéder aux commandes associées. Exécutez **azure help vm extension set** pour utiliser l’extension détaillée.

L’interface de ligne de commande Microsoft Azure vous permet d’effectuer les tâches suivantes :

+ [Réinitialisation du mot de passe](#pwresetcli)
+ [Réinitialisation de la clé SSH](#sshkeyresetcli)
+ [Réinitialisation du mot de passe et de la clé SSH](#resetbothcli)
+ [Création d’un nouveau compte utilisateur sudo](#createnewsudocli)
+ [Réinitialisation de la configuration SSH](#sshconfigresetcli)
+ [Suppression d’un utilisateur](#deletecli)
+ [Affichage de l’état de l’extension VMAccess](#statuscli)
+ [Vérification de la cohérence des disques ajoutés](#checkdisk)
+ [Réparation des disques ajoutées sur votre VM Linux](#repairdisk)

### <a name="pwresetcli"></a>Réinitialisation du mot de passe

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments, en lieu et place des valeurs des espaces réservés.

	{
	"username":"currentusername",
	"password":"newpassword",
	"expiration":"2016-01-01"
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

### <a name="sshkeyresetcli"></a>Réinitialisation de la clé SSH

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments, en lieu et place des valeurs des espaces réservés.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey"
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="resetbothcli"></a>Réinitialisation du mot de passe et de la clé SSH

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments, en lieu et place des valeurs des espaces réservés.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	"password":"newpassword"
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

> [AZURE.NOTE] Le fichier de configuration SSH réinitialisé se trouve dans /etc/ssh/sshd\_config.

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments.

	{
	"reset_ssh":"True"
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="deletecli"></a>Suppression d’un utilisateur

Si vous souhaitez supprimer un compte utilisateur sans vous connecter directement à la machine virtuelle, vous pouvez utiliser ce script.

Étape 1 : Créez un fichier appelé PrivateConf.json avec ces éléments, en lieu et place de la valeur d’espace réservé.

	{
	"remove_user":"usernametoremove"
	}

Étape 2 : Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par « vmname ».

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="statuscli"></a>Affichage de l’état de l’extension VMAccess

Pour afficher l’état de l’extension VMAccess, exécutez cette commande.

	azure vm extension get

### <a name='checkdisk'<</a>Vérification de la cohérence des disques ajoutés

Pour exécuter la commande fsck sur tous les disques de votre machine virtuelle Linux, vous devez effectuer les opérations suivantes :

Étape 1 : Créez un fichier nommé PublicConf.json avec ces éléments. Vérifiez que le disque prend une valeur booléenne pour vérifier les disques attachés à votre machine virtuelle ou non.

    {   
    "check_disk": "true"
    }

Étape 2 : Exécutez cette commande, en remplaçant les valeurs d'espace réservé.

   azure vm extension set vm-name VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json

### <a name='repairdisk'></a>Réparation des disques ajoutés sur votre machine virtuelle Linux

Pour réparer les disques qui ne se montent pas ou dont la configuration de montage présente des erreurs, utilisez l'extension VMAccess pour réinitialiser la configuration de montage sur votre machine virtuelle Linux.

Étape 1 : Créez un fichier nommé PublicConf.json avec ces éléments.

    {
    "repair_disk":"true",
    "disk_name":"yourdisk"
    }

Étape 2 : Exécutez cette commande, en remplaçant les valeurs d'espace réservé.

    azure vm extension set vm-name VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json



## Ressources supplémentaires

* Si vous souhaitez utiliser des applets de commande Azure PowerShell ou des modèles Azure Resource Manager pour réinitialiser le mot de passe ou la clé SSH, corrigez la configuration SSH et vérifiez la cohérence des disques, consultez la [documentation de l’extension VMAccess sur GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 

* Vous pouvez également utiliser le [portail Azure](https://portal.azure.com) pour réinitialiser le mot de passe ou la clé SSH d’une machine virtuelle Linux déployée dans le modèle de déploiement classique. Vous ne pouvez pas utiliser actuellement le portail dans ce but pour une machine virtuelle Linux déployée dans le modèle de déploiement Resource Manager.

* Consultez la page [À propos des extensions et des fonctionnalités des machines virtuelles](virtual-machines-linux-extensions-features.md) pour plus d’informations sur l’utilisation des extensions de machine virtuelle pour les machines virtuelles Azure.

<!---HONumber=AcomDC_0504_2016-->