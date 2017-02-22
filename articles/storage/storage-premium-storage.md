---
title: Stockage Premium hautes performances et disques de machine virtuelle Azure | Microsoft Docs
description: "Présentation du stockage Premium hautes performances et des disques de machine virtuelle gérés et non gérés. Les machines virtuelles Azure des séries DS, DSv2 et GS prennent en charge Premium Storage."
services: storage
documentationcenter: 
author: ramankumarlive
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: ramankum
translationtype: Human Translation
ms.sourcegitcommit: 3a353bc874c1827f8a0fc85352894ad96cff16b5
ms.openlocfilehash: c9e43df37784999036c6cf250f27a808f79ebe2f


---
# <a name="high-performance-premium-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Stockage Premium hautes performances et disques de machine virtuelle gérés et non gérés
Le stockage Premium Microsoft Azure offre une prise en charge des disques haute performance et à faible latence pour les machines virtuelles exécutant des charges de travail qui utilisent beaucoup d’E/S. Les disques de machine virtuelle qui utilisent le stockage Premium stockent les données sur des disques SSD. Vous pouvez migrer les disques de machine virtuelle de votre application dans Azure Premium Storage pour tirer parti de la vitesse et des performances de ces disques.

Une machine virtuelle Azure prend en charge l’association de plusieurs disques de stockage Premium afin que vos applications puissent avoir jusqu’à 64 To de stockage par machine virtuelle. Avec le stockage Premium, vos applications peuvent atteindre jusqu’à 80 000 opérations d’E/S par seconde par machine virtuelle et un débit de disque de 2 000 Mo/s, avec une latence extrêmement faible pour les opérations de lecture.

Dans le cadre du stockage Premium, Azure offre la possibilité de réellement déplacer vers le cloud vos applications d’entreprise exigeantes comme Dynamics AX, Dynamics CRM, Exchange Server, batteries de serveurs SharePoint et SAP Business Suite. Vous pouvez exécuter une multitude de charges de travail intensives et exigeantes en lien avec les bases de données (comme SQL Server, Oracle, MongoDB, MySQL et Redis) qui nécessitent continuellement des performances élevées et une faible latence sur le stockage Premium.

> [!NOTE]
> Nous vous recommandons de migrer les disques de machine virtuelle nécessitant un nombre élevé d’E/S par seconde dans le stockage Premium pour que votre application bénéficie de performances optimales. Si votre disque ne nécessite pas un nombre élevé d'IOPS, vous pouvez limiter les coûts en le conservant dans le stockage Standard qui stocke les données de disque de machine virtuelle sur des disques durs et non des disques SSD.
> 

Il existe deux façons de créer des disques Premium pour les machines virtuelles Azure :

**Disques non gérés** : avec cette méthode d’origine, vous gérez les comptes de stockage utilisés pour stocker les fichiers VHD qui correspondent aux disques des machines virtuelles. Les fichiers VHD sont stockés en tant qu’objets blob de pages dans les comptes de stockage. 

**[Disques gérés Azure](storage-managed-disks-overview.md)** : cette fonctionnalité gère les comptes de stockage que vous utilisez pour les disques de machines virtuelles. Vous spécifiez le type (Premium ou Standard) et la taille du disque dont vous avez besoin, et Azure crée et gère le disque pour vous. Vous n’avez pas à positionner les disques sur plusieurs comptes de stockage afin de garantir le respect des limites d’extensibilité des comptes de stockage : Azure le fait pour vous.

Même si les deux types de disques sont disponibles, nous vous recommandons d’utiliser des disques gérés pour tirer parti de leurs nombreuses fonctionnalités.

Pour une prise en main du stockage Premium Azure, consultez [Évaluation d’un mois gratuite](https://azure.microsoft.com/pricing/free-trial/) . 

Pour plus d’informations sur la migration de vos machines virtuelles existantes vers le stockage Premium, découvrez comment [migrer une machine virtuelle Windows Azure existante vers des disques gérés](../virtual-machines/virtual-machines-windows-convert-unmanaged-to-managed-disks.md) ou [migrer une machine virtuelle Linux Azure existante vers des disques gérés](../virtual-machines/virtual-machines-linux-convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Premium Storage est actuellement pris en charge dans la plupart des régions. Consultez la liste des régions disponibles dans [Services Azure par région](https://azure.microsoft.com/regions/#services) en recherchant les régions dans lesquelles les machines virtuelles des séries appropriées (DS, DSV2, Fs et GS) sont prises en charge.
> 

## <a name="premium-storage-features"></a>Caractéristiques du stockage Premium

Examinons certaines des fonctionnalités du stockage Premium.

**Disques de stockage Premium** : le stockage Premium Azure prend en charge les disques de machines virtuelles pouvant être associés à des machines virtuelles de taille spécifique, notamment les séries DS, DSv2, GS et Fs. Vous avez le choix entre trois tailles de disque, à savoir, P10 (128 Gio), P20 (512 Gio) et P30 (1 024 Gio), chacun avec ses propres spécifications en matière de performances. Selon les besoins de votre application, vous pouvez associer un ou plusieurs de ces disques à votre machine virtuelle. La section suivante sur les [objectifs de performance et d’extensibilité de Premium Storage ](#premium-storage-scalability-and-performance-targets) décrit ces spécifications plus en détail.

**Objet blob de pages Premium** : le stockage Premium prend en charge les objets blob de pages, utilisés pour stocker les disques persistants non gérés des machines virtuelles. Contrairement au stockage Standard, le stockage Premium ne prend pas en charge les objets blob de blocs, les objets blob d’ajout, les fichiers, les tables ou les files d’attente.
Tout objet placé dans un compte de stockage Premium est un objet blob de pages, et il s’aligne sur une des tailles configurées prises en charge. Un compte de stockage Premium n’est donc pas conçu pour stocker de petits objets blob.

**Compte de stockage Premium** : pour commencer à utiliser le stockage Premium, créez un compte de stockage Premium pour les disques non gérés. Si vous préférez utiliser le [portail Azure](https://portal.azure.com), vous pouvez créer un compte de stockage Premium en spécifiant le niveau de performances « Premium » et le « stockage localement redondant (LRS) » comme option de réplication. Vous pouvez également créer un compte de stockage Premium en spécifiant le type « Premium_LRS » à l’aide de [l’API REST de stockage](/rest/api/storageservices/fileservices/Azure-Storage-Services-REST-API-Reference) version 2014-02-14 ou ultérieure, [l’API REST de gestion des services](http://msdn.microsoft.com/library/azure/ee460799.aspx) version 2014-10-01 ou ultérieure (déploiements classiques), la [Référence de l’API REST du fournisseur de ressources Azure Storage](/rest/api/storagerp) (déploiements Resource Manager) et [Azure PowerShell](../powershell-install-configure.md) version 0.8.10 ou ultérieure. Pour en savoir plus sur les limites de compte de stockage Premium, voir la section suivante sur les [objectifs de performance et d’extensibilité de Premium Storage](#premium-storage-scalability-and-performance-targets.md).

**Stockage Premium localement redondant** : un compte de stockage Premium prend uniquement en charge l’option Stockage localement redondant (LRS) pour la réplication. Il conserve donc trois copies des données dans une même région. Pour plus d’informations sur la géo-réplication pendant l’utilisation de Premium Storage, voir la section [Captures instantanées et copie d’objets blob](#snapshots-and-copy-blob) dans cet article.

Azure utilise le compte de stockage comme conteneur pour vos disques non gérés. Lorsque vous créez une machine virtuelle Azure de série DS, DSv2, GS ou Fs avec des disques non gérés, puis sélectionnez un compte de stockage Premium, votre système d’exploitation et les disques de données sont stockés dans ce compte de stockage.

## <a name="premium-storage-supported-vms"></a>Machines virtuelles non prises en charge par le stockage Premium
Le stockage Premium prend en charge les machines virtuelles des séries DS, DSv2, GS et Fs. Vous pouvez utiliser des disques de stockage Standard et Premium avec ces machines virtuelles. Vous ne pouvez pas utiliser des disques de stockage Premium avec des séries de machines virtuelles non compatibles avec le stockage Premium.

Pour plus d’informations sur les tailles et les types de machines virtuelles Azure disponibles en ce qui concerne les machines virtuelles Windows, consultez [Tailles des machines virtuelles Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pour plus d’informations sur les tailles et les types de machines virtuelles disponibles en ce qui concerne les machines virtuelles Linux, consultez [Tailles des machines virtuelles Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Voici quelques-unes des caractéristiques des machines virtuelles des séries DS, DSv2, GS et Fs :

**Service cloud**: vous pouvez ajouter des machines virtuelles de la série DS à un service cloud incluant uniquement des machines virtuelles de ce type. Évitez d’ajouter des machines virtuelles de la série DS à un service cloud incluant des machines virtuelles d’un autre type. Vous pouvez migrer vos disques durs virtuels vers un nouveau service cloud exécutant uniquement des machines virtuelles de la série DS. Si vous souhaitez conserver la même adresse IP virtuelle (VIP) pour le nouveau service cloud hébergeant vos machines virtuelles de la série DS, utilisez les [adresses IP réservées](../virtual-network/virtual-networks-instance-level-public-ip.md). Des machines virtuelles de la série GS peuvent être ajoutés à un service cloud existant exécutant seulement des machines virtuelles de la série GS.

**Disque de système d’exploitation** : les machines virtuelles en mesure de s’exécuter sur du stockage Premium peuvent être configurées pour utiliser un disque de système d’exploitation Premium ou Standard. Nous vous recommandons d’utiliser un disque de système d’exploitation basé sur du stockage Premium pour une meilleure expérience.

**Disques de données** : vous pouvez utiliser des disques Standard et Premium dans une même machine virtuelle s’exécutant sur du stockage Premium. Avec le stockage Premium, vous pouvez configurer une machine virtuelle et associer plusieurs disques de données persistants sur la machine virtuelle. Si nécessaire, vous pouvez répartir les données sur les disques pour augmenter la capacité et les performances du volume.

> [!NOTE]
> Si vous équilibrez les disques de données de stockage Premium à l’aide des [espaces de stockage](http://technet.microsoft.com/library/hh831739.aspx), vous devez les configurer avec une colonne pour chaque disque utilisé. Dans le cas contraire, les performances globales du volume agrégé par bandes peuvent être limitées, en raison d'une distribution inégale du trafic sur les disques. Par défaut, l’interface utilisateur (IU) du Gestionnaire de serveur vous permet de créer des colonnes jusqu’à 8 disques. Si vous avez plus de 8 disques, vous devez utiliser PowerShell pour créer le volume et spécifier manuellement le nombre de colonnes. Dans le cas contraire, l'IU du Gestionnaire de serveur continue d'utiliser 8 colonnes, même si vous disposez de plusieurs disques. Par exemple, si vous disposez de 32 disques dans un agrégat unique, vous devez spécifier 32 colonnes. Vous pouvez utiliser le paramètre *NumberOfColumns* de l’applet de commande PowerShell [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) pour spécifier le nombre de colonnes utilisées par le disque virtuel. Pour plus d’informations, consultez [Vue d’ensemble des espaces de stockage](http://technet.microsoft.com/library/hh831739.aspx) et [Forum aux Questions sur les espaces de stockage](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
> 

**Cache** : les machines virtuelles des séries prenant en charge le stockage Premium ont une capacité de mise en cache unique, permettant d’obtenir des niveaux élevés de débit et de latence, supérieurs aux performances du disque de stockage Premium sous-jacent. Vous pouvez configurer une stratégie de mise en cache de disque sur les disques de stockage Premium en ReadOnly (lecture seule), ReadWrite (lecture/écriture) ou None (aucune). La stratégie de mise en cache de disque par défaut est ReadOnly pour tous les disques de données Premium, et ReadWrite pour les disques de système d’exploitation. Utilisez le paramètre de configuration approprié pour optimiser les performances de votre application. Ainsi, pour préparer des disques de données lourds ou en lecture seule, contenant par exemple des fichiers de données SQL Server, définissez la stratégie de mise en cache « ReadOnly ». Pour écrire des disques de données lourds ou en écriture seule, contenant, par exemple, des fichiers journaux SQL Server, définissez la stratégie de mise en cache sur « None ». Pour en savoir plus sur l’optimisation de votre conception avec Premium Storage, consultez [Conception optimisée pour les performances avec Premium Storage](storage-premium-storage-performance.md).

**Analyses** : pour analyser les performances de machines virtuelles utilisant des disques sur du stockage Premium, vous pouvez activer les diagnostics de machine virtuelle dans le [portail Azure](https://portal.azure.com). Pour plus d’informations, reportez-vous à la rubrique [Surveillance des machines virtuelles Microsoft Azure avec l’extension Azure Diagnostics](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) . Pour afficher les performances du disque, utilisez les outils du système d’exploitation, tels que [l’Analyseur de performances Windows](https://technet.microsoft.com/library/cc749249.aspx) pour les machines virtuelles Windows, et [IOSTAT](http://linux.die.net/man/1/iostat) pour les machines virtuelles Linux.

**Performances et limites de mise à l’échelle des machines virtuelles** : à chaque taille de machine virtuelle prise en charge par le stockage Premium est associée une spécification relative aux performances et limites de mise à l’échelle en relation avec les opérations d’E/S par seconde, la bande passante et le nombre de disques pouvant être connectés par machine virtuelle. Lorsque vous utilisez des disques de stockage Premium avec des machines virtuelles, assurez-vous de disposer d’une capacité suffisante d’E/S par seconde et de bande passante sur vos machines virtuelles pour gérer le trafic du disque.
Par exemple, une machine virtuelle STANDARD_DS1 a une bande passante dédiée de 32 Mo/s pour le trafic des disques de stockage Premium. Un disque de stockage Premium P10 peut fournir 100 Mo/s de bande passante. Si un disque de stockage Premium P10 est associé à cette machine virtuelle, il ne peut pas dépasser 32 Mo/s, au lieu des 100 Mo/s qu’il est capable d’atteindre.

Actuellement, la machine virtuelle de série DS la plus puissante est Standard_DS15_v2, qui offre un débit maximum de 960 Mo/s sur l’ensemble des disques. La machine virtuelle de série GS la plus puissante est Standard_GS5, qui offre un débit allant jusqu’à 2000 Mo/s sur l’ensemble des disques.
Notez que ces limites ne valent que pour le trafic de disques, pas pour les présences dans le cache ou le trafic réseau. Une bande passante distincte est disponible pour le trafic réseau des machines virtuelles. Il ne s’agit pas de la bande passante dédiée aux disques de stockage Premium.

Pour obtenir les informations les plus récentes sur le maximum d’E/S par seconde et de bande passante concernant les machines virtuelles prises en charge par le stockage Premium, consultez [Tailles des machines virtuelles Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Tailles des machines virtuelles Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour en savoir plus sur les disques de stockage Premium et leurs limites d’E/S par seconde et de débit, voir le tableau de la section relative aux [objectifs de performances et d’extensibilité du stockage Premium](#premium-storage-scalability-and-performance-targets) dans cet article.

## <a name="premium-storage-scalability-and-performance-targets"></a>Objectifs de performance et d’extensibilité du stockage Premium
Dans cette section, nous allons décrire les objectifs de performances et d’extensibilité à prendre en considération lors de l’utilisation de Premium Storage.

Les objectifs d’extensibilité des comptes de stockage Premium sont les suivants :

| Capacité totale des comptes | Bande passante totale pour un compte de stockage localement redondant |
| --- | --- | 
| Capacité du disque : 35 To <br>Capacité d’instantanés : 10 To | Jusqu'à 50 Go par seconde pour les données entrantes/sortantes |

* Les données entrantes sont toutes les données (demandes) envoyées à un compte de stockage.
* Les données sortantes sont toutes les données (réponses) reçues d'un compte de stockage.

Pour plus d'informations, consultez [Objectifs d'extensibilité et de performances d'Azure Storage](storage-scalability-targets.md).

Si vous utilisez des comptes de stockage Premium pour des disques non managés et que votre application dépasse les objectifs d’extensibilité d’un compte de stockage unique, vous pouvez envisager la migration vers des disques gérés. Si vous ne souhaitez pas migrer vers des disques gérés, générez votre application pour utiliser plusieurs comptes de stockage et partitionnez vos données sur ces différents comptes. Par exemple, si vous souhaitez associer des disques de 51 To sur plusieurs machines virtuelles, répartissez-les entre deux comptes de stockage, la limite d’un compte de stockage Premium étant de 35 To. Vérifiez qu’un compte de stockage Premium n’a jamais plus de 35 To de disques configurés.

### <a name="premium-storage-disk-limits"></a>Limites des disques de stockage Premium
Lorsque vous configurez un disque de stockage Premium, la taille du disque détermine la valeur maximale d’E/S par seconde et le débit (bande passante). Il existe trois types de disques de stockage Premium : P10, P20 et P30. Chaque type présente des limites spécifiques en matière d'IOPS et de débit, comme indiqué dans le tableau suivant :

|Type de disque de stockage Premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Taille du disque | 128 Go | 512 Go | 1024 Go (1 To) |
| IOPS par disque | 500 | 2 300 | 5 000 |
Débit par disque | 100 Mo/s | 150 Mo/s | 200 Mo/s |

> [!NOTE]
> Vérifiez que la bande passante disponible sur votre machine virtuelle est suffisante pour le trafic de disques, comme l’explique la section [Machines virtuelles prises en charge par le stockage Premium](#premium-storage-supported-vms) plus haut dans cet article. Dans le cas contraire, le débit et l’IOPS du disque seront limitées à des valeurs inférieures basées sur les limites de la machine virtuelle, et non aux valeurs de disque mentionnées dans le tableau précédent.  
> 
> 

Voici quelques éléments importants que vous devez savoir sur les objectifs de performances et d’extensibilité de Premium Storage :

* **Capacité et performances configurées** : lorsque vous configurez un disque de stockage Premium, contrairement au stockage standard, la capacité, les E/S par seconde et le débit de ce disque sont garantis. Par exemple, si vous créez un disque P30, Azure configure une capacité de stockage de 1 024 Go, 5 000 E/S par seconde et un débit de 200 Mo/s pour ce disque. Votre application peut utiliser tout ou partie de la capacité et des performances.

* **Taille du disque** : Azure mappe la taille du disque (arrondie à la valeur supérieure) sur l’option de disque de stockage Premium la plus proche, comme indiqué dans le tableau. Par exemple, un disque de 100 Go correspond à l’option P10 et peut prendre en charge jusqu’à 500 E/S par seconde et un débit de 100 Mo/s. De même, un disque de 400 Go correspond à l’option P20 et peut prendre en charge jusqu’à 2 300 E/S par seconde et un débit de 150 Mo/s.
  
> [!NOTE]
> Vous pouvez facilement augmenter la taille des disques existants. Par exemple, vous pouvez augmenter la taille d’un disque de 30 Go à 128 Go ou même 1 To. Ou bien, vous pouvez convertir votre disque P20 en disque P30, si vous avez besoin de davantage de capacités ou de plus d’E/S par seconde et de débit. 
> 
 
* **Taille d’E/S**: la taille d’une unité d’E/S est de 256 Ko. Si la taille des données transférées est inférieure à 256 Ko, elles sont considérées comme une seule unité d'E/S. Les tailles d'E/S supérieures sont divisées en plusieurs unités d'E/S de 256 Ko. Par exemple, 1 100 Ko d'E/S correspond à cinq unités d'E/S.

* **Débit**: la limite de débit comprend les écritures sur le disque et les lectures à partir de ce dernier, qui ne sont pas effectuées à partir du cache. Par exemple, un disque P10 offre un débit de 100 Mo/s. Les débits valides pour un disque P10 sont, par exemple,

| Débit maximum par disque P10 | Lectures à partir du disque non mises en cache | Lectures à partir sur le disque non mises en cache |
| --- | --- | --- |
| 100 Mo par seconde | 100 Mo par seconde | 0 |
| 100 Mo par seconde | 0 | 100 Mo par seconde |
| 100 Mo par seconde | 60 Mo par seconde | 40 Mo par seconde |

* **Présences dans le cache** : les présences dans le cache ne sont pas limitées par les E/S par seconde ou le débit du disque alloués. Par exemple, quand vous utilisez un disque de données avec le paramètre de cache ReadOnly sur une machine virtuelle prise en charge par le stockage Premium, les lectures effectuées à partir du cache ne sont pas soumises aux limites d’E/S par seconde et de débit du disque. Vous pouvez donc obtenir un très haut débit à partir d’un disque, si la charge de travail concerne essentiellement des lectures. Notez que le cache est soumis à des limites d’E/S par seconde et de débit séparées au niveau de la machine virtuelle, en fonction de la taille de celle-ci. Les machines virtuelles DS exécutent environ 4000 IOPS et ont un débit de 33 Mo/s par cœur pour les E/S du cache et du disque SSD local. Les machines virtuelles de la série GS sont limitées à 5 000 IOPS et à 50 Mo/s par cœur pour le cache et les E/S du disque SSD local. 

## <a name="throttling"></a>Limitation
Vous pouvez constater une limitation de bande passante si les E/S par seconde ou le débit de votre application dépassent les limites allouées à un disque de stockage Premium, ou si le trafic total de l’ensemble des disques de la machine virtuelle dépasse la limite de bande passante de disque disponible pour la machine virtuelle. Pour éviter cette situation, nous vous recommandons de limiter le nombre de demandes d’E/S en attente, en fonction des objectifs d’extensibilité et de performances du disque configuré et de la bande passante de disques disponible pour la machine virtuelle.  

Quand votre application est conçue pour éviter les limitations, elle bénéficie d'une latence moindre. En revanche, si le nombre de demandes d'E/S en attente est trop faible, votre application ne peut pas bénéficier des niveaux maximum de débit et d'IOPS disponibles sur le disque.

Les exemples suivants montrent comment calculer les niveaux de limitation. Tous les calculs sont basés sur une taille d’unité d’E/S de 256 Ko.

### <a name="example-1"></a>Exemple 1 :
Votre application a traité 495 unités d’E/S d’une taille de 16 Ko par seconde sur un disque P10. Celles-ci sont comptées comme 495 unités d’E/S par seconde (IOPS). Si vous tentez une E/S de 2 Mo dans la même seconde, le total d'unités d'E/S est égal à 495 + 8. En effet, une E/S de 2 Mo équivaut à 2 048 Ko / 256 Ko = 8 unités d'E/S, avec une taille d'unité d'E/S égale à 256 Ko. Étant donné que la somme de 495 + 8 dépasse la limite de 500 IOPS du disque, il se produit une limitation.

### <a name="example-2"></a>Exemple 2 :
Votre application a traité 400 unités d’E/S de 256 Ko sur un disque P10. La bande passante totale consommée est de (400 * 256) / 1 024 = 100 Mo/s. Un disque P10 a une limite de débit de 100 Mo par seconde. Si votre application tente d'effectuer davantage d'E/S dans la même seconde, elle sera limitée, car elle dépassera la limite allouée.

### <a name="example-3"></a>Exemple 3 :
Vous avez une machine virtuelle DS4 avec deux disques P30. Chaque disque P30 peut gérer un débit de 200 Mo par seconde. Toutefois, une machine virtuelle DS4 a une bande passante disque maximale de 256 Mo par seconde. Par conséquent, vous ne pouvez pas obtenir un débit maximal sur tous les disques de cette machine virtuelle DS4 en même temps. La solution consiste à maintenir un trafic de 200 Mo par seconde sur un disque et un trafic de 56 Mo par seconde sur l'autre disque. Si la somme de votre trafic de disques excède 256 Mo par seconde, il est limité.

> [!NOTE]
> si le trafic du disque consiste essentiellement en des E/S de petite taille, il est très probable que votre application atteindra la limite d'IOPS avant la limite de débit. En revanche, si le trafic du disque consiste essentiellement en des E/S de grande taille, il est très probable que votre application atteindra la limite de débit avant la limite d'IOPS. Vous pouvez optimiser la capacité de votre application en termes d’IOPS et de débit en utilisant des tailles d’E/S optimales et en limitant le nombre de demandes d’E/S en attente pour le disque.
> 

Pour en savoir plus sur la conception appropriée permettant des performances élevées à l’aide de Premium Storage, consultez [Conception optimisée pour les performances avec Premium Storage](storage-premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Captures instantanées et copie d’objets blob

Pour le service de stockage, le fichier VHD est un objet blob de pages. Vous pouvez réaliser des captures instantanées d’objets blob de pages et les copier dans un autre emplacement, tel qu’un compte de stockage différent.

### <a name="unmanaged-disks"></a>Disques non gérés

Vous pouvez créer des [captures instantanées incrémentielles](storage-incremental-snapshots.md) pour les disques Prmium non gérés, de la même manière que vous utilisez des captures instantanées avec le stockage Standard. Étant donné que Premium Storage prend uniquement en charge l’option Stockage localement redondant (LRS) pour la réplication, nous vous recommandons de créer des captures instantanées et de les copier sur un compte de stockage Standard géo-redondant. Pour plus d'informations, consultez [Options de redondance du stockage Azure](storage-redundancy.md).

Si un disque est associé à une machine virtuelle, certaines opérations d’API ne sont pas autorisées sur les disques. Par exemple, vous ne pouvez pas effectuer une opération [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob) sur cet objet blob, tant que le disque est attaché à une machine virtuelle. À la place, commencez par créer une capture instantanée de cet objet blob à l’aide de la méthode [Snapshot Blob](/rest/api/storageservices/fileservices/Snapshot-Blob) de l’API REST, puis exécutez l’opération [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob) de la capture instantanée pour copier le disque attaché. Vous pouvez également dissocier le disque et effectuer les opérations nécessaires.

Les limites suivantes s’appliquent aux captures instantanées d’objets blob de stockage Premium :

| Limites de Premium Storage | Valeur |
| --- | --- |
| Bande passante nombre d’instantanés par objet blob | 100 |
| Capacité du compte de stockage pour les instantanés (inclut uniquement les données des instantanés, pas celles d’un objet blob de base) | 10 To |
| min. intervalle entre deux instantanés consécutifs | 10 minutes |

Pour conserver des copies géo-redondantes de vos captures instantanées, vous pouvez copier des captures instantanées d'un compte de stockage Premium vers un compte de stockage Standard géo-redondant à l'aide des opérations AzCopy ou Copy Blob. Pour plus d’informations, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md) et [Copie d’un objet blob](/rest/api/storageservices/fileservices/Copy-Blob).

Pour plus d’informations sur l’exécution d’opérations REST sur les objets blob de pages dans les comptes de stockage Premium, consultez [Utilisation des opérations de service blob avec le stockage Azure Premium](http://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Disques gérés

Une capture instantanée d’un disque géré est une copie en lecture seule du disque géré qui est stockée comme un disque géré standard. Pour le moment, les [captures instantanées incrémentielles](storage-incremental-snapshots.md) ne sont pas prises en charge pour les disques gérés, mais elles le seront dans le futur. Pour savoir comment réaliser une capture instantanée d’un disque géré, consultez [Create a copy of a VHD stored as an Azure Managed Disk by using Managed Snapshots in Windows](../virtual-machines/virtual-machines-windows-snapshot-copy-managed-disk.md) (Créer une copie de disque dur virtuel stocké en tant que disque géré Azure à l’aide des captures instantanées gérées dans Windows) ou [Create a copy of a VHD stored as an Azure Managed Disk by using Managed Snapshots in Linux](../virtual-machines/linux/virtual-machines-linux-snapshot-copy-managed-disk.md) (Créer une copie de disque dur virtuel stocké en tant que disque géré Azure à l’aide des captures instantanées gérées dans Linux).

Si un disque géré est associé à une machine virtuelle, certaines opérations d’API ne sont pas autorisées sur les disques. Par exemple, vous ne pouvez pas générer une signature d’accès partagé (SAP) pour effectuer une opération de copie alors que le disque est associé à une machine virtuelle. Au lieu de cela, commencez par créer une capture instantanée du disque, puis réalisez-en une copie. Sinon, vous pouvez aussi dissocier le disque, puis générer une signature d’accès partagé (SAP) pour réaliser l’opération de copie.


## <a name="using-linux-vms-with-premium-storage"></a>Utilisation de machines virtuelles Linux avec le stockage Premium
Consultez les instructions importantes ci-dessous pour configurer vos machines virtuelles Linux sur un stockage Premium :

* Pour tous les disques de stockage Premium dont le paramètre de cache est défini sur « ReadOnly » ou « None », vous devez désactiver les « barrières » lors du montage du système de fichiers pour atteindre les objectifs d’extensibilité du stockage Premium. Ces barrières sont inutiles dans ce cas de figure, car les écritures sur les disques de stockage Premium sont pérennes avec ces paramètres de cache. Lorsque la demande d'écriture est terminée, les données sont stockées dans le magasin permanent. Utilisez les méthodes suivantes pour désactiver les « barrières », en choisissant celle qui convient pour votre système de fichiers :
  
* Si vous utilisez **reiserFS**, désactivez les barrières à l’aide de l’option de montage « barrier=none ». (Pour activer les barrières, utilisez « barrier=flush ».)
* Si vous utilisez **ext3/ext4**, désactivez les barrières à l’aide de l’option de montage « barrier=0 ». (Pour activer les barrières, utilisez « barrier=1 ».)
* Si vous utilisez **XFS**, désactivez les barrières à l’aide de l’option de montage « nobarrier ». (Pour activer les barrières, utilisez l’option « barrier ».)
* Pour les disques de stockage Premium dont le paramètre de cache est « ReadWrite », les barrières doivent être activées pour pérenniser les écritures.
* Pour conserver les étiquettes de volume après le redémarrage de la machine virtuelle, vous devez mettre à jour /etc/fstab avec les références UUID aux disques. Reportez-vous également à [Ajouter un disque géré à une machine virtuelle Linux](../virtual-machines/virtual-machines-linux-add-disk.md).

Les distributions Linux suivantes ont été validées avec le stockage Premium. Nous vous recommandons de mettre vos machines virtuelles au niveau de l’une de ces versions (ou d’une version ultérieure) pour améliorer les performances et la stabilité du stockage Premium. De plus, certaines versions nécessitent également la dernière version&4;.0 de Linux Integration Services (LIS) pour Microsoft Azure. Cliquez sur le lien ci-dessous pour effectuer le téléchargement et l'installation. Nous allons continuer à ajouter des images à la liste, à chaque validation supplémentaire. Veuillez noter que, selon nos validations, les performances varient pour ces images. Elles dépendent également des caractéristiques de la charge de travail et des paramètres des images. Chaque image est optimisée pour des charges de travail particulières.

| Distribution | Version | Noyau pris en charge | Détails |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-Server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-Server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 requis](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Voir la remarque ci-dessous* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 recommandé](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Voir la remarque ci-dessous* |
| RHEL | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 ou RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 ou RHCK avec [LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 ou RHCK avec [LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Pilotes LIS pour Openlogic CentOS

Les clients exécutant des machines virtuelles OpenLogic CentOS doivent exécuter la commande suivante pour installer les pilotes les plus récents :

```
sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
sudo yum install microsoft-hyper-v
```

Un redémarrage est nécessaire pour activer les nouveaux pilotes.

## <a name="pricing-and-billing"></a>Tarification et facturation

Les considérations de facturation suivantes s'appliquent à l'utilisation du stockage Premium :

* Taille de disque de stockage Premium/de l’objet blob
* Captures instantanées du stockage Premium
* Transferts de données sortantes

**Taille de disque de stockage Premium/de l’objet blob** : la facturation pour un disque de stockage Premium/un objet blob dépend de la taille configurée du disque/de l’objet blob. Azure mappe la taille approvisionnée (arrondie à la valeur supérieure) sur l’option de disque de stockage Premium la plus proche, comme indiqué dans le tableau de la section [Objectifs d’extensibilité et de performances avec le stockage Premium](#premium-storage-scalability-and-performance-targets). Chaque disque est mappé sur l’une des tailles prises en charge configurées et est facturé en conséquence. La facturation de n'importe quel disque configuré est calculée au prorata horaire sur la base du tarif mensuel de l'offre de stockage Premium. Par exemple, si vous configurez un disque P10 et le supprimez au bout de 20 heures, vous êtes facturé 20 heures pour l'offre P10. Le montant facturé est indépendant de la quantité de données écrites sur le disque ou de la quantité de débit/IOPS utilisés.

**Captures instantanées de disques de stockage Premium** : les captures instantanées sur disque non gérés Premium sont facturées en fonction de la capacité supplémentaire utilisée par les captures instantanées. Pour plus d'informations sur les captures instantanées, consultez [Création d'un instantané d'objet blob](/rest/api/storageservices/fileservices/Snapshot-Blob).

**Captures instantanées de disques gérés Premium** : une capture instantanée d’un disque géré est une copie en lecture seule du disque qui est stockée comme un disque géré Standard. Le coût d’une capture instantanée est le même que celui d’un disque géré Standard. Par exemple, si vous avez une capture instantanée d’un disque géré Premium de 128 Go, le coût d’une capture instantanée équivaut à celui d’un disque Standard de 128 Go.  

**Transferts de données sortantes**: les [transferts de données sortantes](https://azure.microsoft.com/pricing/details/data-transfers/) (données sortant des centres de données Azure) sont facturés en fonction de la bande passante utilisée.

Pour plus d’informations sur la tarification du stockage Premium, les machines virtuelles prises en charge par le stockage Premium et les disques gérés, consultez :

* [Tarification d’Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Tarification des disques gérés](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-service-support"></a>Prise en charge du service Azure Backup 

Les machines virtuelles avec disques non gérés peuvent être sauvegardées à l’aide d’Azure Backup. [Détails supplémentaires](../backup/backup-azure-vms-first-look-arm.md).

Vous pouvez également utiliser le service Azure Backup avec des disques gérés pour créer un travail de sauvegarde avec des sauvegardes périodiques, une restauration facile des machines virtuelles et des stratégies de rétention de sauvegarde. Vous pouvez en savoir plus sur ce point dans [Using Azure Backup service for VMs with Managed Disks](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup) (Utilisation du service Azure Backup pour les machines virtuelles avec disques gérés). 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur Azure Storage Premium, voir les articles suivants.

### <a name="design-and-implement-with-azure-premium-storage"></a>Conception et implémentation avec Azure Storage Premium
* [Conception optimisée pour les performances avec Premium Storage](storage-premium-storage-performance.md)
* [Utilisation des opérations de service blob avec le stockage Azure Premium](http://go.microsoft.com/fwlink/?LinkId=521969)

### <a name="operational-guidance"></a>Instructions d’utilisation
* [Migration vers le stockage Premium Azure](storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>Billets de blog
* [Mise à la disposition générale d’Azure Storage Premium](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
* [Annonce de la série GS : ajout de prise en charge de Premium Storage pour les machines virtuelles les plus grandes dans le Cloud Public](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)


<!--HONumber=Feb17_HO2-->


