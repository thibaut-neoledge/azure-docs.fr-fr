
#Détachement d’un disque de données d’une machine virtuelle avec l’interface de ligne de commande

Lorsque vous n’avez plus besoin d’un disque de données qui est attaché à une machine virtuelle, vous pouvez le détacher facilement. Cela supprime le disque de la machine virtuelle, mais pas du stockage. Si vous souhaitez réutiliser les données du disque, vous pouvez l'attacher à la même machine virtuelle ou à une autre.

> [AZURE.NOTE]Une machine virtuelle dans Azure utilise différents types de disque : un disque de système d'exploitation, un disque local temporaire et des disques de données facultatifs. Les disques de données sont le mode de stockage recommandé pour les données des machines virtuelles. Pour plus de détails au sujet des disques, consultez la rubrique [À propos des disques et des images](http://go.microsoft.com/fwlink/p/?LinkId=263439). Il n'est pas encore possible de détacher un disque de système d'exploitation.


1. Obtenez la liste des disques attachés à votre machine virtuelle :

        vm disk list <vm-name>

    Si vous omettez `<vm-name>`, vous obtiendrez une liste de tous les disques de votre abonnement.


2. Détachement d’un disque :

        vm disk detach <vm-name> <lun>

    `lun` identifie le disque à détacher et sera un nombre disponible dans la liste des disques de votre machine virtuelle.

Exemple de procédure, incluant la sortie de terminal :

    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    data:    2    7         kmlinux-602362868dbb7439.vhd
    info:    vm disk list command OK
    ~$ azure vm disk detach kmlinux 2
    info:    Executing command vm disk detach
    + Getting virtual machines
    + Removing Data-Disk
    info:    vm disk detach command OK
    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=July15_HO4-->