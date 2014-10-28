<properties linkid="manage-linux-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Linux" metaKeywords="Azure Linux vm, Linux vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Capture d'une machine virtuelle Linux pour l'utiliser comme modèle

Cet article vous montre comment capturer une machine virtuelle Linux pour l'utiliser comme modèle afin de créer d'autres machines virtuelles. Ce modèle de machine virtuelle comprend le disque du système d'exploitation ainsi que les disques de données attachés à la machine virtuelle. Il ne comprend pas la configuration réseau, vous devez donc la configurer lors de la création d'autres machines virtuelles utilisant le modèle.

Après la capture de la machine virtuelle, celle-ci est disponible dans **Mes images** lorsque vous créez une machine virtuelle. Le fichier image est conservé sous la forme d'un disque dur virtuel dans votre compte de stockage. Pour plus d'informations concernant les images, consultez [Gestion des disques et des images][].

## Avant de commencer

Ces étapes partent du principe que vous avez déjà créé une machine virtuelle Azure, configuré le système d'exploitation et attaché les disques de données. Si vous ne l'avez pas encore fait, consultez ces instructions :

-   [Création d’une machine virtuelle personnalisée][]
-   [Association d’un disque de données avec une machine virtuelle][]

## Capture de la machine virtuelle

1.  Connectez-vous à la machine virtuelle en cliquant sur **Connecter** dans la barre de commandes. Pour plus d'informations, consultez [Connexion à une machine virtuelle exécutant Linux][].

2.  Dans la fenêtre SSH, tapez la commande suivante, puis entrez le mot de passe du compte que vous avez créé sur la machine virtuelle. Notez que le résultat de `waagent` peut varier légèrement en fonction de la version utilisée :

    `sudo waagent -deprovision`

    ![Annuler l'approvisionnement de la machine virtuelle][]

3.  Tapez **y** pour continuer.

    ![Annulation de l'approvisionnement de la machine virtuelle réussie][]

4.  Tapez **Exit** pour fermer le client SSH.

5.  Dans le [portail de gestion][], sélectionnez la machine virtuelle, puis cliquez sur **Shut down**.

6.  Une fois la machine virtuelle arrêtée, cliquez sur **Capture** dans la barre de commande pour ouvrir la boîte de dialogue **Capture the Virtual Machine**.

7.  Dans **Image Name**, entrez le nom de la nouvelle image.

8.  L'approvisionnement de toutes les images Linux doit être *annulé* à l'aide de la commande `waagent` avec l'option `-deprovision`. Cliquez sur **I have run waagent-deprovision on the virtual machine** pour indiquer que le système d'exploitation être prêt à être converti en image.

9.  Cliquez sur la coche pour capturer l'image.

    La nouvelle image est disponible dans **Images**. La machine virtuelle est supprimée une fois l'image capturée.

    ![Capture d'image réussie][]

## Étapes suivantes

L'image est prête à être utilisée comme modèle pour la création de machines virtuelles. Pour ce faire, vous créerez une machine virtuelle personalisée en utilisant la méthode **From Gallery** et en sélectionnant l'image que vous venez de créer. Pour obtenir des instructions, consultez le guide [Création d'une machine virtuelle personnalisée][Création d’une machine virtuelle personnalisée].

  [Gestion des disques et des images]: http://go.microsoft.com/fwlink/p/?LinkId=397536
  [Création d’une machine virtuelle personnalisée]: ../virtual-machines-create-custom/
  [Association d’un disque de données avec une machine virtuelle]: ../storage-windows-attach-disk/
  [Connexion à une machine virtuelle exécutant Linux]: ../virtual-machines-linux-how-to-log-on
  [Annuler l'approvisionnement de la machine virtuelle]: ./media/virtual-machines-linux-capture-image/LinuxDeprovision.png
  [Annulation de l'approvisionnement de la machine virtuelle réussie]: ./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png
  [portail de gestion]: http://manage.windowsazure.com
  [Capture d'image réussie]: ./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png
