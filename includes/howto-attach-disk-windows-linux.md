
Pour plus d'informations sur les disques, consultez la page [À propos des disques des machines virtuelles dans Azure](http://go.microsoft.com/fwlink/p/?LinkId=403697).

##<a id="attachempty"></a>Procédure : Attacher un disque vide
Pour ajouter un disque de données, le plus simple consiste à attacher un disque vide, car Azure crée le fichier de disque dur virtuel (.vhd) pour vous et le stocke dans le compte de stockage.

1. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle appropriée.

2. Dans la barre de commandes, cliquez sur **Attacher**, puis sur **Attacher un disque vide**.


	![Attacher un disque vide](./media/howto-attach-disk-window-linux/AttachEmptyDisk.png)

3.	La boîte de dialogue **Attacher un disque vide** apparaît.


	![Attacher un nouveau disque vide](./media/howto-attach-disk-window-linux/AttachEmptyDetail.png)

 
	Effectuez les actions suivantes :

	- Dans **Nom de fichier**, acceptez le nom par défaut ou tapez-en un autre pour le fichier .vhd, qui est utilisé pour le disque. Le disque de données utilise un nom généré automatiquement, même si vous tapez un autre nom pour le fichier .vhd.

	- Tapez la **Taille (Go)** du disque de données. 

	- Cliquez sur la coche pour continuer.

4.	Une fois créé et attaché, le disque de données est répertorié dans le tableau de bord de la machine virtuelle.

	![Disque de données vide correctement attaché](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

##<a id="attachexisting"></a>Procédure : Association d'un disque existant

Pour attacher un disque existant, vous devez disposer d'un fichier .vhd dans un compte de stockage. Utilisez l'applet de commande [Add-AzureVhd](http://go.microsoft.com/FWLink/p/?LinkID=391684) pour télécharger le fichier .vhd sur le compte de stockage. Après avoir créé et téléchargé le fichier .vhd, vous pouvez l'attacher à une machine virtuelle. 

1. Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle appropriée.

2. Dans la barre de commandes, cliquez sur **Attacher**, puis sélectionnez **Attacher un disque**.


	![Attacher un disque de données](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

	La boîte de dialogue **Attacher un disque** apparaît.



	![Entrer les détails du disque de données](./media/howto-attach-disk-window-linux/AttachExistingDetail.png)

3. Sélectionnez le disque de données que vous souhaitez attacher à la machine virtuelle.

4. Cliquez sur la coche pour attacher le disque de données à la machine virtuelle.
 
5.	Une fois attaché, le disque de données est répertorié dans le tableau de bord de la machine virtuelle.


	![Disque de données correctement attaché](./media/howto-attach-disk-window-linux/AttachExistingSuccess.png)

> [AZURE.NOTE] 
> Après avoir ajouté un disque de données, vous devez vous connecter à la machine virtuelle et initialiser le disque. La machine virtuelle pourra alors utiliser ce disque pour le stockage.




<!--HONumber=42-->
