<properties 
	pageTitle="Création d'une machine virtuelle exécutant Windows dans Azure" 
	description="Apprenez à créer une machine virtuelle Windows dans Azure et à connecter et attacher un disque de données" 
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
	ms.date="03/04/2015" 
	ms.author="kathydav"/>



# Création d'une machine virtuelle exécutant Windows

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal" class="current">Portail Azure</a><a href="/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal">Portail Azure en version préliminaire</a></div>

Ce didacticiel vous montre combien il est facile de créer une machine virtuelle Azure. Ce didacticiel utilise une image Windows Server, qui n'est qu'une des nombreuses images disponibles via Azure. Cela inclut les systèmes d'exploitation Windows, les systèmes d'exploitation Linux et les images avec des applications préinstallées. Les images que vous pouvez choisir dépendent du type d'abonnement que vous possédez. Par exemple, les images de bureau sont disponibles pour les abonnés MSDN.


Vous pouvez également créer des machines virtuelles Windows en utilisant [vos propres images en tant que modèles](virtual-machines-create-upload-vhd-windows-server.md). Pour en savoir plus sur les machines virtuelles Azure, consultez [Présentation des machines virtuelles Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx).

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>Création de la machine virtuelle

Ce didacticiel vous montre comment utiliser l'option **À partir de la galerie** dans le portail de gestion pour créer la machine virtuelle. Cette option propose davantage de possibilités de configuration que l'option **Création rapide**. Par exemple, si vous voulez associer une machine virtuelle à un réseau virtuel, vous devez utiliser l'option **À partir de la galerie**.

> [AZURE.NOTE] Vous pouvez également faire un essai avec le [portail Azure en version préliminaire](https://portal.azure.com), plus riche et personnalisable, pour créer une machine virtuelle, automatiser le déploiement de modèles d'application sur plusieurs machines virtuelles, utiliser les fonctions avancées de surveillance et de diagnostic des machines virtuelles, etc. Bien qu'il existe des recoupements importants entre les options de configuration des machines virtuelles disponibles dans les deux portails, ces options ne sont pas identiques.  

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>Connexion à la machine virtuelle une fois celle-ci créée 

Cette section explique comment se connecter à la machine virtuelle de manière à pouvoir gérer ses paramètres et les applications qui seront exécutées dessus.

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## <a id="attachdisk"> </a>Association d'un disque de données avec la nouvelle machine virtuelle 

Cette section explique comment associer un disque de données vide à la machine virtuelle. Pour plus d'informations, notamment sur l'association de disques existants, consultez la page [Association d'un disque de données](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/).

1. Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com).

2. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle **MonTestMV**.

	![Select MyTestVM](./media/virtual-machines-windows-tutorial/selectvm.png)
	
3. Vous pouvez consulter la page Démarrage rapide au préalable. Si c'est le cas, cliquez sur **Tableau de bord** en haut.

	![Select Dashboard](./media/virtual-machines-windows-tutorial/dashboard.png)

4. Dans la barre de commandes, cliquez sur **Attacher**, puis sur **Attacher un disque vide** lorsque l'option apparaît.

	![Select Attach from the command bar](./media/virtual-machines-windows-tutorial/commandbarattach.png)	

5. Les options **Nom de la machine virtuelle**, **Emplacement de stockage**, **Nom de fichier** et **Préférences de cache d'hôte** sont déjà définies. Il vous suffit de spécifier la taille du disque. Par exemple, tapez **5** dans le champ**taille**. Cliquez sur la coche pour attacher le disque.


	>[AZURE.NOTE] Tous les disques sont créés à partir des fichiers de disque dur virtuel dans le stockage Azure. **Nom de fichier** vous permet de nommer le fichier .vhd utilisé par le disque, et non le disque. Azure attribue automatiquement un nom au disque. 

	![Specify the size of the empty disk](./media/virtual-machines-windows-tutorial/emptydisksize.png)	
	
	>[AZURE.NOTE] Les fichiers .vhd sont stockés en tant qu'objets blob de pages dans le stockage Azure. En dehors d'Azure, les disques durs virtuels peuvent utiliser le format VHD ou VHDX. Il est également possible de les corriger, de les étendre et de les différencier. Azure prend en charge les disques fixes au format VHD. Pour plus d'informations, consultez la page [À propos des disques durs virtuels dans Azure](http://msdn.microsoft.com/library/azure/dn790344.aspx)  

6. Revenez au tableau de bord pour vérifier que le disque de données vide a bien été attaché à la machine virtuelle. Il doit s'afficher dans la liste **disques**, après le disque du système d'exploitation.

	![Attach empty disk](./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png)

	Lorsque vous attachez un disque de données, il est en mode hors connexion et non initialisé. Pour pouvoir l'utiliser en vue de stocker des données, vous devez vous connecter à la machine virtuelle et initialiser le disque.

7. Connectez-vous à la machine virtuelle en suivant la procédure de la section précédente, [Connexion à la machine virtuelle une fois celle-ci créée](#logon).

8. Une fois que vous êtes connecté à la machine virtuelle, ouvrez **Server Manager**. Dans le volet gauche, sélectionnez **Services de fichiers et de stockage**.

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/fileandstorageservices.png)

9. Sélectionnez **Disques** dans le menu développé.

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/selectdisks.png)	
	
10.	La section **Disques** répertorie les disques 0, 1 et 2. Le disque 0 est le disque du système d'exploitation, le disque 1 est un disque de ressources temporaires (qui ne doit pas être utilisé pour le stockage des données) et le disque 2 est le disque de données que vous avez attaché à la machine virtuelle. Le disque de données a une capacité de 5 Go, conformément à ce que vous avez spécifié lorsque vous avez attaché le disque. Cliquez avec le bouton droit sur disque 2 et  sélectionnez **Initialiser**.

	![Start initialization](./media/virtual-machines-windows-tutorial/initializedisk.png)

11. Cliquez sur **Oui**.

	![Continue initialization](./media/virtual-machines-windows-tutorial/yesinitialize.png)

12. Cliquez de nouveau avec le bouton droit sur le disque 2 et sélectionnez **Nouveau volume**. 

	![Create the volume](./media/virtual-machines-windows-tutorial/initializediskvolume.png)

13. Parcourez les étapes de l'Assistant en acceptant les valeurs par défaut. Lorsque l'Assistant est terminé, la section **Volumes** répertorie le nouveau volume. Le disque est désormais en ligne et prêt à stocker des données. 

	![Create the volume](./media/virtual-machines-windows-tutorial/newvolumecreated.png)
	
## Étapes suivantes 

Pour en savoir plus sur la configuration des machines virtuelles Windows sur Azure, consultez :

[Connexion de machines virtuelles à un réseau virtuel ou un service cloud](cloud-services-connect-virtual-machine.md)

[Création et téléchargement d'un disque dur virtuel Windows Server dans Azure](virtual-machines-create-upload-vhd-windows-server.md)

[Gestion de la disponibilité des machines virtuelles](manage-availability-virtual-machines.md)

[À propos des paramètres de configuration de machine virtuelle Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[VIDÉO : Prise en main des disques durs virtuels - Ce qui se passe réellement](http://azure.microsoft.com/documentation/videos/getting-started-with-azure-virtual-machines)

[VIDÉO : FAQ avec Mark Russinovich - Microsoft Azure s'exécute-t-il dans Windows ?](http://azure.microsoft.com/documentation/videos/mark-russinovich-windows-on-azure)

[VIDÉO : Ajout d'une nouvelle machine virtuelle dans une batterie de serveurs web en réalisant des images réutilisables](http://azure.microsoft.com/documentation/videos/adding-virtual-machines-web-farm)

[VIDÉO : Ajout de disques durs virtuels, de comptes de stockage et mise à l'échelle des machines virtuelles](http://azure.microsoft.com/documentation/videos/adding-drives-scaling-virtual-machines)

[VIDÉO : Scott Guthrie commence par les machines virtuelles](http://azure.microsoft.com/documentation/videos/virtual-machines-scottgu)

[VIDÉO : Notions de base sur le stockage et les disques avec des machines virtuelles Azure](http://azure.microsoft.com/documentation/videos/storage-and-disks-virtual-machines)



[À propos des machines virtuelles dans Azure]: #virtualmachine
[Création de la machine virtuelle]: #custommachine
[Connexion à la machine virtuelle une fois celle-ci créée]: #logon
[Association d'un disque de données à la nouvelle machine virtuelle]: #attachdisk
[Configuration de la communication avec la machine virtuelle]: #endpoints

<!--HONumber=47-->
