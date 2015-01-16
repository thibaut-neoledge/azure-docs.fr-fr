<properties urlDisplayName="Capture an image" pageTitle="Capture de l'image d'une machine virtuelle exécutant Linux" metaKeywords="Azure Linux vm, Linux vm" description="Apprenez à capturer une image d'une machine virtuelle Azure exécutant Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/25/2014" ms.author="kathydav" />


# Capture d'une machine virtuelle Linux à utiliser comme modèle##

Cet article vous montre comment capturer une machine virtuelle Azure exécutant Linux pour l'utiliser comme modèle afin de créer d'autres machines virtuelles. Ce modèle comprend le disque du système d'exploitation ainsi que les disques de données attachés à la machine virtuelle. Il ne comprend pas la configuration réseau, vous devez donc la configurer lors de la création d'autres machines virtuelles utilisant le modèle.

Azure traite ce modèle comme une image et le stocke sous **Mes images**. C'est aussi là que sont stockées les images que vous avez chargées. Pour plus d'informations sur les images, consultez [À propos des images de machines virtuelles dans Azure] [].

##Avant de commencer##

Ces étapes partent du principe que vous avez déjà créé une machine virtuelle Azure, configuré le système d'exploitation et attaché les disques de données. Si vous ne l'avez pas encore fait, consultez ces instructions :

- [Création d'une machine virtuelle personnalisée] []
- [Association d'un disque de données avec une machine virtuelle] []

##Capture de la machine virtuelle##

1. Connectez-vous à la machine virtuelle en cliquant sur **Connecter** dans la barre de commandes. Pour plus d'informations, consultez [Connexion à une machine virtuelle exécutant Linux][].

2. Dans la fenêtre SSH, tapez la commande suivante, puis entrez le mot de passe du compte que vous avez créé sur la machine virtuelle.  Notez que le résultat de `waagent` peut varier légèrement en fonction de la version utilisée :

	`sudo waagent -deprovision`

	![Deprovision the virtual machine](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)


3. Tapez **y** pour continuer.

	![Deprovision of virtual machine successful](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

4. Tapez **Exit** pour fermer le client SSH.

5. Dans le [portail de gestion](http://manage.windowsazure.com), sélectionnez la machine virtuelle, puis cliquez sur **Arrêter**.

6. Une fois la machine virtuelle arrêtée, cliquez sur **Capturer** dans la barre de commande pour ouvrir la boîte de dialogue **Capturer la machine virtuelle**.

7.	Dans **Nom de l'image**, entrez le nom de la nouvelle image.

8.	L'approvisionnement de toutes les images Linux doit être *annulé* à l'aide de la commande `waagent` avec l'option `-deprovision`. Cliquez sur **J'ai exécuté waagent -deprovision sur la machine virtuelle.** pour indiquer que le système d'exploitation être prêt à être converti en image.

9.	Cliquez sur la coche pour capturer l'image.

	La nouvelle image est désormais disponible dans **Images**. La machine virtuelle est supprimée une fois l'image capturée.

	![Image capture successful](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)

##Étapes suivantes##
L'image est prête à être utilisée comme modèle pour la création de machines virtuelles. Pour ce faire, vous créerez une machine virtuelle personnalisée en utilisant l'option **À partir de la galerie** et en sélectionnant l'image que vous venez de créer. Pour obtenir des instructions, consultez le guide [Création d'une machine virtuelle personnalisée] [].
	
[Connexion à une machine virtuelle exécutant Linux]: ../virtual-machines-linux-how-to-log-on
[À propos des machines virtuelles dans Azure]: http://msdn.microsoft.com/fr-fr/library/azure/dn790290.aspx
[Création d'une machine virtuelle personnalisée]: ../virtual-machines-create-custom/
[Association d'un disque de données avec une machine virtuelle]: ../storage-windows-attach-disk/


<!--HONumber=35.1-->
