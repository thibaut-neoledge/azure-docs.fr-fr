<properties linkid="manage-linux-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Linux" metaKeywords="Azure Linux vm, Linux vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Capture de l'image d'une machine virtuelle Linux
================================================

Vous pouvez utiliser des images de Image Gallery pour créer rapidement des machines virtuelles ou bien vous pouvez capturer et utiliser vos propres images pour créer des machines virtuelles personnalisées. Les images sont des fichiers de disque dur virtuel (.vhd) qui sont utilisés comme modèles pour la création de machines virtuelles. Une image est considérée comme un modèle, car elle n'inclut aucun paramètre spécifique, tel que ceux liés au nom d'hôte et au compte d'utilisateur, contrairement à une machine virtuelle configurée. Si vous souhaitez créer plusieurs machines virtuelles configurées de la même manière, vous pouvez capturer l'image d'une machine virtuelle configurée et utiliser cette image comme modèle.

1.  Connectez-vous à la machine virtuelle en suivant les étapes indiquées dans la rubrique [Connexion à une machine virtuelle exécutant Linux](../virtual-machines-linux-how-to-log-on).

2.  Dans la fenêtre SSH, tapez la commande suivante, puis entrez le mot de passe du compte que vous avez créé sur la machine virtuelle. Notez que le résultat de `waagent` peut varier légèrement en fonction de la version utilisée :

    `sudo waagent -deprovision`

    ![Annuler l'approvisionnement de la machine virtuelle](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)

3.  Tapez **y** pour continuer.

    ![Annulation de l'approvisionnement de la machine virtuelle réussie](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

4.  Tapez **Exit** pour fermer le client SSH.

5.  Dans le [portail de gestion](http://manage.windowsazure.com), sélectionnez la machine virtuelle, puis cliquez sur **Shutdown**.

    ![Arrêter la machine virtuelle](./media/virtual-machines-linux-capture-image/ShutdownVM.png)

6.  Cliquez sur **Yes** pour accepter le fait que vous continuerez à être facturé pour la machine virtuelle lorsqu'elle ne s'exécute pas.

7.  Une fois la machine virtuelle arrêtée, dans la barre de commandes, cliquez sur **Capture**.

    ![Capturer l'image de la machine virtuelle](./media/virtual-machines-linux-capture-image/CaptureVM.png)

    La boîte de dialogue **Capture Virtual Machine** s'affiche.

    ![Entrer les détails de la capture](./media/virtual-machines-linux-capture-image/CaptureLinux.png)

8.  Dans **Image Name**, entrez le nom de la nouvelle image.

9.  L'approvisionnement de toutes les images Linux doit être *annulé* à l'aide de la commande `waagent` avec l'option `-deprovision`. Cliquez sur **I have run the de-provision command on the Virtual Machine** pour indiquer que le système d'exploitation être prêt à être converti en image.

10. Cliquez sur la coche pour capturer l'image.

    La nouvelle image est disponible dans **Images**. La machine virtuelle est supprimée une fois l'image capturée.

    ![Capture d'image réussie](./media/virtual-machines-linux-capture-image/CaptureSuccess.png)

    Lorsque vous créez une machine virtuelle avec la méthode **From Gallery**, vous pouvez utiliser l'image capturée en cliquant sur **My Images** sur la page **Select the virtual machine operating system** page.


