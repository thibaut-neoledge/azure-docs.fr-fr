1. Dans le [portail de gestion](http://manage.windowsazure.com) Azure, cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle créée précédemment (**testlinuxvm**).

2. Dans la barre de commandes, cliquez sur **Attach**, puis sur **Attach Empty Disk**.

	La boîte de dialogue **Attach Empty Disk** apparaît.


3. Les options **Nom de la machine virtuelle**, **Emplacement de stockage** et **Nom de fichier** sont déjà définies. Il vous suffit d’entrer la taille de disque souhaitée. Tapez **5** dans le champ **Size**.

	![Attacher un disque vide][Image2]

	**Remarque** : tous les disques sont créés à partir d'un fichier de disque dur virtuel dans le stockage Azure. Vous pouvez fournir le nom du fichier de disque dur virtuel ajouté au stockage. Quant au nom du disque, Azure le génère automatiquement.

4. Cliquez sur la coche pour attacher le disque de données à la machine virtuelle.

5. Cliquez sur le nom de la machine virtuelle pour afficher le tableau de bord et vérifier que le disque de données a été correctement attaché à la machine virtuelle. Le disque que vous avez attaché est répertorié dans la table **Disks**.

	Une fois attaché, le disque de données restera inutilisable tant que vous ne vous serez pas connecté pour terminer l'installation.

##Connexion à la machine virtuelle à l’aide de SSH ou PuTTY et achèvement de l’installation
Connectez-vous à la machine virtuelle pour terminer l'installation du disque et pouvoir ainsi l'utiliser pour stocker des données.

1. Une fois la machine virtuelle déployée, connectez-vous en tant que **newuser** (comme décrit dans les étapes précédentes) à l’aide de SSH ou PuTTY.	


2. Dans la fenêtre SSH ou PuTTY, entrez la commande suivante, puis le mot de passe du compte :

	`$ sudo grep SCSI /var/log/messages`

	L’identificateur du dernier disque de données ajouté est disponible dans les messages qui s’affichent (**sdc**, dans cet exemple).

	![GREP][Image4]


3. Dans la fenêtre SSH ou PuTTY, entrez la commande suivante pour partitionner le disque **/dev/sdc** :

	`$ sudo fdisk /dev/sdc`


4. Entrez **n** pour créer une partition.

	![FDISK][Image5]


5. Tapez **p** pour définir la partition comme partition principale, tapez **1** pour la définir comme première partition, puis appuyez sur Entrée pour accepter la valeur par défaut (1) du cylindre.

	![FDISK][Image6]


6. Tapez **p** pour afficher les détails relatifs au disque faisant l’objet de la partition.

	![FDISK][Image7]


7. Tapez **w** pour écrire les paramètres du disque.

	![FDISK][Image8]


8. Formatez le nouveau disque à l’aide de la commande **mkfs** :

	`$ sudo mkfs -t ext4 /dev/sdc1`

9. Vous devez ensuite avoir un répertoire disponible pour monter le nouveau système de fichiers. Par exemple, tapez la commande suivante pour créer un répertoire de montage du lecteur, puis entrez le mot de passe du compte :

	`sudo mkdir /datadrive`


10. Tapez la commande suivante pour monter le lecteur :

	`sudo mount /dev/sdc1 /datadrive`

	Le disque de données est désormais prêt à être utilisé en tant que **/datadrive**.


11. Ajoutez le nouveau lecteur à /etc/fstab :

	Pour vous assurer que le lecteur est remonté automatiquement après un redémarrage, vous devez l’ajouter au fichier /etc/fstab. En outre, il est vivement recommandé d’utiliser l’UUID (identificateur global unique) dans /etc/fstab comme référence au lecteur, plutôt que le nom d’appareil uniquement (par exemple, /dev/sdc1). Pour rechercher l’UUID du nouveau lecteur, vous pouvez vous servir de l’utilitaire **blkid** :
	
		`sudo -i blkid`

	Le résultat ressemble à ce qui suit :

		`/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
		`/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
		`/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

	>[AZURE.NOTE]Il se peut que blkid ne nécessite pas systématiquement un accès sudo. Toutefois, il peut s'avérer plus facile à exécuter avec `sudo -i` dans certaines distributions si /sbin ou /usr/sbin ne figurent pas dans votre `$PATH`.

	**Attention** : si vous ne modifiez pas correctement le fichier /etc/fstab, il se peut que le système ne puisse plus démarrer. En cas de doute, reportez-vous à la documentation de la distribution pour obtenir des informations sur la modification adéquate de ce fichier. Il est par ailleurs vivement recommandé de créer une sauvegarde du fichier /etc/fstab avant de le modifier.

	Dans un éditeur de texte, entrez les informations relatives au nouveau système de fichiers à la fin du fichier /etc/fstab. Dans cet exemple, nous utilisons la valeur UUID pour le nouvel appareil **/dev/sdc1** créé lors des étapes précédentes et le point de montage **/datadrive** :

		`UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

	Si des lecteurs de données ou partitions supplémentaires sont créés, vous devez également les ajouter séparément au fichier /etc/fstab.

	Vous pouvez désormais vérifier si le système de fichiers est monté correctement en le démontant puis en le remontant, par exemple en utilisant l'exemple de point de montage `/datadrive` créé lors des étapes précédentes :

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	Si la deuxième commande génère une erreur, vérifiez que la syntaxe utilisée dans le fichier /etc/fstab est correcte.


	>[AZURE.NOTE]La suppression ultérieure d’un disque de données sans modifier fstab pourrait entraîner l’échec du démarrage de la machine virtuelle. S’il s’agit d’une occurrence courante, la plupart des distributions proposent les options fstab `nofail` et/ou `nobootwait` qui permettent à un système de démarrer même si le disque n’est pas présent. Pour plus d'informations sur ces paramètres, consultez la documentation de votre distribution.


[Image2]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM2.png
[Image4]: ./media/attach-data-disk-centos-vm-in-portal/GrepScsiMessages.png
[Image5]: ./media/attach-data-disk-centos-vm-in-portal/fdisk1.png
[Image6]: ./media/attach-data-disk-centos-vm-in-portal/fdisk2.png
[Image7]: ./media/attach-data-disk-centos-vm-in-portal/fdisk3.png
[Image8]: ./media/attach-data-disk-centos-vm-in-portal/fdisk4.png
[Image9]: ./media/attach-data-disk-centos-vm-in-portal/mkfs.png

<!---HONumber=July15_HO4-->