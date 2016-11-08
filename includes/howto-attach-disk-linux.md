
Pour en savoir plus sur les disques, consultez la section [À propos des disques et VHD pour machines virtuelles](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md).

<a id="attachempty"></a>

## Attacher un disque vide
1. Ouvrez l’interface CLI Azure, puis [connectez-vous à votre abonnement Azure](../articles/xplat-cli-connect.md). Vérifiez que vous êtes en mode Gestion des services Azure (`azure config mode asm`).
2. Entrez `azure vm disk attach-new` pour créer et attacher un nouveau disque comme indiqué ci-dessous. Remplacez *TestVM* par le nom de votre machine virtuelle Linux et spécifiez la taille du disque en Go (100 Go dans cet exemple) :
   
       azure vm disk attach-new TestVM 100
3. Une fois créé et attaché, le disque de données s’affiche dans la sortie `azure vm disk list <virtual-machine-name>` de la manière suivante :
   
     $ azure vm disk list TestVM
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK

<a id="attachexisting"></a>

## Association d'un disque existant
Pour attacher un disque existant, vous devez disposer d’un fichier .vhd dans un compte de stockage.

1. Ouvrez l’interface CLI Azure, puis [connectez-vous à votre abonnement Azure](../articles/xplat-cli-connect.md). Vérifiez que vous êtes en mode Gestion des services Azure (`azure config mode asm`).
2. Vérifiez si le disque dur virtuel que vous souhaitez attacher est déjà chargé dans votre abonnement Azure :
   
     $azure vm disk list
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
3. Si vous ne trouvez pas le disque que vous souhaitez utiliser, vous pouvez télécharger un disque dur virtuel local dans votre abonnement à l'aide de la commande `azure vm disk create` ou `azure vm disk upload`. Un exemple de `disk create` serait similaire à ce qui suit :
   
       $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
       info:    Executing command vm disk create
       + Retrieving storage accounts
       info:    VHD size : 10 GB
       info:    Uploading 10485760.5 KB
       Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
       info:    Finishing computing MD5 hash, 16% is complete.
       info:    https://mystorageaccount.blob.core.windows.net/disks/test.VHD was
       uploaded successfully
       info:    vm disk create command OK
   
   Vous pouvez également utiliser la commande `azure vm disk upload` pour charger un disque dur virtuel dans un compte de stockage. Vous trouverez plus d'informations sur les commandes de gestion de vos disques de données de machine virtuelle Azure [ici](../articles/virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks).
4. Maintenant, vous attachez le disque dur virtuel souhaité à votre machine virtuelle :
   
       $azure vm disk attach TestVM myTestVhd
       info:    Executing command vm disk attach
       + Getting virtual machines
       + Adding Data-Disk
       info:    vm disk attach command OK
   
   Veillez à remplacer *TestVM* par le nom de votre machine virtuelle et *myTestVhd* par le disque dur virtuel de votre choix.
5. Vous pouvez vérifier que le disque est attaché à la machine virtuelle avec la commande `azure vm disk list <virtual-machine-name>` :
   
     $azure vm disk list TestVM
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK

> [!NOTE]
> Après avoir ajouté un disque de données, vous devez vous connecter à la machine virtuelle et initialiser le disque. La machine virtuelle pourra alors l’utiliser pour le stockage. La marche à suivre est décrite ci-après.
> 
> 

