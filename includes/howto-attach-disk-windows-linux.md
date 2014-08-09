
# Association d'un disque de données avec une machine virtuelle

* [Concepts](#concepts)
* [Association d'un disque existant](#attachexisting)
* [Association d'un disque vide](#attachempty)
* [Initialisation d'un nouveau disque de données sous Windows
  Server 2008 R2](#initializeinWS)
* [Initialisation d'un nouveau disque de données sous
  Linux](#initializeinlinux)

## <a id="concepts" ></a>Concepts

Vous pouvez attacher un disque de données à une machine virtuelle pour y stocker des données d'applications. Un disque de données est un disque dur virtuel que vous pouvez créer localement sur votre ordinateur ou dans le cloud avec Azure. Vous gérez les disques de données dans la machine virtuelle de la même façon que sur un serveur au sein de votre entreprise.

Vous pouvez utiliser le portail de gestion pour télécharger et attacher un disque de données contenant des données à la machine virtuelle, ainsi que pour ajouter un disque vide à partir du compte de stockage utilisé par la machine virtuelle. Cet article décrit ces procédures. Pour attacher un disque vide situé sur un autre compte de stockage, utilisez le cmdlet [Add-AzureDataDisk][1], disponible dans le module Azure PowerShell. Pour télécharger le module, consultez la page
[Téléchargements][2].

Le fonctionnement de la machine virtuelle n'est pas interrompu lors de l'ajout du disque. Le nombre de disques que vous pouvez attacher à une machine virtuelle dépend de la taille de celle-ci. Pour plus d'informations sur les tailles de machines virtuelles et de disques, consultez la page [Tailles de machines virtuelles et services de cloud computing pour Azure][3].

> [WACOM.NOTE] Le stockage Azure prend en charge les objets blob jusqu'à 1 To, ce qui permet d'héberger un disque dur virtuel d'une taille virtuelle maximale de 999 Go. Toutefois, si vous utilisez Hyper-V pour créer un disque dur virtuel, la taille que vous indiquez représente la taille virtuelle. Pour utiliser le disque dur virtuel dans Azure, spécifiez une taille maximale de 999 Go.

**Disque de données et disque de ressources**  
 Les disques de données résident sur le stockage Azure et sont utilisés pour le stockage permanent des fichiers et des données de l'application.

Chaque machine virtuelle est également associée à un *disque de ressources* local temporaire. Étant donné que les données qui y figurent risquent de ne pas résister aux redémarrages, le disque de ressources est souvent utilisé par les applications et les processus exécutés dans la machine virtuelle pour le stockage temporaire des données, ainsi que pour stocker les fichiers de pagination ou d'échange du système d'exploitation.

Sous Windows, le disque de ressources correspond au lecteur **D:** . Sous Linux, le disque de ressources est habituellement géré par l'agent Linux Azure et monté automatiquement dans **/mnt/resource** (ou **/mnt** pour les images Ubuntu). Pour plus d'informations, consultez le [guide d'utilisation de l'agent Linux Azure][4].

Pour plus d'informations sur l'utilisation des disques de données, consultez la page [Gestion des disques et des images][5].

## <a id="attachexisting" ></a>Association d'un disque existant

1.  Si ce n'est pas déjà fait, connectez-vous au [portail de gestion Azure][6].

2.  Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle à laquelle vous souhaitez attacher le disque.

3.  Dans la barre de commandes, cliquez sur **Attach**, puis sur **Attach Disk**.
    
    ![Attacher un disque de données](./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png)
    
    La boîte de dialogue **Attach Disk** s'affiche.
    
    ![Entrer les détails du disque de données](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

4.  Sélectionnez le disque de données que vous souhaitez attacher à la machine virtuelle.
5.  Cliquez sur la coche pour attacher le disque de données à la machine virtuelle.
    
    Le disque de données est désormais répertorié dans le tableau de bord de la machine virtuelle.
    
    ![Disque de données correctement
    attaché](./media/howto-attach-disk-window-linux/AttachSuccess.png)

## <a id="attachempty" ></a>Association d'un disque vide

Après avoir créé et téléchargé un fichier .vhd à utiliser comme disque vide, vous pouvez l'attacher à une machine virtuelle. Utilisez le cmdlet [Add-AzureVhd][7] pour télécharger le fichier .vhd dans le compte de stockage.

1.  Cliquez sur **Virtual Machines**, puis sélectionnez la machine virtuelle à laquelle vous souhaitez attacher le disque de données.

2.  Dans la barre de commandes, cliquez sur **Attach**, puis sélectionnez **Attach Empty Disk**.
    
    ![Attacher un disque vide](./media/howto-attach-disk-window-linux/AttachDiskWindows.png)
    
    La boîte de dialogue **Attach Empty Disk** apparaît.
    
    ![Attacher un nouveau disque vide](./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png)

3.  Dans la zone **File Name**, conservez le nom généré automatiquement ou entrez le nom que vous souhaitez utiliser pour le fichier de disque dur virtuel stocké. Le disque de données créé à partir du fichier de disque dur virtuel utilisera toujours le nom généré automatiquement.

4.  Dans le champ **Size**, entrez la taille du disque de données.

5.  Cliquez sur la coche pour attacher le disque de données vide.
    
    Le disque de données est désormais répertorié dans le tableau de bord de la machine virtuelle.
    
    ![Disque de données vide correctement
    attaché](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

> [WACOM.NOTE] Après avoir ajouté un disque de données, vous devez vous connecter à la machine virtuelle et initialiser le disque. La machine virtuelle pourra alors utiliser ce disque pour le stockage.

## <a id="initializeinWS" ></a>Initialisation d'un nouveau disque de données sous Windows Server

1.  Pour vous connecter à la machine virtuelle, suivez les étapes indiquées dans la rubrique [Connexion à une machine virtuelle exécutant Windows Server](../virtual-machines-log-on-windows-server/).

2.  Une fois connecté, ouvrez **Server Manager**. Dans le volet gauche, développez l'entrée **Stockage**, puis cliquez sur **Disk Management**.
    
    ![Ouvrir le gestionnaire de
    serveur](./media/howto-attach-disk-window-linux/ServerManager.png)

3.  Cliquez avec le bouton droit sur **Disk 2**, puis cliquez sur **Initialize Disk**.
    
    ![Initialiser le
    disque](./media/howto-attach-disk-window-linux/InitializeDisk.png)

4.  Cliquez sur **OK** pour démarrer le processus d'initialisation.

5.  Cliquez avec le bouton droit sur la zone d'allocation d'espace pour le disque 2, cliquez sur **New Simple Volume**, puis terminez l'Assistant en utilisant les valeurs par défaut.
    
    ![Initialiser le
    volume](./media/howto-attach-disk-window-linux/InitializeDiskVolume.png)
    
    Le disque est à présent accessible en ligne et prêt à être utilisé avec une nouvelle lettre de lecteur.
    
    ![Volume correctement
    initialisé](./media/howto-attach-disk-window-linux/InitializeSuccess.png)

## <a id="initializeinlinux" ></a>Initialisation d'un nouveau disque de données sous Linux

1.  Pour vous connecter à la machine virtuelle, suivez les étapes indiquées dans la rubrique [Connexion à une machine virtuelle exécutant Linux](../virtual-machines-linux-how-to-log-on/).

2.  Dans la fenêtre SSH, tapez la commande suivante, puis entrez le mot de passe du compte créé pour gérer la machine virtuelle :
    
    `sudo grep SCSI /var/log/messages`
    
    L'identificateur du dernier disque de données ajouté est disponible dans les messages qui s'affichent.
    
    ![Obtenir les messages du disque](./media/howto-attach-disk-window-linux/DiskMessages.png)

3.  Dans la fenêtre SSH, tapez la commande suivante pour créer un appareil, puis entrez le mot de passe du compte :
    
    `sudo fdisk /dev/sdc`


   
    > [WACOM.NOTE] Dans cet exemple, vous pouvez devoir utiliser `sudo -i` avec certaines distributions si /sbin ou /usr/sbin n'est pas dans votre `$PATH`.

4.  Tapez **n** pour créer une partition.
    
    ![Créer un appareil](./media/howto-attach-disk-window-linux/DiskPartition.png)

5.  Tapez **p** pour définir la partition comme partition principale, tapez **1** pour la définir comme première partition, puis appuyez sur Entrée pour accepter la valeur par défaut du cylindre.
    
    ![Créer une partition](./media/howto-attach-disk-window-linux/DiskCylinder.png)

6.  Tapez **p** pour afficher les détails relatifs au disque faisant l'objet de la partition.
    
    ![Répertorier les informations sur le disque](./media/howto-attach-disk-window-linux/DiskInfo.png)

7.  Tapez **w** pour écrire les paramètres du disque.
    
    ![Écrire les modifications apportées au disque](./media/howto-attach-disk-window-linux/DiskWrite.png)

8.  Vous devez créer le système de fichiers sur la nouvelle partition.
    Par exemple, tapez la commande suivante pour créer le système de
    fichiers, puis entrez le mot de passe du compte :
    
    `sudo mkfs -t ext4 /dev/sdc1`
    
    ![Créer le système de fichiers](./media/howto-attach-disk-window-linux/DiskFileSystem.png)


   
    > [WACOM.NOTE] Notez que les systèmes SUSE Linux Enterprise 11 n'offrent que l'accès en lecture seule aux systèmes de fichiers ext4. Pour ces systèmes, il est recommandé de formater le nouveausystème de fichiers en ext3 plutôt que ext4.

9.  Vous devez ensuite avoir un répertoire disponible pour monter le nouveau système de fichiers. Par exemple, tapez la commande suivante pour créer un répertoire de montage du lecteur, puis entrez le mot de passe du compte :
    
    `sudo mkdir /datadrive`

10. Tapez la commande suivante pour monter le lecteur :
    
    `sudo mount /dev/sdc1 /datadrive`
    
    Le disque de données est désormais prêt à être utilisé en tant que **/datadrive**.

11. Ajoutez le nouveau lecteur à /etc/fstab :
    
    Pour vous assurer que le lecteur est remonté automatiquement après un redémarrage, vous devez l'ajouter au fichier /etc/fstab. En outre, il est vivement recommandé d'utiliser l'UUID (identificateur global unique) dans /etc/fstab comme référence au lecteur, plutôt que le nom d'appareil uniquement (par exemple, /dev/sdc1). Pour rechercher l'UUID du nouveau lecteur, vous pouvez vous servir de l'utilitaire **blkid** :
    
        `sudo -i blkid`
    
    Le résultat ressemble à ce qui suit :
    
        `/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
        `/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
        `/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`


   
    > [WACOM.NOTE] il se peut que blkid ne nécessite pas systématiquement un accès sudo. Toutefois, il peut s'avérer plus simple à exécuter avec `sudo -i` dans certaines distributions si /sbin ou /usr/sbin ne sont pas situés dans `$PATH`.
    
    **Attention :** si vous ne modifiez pas correctement le fichier /etc/fstab, il se peut que le système ne puisse plus démarrer. En cas de doute, référez-vous à la documentation de la distribution pour obtenir des informations sur la modification adéquate de ce fichier. Il est également fortement recommandé de créer une sauvegarde du fichier /etc/fstab avant de le modifier.
    
    Dans un éditeur de texte, entrez les informations relatives au nouveau système de fichiers à la fin du fichier /etc/fstab. Dans cet exemple, nous utilisons la valeur UUID pour le nouvel appareil **/dev/sdc1** créé lors des étapes précédentes et le point de montage **/datadrive** :
    
        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`
    
    Ou, sur les systèmes basés sur SUSE Linux, vous pouvez êtes amené à devoir utiliser un format légèrement différent :
    
        `/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`
    
    Si des lecteurs de données ou partitions supplémentaires sont créés, vous devez également les ajouter séparément au fichier /etc/fstab.
    
    Vous pouvez désormais vérifier si le système de fichiers est monté correctement en le démontant puis en le remontant, par exemple en utilisant l'exemple de point de montage `/datadrive` créé lors des étapes précédentes :
    
        `sudo umount /datadrive`
        `sudo mount /datadrive`
    
    Si la deuxième commande génère une erreur, vérifiez que la syntaxe utilisée dans le fichier /etc/fstab est correcte.


   
    > [WACOM.NOTE] la suppression ultérieure d'un disque de données sans modifier fstab pourrait entraînter l'échec du démarrage de la machine virtuelle. S'il s'agit d'une occurrence courante, la plupart des distributions proposent les options fstab `nofail` et/ou `nobootwait` qui permettent à un système de démarrer même si le disque n'est pas présent. Pour plus d'informations sur ces paramètres, consultez la documentation de votre distribution.



[1]: http://go.microsoft.com/fwlink/p/?LinkId=391661
[2]: http://www.windowsazure.com/fr-fr/downloads/
[3]: http://go.microsoft.com/FWLink/p/?LinkID=294683
[4]: http://www.windowsazure.com/fr-fr/manage/linux/how-to-guides/linux-agent-guide/
[5]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj672979.aspx
[6]: http://manage.windowsazure.com
[7]: http://go.microsoft.com/FWLink/p/?LinkID=391684