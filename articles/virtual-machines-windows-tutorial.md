<properties linkid="manage-windows-tutorial-virtual-machine-gallery" urlDisplayName="Create a virtual machine" pageTitle="Create a virtual machine running Windows Server in Azure" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server. " metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Création d’une machine virtuelle exécutant Windows Server

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/virtual-machines-windows-tutorial/" title="Portail Azure" class="current">Portail Azure</a><a href="/en-us/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Version pr&eacute;liminaire du portail Azure">Version pr&eacute;liminaire du portail Azure</a></div>

Ce didacticiel montre à quel point il est facile de créer une machine virtuelle Azure exécutant Windows Server à partir de la galerie d'images du portail de gestion Azure. La galerie d'images propose de nombreuses images, notamment des systèmes d'exploitation Windows et Linux, ainsi que des images d'applications.

> [WACOM.NOTE] Vous n'avez besoin d'aucune expérience préalable avec les machines virtuelles Azure pour pouvoir suivre ce didacticiel. Vous devez cependant posséder un compte Azure. Vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page [Création d’un compte Azure][].

Ce didacticiel vous montre comment effectuer les opérations suivantes :

-   [Création de la machine virtuelle][]
-   [Connexion à la machine virtuelle une fois celle-ci créée][]
-   [Association d’un disque de données avec la nouvelle machine virtuelle][]

Pour en savoir plus, consultez la page [Machines virtuelles][].

## <a id="createvirtualmachine"> </a> Création de la machine virtuelle

Ce didacticiel vous montre comment utiliser l'option **À partir de la galerie** dans le portail de gestion pour créer la machine virtuelle. Cette option propose davantage de possibilités de configuration que l'option **Création rapide**. Par exemple, si vous voulez associer une machine virtuelle à un réseau virtuel, vous devez utiliser l'option **À partir de la galerie**.

> [WACOM.NOTE] Vous pouvez faire un essai avec la [version préliminaire du portail Azure][], plus riche et personnalisable, pour créer une machine virtuelle, automatiser le déploiement de modèles d'application sur plusieurs ordinateurs, utiliser les fonctions avancées de surveillance et de diagnostic des machines virtuelles, etc. Bien qu'il existe des recoupements importants entre les options de configuration des machines virtuelles disponibles dans les deux portails, ces options ne sont pas identiques.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM][]]

## <a id="logon"> </a> Connexion à la machine virtuelle une fois celle-ci créée

Cette section explique comment se connecter à la machine virtuelle de manière à pouvoir gérer ses paramètres et les applications qui seront exécutées dessus.

[WACOM.INCLUDE [virtual-machines-log-on-win-server][]]

## <a id="attachdisk"></a> Association d’un disque de données avec la nouvelle machine virtuelle

Cette section explique comment associer un disque de données vide à la machine virtuelle. Pour plus d'informations sur l'association de disques vides ou existants, consultez la page [Association d'un disque de données][].

1.  Connectez-vous au [portail de gestion][] Azure.

2.  Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle **MonTestMV**.

    ![Select MyTestVM][]

3.  Vous pouvez être renvoyé d'abord à la page Démarrage rapide. Si c'est le cas, cliquez sur **Tableau de bord** en haut.

    ![Select Dashboard][]

4.  Dans la barre de commandes, cliquez sur **Attacher**, puis sur **Attacher un disque vide** lorsque l'option apparaît.

    ![Select Attach from the command bar][]

5.  Les options **Virtual Machine Name**, **Storage Location**, **File Name** et **Host Cache Preference** sont déjà définies. Il vous suffit d’entrer la taille de disque souhaitée. Tapez **5** dans le champ **Size**. Cliquez ensuite sur la coche pour attacher le disque vide à la machine virtuelle.

    ![Specify the size of the empty disk][]

    > [WACOM.NOTE] Tous les disques sont créés à partir d'un fichier de disque dur virtuel dans Windows Azure Storage. Sous **Nom de fichier**, vous pouvez fournir le nom du fichier de disque dur virtuel ajouté au stockage. Quant au nom du disque, Azure le génère automatiquement.

6.  Revenez au tableau de bord pour vérifier que le disque de données vide a bien été attaché à la machine virtuelle. Il sera répertorié en tant que second disque dans la liste **Disques**, avec le disque du système d'exploitation.

    ![Attacher un disque vide][]

    Une fois le disque de données attaché à la machine virtuelle, il apparaît hors connexion et n’est pas initialisé. Pour pouvoir l’utiliser en vue de stocker des données, vous devez vous connecter à la machine virtuelle et initialiser le disque.

7.  Connectez-vous à la machine virtuelle en suivant la procédure de la section précédente [Connexion à la machine virtuelle une fois celle-ci créée] (#logon).

8.  Une fois que vous êtes connecté à la machine virtuelle, ouvrez **Server Manager**. Dans le volet gauche, sélectionnez **Services de fichiers et de stockage**.

    ![Expand File and Storage Services in Server Manager][]

9.  Sélectionnez **Disques** dans le menu développé.

    ![Expand File and Storage Services in Server Manager][1]

10. Dans la section **Disques**, la liste comprend trois disques : disque 0, disque 1 et disque 2. Le disque 0 est le disque du système d'exploitation, le disque 1 est un disque de ressources temporaires (qui ne doit pas être utilisé pour le stockage des données) et le disque 2 est le disque de données que vous avez attaché à la machine virtuelle. Notez que le disque de données a une capacité de 5 Go, comme indiqué plus haut. Cliquez avec le bouton droit sur le disque 2, puis sélectionnez **Initialiser**.

    ![Démarrer l’initialisation][]

11. Cliquez sur **Oui** pour démarrer le processus d’initialisation.

    ![Continue initialization][]

12. Cliquez de nouveau avec le bouton droit sur le disque 2 et sélectionnez **Nouveau volume**.

    ![Create the volume][]

13. Parcourez les étapes de l'Assistant en acceptant les valeurs par défaut fournies. Une fois l'Assistant terminé, un nouveau volume est répertorié dans la section **Volumes**.

    ![Create the volume][2]

    Le disque est à présent accessible en ligne et prêt à être utilisé avec une nouvelle lettre de lecteur.

## Étapes suivantes

Pour en savoir plus sur la configuration des machines virtuelles Windows sur Azure, consultez les articles suivants :

[Connexion des machines virtuelles dans un service cloud][]

[Création et téléchargement de votre disque dur virtuel contenant le système d'exploitation Windows Server][]

[Association de disques de données avec une machine virtuelle][Association d'un disque de données]

[Gestion de la disponibilité des machines virtuelles][]

  [Portail Azure]: /en-us/documentation/articles/virtual-machines-windows-tutorial/ "Portail Azure"
  [Version préliminaire du portail Azure]: /en-us/documentation/articles/virtual-machines-windows-tutorial-azure-preview/ "Version préliminaire du portail Azure"
  [Création d’un compte Azure]: http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/
  [Création de la machine virtuelle]: #createvirtualmachine
  [Connexion à la machine virtuelle une fois celle-ci créée]: #logon
  [Association d’un disque de données avec la nouvelle machine virtuelle]: #attachdisk
  [Machines virtuelles]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [version préliminaire du portail Azure]: https://portal.azure.com
  [virtual-machines-create-WindowsVM]: ../includes/virtual-machines-create-WindowsVM.md
  [virtual-machines-log-on-win-server]: ../includes/virtual-machines-log-on-win-server.md
  [Association d'un disque de données]: http://www.windowsazure.com/en-us/documentation/articles/storage-windows-attach-disk/
  [portail de gestion]: http://manage.windowsazure.com
  [Select MyTestVM]: ./media/virtual-machines-windows-tutorial/selectvm.png
  [Select Dashboard]: ./media/virtual-machines-windows-tutorial/dashboard.png
  [Select Attach from the command bar]: ./media/virtual-machines-windows-tutorial/commandbarattach.png
  [Specify the size of the empty disk]: ./media/virtual-machines-windows-tutorial/emptydisksize.png
  [Attacher un disque vide]: ./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png
  [Expand File and Storage Services in Server Manager]: ./media/virtual-machines-windows-tutorial/fileandstorageservices.png
  [1]: ./media/virtual-machines-windows-tutorial/selectdisks.png
  [Démarrer l’initialisation]: ./media/virtual-machines-windows-tutorial/initializedisk.png
  [Continue initialization]: ./media/virtual-machines-windows-tutorial/yesinitialize.png
  [Create the volume]: ./media/virtual-machines-windows-tutorial/initializediskvolume.png
  [2]: ./media/virtual-machines-windows-tutorial/newvolumecreated.png
  [Connexion des machines virtuelles dans un service cloud]: http://www.windowsazure.com/en-us/documentation/articles/cloud-services-connect-virtual-machine/
  [Création et téléchargement de votre disque dur virtuel contenant le système d'exploitation Windows Server]: http://www.windowsazure.com/en-us/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [Gestion de la disponibilité des machines virtuelles]: http://www.windowsazure.com/en-us/documentation/articles/manage-availability-virtual-machines/
