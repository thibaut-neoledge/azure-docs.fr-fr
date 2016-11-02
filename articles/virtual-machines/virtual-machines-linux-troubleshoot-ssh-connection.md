<properties
	pageTitle="La connexion SSH à une machine virtuelle Linux est refusée, échoue ou génère une erreur | Microsoft Azure"
	description="Dépannez et résolvez les erreurs SSH comme l’échec de connexion SSH ou le refus de connexion SSH pour une machine virtuelle Azure exécutant Linux."
	keywords="connexion ssh refusée, erreur ssh, ssh azure, échec de connexion SSH"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="iainfou"/>

# Dépanner une connexion SSH à une machine virtuelle Linux Azure défaillante, qui génère une erreur ou qui est refusée

Il existe différentes raisons pour lesquelles vous pouvez obtenir des erreurs SSH (Secure Shell), la connexion SSH échoue ou cette connexion est refusée lorsque vous tentez de vous connecter à une machine virtuelle Azure basée sur Linux. Cet article vous aide à identifier et corriger ces problèmes.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si vous avez besoin d’aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](http://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](http://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](http://azure.microsoft.com/support/faq/).

## Machines virtuelles créées à l’aide du modèle de déploiement Resource Manager

Vous pouvez réinitialiser les informations d’identification ou SSHD à l’aide des commandes de l’interface de ligne de commande Azure directement ou à l’aide de [l’extension Azure VMAccessForLinux](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). Après chaque étape de résolution des problèmes, essayez de nouveau de vous connecter à la machine virtuelle.

### Configuration requise pour l’interface de ligne de commande (CLI) Azure

Si ce n’est déjà fait, [installez l’interface CLI Azure et connectez-vous à votre abonnement Azure](../xplat-cli-install.md). Connectez-vous à l’aide de la commande `azure login` et vérifiez que le mode Resource Manager est activé (`azure config mode arm`).

Vérifiez que [Microsoft Azure Linux Agent](virtual-machines-linux-agent-user-guide.md) version 2.0.5 ou ultérieure est installé.

### Réinitialiser SSHD
Il est possible que la configuration SSHD soit mal configurée ou que le service ait rencontré une erreur. Vous pouvez réinitialiser SSHD pour vous assurer que la configuration SSH elle-même est valide.

#### Interface de ligne de commande Azure
```bash
azure vm reset-access -g <resource group> -n <vm name> -r
```

#### Extension d’accès aux machines virtuelles
Les extensions d’accès lisent un fichier json qui définit l’action à effectuer, comme la réinitialisation de SSH, la réinitialisation d’une clé SSH, l’ajout d’un nouvel utilisateur, etc. Pour commencer, créez un fichier nommé PrivateConf.json avec le contenu suivant :

```bash
{  
	"reset_ssh":"True"
}
```

Ensuite, exécutez manuellement l’extension `VMAccessForLinux` pour réinitialiser votre connexion SSHD :

```bash
azure vm extension set <resource group> <vm name> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### Réinitialisation des informations d’identification SSH d’un utilisateur
Si SSHD semble fonctionner correctement, vous pouvez réinitialiser le mot de passe d’un utilisateur donné.

#### Interface de ligne de commande Azure
```bash
azure vm reset-access -g <resource group> <vm name> -u <username> -p <new password>
```

Si vous utilisez l’authentification par clé SSH, vous pouvez réinitialiser la clé SSH pour un utilisateur donné :

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <username> -M <~/.ssh/azure_id_rsa.pub>
```

#### Extension d’accès aux machines virtuelles
Créez un fichier nommé PrivateConf.json avec les éléments suivants :

```bash
{
	"username":"Username", "password":"NewPassword"
}
```

Ou pour réinitialiser la clé SSH pour un utilisateur donné, créez un fichier nommé PrivateConf.json avec les éléments suivants :

```bash
{
	"username":"Username", "ssh_key":"ContentsOfNewSSHKey"
}
```

Après avoir effectué l’une des étapes ci-dessus, vous exécutez manuellement l’extension `VMAccessForLinux` pour réinitialiser les informations d’identification d’utilisateur SSH :

```
azure vm extension set <resource group> <vmname> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### Redéploiement d’une machine virtuelle
Vous pouvez redéployer une machine virtuelle vers un autre nœud dans Azure, ce qui peut permettre de résoudre les problèmes de mise en réseau sous-jacents. Pour redéployer une machine virtuelle à l’aide du portail Azure, sélectionnez **Parcourir** > **Machines virtuelles** > *votre machine virtuelle Linux* > **Redéployer**. Pour plus d’informations sur la procédure à suivre, consultez [Redéployer une machine virtuelle vers un nouveau nœud Azure](virtual-machines-windows-redeploy-to-new-node.md). Pour le moment, vous ne pouvez pas redéployer une machine virtuelle à l’aide de l’interface de ligne de commande (CLI) Azure.

> [AZURE.NOTE] Notez qu’une fois cette opération terminée, les données de disque éphémères seront perdues et les adresses IP dynamiques associées à la machine virtuelle seront mises à jour.


## Machines virtuelles créées à l’aide du modèle de déploiement Classic

Procédez comme suit pour résoudre les problèmes de connexion SSH les plus courants sur les machines virtuelles créées à l’aide du modèle de déploiement Classic. Après chaque étape, essayez de vous reconnecter à la machine virtuelle.

- Réinitialisez l’accès à distance à partir du [portail Azure](https://portal.azure.com). Sur le portail Azure, sélectionnez **Parcourir** > **Machines virtuelles (classiques)** > *votre machine virtuelle Linux* > **Réinitialiser l’accès à distance...**.

- Redémarrez la machine virtuelle. Sur le [portail Azure](https://portal.azure.com), sélectionnez **Parcourir** > **Machines virtuelles (classiques)** > *votre machine virtuelle Linux* > **Redémarrer**.

	OU

	Sur le [portail Azure Classic](https://manage.windowsazure.com), sélectionnez **Machines virtuelles** > **Instances** > **Redémarrer**.

- Redéployez la machine virtuelle vers un nouveau nœud Azure. Pour plus d’informations sur la procédure à suivre, consultez [Redéployer une machine virtuelle vers un nouveau nœud Azure](virtual-machines-windows-redeploy-to-new-node.md).

	Notez qu’une fois cette opération terminée, les données de disque éphémères seront perdues et les adresses IP dynamiques associées à la machine virtuelle seront mises à jour.

- Suivez les instructions dans [Comment réinitialiser le service Bureau à distance ou son mot de passe de connexion dans une machine virtuelle Windows](virtual-machines-linux-classic-reset-access.md) pour :
	- réinitialiser le mot de passe ou la clé SSH ;
	- créer un nouveau compte d’utilisateur _sudo_ ;
	- réinitialiser la configuration SSH.

- Vérifiez l’intégrité des ressources de la machine virtuelle et recherchez s’il existe des problèmes de plateforme. <br> Sélectionnez **Parcourir** > **Machines virtuelles (classiques)** > *votre machine virtuelle Linux* > **Paramètres** > **Vérifier l’intégrité**.


## Ressources supplémentaires

- Si vous ne parvenez toujours pas à établir une connexion SSH à votre machine virtuelle une fois ces étapes effectuées, vous pouvez suivre les [étapes supplémentaires de dépannage détaillées](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) pour consulter des configurations de mise en réseau et des étapes supplémentaires susceptibles de résoudre votre problème.

- Pour plus d’informations sur la résolution des problèmes d’accès aux applications, consultez la page [Résolution des problèmes d’accès à une application exécutée sur une machine virtuelle Azure](virtual-machines-linux-troubleshoot-app-connection.md)

- Pour plus d’informations sur la résolution des problèmes des machines virtuelles créées à l’aide du modèle de déploiement Classic, consultez [Réinitialisation d’un mot de passe ou de SSH pour les machines virtuelles basées sur Linux](virtual-machines-linux-classic-reset-access.md).

<!---HONumber=AcomDC_0803_2016-->