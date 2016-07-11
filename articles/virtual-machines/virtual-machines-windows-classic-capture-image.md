<properties
	pageTitle="Capture d’une image de machine virtuelle Microsoft Azure | Microsoft Azure"
	description="Capturer l’image d’une machine virtuelle Microsoft Azure créée avec le modèle de déploiement classique"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="cynthn"/>

#Capturer l’image d’une machine virtuelle Microsoft Azure créée avec le modèle de déploiement classique

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](virtual-machines-windows-capture-image.md).


Cet article vous montre comment capturer une machine virtuelle Azure exécutant Windows de façon à l’utiliser comme image pour créer d’autres machines virtuelles. Cette image contient le disque du système d’exploitation et les disques de données éventuellement associés à la machine virtuelle. Comme elle ne comporte pas de configurations de mise en réseau, vous devez effectuer ces tâches de configuration quand vous créez d’autres machines virtuelles à partir de l’image.

Azure stocke l’image sous **Mes images**. Il s’agit de l’emplacement où sont stockées les images que vous avez éventuellement téléchargées. Pour plus d’informations sur les images, consultez la page [À propos des images pour les machines virtuelles](virtual-machines-linux-classic-about-images.md).

##Avant de commencer##

Ces étapes partent du principe que vous avez déjà créé une machine virtuelle Azure, configuré le système d’exploitation et attaché les disques de données. Si vous ne l’avez pas encore fait, voir ces instructions :

- [Création d’une machine virtuelle à partir d’une image](virtual-machines-windows-classic-createportal.md)
- [Comment attacher un disque de données à une machine virtuelle](virtual-machines-windows-classic-attach-disk.md)

> [AZURE.WARNING] Ce processus supprime la machine virtuelle d’origine une fois celle-ci capturée.

Avant de capturer une image d’une machine virtuelle Azure, nous vous recommandons de sauvegarder la machine virtuelle cible. Les machines virtuelles Azure peuvent être sauvegardées à l’aide d’Azure Backup. Pour plus d’informations, voir [Sauvegarde des machines virtuelles Azure](../backup/backup-azure-vms.md). D’autres solutions sont disponibles auprès de partenaires certifiés. Pour savoir ce qui est actuellement disponible, faites une recherche dans Azure Marketplace.


##Capture de la machine virtuelle

1. Dans le [portail Azure Classic](http://manage.windowsazure.com), **connectez-vous** à la machine virtuelle. Pour obtenir des instructions, consultez [Comment se connecter à une machine virtuelle exécutant Windows Server][].

2.	Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.

3.	Accédez au répertoire `%windir%\system32\sysprep`, puis exécutez sysprep.exe.

4. 	La boîte de dialogue **Outil de préparation système** apparaît. Effectuez les actions suivantes :

	- Dans **Action de nettoyage du système**, sélectionnez **Enter System Out-of-Box Experience (OOBE)** et vérifiez que la case à cocher **Generalize** est activée. Pour plus d’informations sur l’utilisation de Sysprep, voir la page [Introduction à l’utilisation de Sysprep][].

	- Dans **Shutdown Options**, sélectionnez **Shutdown**.

	- Cliquez sur **OK**.

	![Exécutez Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)

7.	Sysprep arrête la machine virtuelle, ce qui a pour effet de modifier l’état de celle-ci en **Arrêté** dans le portail Azure Classic.

8.	Dans le portail Azure Classic, cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle à capturer.

9.	Dans la barre de commandes, cliquez sur **Capture**.

	![Capture machine virtuelle](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)

	La boîte de dialogue **Capture the Virtual Machine** s’affiche.

10.	Dans **Image Name**, entrez le nom de la nouvelle image.

11.	Avant d’ajouter une image Windows Server à votre jeu d’images personnalisées, elle doit être généralisée en exécutant Sysprep comme indiqué dans les étapes précédentes. Cliquez sur **J’ai exécuté Sysprep sur la machine virtuelle** pour indiquer que vous l’avez fait.

12.	Cliquez sur la coche pour capturer l’image. La nouvelle image est disponible dans **Images**.

 	![Capture d’image réussie](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

##Étapes suivantes

L’image est prête à être utilisée pour créer des machines virtuelles. Pour cela, créez une machine virtuelle en utilisant l’élément de menu **À partir de la galerie**, puis en sélectionnant l’image que vous venez de créer. Pour obtenir des instructions, consultez [Création d’une machine virtuelle à partir d’une image](virtual-machines-windows-classic-createportal.md).



[Comment se connecter à une machine virtuelle exécutant Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Introduction à l’utilisation de Sysprep]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=AcomDC_0629_2016-->