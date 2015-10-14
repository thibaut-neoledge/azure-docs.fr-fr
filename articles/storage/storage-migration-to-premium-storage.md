<properties
    pageTitle="Migration vers le stockage Azure Premium | Microsoft Azure"
    description="Migrez vers le stockage Premium Azure afin d’obtenir une prise en charge de disque haute performance et à faible latence pour les charges de travail gourmandes en E/S en cours d’exécution sur des machines virtuelles Azure."
    services="storage"
    documentationCenter="na"
    authors="tamram"
    manager="adinah"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2015"
    ms.author="tamram"/>


# Migration vers le stockage Premium Azure

## Vue d'ensemble

Le stockage Premium stocke les données sur la dernière technologie de disques SSD et offre une prise en charge de disque haute performance et à faible latence pour les charges de travail gourmandes en E/S en cours d’exécution sur des machines virtuelles Azure. Avec le stockage Premium, vos applications peuvent utiliser jusqu'à 32 To de stockage et atteindre 50 000 IOPS (opérations d'E/S par seconde) par machine virtuelle, avec une latence extrêmement faible pour les opérations de lecture. Cet article vous oriente sur la migration de vos disques et de vos machines virtuelles à partir d’un stockage Standard ou local ou d’une plateforme du cloud différente vers le stockage Premium Azure. Pour obtenir une présentation détaillée de l’offre de stockage Premium Azure, consultez [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](storage-premium-storage-preview-portal.md).

## Avant de commencer

### Configuration requise
- Vous aurez besoin d’un abonnement Azure. Si vous n’en avez pas, vous pouvez souscrire un abonnement pour un [essai gratuit](http://azure.microsoft.com/pricing/free-trial/) d’un mois ou visiter la page [Tarification Azure](http://azure.microsoft.com/pricing/) pour davantage d’options.
- Pour exécuter les applets de commande PowerShell, vous avez besoin du module Microsoft Azure PowerShell. Pour télécharger le module, consultez la page [Téléchargements Microsoft Azure](http://azure.microsoft.com/downloads/).
- Lorsque vous prévoyez d’utiliser des machines virtuelles Azure exécutées sur un stockage Premium, vous devez utiliser les machines virtuelles de série DS. Vous pouvez utiliser des disques de stockage Standard et Premium avec les machines virtuelles de série DS. Les disques de stockage Premium seront bientôt disponibles avec plusieurs types de machines virtuelles. Pour plus d’informations sur les tailles et les types de disque de machine virtuelle Azure disponibles, consultez la page [Tailles de machines virtuelles et de services cloud pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

### Considérations

#### Tailles de machine virtuelle
Les tailles de machine virtuelle de série DS et leurs caractéristiques sont résumées ci-dessous. Passez en revue les caractéristiques de performances de ces offres de stockage Premium et choisissez l’option la plus appropriée pour vos disques et vos machines virtuelles Azure qui correspond le mieux à votre charge de travail. Assurez-vous que la bande passante disponible est suffisante sur votre machine virtuelle pour gérer le trafic du disque.

|Taille de la machine virtuelle|Cœurs d’unité centrale|Nombre maximal d’opérations d’E/S par seconde|Bande passante maximale du disque|
|:---:|:---:|:---:|:---:|
|**STANDARD\_DS1**|1|3 200|32 Mo par seconde|
|**STANDARD\_DS2**|2|6 400|64 Mo par seconde|
|**STANDARD\_DS3**|4|12 800|128 Mo par seconde|
|**STANDARD\_DS4**|8|25 600|256 Mo par seconde|
|**STANDARD\_DS11**|2|6 400|64 Mo par seconde|
|**STANDARD\_DS12**|4|12 800|128 Mo par seconde|
|**STANDARD\_DS13**|8|25 600|256 Mo par seconde|
|**STANDARD\_DS14**|16|50 000|512 Mo par seconde|

#### Tailles du disque
Il existe trois types de disque qui peuvent être utilisés avec votre machine virtuelle et chacun possède des E/S par seconde spécifiques avec des limites. Prenez en compte ces limites lors de la sélection du type de disque pour votre machine virtuelle en fonction des besoins de votre application en termes de capacité, de performances, d’extensibilité et de charges maximales.

|Type de disque de stockage Premium|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Taille du disque|128 Go|512 Go|1024 Go (1 To)|
|IOPS par disque|500|2 300|5 000|
|Débit par disque|100 Mo par seconde|150 Mo par seconde|200 Mo par seconde|

#### Objectifs d’évolutivité de compte de stockage

Les comptes de stockage Premium ont les objectifs d’évolutivité suivants en plus des [Objectifs d’évolutivité et de performances de stockage Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx). Si les besoins de votre application dépassent les objectifs d’évolutivité d’un compte de stockage unique, générez votre application pour qu’elle utilise plusieurs comptes de stockage et partitionnez vos données sur ces comptes.

|Capacité totale des comptes|Bande passante totale pour un compte de stockage localement redondant|
|:--|:---|
|Capacité du disque : 35 To<br />Capacité d’instantané : 10 To|Jusqu'à 50 Go par seconde pour les données entrantes/sortantes|

Pour plus d’informations sur les spécifications de stockage Premium, consultez la page [Objectifs d’évolutivité et de performances lors de l’utilisation du stockage Premium](storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whfr-FRing-premium-storage).

#### Disques de données supplémentaires
En fonction de votre charge de travail, déterminez si les disques de données supplémentaires sont nécessaires pour votre machine virtuelle. Vous pouvez joindre plusieurs disques de données persistantes à votre machine virtuelle. Si nécessaire, vous pouvez répartir les données sur les disques pour augmenter la capacité et les performances du volume. Si vous équilibrez les disques de données de stockage Premium à l’aide des [espaces de stockage](http://technet.microsoft.com/library/hh831739.aspx), vous devez les configurer avec une colonne pour chaque disque utilisé. Dans le cas contraire, les performances globales du volume agrégé par bandes peuvent être limitées, en raison d'une distribution inégale du trafic sur les disques. Pour les machines virtuelles Linux, vous pouvez utiliser l’utilitaire *mdadm* pour obtenir le même résultat. Consultez l’article [Configuration d’un RAID logiciel sur Linux](../virtual-machines-linux-configure-raid.md) pour plus d’informations.

#### Stratégie de mise en cache du disque
Par défaut, la stratégie de mise en cache est *Lecture seule* pour tous les disques de données Premium et *Lecture-écriture* pour le disque du système d’exploitation Premium attaché à la machine virtuelle. Ce paramètre de configuration est recommandé pour optimiser les performances des E/S de votre application. Pour les disques de données en écriture seule ou avec d'importantes opérations d'écriture (par ex., les fichiers journaux de SQL Server), désactivez la mise en cache du disque pour de meilleures performances de l'application. Les paramètres du cache pour les disques de données existants peuvent être mis à jour à l’aide du portail Azure ou du paramètre *- HostCaching* de l’applet de commande *Set-AzureDataDisk*.

#### Emplacement 
Choisissez un emplacement où le stockage Azure Premium est disponible. Consultez la section [Informations importantes à connaître sur le stockage Premium](storage-premium-storage-preview-portal.md#important-things-to-know-about-premium-storage) pour plus d’informations à jour sur des emplacements disponibles. Les machines virtuelles situées dans la même région que le compte de stockage qui stocke les disques de la machine virtuelle offrent des performances supérieures que si elles se trouvent dans des régions distinctes.

#### Autres paramètres de configuration de machine virtuelle Azure

Lorsque vous créez une machine virtuelle Azure, vous devez en configurer certains paramètres. N’oubliez pas : certains paramètres sont fixes pour la durée de vie de la machine virtuelle, tandis que d’autres peuvent être modifiés ou ajoutés ultérieurement. Passez en revue les paramètres de configuration des machines virtuelles Azure et assurez-vous qu’ils sont correctement configurés pour répondre aux besoins de votre charge de travail.

## Préparer des disques durs virtuels pour la migration

La section suivante fournit des instructions pour préparer les disques durs virtuels à partir de votre machine virtuelle afin qu’ils soient prêts à migrer. Le disque dur virtuel peut être :

- Une image de système d’exploitation généralisée permettant de créer plusieurs machines virtuelles Azure.  
- Un disque de système d’exploitation qui peut être utilisé avec une instance unique de machine virtuelle Azure.  
- Un disque de données qui peut être joint à une machine virtuelle Azure pour un stockage persistant.

### Conditions préalables

Pour migrer vos machines virtuelles, vous devez :

- Un abonnement Azure, un compte de stockage et un conteneur dans ce compte de stockage où copier votre disque dur virtuel. Notez que le compte de stockage de destination peut être un compte de stockage Standard ou Premium selon vos besoins.
- Un outil pour généraliser le disque dur virtuel si vous envisagez de créer plusieurs instances de machine virtuelle à partir de celui-ci. Par exemple, sysprep pour Windows ou virt-sysprep pour Ubuntu.
- Un outil pour télécharger le fichier de disque dur virtuel sur le compte de stockage. Par exemple, [AzCopy](storage-use-azcopy.md) ou [Explorateur de stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Ce guide décrit la copie de votre disque dur virtuel à l’aide de l’outil AzCopy.

> [AZURE.NOTE]Pour des performances optimales, copiez votre disque dur virtuel en exécutant l’un de ces outils à partir d’une machine virtuelle Azure se trouvant dans la même région que le compte de stockage de destination. Si vous copiez un disque dur virtuel à partir d’une machine virtuelle Azure se trouvant dans une autre région, les performances risquent d’être ralenties.
>
> Pour copier une grande quantité de données via une bande passante limitée, vous pouvez envisager d’utiliser le [service Microsoft Azure Import/Export](storage-import-export-service.md) pour transférer les données par expédition des lecteurs de disque dur à un centre de données Azure. Vous pouvez utiliser le service Azure Import/Export pour copier les données vers un compte de stockage Standard uniquement. Une fois les données dans votre compte de stockage Standard, utilisez [l’API copie d’objet blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou AzCopy pour transférer les données vers votre compte de stockage Premium.
>
> Notez que Microsoft Azure prend uniquement en charge les fichiers de disque dur virtuel de taille fixe. Les fichiers VHDX ou les disques durs virtuels dynamiques ne sont pas pris en charge. Si vous avez un disque dur virtuel dynamique, vous pouvez le convertir à taille fixe à l’aide de l’applet de commande [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx).

### Scénarios pour la préparation des disques durs virtuels

Nous vous présentons ci-dessous différents scénarios pour la préparation de vos disques durs virtuels.

#### Disque dur virtuel de système d’exploitation généralisé pour créer plusieurs instances de machine virtuelle

Si vous téléchargez un disque dur virtuel qui permet de créer plusieurs instances de machine virtuelle Azure génériques, vous devez tout d’abord généraliser un disque dur virtuel à l’aide d’un utilitaire sysprep. Cette procédure s’applique à un disque dur virtuel local ou dans le cloud. Sysprep supprime des informations spécifiques sur une machine à partir du disque dur virtuel.

>[AZURE.IMPORTANT]Réalisez un instantané ou une sauvegarde de votre machine virtuelle avant la généralisation. L’exécution de sysprep supprime l’instance de la machine virtuelle. Suivez les étapes ci-dessous pour exécuter sysprep sur un disque dur virtuel de système d’exploitation Windows. Notez que vous devez arrêter la machine virtuelle pour pouvoir exécuter la commande Sysprep. Pour plus d’informations sur Sysprep, consultez [Présentation de Sysprep](http://technet.microsoft.com/library/hh825209.aspx) ou le Manuel de [référence technique Sysprep](http://technet.microsoft.com/library/cc766049(v=ws.10).aspx).

1. Ouvrez une fenêtre d'invite de commandes en tant qu'administrateur.
2. Entrez la commande suivante pour ouvrir Sypsrep :

		%windir%\system32\sysprep\sysprep.exe

4. Dans l’outil de préparation du système, choisissez Entrer en mode OOBE (Out-of-Box Experience), cochez la case à cocher Généraliser, sélectionnez **Arrêter**, puis cliquez sur **OK**, comme illustré dans l’image ci-dessous. Le système d’exploitation sera généralisé et le système arrêté.

	![][1]

Pour une machine virtuelle Ubuntu, utilisez virt-sysprep pour obtenir le même résultat. Pour plus d’informations, consultez la page [virt-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) . Consultez également les informations relatives à certains des [logiciels de configuration du serveur Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) open source pour d’autres systèmes d’exploitation Linux.

#### Disque dur virtuel de système d’exploitation unique pour créer une seule instance de machine virtuelle

Si une application exécutée sur la machine virtuelle requiert les données spécifiques de la machine, ne généralisez pas le disque dur virtuel. Un disque dur virtuel non généralisé peut servir à créer une instance de machine virtuelle Azure unique. Par exemple, si vous avez un contrôleur de domaine sur votre disque dur virtuel, l’exécution de sysprep le rend inefficace comme contrôleur de domaine. Passez en revue les applications s’exécutant sur votre machine virtuelle et l’impact de sysprep sur ces dernières avant la généralisation du disque dur virtuel.

#### Disques durs virtuels de disque de données à joindre aux instances de la machine virtuelle

Si vous avez des disques de données dans un stockage cloud à migrer, vous devez vous assurer que les machines virtuelles qui utilisent ces disques de données sont arrêtées. Pour les disques de données qui sont stockés en local, créez un disque dur virtuel cohérent.

## Copier les disques durs virtuels vers Azure Storage

Maintenant que le disque dur virtuel est prêt, suivez les étapes décrites ci-dessous pour télécharger le disque dur virtuel dans le stockage Azure et l’inscrire comme une image de système d’exploitation, un disque de système d’exploitation configuré ou un disque de données configuré.

### Création de la destination de votre disque dur virtuel

Créez un compte de stockage pour gérer vos disques durs virtuels. Prenez en compte les points suivants lors de la planification de l’emplacement où stocker vos disques durs virtuels :

- Le compte de stockage cible peut être Standard ou Premium selon les besoins de votre application.
- L’emplacement du compte de stockage doit être identique dans les machines virtuelles Azure de série DS que vous allez créer lors de l’étape finale. Vous pouvez copier vers un nouveau compte de stockage, ou envisager d’utiliser le même compte de stockage selon vos besoins.
- Copiez et enregistrez la clé de compte de stockage du compte de stockage de destination pour l’étape suivante.
- Pour les disques de données, vous pouvez choisir d’en conserver certains dans un compte de stockage Standard (par exemple, les disques qui ont un stockage de refroidissement) et de déplacer ceux avec des E/S par seconde importants dans le compte de stockage Premium.

### Copie de votre disque dur virtuel à partir de la source

Vous trouverez ci-dessous différents scénarios de copie de votre disque dur virtuel.

#### Copie de disque dur virtuel à partir d’Azure Storage

Si vous migrez un disque dur virtuel d’un compte de stockage Azure Standard vers un compte de stockage Azure Premium, vous devez copier le chemin source du conteneur de disque dur virtuel, le nom de fichier de disque dur virtuel et la clé de compte de stockage du compte de stockage source.

1. Accédez à **Portail Azure > Machines virtuelles > Disques**.
2. Copiez et enregistrez l’URL du conteneur de disque dur virtuel à partir de la colonne emplacement. L’URL du conteneur est semblable au suivant : `https://myaccount.blob.core.windows.net/mycontainer/`.

#### Copie d’un disque dur virtuel à partir d’un cloud non Azure

Si vous migrez un disque dur virtuel d’un stockage cloud non Azure vers Azure, vous devez d’abord exporter le disque dur virtuel vers un répertoire local. Copiez le chemin source complet du répertoire local où le disque dur virtuel est stocké.

1. Si vous utilisez AWS, exportez l’instance EC2 vers un disque dur virtuel dans un compartiment S3 Amazon. Suivez les étapes décrites dans la section [Exportation des instances Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html) de la documentation Amazon pour installer l’outil d’interface de ligne de commande (CLI) Amazon EC2 et exécutez la commande pour exporter l’instance EC2 vers un fichier de disque dur virtuel.

	Veillez à utiliser **VHD** pour la variable DISK&#95;IMAGE&#95;FORMAT lors de l’exécution de la commande. Le fichier de disque dur virtuel exporté est enregistré dans le compartiment Amazon S3 que vous désignez au cours de ce processus.

	![][2]

2. Téléchargez le fichier de disque dur virtuel depuis le compartiment S3. Sélectionnez le fichier de disque dur virtuel, puis **Actions** > **Télécharger**.

	![][3]|

#### Copie d’un disque dur virtuel en local

Si vous migrez un disque dur virtuel à partir d’un environnement local, vous avez besoin du chemin d’accès source complet où le disque dur virtuel est stocké. Cela peut être un partage de fichiers ou un emplacement de serveur.

### Copie d’un disque dur virtuel avec AzCopy

À l’aide d’AzCopy, vous pouvez facilement télécharger le disque dur virtuel sur Internet. Selon la taille des disques durs virtuels, cela peut prendre du temps. N’oubliez pas de vérifier les limites d’entrées/sorties de compte de stockage lors de l’utilisation de cette option. Pour plus d’informations, consultez [Objectifs de performance et d’extensibilité d’Azure Storage](storage-scalability-targets.md).

1. Téléchargez et installez AzCopy à partir d’ici : [version la plus récente d’AzCopy](http://aka.ms/downloadazcopy)  
2. Ouvrez Azure PowerShell et accédez au dossier où AzCopy est installé.  
3. Utilisez la commande suivante pour copier le fichier de disque dur virtuel à partir de « Source » vers « Destination ».

		AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>  

	Les paramètres utilisés dans la commande AzCopy sont décrits ci-dessous :

 - **/Source: *&lt;source&gt; :*** emplacement du dossier ou URL du conteneur de stockage qui contient le disque dur virtuel.    
 - **/SourceKey: *&lt;clé\_compte\_source&gt; :*** clé de compte de stockage du compte de stockage source.  
 - **/Dest: *&lt;destination&gt; :*** URL du conteneur de stockage où copier le disque dur virtuel.
 - **/DestKey: *&lt;clé\_compte\_destination&gt; :*** clé de compte de stockage du compte de stockage de destination.
 - **/BlobType: page :** spécifie si la destination est un objet blob de pages.
 - **/Pattern: *&lt;nom\_fichier&gt; :*** spécifiez le nom du fichier de disque dur virtuel que vous copiez.

Pour plus d’informations sur l’utilisation de l’outil AzCopy, consultez [Prise en main de l’utilitaire en ligne de commande AzCopy](storage-use-azcopy.md).

### Autres options de téléchargement d’un disque dur virtuel

Vous pouvez également télécharger un disque dur virtuel sur votre compte de stockage en utilisant l’un des moyens suivants :

- [API de copie d’un objet blob de stockage Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Azure Import/Export Service](https://msdn.microsoft.com/library/dn529096.aspx)

>[AZURE.NOTE]Le service Import/Export permet de copier sur un seul compte de stockage Standard. Vous devez effectuer une copie d’un compte de stockage Standard vers un compte de stockage Premium à l’aide d’un outil tel qu’AzCopy.

## Créer des machines virtuelles Azure à l’aide du stockage Premium

Une fois le disque dur virtuel téléchargé vers le compte de stockage souhaité, suivez les instructions de cette section pour inscrire le disque dur virtuel en tant qu’image de système d’exploitation ou disque de système d’exploitation en fonction de votre scénario et créez ensuite une instance de machine virtuelle à partir de celui-ci. Le disque dur virtuel de disque de données peut être joint à la machine virtuelle qui a été créée.

### Enregistrez votre disque dur virtuel.

Pour créer une machine virtuelle à partir du disque dur virtuel de système d’exploitation ou joindre un disque de données à une nouvelle machine virtuelle, vous devez tout d’abord les inscrire. Suivez les étapes ci-dessous en fonction de votre scénario.

#### Disque dur virtuel de système d’exploitation généralisé pour créer plusieurs instances de machine virtuelle Azure

Une fois le disque dur virtuel d’image du système d’exploitation généralisé téléchargé vers le compte de stockage, inscrivez-le comme une **Image de machine virtuelle Azure** afin de pouvoir créer une ou plusieurs instances de machine virtuelle à partir de celui-ci. Utilisez les applets de commande PowerShell suivantes pour inscrire votre disque dur virtuel comme image de système d’exploitation de la machine virtuelle Azure. Fournissez l’URL de conteneur complet où le disque dur virtuel a été copié.

	Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

Copiez et enregistrez le nom de cette nouvelle image de machine virtuelle Azure. Dans l’exemple ci-dessus, il s’agit de *OSImageName*.

#### Disque dur virtuel de système d’exploitation unique pour créer une seule instance de machine virtuelle Azure

Une fois le disque dur virtuel de système d’exploitation unique téléchargé vers le compte de stockage, inscrivez-le comme **disque de système d’exploitation Azure** afin de pouvoir créer une instance de machine virtuelle à partir de celui-ci. Utilisez les applets de commande PowerShell suivantes pour inscrire votre disque dur virtuel comme disque de système d’exploitation Azure. Fournissez l’URL de conteneur complet où le disque dur virtuel a été copié.

	Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

Copiez et enregistrez le nom de ce nouveau disque de système d’exploitation Azure. Dans l’exemple ci-dessus, il s’agit de *OSDisk*.

#### Disque dur virtuel de disque de données à joindre aux nouvelles instances de machine virtuelle Azure

Une fois le disque dur virtuel de disque de données téléchargé vers le compte de stockage, inscrivez-le comme disque de données Azure afin qu’il puisse être joint à votre nouvelle instance de machine virtuelle Azure de série DS.

Utilisez les applets de commande PowerShell suivantes pour inscrire votre disque dur virtuel comme disque de données Azure. Fournissez l’URL de conteneur complet où le disque dur virtuel a été copié.

	Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

Copiez et enregistrez le nom de ce nouveau disque de données Azure. Dans l’exemple ci-dessus, il s’agit de *DataDisk*.

### Création d’une machine virtuelle Azure de série DS  

Une fois l’image du système d’exploitation ou le disque de système d’exploitation inscrit, créez une nouvelle instance de machine virtuelle Azure de série DS. Vous utiliserez l’image du système d’exploitation ou le nom de disque de système d’exploitation que vous avez inscrit. Sélectionnez le type de machine virtuelle à partir du niveau de stockage Premium. Dans l’exemple ci-dessous, nous utilisons la taille de machine virtuelle *Standard\_DS2*.

>[AZURE.NOTE]Mettez à jour la taille du disque pour vous assurer qu’il correspond à votre capacité, à l’exigence de performance et aux tailles de disque Azure disponibles.

Suivez les applets de commande PowerShell étape par étape ci-dessous pour créer la machine virtuelle. Tout d’abord, définissez les paramètres communs :

	$serviceName = "yourVM"
	$location = "location-name" (e.g., West US)
	$vmSize ="Standard_DS2"
	$adminUser = "youradmin"
	$adminPassword = "yourpassword"
	$vmName ="yourVM"
	$vmSize = "Standard_DS2"

Commencez par créer un service cloud dans lequel vous hébergerez vos nouvelles machines virtuelles.

	New-AzureService -ServiceName $serviceName -Location $location

Ensuite, selon votre scénario, créez l’instance de machine virtuelle Azure à partir de l’image de système d’exploitation ou du disque de système d’exploitation que vous avez inscrit.

#### Disque dur virtuel de système d’exploitation généralisé pour créer plusieurs instances de machine virtuelle Azure

Créez la ou les nouvelles instances de machine virtuelle Azure de série DS à l’aide de l’**Image de système d’exploitation Azure** que vous avez inscrite. Spécifiez ce nom d’image de système d’exploitation dans la configuration de la machine virtuelle lors de la création de nouvelles machines virtuelles comme indiqué ci-dessous.

	$OSImage = Get-AzureVMImage –ImageName "OSImageName"

	$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

	Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm  

	New-AzureVM -ServiceName $serviceName -VM $vm

#### Disque dur virtuel de système d’exploitation unique pour créer une seule instance de machine virtuelle Azure

Créez une nouvelle instance de machine virtuelle Azure de série DS à l’aide du **disque de système d’exploitation Azure** que vous avez inscrit. Spécifiez ce nom de disque du système d’exploitation dans la configuration de la machine virtuelle lors de la création de la nouvelle machine virtuelle comme indiqué ci-dessous.

	$OSDisk = Get-AzureDisk –DiskName "OSDisk"  

	$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

	New-AzureVM -ServiceName $serviceName –VM $vm

Spécifiez d’autres informations de machine virtuelle Azure, comme un service cloud, une région, un compte de stockage, un groupe à haute disponibilité et une stratégie de mise en cache. Notez que l’instance de machine virtuelle doit se trouver avec le système d’exploitation ou les disques de données associés ; le service cloud, la région et le compte de stockage sélectionnés doivent donc tous se trouver au même emplacement que les disques durs virtuels sous-jacents de ces disques.

### Joindre un disque de données  

Enfin, si vous avez inscrit les disques durs virtuels des disques de données, joignez-les à la nouvelle machine virtuelle Azure de série DS.

Utilisez l’applet de commande PowerShell suivante pour joindre un disque de données à la nouvelle machine virtuelle et spécifiez la stratégie de mise en cache. Dans l’exemple ci-dessous, la stratégie de mise en cache est définie sur *ReadOnly*.

	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

	Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm  

	Update-AzureVM  -VM $vm

>[AZURE.NOTE]Des étapes supplémentaires susceptibles de ne pas être exposées dans ce guide peuvent être nécessaires pour prendre en charge vos applications.


## Étapes suivantes  

Consultez les ressources suivantes pour des scénarios spécifiques de migration des machines virtuelles :

- [Migrer des machines virtuelles Azure entre les comptes de stockage](http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)  
- [Création et téléchargement d’un disque dur virtuel Windows Server dans Azure.](../virtual-machines-create-upload-vhd-windows-server.md)  
- [Création et téléchargement d’un disque dur virtuel contenant le système d’exploitation Linux](../virtual-machines-linux-create-upload-vhd.md)  
- [Migration de machines virtuelles à partir d’Amazon AWS vers Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)  

Consultez également les ressources suivantes pour en savoir plus sur Azure Storage and Azure Virtual Machines :

- [Azure Storage](http://azure.microsoft.com/documentation/services/storage/)   
- [Azure Virtual Machines](http://azure.microsoft.com/documentation/services/virtual-machines/)  
- [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](storage-premium-storage-preview-portal.md)  

[1]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png

<!----HONumber=Oct15_HO1-->