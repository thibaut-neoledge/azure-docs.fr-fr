<properties
	pageTitle="Capturer l’image d'une machine virtuelle Microsoft Azure | Microsoft Azure"
	description="Capturer l’image d’une machine virtuelle Microsoft Azure créée avec le modèle de déploiement classique"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/05/2015"
	ms.author="cynthn"/>

#Capturer l’image d’une machine virtuelle Microsoft Azure créée avec le modèle de déploiement classique

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Cet article vous montre comment capturer une machine virtuelle Azure exécutant Windows de façon à l’utiliser comme image pour créer d’autres machines virtuelles. Cette image contient le disque du système d'exploitation et les disques de données éventuellement associés à la machine virtuelle. Comme elle ne comporte pas de configurations de mise en réseau, vous devez effectuer ces tâches de configuration quand vous créez d’autres machines virtuelles à partir de l’image.

Azure stocke l'image sous **Mes images**. Il s’agit de l’emplacement où sont stockées les images que vous avez éventuellement téléchargées. Pour plus d’informations sur les images, consultez la page [À propos des images pour les machines virtuelles](virtual-machines-images.md).

##Avant de commencer##

Ces étapes partent du principe que vous avez déjà créé une machine virtuelle Azure, configuré le système d'exploitation et attaché les disques de données. Si vous ne l’avez pas encore fait, consultez ces instructions :

- [Créer une machine virtuelle à partir d’une image](virtual-machines-create-custom.md)
- [Comment attacher un disque de données à une machine virtuelle](storage-windows-attach-disk.md)

> [AZURE.WARNING]Ce processus supprime la machine virtuelle d'origine une fois celle-ci capturée.

Il ne doit pas être considéré comme un moyen de sauvegarder une machine virtuelle. Pour cela, vous pouvez utiliser Azure Backup, qui est disponible sous forme de version préliminaire dans certaines régions. Pour plus d’informations, consultez la page [Sauvegarde des machines virtuelles Azure](../backup/backup-azure-vms.md). D’autres solutions sont disponibles auprès de partenaires certifiés. Pour savoir ce qui est actuellement disponible, faites une recherche dans Azure Marketplace.


##Capture de la machine virtuelle

1. Dans le [portail Azure](http://manage.windowsazure.com), **connectez-vous** à la machine virtuelle. Pour obtenir des instructions, consultez la page [Comment se connecter à une machine virtuelle exécutant Windows Server][].

2.	Ouvrez une fenêtre d'invite de commandes en tant qu'administrateur.

3.	Accédez au répertoire `%windir%\system32\sysprep`, puis exécutez sysprep.exe.

4. 	La boîte de dialogue **Outil de préparation système** apparaît. Effectuez les actions suivantes :

	- Dans **Action de nettoyage du système**, sélectionnez **Enter System Out-of-Box Experience (OOBE)** et vérifiez que la case à cocher **Generalize** est activée. Pour plus d'informations sur l'utilisation de Sysprep, consultez la page [Introduction à l'utilisation de Sysprep][].

	- Dans **Shutdown Options**, sélectionnez **Shutdown**.

	- Cliquez sur **OK**.

	![Exécutez Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep arrête la machine virtuelle, ce qui a pour effet de modifier son état dans le portail Azure, qui passe alors à **Arrêté**.

8.	Dans le portail Azure, cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle à capturer.

9.	Dans la barre de commandes, cliquez sur **Capture**.

	![Capture machine virtuelle](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	La boîte de dialogue **Capture the Virtual Machine** s'affiche.

10.	Dans **Image Name**, entrez le nom de la nouvelle image.

11.	Avant d'ajouter une image Windows Server à votre jeu d'images personnalisées, elle doit être généralisée en exécutant Sysprep comme indiqué dans les étapes précédentes. Cliquez sur **J’ai exécuté Sysprep sur la machine virtuelle** pour indiquer que vous l’avez fait.

12.	Cliquez sur la coche pour capturer l’image. La nouvelle image est disponible dans **Images**.

 	![Capture d'image réussie](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Étapes suivantes

L'image est prête à être utilisée pour créer des machines virtuelles. Pour cela, créez une machine virtuelle en utilisant l’élément de menu **Depuis la galerie** et sélectionnez l'image que vous venez de créer. Pour obtenir des instructions, consultez [Création d'une machine virtuelle à partir d’une image](virtual-machines-create-custom.md).



[Comment se connecter à une machine virtuelle exécutant Windows Server]: virtual-machines-log-on-windows-server.md
[Introduction à l'utilisation de Sysprep]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=Nov15_HO3-->