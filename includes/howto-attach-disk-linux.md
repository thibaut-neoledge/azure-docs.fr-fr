
Pour en savoir plus sur les disques, consultez la section [À propos des disques et VHD pour machines virtuelles](../articles/virtual-machines-disks-vhds.md).

<a id="attachempty"></a>
## Association d'un disque vide
Pour ajouter un disque de données, le plus simple consiste à attacher un disque vide, car Azure crée le fichier de disque dur virtuel (.vhd) pour vous et le stocke dans le compte de stockage.

1.  Ouvrez l'interface de ligne de commande Azure (Azure CLI) pour Mac, Linux et Windows, puis connectez-vous à votre abonnement Azure. Pour en savoir plus, consultez la rubrique [Se connecter à Azure à partir de l'interface de ligne de commande Azure (Azure CLI)](../articles/xplat-cli-connect.md).

2.  Vérifiez que vous êtes en mode de gestion des services Azure (la valeur par défaut) en saisissant `azure config
 	mode asm`.

3.  Utilisez la commande `azure vm disk attach-new` pour créer et attacher un nouveau disque, comme indiqué ci-dessous. Notez qu'_ubuntuVMasm_ sera remplacé par le nom de la machine virtuelle Linux que vous avez créée dans votre abonnement Dans cet exemple, 30 est la taille du disque en Go.

        azure vm disk attach-new ubuntuVMasm 30

4.	Une fois créé et attaché, le disque de données est répertorié dans la sortie `azure vm disk list
    <virtual-machine-name>` de la manière suivante :

        $ azure vm disk list ubuntuVMasm
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
        data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK

<a id="attachexisting"></a>
## Association d'un disque existant

Pour attacher un disque existant, vous devez disposer d’un fichier .vhd dans un compte de stockage.

1. 	Ouvrez l'interface de ligne de commande Azure (Azure CLI) pour Mac, Linux et Windows, puis connectez-vous à votre abonnement Azure. Pour en savoir plus, consultez la rubrique [Se connecter à Azure à partir de l'interface de ligne de commande Azure (Azure CLI)](../articles/xplat-cli-connect.md).

2.  Vérifiez que vous êtes en mode de gestion des services Azure qui est la valeur par défaut. Si vous avez modifié le mode de gestion des ressources, vous pouvez rétablir le paramétrage en saisissant simplement `azure config mode asm`.

3.	Pour savoir si le disque dur virtuel que vous souhaitez attacher est déjà téléchargé dans votre abonnement Azure, utilisez la commande :

        $azure vm disk list
    	info:    Executing command vm disk list
    	+ Fetching disk images
    	data:    Name                                          OS
    	data:    --------------------------------------------  -----
    	data:    myTestVhd                                     Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060029150744  Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060040530369
    	info:    vm disk list command OK

4.  Si vous ne trouvez pas le disque que vous souhaitez utiliser, vous pouvez télécharger un disque dur virtuel local dans votre abonnement à l'aide de la commande `azure vm disk create` ou `azure vm disk upload`. Voici un exemple possible :

        $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
		info:    Executing command vm disk create
		+ Retrieving storage accounts
		info:    VHD size : 10 GB
		info:    Uploading 10485760.5 KB
		Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
		info:    Finishing computing MD5 hash, 16% is complete.
		info:    https://portalvhdsq1s6mc7mqf4gn.blob.core.windows.net/disks/test.VHD was
		uploaded successfully
		info:    vm disk create command OK

	Vous pouvez également utiliser la commande `azure vm disk upload` pour télécharger un disque dur virtuel dans un compte de stockage spécifique. Vous trouverez plus d'informations sur les commandes de gestion de vos disques de données de machine virtuelle Azure [ici](../virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks).

5.  Saisissez la commande suivante pour attacher le disque dur virtuel chargé souhaité à votre machine virtuelle :

		$azure vm disk attach ubuntuVMasm myTestVhd
		info:    Executing command vm disk attach
		+ Getting virtual machines
		+ Adding Data-Disk
		info:    vm disk attach command OK

	Veillez à remplacer _ubuntuVMasm_ par le nom de votre machine virtuelle et _myTestVhd_ par le disque dur virtuel de votre choix.

6.	Vous pouvez vérifier si le disque est attaché à la machine virtuelle avec la commande `azure vm disk list
 	<virtual-machine-name>` de la manière suivante :

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK


> [AZURE.NOTE]Après avoir ajouté un disque de données, vous devez vous connecter à la machine virtuelle et initialiser le disque. La machine virtuelle pourra alors utiliser ce disque pour le stockage.

<!---HONumber=Oct15_HO3-->