Pour plus d'informations sur les disques des machines virtuelles Azure, consultez la rubrique [À propos des disques des machines virtuelles dans Azure][À propos des disques des machines virtuelles dans Azure].

## <span id="attachempty"></span></a> Attacher un disque vide

Pour ajouter un disque de données, le plus simple consiste à attacher un disque vide, car Azure crée le fichier de disque dur virtuel (.vhd) pour vous et le stocke dans le compte de stockage.

1.  Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle appropriée.

2.  Dans la barre de commandes, cliquez sur **Attach**, puis sélectionnez **Attach Empty Disk**.

    ![Attacher un disque vide][Attacher un disque vide]

    La boîte de dialogue **Attach Empty Disk** apparaît.

    ![Attacher un nouveau disque vide][Attacher un nouveau disque vide]

3.  Dans **Nom de fichier**, acceptez le nom généré automatiquement ou entrez un nom descriptif. Le disque de données créé à partir du fichier de disque dur virtuel (.vhd) utilisera toujours le nom généré automatiquement.

4.  Dans le champ **Size**, entrez la taille du disque de données.

5.  Cliquez sur la coche pour attacher le disque de données vide.

    Le disque de données est désormais répertorié dans le tableau de bord de la machine virtuelle.

    ![Disque de données vide correctement attaché][Disque de données vide correctement attaché]

## <span id="attachexisting"></span></a> Association d'un disque existant

Pour attacher un disque existant, vous devez disposer d'un fichier .vhd dans un compte de stockage. Utilisez le cmdlet [Add-AzureVhd][Add-AzureVhd] pour télécharger le fichier .vhd dans le compte de stockage. Après avoir créé et téléchargé le fichier .vhd, vous pouvez l'attacher à une machine virtuelle.

1.  Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle appropriée.

2.  Dans la barre de commandes, cliquez sur **Attach**, puis sur **Attach Disk**.

    ![Attacher un disque de données][Attacher un disque de données]

    La boîte de dialogue **Attach Disk** s’affiche.

    ![Entrer les détails du disque de données][Entrer les détails du disque de données]

3.  Sélectionnez le disque de données que vous souhaitez attacher à la machine virtuelle.
4.  Cliquez sur la coche pour attacher le disque de données à la machine virtuelle.

    Le disque de données est désormais répertorié dans le tableau de bord de la machine virtuelle.

    ![Disque de données correctement attaché][Disque de données correctement attaché]

> [WACOM.NOTE]
> Après avoir ajouté un disque de données, vous devez vous connecter à la machine virtuelle et initialiser le disque. La machine virtuelle pourra alors utiliser ce disque pour le stockage.

  [À propos des disques des machines virtuelles dans Azure]: http://go.microsoft.com/fwlink/p/?LinkId=403697
  [Attacher un disque vide]: ./media/howto-attach-disk-window-linux/AttachDiskWindows.png
  [Attacher un nouveau disque vide]: ./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png
  [Disque de données vide correctement attaché]: ./media/howto-attach-disk-window-linux/AttachEmptySuccess.png
  [Add-AzureVhd]: http://go.microsoft.com/FWLink/p/?LinkID=391684
  [Attacher un disque de données]: ./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png
  [Entrer les détails du disque de données]: ./media/howto-attach-disk-window-linux/AttachExistingDisk.png
  [Disque de données correctement attaché]: ./media/howto-attach-disk-window-linux/AttachSuccess.png
