<properties
	pageTitle="Capturer l’image d'une machine virtuelle Linux | Microsoft Azure"
	description="Apprenez à capturer l’image d’une machine virtuelle Azure sous Linux créée avec le modèle de déploiement classique."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="dkshir"/>


# Capture d'une machine virtuelle Linux classique en tant qu'image

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-linux-capture-image.md).


Cet article vous montre comment capturer une machine virtuelle Azure classique exécutant Linux en tant qu'image pour créer d'autres machines virtuelles. Cette image comprend le disque du système d'exploitation ainsi que les disques de données attachés à la machine virtuelle. Elle ne comprend pas la configuration réseau, vous devez donc la configurer lors de la création d'autres machines virtuelles à partir de l'image.

Azure stocke l'image sous **Images**. C’est aussi là que sont stockées les images que vous avez chargées. Pour plus d’informations sur les images, voir l’article [À propos des images de machine virtuelle dans Azure][].

## Avant de commencer

Ces étapes partent du principe que vous avez déjà créé une machine virtuelle Azure à l’aide du modèle de déploiement classique, configuré le système d'exploitation et attaché les disques de données. Si vous ne l’avez pas encore fait, consultez [Comment créer une machine virtuelle Linux][].


## Capture de la machine virtuelle

1. Connectez-vous à la machine virtuelle à l'aide d'un client SSH de votre choix. Pour plus d'informations, consultez [Connexion à une machine virtuelle exécutant Linux][].

2. Dans la fenêtre SSH, tapez la commande suivante. Notez que le résultat de `waagent` peut varier légèrement en fonction de la version utilisée :

	`sudo waagent -deprovision+user`

	Cette commande essaie de nettoyer le système et de le préparer pour le réapprovisionnement. Cette opération effectue les tâches suivantes :

	- supprime toutes les clés de l'hôte SSH (si Provisioning.RegenerateSshHostKeyPair a la valeur « y » dans le fichier de configuration) ;
	- efface la configuration de Nameserver dans /etc/resolv.conf ;
	- supprime le mot de passe `root` de l’utilisateur de /etc/shadow (si Provisioning.DeleteRootPassword a la valeur « y » dans le fichier de configuration) ;
	- supprime les baux du client DHCP mis en cache ;
	- Réinitialise le nom d'hôte sur localhost.localdomain.
	- efface le dernier compte d’utilisateur approvisionné (obtenu de /var/lib/waagent) **et les données associées**.

	>[AZURE.NOTE] L’annulation de l’approvisionnement supprime les fichiers et les données dans le but de « généraliser » l’image. Exécutez uniquement cette commande sur des machines virtuelles que vous souhaitez capturer dans un nouveau modèle d'image. Cela ne garantit pas que l’image est exempte de toute information sensible ou qu’elle convient pour la redistribution à des tiers.


3. Tapez **y** pour continuer. Vous pouvez ajouter le paramètre `-force` pour éviter cette étape de confirmation.

4. Tapez **Exit** pour fermer le client SSH.


	>[AZURE.NOTE] Les étapes suivantes supposent que vous avez déjà [installé la CLI Azure](../xplat-cli-install.md) sur votre ordinateur client. Toutes les étapes ci-dessous peuvent également être effectuées dans le [portail Azure Classic][].

5. À partir de votre ordinateur client, ouvrez la CLI Azure et connectez-vous à votre abonnement Azure. Pour plus d’informations, consultez [Se connecter à un abonnement Azure à partir de la CLI Azure](../xplat-cli-connect.md).

6. Assurez-vous que vous êtes en mode de gestion des services :

	`azure config mode asm`

7. Arrêtez la machine virtuelle déjà déprovisionnée dans les étapes ci-dessus avec :

	`azure vm shutdown <your-virtual-machine-name>`

	>[AZURE.NOTE] Vous trouverez toutes les machines virtuelles créées dans votre abonnement à l'aide de `azure vm list`

8. Une fois la machine virtuelle arrêtée, capturez l’image avec la commande :

	`azure vm capture -t <your-virtual-machine-name> <new-image-name>`

	Tapez le nom d’image que vous souhaitez à la place de _new-image-name_. Cette commande crée une image de système d'exploitation généralisée. La sous-commande `-t` supprime la machine virtuelle d’origine.

9.	La nouvelle image est maintenant disponible dans la liste des images pouvant être utilisées pour configurer de nouvelles machines virtuelles. Vous pouvez l'afficher avec la commande :

	`azure vm image list`

	Dans le [portail Azure Classic][], elle apparaît dans la liste **IMAGES**.

	![Capture d'image réussie](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)


## Étapes suivantes
L'image est prête à être utilisée pour créer des machines virtuelles. Vous pouvez utiliser la commande `azure vm create` de la CLI Azure et fournir le nom de l’image que vous venez de créer. Pour plus d'informations sur la commande, consultez [Utilisation de l'interface de ligne de commande Microsoft Azure pour Mac, Linux et Windows avec la gestion des services Azure](../virtual-machines-command-line-tools.md). Vous pouvez également utiliser le [portail Azure Classic][] pour créer une machine virtuelle personnalisée en utilisant l'option **À partir de la galerie** et en sélectionnant l'image que vous venez de créer. Pour plus d’informations, consultez [Création d’une machine virtuelle personnalisée][].

**Voir aussi :** [Guide d’utilisateur de l’agent Linux Azure](virtual-machines-linux-agent-user-guide.md)

[portail Azure Classic]: http://manage.windowsazure.com
[Connexion à une machine virtuelle exécutant Linux]: virtual-machines-linux-classic-log-on.md
[À propos des images de machine virtuelle dans Azure]: virtual-machines-linux-classic-about-images.md
[Création d’une machine virtuelle personnalisée]: virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: virtual-machines-windows-classic-attach-disk.md
[Comment créer une machine virtuelle Linux]: virtual-machines-linux-classic-create-custom.md

<!---HONumber=AcomDC_0330_2016-->