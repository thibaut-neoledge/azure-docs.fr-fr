<properties
	pageTitle="Capture de l'image d'une machine virtuelle exécutant Linux"
	description="Apprenez à capturer une image d'une machine virtuelle Azure exécutant Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/11/2015"
	ms.author="dkshir"/>


# Capture d’une machine virtuelle Linux à utiliser comme modèle##

Cet article vous montre comment capturer une machine virtuelle Azure exécutant Linux pour l'utiliser comme modèle afin de créer d'autres machines virtuelles. Ce modèle comprend le disque du système d’exploitation ainsi que les disques de données attachés à la machine virtuelle. Il ne comprend pas la configuration réseau, vous devez donc la configurer lors de la création d’autres machines virtuelles utilisant le modèle.

Microsoft Azure traite ce modèle comme une image et le stocke sous **Mes Images**. C’est aussi là que sont stockées les images que vous avez chargées. Pour plus d’informations sur les images, voir l’article [À propos des images de machine virtuelle dans Azure][].

##Avant de commencer##

Ces étapes partent du principe que vous avez déjà créé une machine virtuelle Azure, configuré le système d'exploitation et attaché les disques de données. Si vous ne l’avez pas encore fait, consultez ces instructions :

- [Création d’une machine virtuelle personnalisée][]
- [Association d’un disque de données à une machine virtuelle][]

##Capture de la machine virtuelle##

1. Connectez-vous à la machine virtuelle en cliquant sur **Connecter** dans la barre de commandes. Pour plus d'informations, consultez [Connexion à une machine virtuelle exécutant Linux][].

2. Dans la fenêtre SSH, tapez la commande suivante. Notez que le résultat de `waagent` peut varier légèrement en fonction de la version utilisée :

	`$ sudo waagent -deprovision+user`

	Cette commande essaie de nettoyer le système et de le préparer pour le réapprovisionnement. Cette opération effectue les tâches suivantes :

	- supprime toutes les clés de l'hôte SSH (si Provisioning.RegenerateSshHostKeyPair a la valeur « y » dans le fichier de configuration) ;
	- efface la configuration de Nameserver dans /etc/resolv.conf ;
	- supprime le mot de passe `root` de l’utilisateur de /etc/shadow (si Provisioning.DeleteRootPassword a la valeur « y » dans le fichier de configuration) ;
	- supprime les baux du client DHCP mis en cache ;
	- Réinitialise le nom d'hôte sur localhost.localdomain.
	- efface le dernier compte d’utilisateur approvisionné (obtenu de /var/lib/waagent) **et les données associées**.

	![Annuler l'approvisionnement de la machine virtuelle](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)

	>[AZURE.NOTE]L’annulation de l’approvisionnement supprime les fichiers et les données dans le but de « généraliser » l’image. Exécutez uniquement cette commande sur des machines virtuelles que vous souhaitez capturer dans un nouveau modèle d’image.


3. Tapez **y** pour continuer.

	![Annulation de l'approvisionnement de la machine virtuelle réussie](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

	**Notez** que vous pouvez ajouter le paramètre `-force` pour éviter cette étape de confirmation.

	>[AZURE.NOTE]L’annulation de l’approvisionnement généralise uniquement l’image Linux, mais ne garantit pas que l’image est exempte de toute information sensible et qu’elle convient pour la redistribution à des tiers.


4. Tapez **Exit** pour fermer le client SSH.

5. Dans le [portail de gestion](http://manage.windowsazure.com), sélectionnez la machine virtuelle, puis cliquez sur **Shut down**.

6. Une fois la machine virtuelle arrêtée, cliquez sur **Capture** dans la barre de commande pour ouvrir la boîte de dialogue **Capture the Virtual Machine**.

7.	Dans **Image Name**, entrez le nom de la nouvelle image.

8.	L’approvisionnement de toutes les images Linux doit être *annulé* à l’aide de la commande `waagent` avec l’option `-deprovision`. Cliquez sur **I have run waagent-deprovision on the virtual machine** pour indiquer que le système d'exploitation être prêt à être converti en image.

9.	Cliquez sur la coche pour capturer l’image.

	La nouvelle image est disponible dans **Images**. La machine virtuelle est supprimée une fois l'image capturée.

	![Capture d'image réussie](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)


##Étapes suivantes##
L'image est prête à être utilisée comme modèle pour la création de machines virtuelles. Pour ce faire, vous créerez une machine virtuelle personalisée en utilisant la méthode **From Gallery** et en sélectionnant l'image que vous venez de créer. Pour obtenir des instructions, consultez le guide [Création d'une machine virtuelle personnalisée][].

**Voir aussi :** [Guide d’utilisateur de l’agent Linux Azure](virtual-machines-linux-agent-user-guide.md)

[Connexion à une machine virtuelle exécutant Linux]: virtual-machines-linux-how-to-log-on.md
[À propos des images de machine virtuelle dans Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[Création d'une machine virtuelle personnalisée]: virtual-machines-create-custom.md
[Création d’une machine virtuelle personnalisée]: virtual-machines-create-custom.md
[Association d’un disque de données à une machine virtuelle]: storage-windows-attach-disk.md

<!---HONumber=July15_HO3-->