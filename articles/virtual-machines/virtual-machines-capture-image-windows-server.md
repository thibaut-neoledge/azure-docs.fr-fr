<properties
	pageTitle="Capture de l’image d’une machine virtuelle exécutant Windows Server"
	description="Apprenez à capturer une image d’une machine virtuelle Azure exécutant Windows Server 2008 R2."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/13/2015"
	ms.author="kathydav"/>

#Capture d’une machine virtuelle Windows à utiliser comme modèle#

Cet article vous montre comment capturer une machine virtuelle Azure exécutant Windows pour l’utiliser comme modèle afin de créer d’autres machines virtuelles. Ce modèle contient le disque du système d’exploitation et tous les disques de données associés à la machine virtuelle. Il ne comprend pas la configuration réseau et vous devez donc la configurer au moment de créer d’autres machines virtuelles utilisant le modèle.

Microsoft Azure traite ce modèle comme une image et le stocke sous **Mes Images**. C’est aussi là que sont stockées les images que vous avez chargées. Pour plus d’informations sur les images, voir la section [À propos des images de machine virtuelle dans Azure][].

##Avant de commencer##

Ces étapes partent du principe que vous avez déjà créé une machine virtuelle Azure, configuré le système d'exploitation et attaché les disques de données. Si vous ne l’avez pas encore fait, consultez ces instructions :

- [Création d’une machine virtuelle personnalisée][]
- [Association d’un disque de données à une machine virtuelle][]

##Capture de la machine virtuelle##

1. Connectez-vous à la machine virtuelle en cliquant sur **Connecter** dans la barre de commandes. Pour plus d’informations, consultez la section [Connexion à une machine virtuelle exécutant Windows Server][].

2.	Ouvrez une fenêtre d'invite de commandes en tant qu'administrateur.


3.	Accédez au répertoire `%windir%\system32\sysprep`, puis exécutez sysprep.exe.


4. 	La boîte de dialogue **Outil de préparation système** apparaît. Effectuez les actions suivantes :


	- Dans **Action de nettoyage du système**, sélectionnez **Enter System Out-of-Box Experience (OOBE)** et vérifiez que la case à cocher **Generalize** est activée. Pour plus d'informations sur l'utilisation de Sysprep, consultez la page [Introduction à l'utilisation de Sysprep][].

	- Dans **Shutdown Options**, sélectionnez **Shutdown**.

	- Cliquez sur **OK**.

	![Exécutez Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep arrête la machine virtuelle, ce qui modifie l’état de la machine virtuelle dans le [Portail Azure](http://manage.windowsazure.com) et lui attribue la valeur **Arrêté**.


8.	Cliquez sur **Virtual Machines**, puis sélectionnez la machine virtuelle à capturer.

9.	Dans la barre de commandes, cliquez sur **Capture**.

	![Capture machine virtuelle](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	La boîte de dialogue **Capture the Virtual Machine** s'affiche.

10.	Dans **Image Name**, entrez le nom de la nouvelle image.

11.	Avant d'ajouter une image Windows Server à votre jeu d'images personnalisées, elle doit être généralisée en exécutant Sysprep comme indiqué dans les étapes précédentes. Cliquez sur **I have run Sysprep on the virtual machine** pour indiquer que la généralisation a été effectuée.

12.	Cliquez sur la coche pour capturer l’image.

  **REMARQUE : lorsque vous capturez l’image d’une machine virtuelle généralisée, la machine virtuelle est supprimée.**

 La nouvelle image est désormais disponible dans **Images**. ![Capture d'image réussie](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Étapes suivantes##
L'image est prête à être utilisée comme modèle pour la création de machines virtuelles. Pour ce faire, vous devez créer une machine virtuelle personnalisée en utilisant l’élément de menu **Depuis la galerie** et en sélectionnant l’image que vous venez de créer. Pour obtenir des instructions, consultez la section [Création d’une machine virtuelle personnalisée][].


[À propos des images de machine virtuelle dans Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[Création d’une machine virtuelle personnalisée]: virtual-machines-create-custom.md
[Association d’un disque de données à une machine virtuelle]: storage-windows-attach-disk.md
[Connexion à une machine virtuelle exécutant Windows Server]: http://www.windowsazure.com/manage/windows/how-to-guides/log-on-a-windows-vm/
[Introduction à l'utilisation de Sysprep]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=July15_HO3-->