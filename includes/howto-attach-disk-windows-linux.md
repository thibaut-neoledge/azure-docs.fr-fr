
Pour en savoir plus sur les disques, consultez la section [À propos des disques et VHD pour machines virtuelles](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md).

##<a id="attachempty"></a>Association d’un disque vide

Pour ajouter un disque de données, le plus simple consiste à attacher un disque vide, car Azure crée le fichier de disque dur virtuel (.vhd) pour vous et le stocke dans le compte de stockage.

1. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle appropriée.

2. Dans la barre de commandes, cliquez sur **Attacher**, puis sur **Attacher un disque vide**.


	![Attacher un disque vide](./media/howto-attach-disk-windows-linux/AttachEmptyDisk.png)

3.	La boîte de dialogue **Attacher un disque vide** apparaît.


	![Attacher un nouveau disque vide](./media/howto-attach-disk-windows-linux/AttachEmptyDetail.png)


	Effectuez les actions suivantes :

	- Dans **Nom de fichier**, acceptez le nom par défaut ou tapez-en un autre pour le fichier .vhd. Le disque de données utilise un nom généré automatiquement, même si vous tapez un autre nom pour le fichier .vhd.

	- Tapez la **Taille (Go)** du disque de données.

	- Cliquez sur la coche pour continuer.

4.	Une fois créé et attaché, le disque de données est répertorié dans le tableau de bord de la machine virtuelle.

	![Disque de données vide correctement attaché](./media/howto-attach-disk-windows-linux/AttachEmptySuccess.png)

> [AZURE.NOTE] Après avoir ajouté un nouveau disque de données, vous devez ouvrir une session sur la machine virtuelle et initialiser le disque pour pouvoir l’utiliser.


##<a id="attachexisting"></a>Association d’un disque existant

Pour attacher un disque existant, vous devez disposer d’un fichier .vhd dans un compte de stockage. Utilisez l’applet de commande [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) pour télécharger le fichier .vhd dans le compte de stockage. Après avoir créé et téléchargé le fichier .vhd, vous pouvez l'attacher à une machine virtuelle.

1. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle appropriée.

2. Dans la barre de commandes, cliquez sur **Attacher**, puis sur **Attacher un disque**.


	![Attacher un disque de données](./media/howto-attach-disk-windows-linux/AttachExistingDisk.png)


3. Sélectionnez le disque de données, puis cliquez sur la coche pour l’attacher au disque de données.

	![Entrer les détails du disque de données](./media/howto-attach-disk-windows-linux/AttachExistingDetail.png)

4.	Une fois attaché, le disque de données est répertorié dans le tableau de bord de la machine virtuelle.


	![Disque de données correctement attaché](./media/howto-attach-disk-windows-linux/AttachExistingSuccess.png)

<!---HONumber=AcomDC_0330_2016-->