

## Disque de système d’exploitation
Chaque machine virtuelle dispose d’un disque de système d’exploitation attaché. Il est enregistré comme disque SATA et porte le nom de lecteur C: par défaut. Ce disque a une capacité maximale de 1 023 gigaoctets.

## Disque temporaire
Le disque temporaire est automatiquement créé pour vous. Sur les machines virtuelles Windows, ce disque porte le nom de lecteur D: par défaut et est utilisé pour le stockage de pagefile.sys. Sur les machines virtuelles Linux, le disque se nomme généralement /dev/sdb et il est formaté et monté sur /mnt/resource par l’agent Linux Azure.

La taille du disque temporaire varie en fonction de la taille de la machine virtuelle. Pour plus d’informations, consultez [Tailles des machines virtuelles Linux](../articles/virtual-machines/virtual-machines-linux-sizes.md) ou [Tailles des machines virtuelles Windows](../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!WARNING]
> Ne stockez pas de données sur le disque temporaire. Il assure un stockage temporaire pour les applications et les processus et est destiné à stocker uniquement des données telles que les fichiers d’échange. Pour remapper ce disque sur une lettre de lecteur différente pour une machine virtuelle Windows, consultez la page [Modification de la lettre de lecteur du disque temporaire Windows](../articles/virtual-machines/virtual-machines-windows-classic-change-drive-letter.md).
> 
> 

Pour plus d’informations sur l’utilisation du disque temporaire par Azure, consultez [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) (Présentation du lecteur temporaire sur Microsoft Azure Virtual Machines)

## Disque de données
Un disque de données est un VHD attaché à une machine virtuelle pour stocker des données d’application ou d’autres données que vous souhaitez conserver. Les disques de données sont enregistrés en tant que disques SCSI et sont nommés avec la lettre de votre choix. Chaque disque de données possède une capacité maximale de 1 023 gigaoctets. La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher et le type de stockage que vous pouvez utiliser pour héberger les disques.

> [!NOTE]
> Pour plus d’informations sur les capacités des machines virtuelles, consultez la page [Tailles des machines virtuelles Linux](../articles/virtual-machines/virtual-machines-linux-sizes.md) ou [Tailles des machines virtuelles Windows](../articles/virtual-machines/virtual-machines-windows-sizes.md).
> 
> 

Lorsque vous créez une machine virtuelle à partir d’une image, Azure crée un disque de système d’exploitation. Si vous utilisez une image incluant des disques de données, Azure crée également ces derniers lors de la création de la machine virtuelle. Vous pouvez également ajouter des disques de données après avoir créé la machine virtuelle.

Vous pouvez ajouter des disques de données à une machine virtuelle à tout moment, **en attachant** le disque à la machine virtuelle. Vous pouvez utiliser un VHD que vous avez téléchargé ou copié sur votre compte de stockage ou qui a été créé par Azure pour vous. Le fait d’attacher un disque de données associe le fichier VHD de votre compte de stockage à la machine virtuelle en plaçant un « bail » sur le VHD afin qu’il ne puisse pas être supprimé du stockage tant qu’il est attaché.

## À propos des VHD
Les VHD utilisés dans Azure sont des fichiers .vhd stockés comme objets blob de pages dans un compte de stockage Standard ou Premium dans Azure. Pour des informations sur les objets blob de pages, consultez la page [Présentation des objets blob de blocs et des objets blob de pages](https://msdn.microsoft.com/library/ee691964.aspx). Pour des informations sur le stockage Premium, consultez la page [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../articles/storage/storage-premium-storage.md).

Azure prend en charge le format VHD de disque fixe. Le format fixe définit linéairement le disque logique dans le fichier de façon que l'offset du disque X soit stocké à l'offset de l'objet blob X. Un petit pied de page à la fin de l'objet blob décrit les propriétés du disque dur virtuel. Souvent, le format fixe gaspille de l'espace du fait que la plupart des disques comportent de grandes plages inutilisées. Cependant, Azure stocke les fichiers .vhd dans un format fragmenté : vous profitez donc en même temps de disques fixes et dynamiques. Pour plus d’informations, consultez la page [Prise en main des disques durs virtuels](https://technet.microsoft.com/library/dd979539.aspx).

Dans Azure, tous les fichiers .vhd que vous souhaitez utiliser comme source pour créer des disques ou des images sont en lecture seule. Lorsque vous créez un disque ou une image, Azure copie les fichiers .vhd. Ces copies peuvent être accessibles en lecture seule et en lecture/écriture selon votre utilisation du VHD.

Lorsque vous créez une machine virtuelle à partir d’une image, Azure crée, pour la machine virtuelle, un disque qui est une copie du fichier .vhd source. Pour vous protéger contre une suppression accidentelle, Azure place un bail sur tout fichier .vhd source utilisé pour créer une image, un disque de système d’exploitation ou un disque de données.

Avant de pouvoir supprimer un fichier .vhd source, vous devrez supprimer le bail en supprimant le disque ou l’image. Pour supprimer un fichier .vhd utilisé par une machine virtuelle comme disque de système d’exploitation, vous pouvez supprimer simultanément la machine virtuelle, le disque de système d’exploitation et le fichier .vhd source en supprimant la machine virtuelle et tous les disques associés. Toutefois, la suppression d’un fichier .vhd qui constitue une source pour un disque de données requiert l’exécution de plusieurs étapes dans un ordre défini : détacher le disque de la machine virtuelle, supprimer le disque, puis supprimer le fichier .vhd.

> [!WARNING]
> Si vous supprimez un fichier .vhd source d’un stockage ou que vous supprimez votre compte de stockage, Microsoft ne pourra pas récupérer ces données pour vous.
> 
> 

<!---HONumber=AcomDC_0622_2016-->