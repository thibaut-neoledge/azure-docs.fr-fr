<properties title="Create a Virtual Machine Running Windows Server" pageTitle="Création d'une machine virtuelle exécutant Windows Server" description="Describes how to create a Windows virtual machine, add a data disk, and log on remotely" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

# Création d'une machine virtuelle exécutant Windows Server #

Ce didacticiel montre à quel point il est facile de créer une machine virtuelle Azure exécutant Windows Server à partir de la galerie d'images du portail de gestion Windows Azure. La galerie d'images propose de nombreuses images, notamment des systèmes d'exploitation Windows et Linux, ainsi que des images d'applications. 

> [WACOM.NOTE]Vous n'avez besoin d'aucune expérience préalable avec les machines virtuelles Azure pour pouvoir suivre ce didacticiel. Vous devez cependant posséder un compte Azure. Vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Création d'un compte Azure](http://www.windowsazure.com/fr-fr/develop/php/tutorials/create-a-windows-azure-account/). 

Ce didacticiel vous montre comment effectuer les opérations suivantes :

- [Création de la machine virtuelle](#createvirtualmachine)
- [Connexion à la machine virtuelle une fois celle-ci créée](#logon)
- [Association d'un disque de données avec la nouvelle machine virtuelle](#attachdisk)

Pour en savoir plus, consultez la page [Machines virtuelles](http://go.microsoft.com/fwlink/p/?LinkID=271224).


##<a id="createvirtualmachine"> </a>Création de la machine virtuelle##

Ce didacticiel vous montre comment utiliser l'option **À partir de la galerie** dans le portail de gestion pour créer la machine virtuelle. Cette option propose davantage de possibilités de configuration que l'option **Création rapide**. Par exemple, si vous voulez associer une machine virtuelle à un réseau virtuel, vous devez utiliser l'option **À partir de la galerie**.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>Connexion à la machine virtuelle une fois celle-ci créée ##

Cette section explique comment se connecter à la machine virtuelle de manière à pouvoir gérer ses paramètres et les applications qui seront exécutées dessus.

1. Connectez-vous au [portail de gestion](http://manage.windowsazure.com) Azure.

2. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle **MaMVTest**.

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)

3. Dans la barre de commandes, cliquez sur **Connecter**.

	![Connect to MyTestVM](./media/CreateVirtualMachineWindowsTutorial/commandbarconnect.png)
	
4. Cliquez sur **Ouvrir** pour utiliser le fichier de protocole RDP (Remote Desktop Protocol) qui a été créé automatiquement pour la machine virtuelle.

	![Open the rdp file](./media/CreateVirtualMachineWindowsTutorial/openrdp.png)
	
5. Cliquez sur **Connecter**.

	![Continue with connecting](./media/CreateVirtualMachineWindowsTutorial/connectrdc.png)

6. Dans la zone de mot de passe, tapez le nom d'utilisateur et le mot de passe que vous avez spécifiés lorsque vous avez créé la machine virtuelle, puis cliquez sur **OK**.

7. Cliquez sur **Oui** pour vérifier l'identité de la machine virtuelle.

	![Verify the identity of the machine](./media/CreateVirtualMachineWindowsTutorial/certificate.png)

	Vous pouvez à présent utiliser la machine virtuelle de la même façon qu'un serveur de votre bureau.

## <a id="attachdisk"> </a>Association d'un disque de données à la nouvelle machine virtuelle ##

Cette section explique comment associer un disque de données vide à la machine virtuelle. Pour plus d'informations sur l'association de disques vides ou existants, consultez la page [Association d'un disque de données](http://www.windowsazure.com/fr-fr/documentation/articles/storage-windows-attach-disk/).

1. Connectez-vous au [portail de gestion](http://manage.windowsazure.com) Azure.

2. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle **MaMVTest**.

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)
	
3. Vous pouvez être renvoyé d'abord à la page Démarrage rapide. Si c'est le cas, cliquez sur **Tableau de bord** en haut.

	![Select Dashboard](./media/CreateVirtualMachineWindowsTutorial/dashboard.png)

4. Dans la barre de commandes, cliquez sur **Attacher**, puis sur **Attacher un disque vide** lorsque l'option apparaît.

	![Select Attach from the command bar](./media/CreateVirtualMachineWindowsTutorial/commandbarattach.png)	

5. Les options **Nom de la machine virtuelle**, **Emplacement de stockage**, **Nom de fichier** et **Préférences de cache d'hôte** sont déjà définies. Il vous suffit d'entrer la taille de disque souhaitée. Tapez **5** dans le champ **Taille**. Cliquez ensuite sur la coche pour attacher le disque vide à la machine virtuelle.

	![Specify the size of the empty disk](./media/CreateVirtualMachineWindowsTutorial/emptydisksize.png)	
	
	>[WACOM.NOTE] Tous les disques sont créés à partir d'un fichier de disque dur virtuel dans le stockage Windows Azure. Sous **Nom de fichier**, vous pouvez fournir le nom du fichier de disque dur virtuel ajouté au stockage. Quant au nom du disque, Azure le génère automatiquement.

6. Revenez au tableau de bord pour vérifier que le disque de données vide a bien été attaché à la machine virtuelle. Il sera répertorié en tant que second disque dans la liste **Disques**, avec le disque du système d'exploitation.

	![Attach empty disk](./media/CreateVirtualMachineWindowsTutorial/disklistwithdatadisk.png)

	Une fois le disque de données attaché à la machine virtuelle, il apparaît hors connexion et n'est pas initialisé. Pour pouvoir l'utiliser en vue de stocker des données, vous devez vous connecter à la machine virtuelle et initialiser le disque.

7. Connectez-vous à la machine virtuelle en suivant la procédure de la section précédente, [Connexion à la machine virtuelle une fois celle-ci créée](#logon).

8. Une fois que vous êtes connecté à la machine virtuelle, ouvrez le **Gestionnaire de serveur**. Dans le volet gauche, sélectionnez **Services de fichiers et de stockage**.

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/fileandstorageservices.png)

9. Sélectionnez **Disques** dans le menu développé.

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/selectdisks.png)	
	
10. Dans la section **Disques**, la liste comprend trois disques : disque 0, disque 1 et disque 2. Le disque 0 est le disque du système d'exploitation, le disque 1 est un disque de ressources temporaires (qui ne doit pas être utilisé pour le stockage des données) et le disque 2 est le disque de données que vous avez attaché à la machine virtuelle. Notez que le disque de données a une capacité de 5 Go, comme indiqué plus haut. Cliquez avec le bouton droit sur le disque 2, puis sélectionnez **Initialiser**.

	![Start initialization](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

11. Cliquez sur **Oui** pour démarrer le processus d'initialisation.

	![Continue initialization](./media/CreateVirtualMachineWindowsTutorial/yesinitialize.png)

12. Cliquez de nouveau avec le bouton droit sur le disque 2 et sélectionnez **Nouveau volume**. 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)

13. Parcourez les étapes de l'Assistant en acceptant les valeurs par défaut fournies. Une fois l'Assistant terminé, un nouveau volume est répertorié dans la section **Volumes**. 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/newvolumecreated.png)

	Le disque est à présent accessible en ligne et prêt à être utilisé avec une nouvelle lettre de lecteur. 
	
##Étapes suivantes 

Pour en savoir plus sur la configuration de machines virtuelles Windows sous Azure, consultez les articles suivants :

[Connexion des machines virtuelles dans un service cloud](http://www.windowsazure.com/fr-fr/documentation/articles/cloud-services-connect-virtual-machine/)

[Création et téléchargement de votre disque dur virtuel contenant le système d'exploitation Windows Server](http://www.windowsazure.com/fr-fr/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[Association de disques de données avec une machine virtuelle](http://www.windowsazure.com/fr-fr/documentation/articles/storage-windows-attach-disk/)

[Gestion de la disponibilité des machines virtuelles](http://www.windowsazure.com/fr-fr/documentation/articles/manage-availability-virtual-machines/)

[À propos des machines virtuelles dans Azure]: #virtualmachine
[Création de la machine virtuelle]: #custommachine
[Connexion à la machine virtuelle une fois celle-ci créée]: #logon
[Association d'un disque de données avec la nouvelle machine virtuelle]: #attachdisk
[Configuration de la communication avec l'ordinateur virtuel]: #endpoints


