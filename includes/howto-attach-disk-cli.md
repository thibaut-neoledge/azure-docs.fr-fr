
Pour plus d’informations sur les disques, consultez la page [À propos des disques de machine virtuelle dans Azure](http://go.microsoft.com/fwlink/p/?LinkId=403697).

## <a id="cliattachempty"></a>Association d’un disque vide
Pour ajouter un disque de données, le plus simple consiste à attacher un disque vide. Exécutez la commande suivante pour attacher un nouveau disque vide :

    vm disk attach-new <vm-name> <size-in-gb> [blob-url]

Remplacez `vm-name` par le nom de votre machine virtuelle, et `size-in-gb` par la taille de votre nouveau disque. Vous pouvez éventuellement utiliser une URL d'objet blob comme dernier argument pour spécifier explicitement l'objet blob cible à créer. Si vous ne spécifiez pas d'URL d'objet blob, un objet blob est généré automatiquement.

Exécutez la commande suivante pour vérifier que votre disque a bien été créé :

    vm disk list <vm-name>

Voici un exemple de procédure de la commande mentionné ci-dessus, incluant la sortie de terminal :

    ~$ azure vm disk attach-new pinkylinux 20 http://pinkylinux.blob.core.windows.net/vhds/pinkydisk1.vhd
    info:   Executing command vm disk attach-new
    + Getting virtual machines
    + Adding Data-Disk
    info:   vm disk attach-new command OK
    ~$ azure vm disk list pinkylinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        pinkylinux-pinkylinux-2015-02-05.vhd    Linux
    data:    0    5         pinkydisk1.vhd
    data:    1    20        pinkylinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=August15_HO6-->