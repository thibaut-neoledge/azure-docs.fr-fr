<properties linkid="manage-windows-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Capture de l'image d'une machine virtuelle exécutant Windows Server
===================================================================

Vous pouvez utiliser des images de Image Gallery pour créer rapidement des machines virtuelles ou bien vous pouvez capturer et utiliser vos propres images pour créer des machines virtuelles personnalisées. Les images sont des fichiers de disque dur virtuel (.vhd) qui sont utilisés comme modèles pour la création de machines virtuelles. Une image est considérée comme un modèle, car elle n'inclut aucun paramètre spécifique, tel que ceux liés au nom d'ordinateur et au compte d'utilisateur, contrairement à une machine virtuelle configurée. Si vous souhaitez créer plusieurs machines virtuelles configurées de la même manière, vous pouvez capturer l'image d'une machine virtuelle configurée et utiliser cette image comme modèle.

1.  Pour vous connecter à la machine virtuelle, suivez les étapes indiquées dans la rubrique [Connexion à une machine virtuelle exécutant Windows Server](http://www.windowsazure.com/en-us/manage/windows/how-to-guides/log-on-a-windows-vm/).

2.  Ouvrez une fenêtre d'invite de commandes en tant qu'administrateur.

3.  Remplacez le répertoire par `%windir%\system32\sysprep`, puis exécutez sysprep.exe.

4.  La boîte de dialogue **Outil de préparation système** apparaît.

    Dans **Action de nettoyage du système**, sélectionnez **Enter System Out-of-Box Experience (OOBE)** et vérifiez que la case à cocher **Generalize** est activée. Pour plus d'informations sur l'utilisation de Sysprep, consultez la page [Utilisation de Sysprep : introduction](http://technet.microsoft.com/en-us/library/bb457073.aspx).

5.  Dans **Shutdown Options**, sélectionnez **Shutdown**.

6.  Cliquez sur **OK**.

7.  Sysprep arrête la machine virtuelle, ce qui définit l'état de la machine dans le [portail de gestion](http://manage.windowsazure.com) sur **Arrêté**.

8.  Cliquez sur **Virtual Machines**, puis sélectionnez la machine virtuelle à capturer.

9.  Dans la barre de commandes, cliquez sur **Capture**.

    La boîte de dialogue **Capture an Image from a Virtual Machine** s'affiche.

10. Dans **Image Name**, entrez le nom de la nouvelle image.

11. Avant d'ajouter une image Windows Server à votre jeu d'images personnalisées, elle doit être généralisée en exécutant Sysprep comme indiqué dans les étapes précédentes. Cliquez sur **I have run Sysprep on the virtual machine** pour indiquer que la généralisation a été effectuée.

12. Cliquez sur la coche pour capturer l'image. Lorsque vous capturez l'image d'une machine virtuelle, la machine est supprimée.

    La nouvelle image est disponible dans **Images**.

    Lorsque vous créez une machine virtuelle avec la méthode **From Gallery**, vous pouvez utiliser l'image capturée en cliquant sur **My Images** sur la page **Choose an Image**.


