<properties urlDisplayName="Capture an image" pageTitle="Capture de l'image d'une machine virtuelle exécutant Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="kathydav" />

#Capture d'une machine virtuelle Windows pour l'utiliser comme modèle#

Cet article vous montre comment capturer une machine virtuelle Azure exécutant Windows pour l'utiliser comme modèle afin de créer d'autres machines virtuelles. Ce modèle comprend le disque du système d'exploitation ainsi que les disques de données attachés à la machine virtuelle. Il ne comprend pas la configuration réseau, vous devez donc la configurer lors de la création d'autres machines virtuelles utilisant le modèle.

Azure traite ce modèle comme une image et le stocke sous **Mes images**. C'est aussi là que sont stockées les images que vous avez chargées. Pour plus d'informations sur les images, consultez [À propos des images de machines virtuelles dans Azure] [].

##Avant de commencer##

Ces étapes partent du principe que vous avez déjà créé une machine virtuelle Azure, configuré le système d'exploitation et attaché les disques de données. Si vous ne l'avez pas encore fait, consultez ces instructions :

- [Création d'une machine virtuelle personnalisée] []
- [Association d'un disque de données avec une machine virtuelle] []

##Capture de la machine virtuelle##

1. Connectez-vous à la machine virtuelle en cliquant sur **Connecter** dans la barre de commandes. Pour plus d'informations, consultez [Connexion à une machine virtuelle exécutant Windows Server] [].

2.	Ouvrez une fenêtre d'invite de commandes en tant qu'administrateur.


3.	Basculez vers le répertoire `%windir%\system32\sysprep`, puis exécutez sysprep.exe.


4. 	La boîte de dialogue **Outil de préparation système** apparaît. Effectuez les actions suivantes :


	- Dans **Action de nettoyage du système**, sélectionnez **Enter System Out-of-Box Experience (OOBE)** et vérifiez que la case à cocher **Generalize** est activée. Pour plus d'informations sur l'utilisation de Sysprep, consultez la page [Utilisation de Sysprep : introduction][].

	- Dans **Shutdown Options**, sélectionnez **Shutdown**.

	- Cliquez sur **OK**.

	![Run Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep arrête la machine virtuelle, ce qui définit l'état de la machine virtuelle dans le [portail de gestion](http://manage.windowsazure.com) sur **Arrêté**.


8.	Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle à capturer.

9.	Dans la barre de commandes, cliquez sur **Capturer**.

	![Capture virtual machine](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	La boîte de dialogue **Capturer la machine virtuelle** s'affiche.

10.	Dans **Nom de l'image**, entrez le nom de la nouvelle image.

11.	Avant d'ajouter une image Windows Server à votre jeu d'images personnalisées, elle doit être généralisée en exécutant Sysprep comme indiqué dans les étapes précédentes. Cliquez sur **J'ai exécuté Sysprep sur la machine virtuelle** pour indiquer que la généralisation a été effectuée.

12.	Cliquez sur la coche pour capturer l'image. Lorsque vous capturez l'image d'une machine virtuelle généralisée, la machine virtuelle est supprimée.

	La nouvelle image est désormais disponible dans **Images**.

	![Image capture successful](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Étapes suivantes##
L'image est prête à être utilisée comme modèle pour la création de machines virtuelles. Pour ce faire, vous créerez une machine virtuelle personnalisée en utilisant l'option **À partir de la galerie** et en sélectionnant l'image que vous venez de créer. Pour obtenir des instructions, consultez le guide [Création d'une machine virtuelle personnalisée] [].

	
[À propos des machines virtuelles dans Azure]: http://msdn.microsoft.com/fr-fr/library/azure/dn790290.aspx
[Création d'une machine virtuelle personnalisée]: ../virtual-machines-create-custom/
[Association d'un disque de données avec une machine virtuelle]: ../storage-windows-attach-disk/
[Connexion à une machine virtuelle exécutant Windows Server]:http://www.windowsazure.com/fr-fr/manage/windows/how-to-guides/log-on-a-windows-vm/
[Utilisation de Sysprep : introduction]:http://technet.microsoft.com/fr-fr/library/bb457073.aspx
[Exécuter Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Entrer des options Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[La machine virtuelle est arrêtée]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capturer une image de la machine virtuelle]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Entrer le nom de l'image]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Capture d'image réussie]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Utiliser l'image capturée]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!--HONumber=35_1-->
