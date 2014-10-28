<properties linkid="manage-windows-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Capture d'une machine virtuelle Windows pour l'utiliser comme modèle

Cet article vous montre comment capturer une machine virtuelle Windows pour l'utiliser comme modèle afin de créer d'autres machines virtuelles. Ce modèle de machine virtuelle comprend le disque du système d'exploitation ainsi que les disques de données attachés à la machine virtuelle. Il ne comprend pas la configuration réseau, vous devez donc la configurer lors de la création d'autres machines virtuelles utilisant le modèle.

Après la capture de la machine virtuelle, celle-ci est disponible dans **Mes images** lorsque vous créez une machine virtuelle. Le fichier image est conservé sous la forme d'un disque dur virtuel dans votre compte de stockage. Pour plus d'informations concernant les images, consultez [Gestion des disques et des images][].

## Avant de commencer

Ces étapes partent du principe que vous avez déjà créé une machine virtuelle Azure, configuré le système d'exploitation et attaché les disques de données. Si vous ne l'avez pas encore fait, consultez ces instructions :

-   [Création d’une machine virtuelle personnalisée][]
-   [Association d’un disque de données avec une machine virtuelle][]

## Capture de la machine virtuelle

1.  Connectez-vous à la machine virtuelle en cliquant sur **Connecter** dans la barre de commandes. Pour plus d'informations, consultez [Connexion à une machine virtuelle exécutant Windows Server][].

2.  Ouvrez une fenêtre d'invite de commandes en tant qu'administrateur.

3.  Placez-vous dans le répertoire `%windir%\system32\sysprep`, puis exécutez sysprep.exe.

4.  La boîte de dialogue **Outil de préparation système** apparaît. Effectuez les actions suivantes :

    -   Dans **Action de nettoyage du système**, sélectionnez **Enter System Out-of-Box Experience (OOBE)** et vérifiez que la case à cocher **Generalize** est activée. Pour plus d'informations sur l'utilisation de Sysprep, consultez la page [Utilisation de Sysprep : introduction][].

    -   Dans **Shutdown Options**, sélectionnez **Shutdown**.

    -   Cliquez sur **OK**.

    ![Exécutez Sysprep][]

5.  Sysprep arrête la machine virtuelle, ce qui définit l'état de la machine virtuelle dans le [portail de gestion][] sur **Arrêté**.

6.  Cliquez sur **Virtual Machines**, puis sélectionnez la machine virtuelle à capturer.

7.  Dans la barre de commandes, cliquez sur **Capture**.

    ![Capture machine virtuelle][]

    La boîte de dialogue **Capture the Virtual Machine** s'affiche.

8.  Dans **Image Name**, entrez le nom de la nouvelle image.

9.  Avant d'ajouter une image Windows Server à votre jeu d'images personnalisées, elle doit être généralisée en exécutant Sysprep comme indiqué dans les étapes précédentes. Cliquez sur **I have run Sysprep on the virtual machine** pour indiquer que la généralisation a été effectuée.

10. Cliquez sur la coche pour capturer l'image. Lorsque vous capturez l'image d'une machine virtuelle généralisée, la machine virtuelle est supprimée.

    La nouvelle image est disponible dans **Images**.

    ![Capture d'image réussie][]

## Étapes suivantes

L'image est prête à être utilisée comme modèle pour la création de machines virtuelles. Pour ce faire, vous créerez une machine virtuelle personalisée en utilisant la méthode **From Gallery** et en sélectionnant l'image que vous venez de créer. Pour obtenir des instructions, consultez le guide [Création d'une machine virtuelle personnalisée][Création d’une machine virtuelle personnalisée].

  [Gestion des disques et des images]: http://go.microsoft.com/fwlink/p/?LinkId=397536
  [Création d’une machine virtuelle personnalisée]: ../virtual-machines-create-custom/
  [Association d’un disque de données avec une machine virtuelle]: ../storage-windows-attach-disk/
  [Connexion à une machine virtuelle exécutant Windows Server]: http://www.windowsazure.com/fr-fr/manage/windows/how-to-guides/log-on-a-windows-vm/
  [Utilisation de Sysprep : introduction]: http://technet.microsoft.com/fr-fr/library/bb457073.aspx
  [Exécutez Sysprep]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
  [portail de gestion]: http://manage.windowsazure.com
  [Capture machine virtuelle]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
  [Capture d'image réussie]: ./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png
