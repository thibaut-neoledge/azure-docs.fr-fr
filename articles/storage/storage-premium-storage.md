<properties
	pageTitle="Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure | Microsoft Azure"
	description="Premium Storage offre une prise en charge très performante et à faible latence des disques pour les charges de travail utilisant beaucoup d'E/S exécutées sur les machines virtuelles Azure. Les machines virtuelles Azure des séries DS et GS prennent en charge Premium Storage."
	services="storage"
	documentationCenter=""
	authors="ms-prkhad"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="robinsh;prkhad"/>


# Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure

## Vue d'ensemble

Azure Premium Storage offre une prise en charge très performante et à faible latence des disques pour les machines virtuelles exécutant des charges de travail qui utilisent beaucoup d'E/S. Les disques de machine virtuelle qui utilisent Premium Storage stockent les données sur des disques SSD. Vous pouvez migrer les disques de machine virtuelle de votre application dans Azure Premium Storage pour tirer parti de la vitesse et des performances de ces disques.

Une machine virtuelle Azure prend en charge l’association de plusieurs disques Premium Storage afin que vos applications puissent avoir jusqu'à 64 To de stockage par machine virtuelle. Avec Premium Storage, vos applications peuvent atteindre jusqu'à 80 000 IOPS (opérations d'E/S par seconde) par machine virtuelle et un débit de disque de 2 000 Mo par seconde, avec une latence extrêmement faible pour les opérations de lecture.

Avec Premium Storage, Azure offre la possibilité de réellement déplacer vers le cloud vos applications d’entreprise exigeantes comme Dynamics AX, Dynamics CRM, Exchange Server, batteries de serveurs SharePoint et SAP Business Suite. Vous pouvez exécuter une multitude de charges de travail intensives et exigeantes en lien avec les bases de données (comme SQL Server, Oracle, MongoDB, MySQL, Redis) qui nécessitent continuellement des performances élevées et une faible latence sur Premium Storage.

>[AZURE.NOTE] Nous vous recommandons de migrer les disques de machine virtuelle nécessitant un nombre élevé d’IOPS dans Azure Premium Storage pour que votre application bénéficie de performances optimales. Si votre disque ne nécessite pas un nombre élevé d'IOPS, vous pouvez limiter les coûts en le conservant dans le stockage Standard qui stocke les données de disque de machine virtuelle sur des disques durs et non des disques SSD.

Pour une prise en main du stockage Azure Premium, consultez la page [Évaluation d’un mois gratuite](https://azure.microsoft.com/pricing/free-trial/). Pour plus d’informations sur la migration de vos machines virtuelles existantes vers Premium Storage, consultez la rubrique [Migration vers Azure Premium Storage](storage-migration-to-premium-storage.md).

## Considérations sur le stockage Premium

Voici une liste des points importants à prendre en compte avant ou pendant l'utilisation du stockage Premium :

- Pour utiliser le stockage Premium, vous devez disposer d'un compte de stockage Premium. Pour savoir comment créer un compte de stockage Premium, consultez [Création et utilisation d'un compte de stockage Premium pour disques](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

- Premium Storage est disponible dans le [portail Azure](https://portal.azure.com) et accessible grâce aux bibliothèques suivantes du Kit de développement logiciel (SDK) : [API REST de stockage](http://msdn.microsoft.com//library/azure/dd179355.aspx) version 2014-02-14 ou ultérieure ; [API REST de gestion des services](http://msdn.microsoft.com/library/azure/ee460799.aspx) version 2014-10-01 ou ultérieure (déploiements classiques) ; [API du fournisseur de ressources de stockage](http://msdn.microsoft.com/library/azure/mt163683.aspx) (déploiements ARM) et [Azure PowerShell](../install-configure-powershell.md) version 0.8.10 ou ultérieure.

- Pour obtenir la liste des régions qui prennent actuellement en charge Premium Storage, consultez [Services Azure par région](https://azure.microsoft.com/regions/#services).

- Le stockage Premium prend uniquement en charge les objets blob de pages Azure, utilisés pour stocker les disques persistants des machines virtuelles Azure. Pour plus d’informations sur les objets blob de pages Azure, consultez [Présentation des objets blob de blocs, des objets blob d’ajout et des objets blob de pages](http://msdn.microsoft.com/library/azure/ee691964.aspx). Premium Storage ne prend pas en charge les objets blob de blocs Azure, les objets blob d’ajout Azure, les fichiers Azure, les tables Azure et les files d’attente Azure.

- Un compte de stockage Premium est localement redondant (LRS) et conserve trois copies des données dans une même région. Concernant la géo-réplication pendant l'utilisation du stockage Premium, consultez la section [Captures instantanées et copie d'objets blob avec le stockage Premium](#snapshots-and-copy-blob-whfr-FRing-premium-storage) dans cet article.

- Si vous souhaitez utiliser un compte de stockage Premium pour vos disques de machine virtuelle, vous devez utiliser des machines virtuelles de série DS ou GS. Vous pouvez utiliser des disques de stockage Standard et Premium avec les machines virtuelles de série DS ou GS. Vous ne pouvez cependant pas utiliser de disques de stockage Premium avec des machines virtuelles qui ne sont pas de série DS ou GS. Pour plus d’informations sur les tailles et les types de disque de machine virtuelle Azure disponibles, consultez [Tailles des machines virtuelles](../virtual-machines/virtual-machines-size-specs.md).

- Le processus de configuration des disques de stockage Premium pour une machine virtuelle est semblable aux disques de stockage Standard. Vous devez choisir l'option de stockage Premium qui convient le mieux à vos disques et votre machine virtuelle Azure. La taille de la machine virtuelle doit être adaptée à votre charge de travail en fonction des caractéristiques de performances de l'offre Premium. Pour plus d'informations, consultez [Objectifs d'extensibilité et de performances avec le stockage Premium](#scalability-and-performance-targets-whfr-FRing-premium-storage).

- Un compte de stockage premium ne peut pas être mappé sur un nom de domaine personnalisé.

- L'analyse de stockage n'est pas prise en charge pour le stockage Premium pour l'instant. Pour analyser les métriques de performances des machines virtuelles à l'aide de disques sur des comptes de stockage Premium, utilisez les outils du système d'exploitation, tels que [Analyseur de performances Windows](https://technet.microsoft.com/library/cc749249.aspx) pour les machines virtuelles Windows et [IOSTAT](http://linux.die.net/man/1/iostat) pour les machines virtuelles Linux. Vous pouvez également activer les diagnostics de machine virtuelle Azure dans le portail Azure. Pour plus d’informations, reportez-vous à la rubrique [Surveillance des machines virtuelles Microsoft Azure avec l’extension Azure Diagnostics](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/).

## Utilisation du stockage Premium pour disques
Vous pouvez utiliser le stockage Premium pour disques de deux manières :

- Commencez par créer un nouveau compte de stockage Premium et utilisez-le pour créer la machine virtuelle.
- Créez une machine virtuelle de série DS ou GS. Pendant la création de la machine virtuelle, vous pouvez sélectionner un compte de stockage Premium créé précédemment, en créer un ou laisser le portail Azure créer un compte Premium par défaut.

Azure utilise le compte de stockage comme conteneur pour votre système d'exploitation et vos disques de données. En d’autres termes, si vous créez une machine virtuelle Azure de série DS ou GS et que vous sélectionnez un compte de stockage Azure Premium, votre système d’exploitation et les disques de données sont stockés dans ce compte de stockage.

Pour plus d'informations sur la migration de vos machines virtuelles existantes vers Premium Storage, consultez [Azure Premium Storage](storage-migration-to-premium-storage.md).

Pour bénéficier des avantages du stockage Premium, créez d'abord un compte de stockage Premium de type *Premium\_LRS*. Pour ce faire, vous pouvez utiliser le [portail Azure](https://portal.azure.com), [Azure PowerShell](../install-configure-powershell.md), l’[API REST de gestion des services](http://msdn.microsoft.com/library/azure/ee460799.aspx) (déploiements classiques) ou l’[API REST du fournisseur de ressources de stockage](http://msdn.microsoft.com/library/azure/mt163683.aspx) (déploiements ARM). Pour des instructions détaillées, consultez [Création et utilisation du compte de stockage Premium pour disques](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

### Remarques importantes :

- Pour connaître les limites des comptes de stockage Premium en termes de capacité et de bande passante, consultez la section [Objectifs d'extensibilité et de performances avec le stockage Premium](#scalability-and-performance-targets-whfr-FRing-premium-storage). Si les besoins de votre application dépassent les objectifs d'extensibilité d'un compte de stockage unique, générez votre application pour qu'elle utilise plusieurs comptes de stockage et partitionnez vos données sur ces comptes. Par exemple, si vous souhaitez joindre des disques de 51 To sur plusieurs machines virtuelles, répartissez-les entre deux comptes de stockage, la limite d'un compte de stockage Premium étant de 35 To. Vérifiez qu’un compte de stockage Premium n’a jamais plus de 35 To de disques approvisionnés.
- Par défaut, la stratégie de mise en cache est « Lecture seule » pour tous les disques de données Premium et « Lecture-écriture » pour le disque du système d'exploitation Premium attaché à la machine virtuelle. Ce paramètre de configuration est recommandé pour optimiser les performances des E/S de votre application. Pour les disques de données en écriture seule ou avec d'importantes opérations d'écriture (par ex., les fichiers journaux de SQL Server), désactivez la mise en cache du disque pour de meilleures performances de l'application.
- Assurez-vous que la bande passante disponible est suffisante sur votre machine virtuelle pour gérer le trafic du disque. Par exemple, une machine virtuelle STANDARD\_DS1 a une bande passante dédiée de 32 Mo par seconde pour le trafic des disques de stockage Premium. Autrement dit, un disque de stockage Premium P10 associé à cette machine virtuelle est limité à 32 Mo par seconde, et non à 100 Mo par seconde (seuil maximum du disque P10). De même, une machine virtuelle STANDARD\_DS13 peut aller jusqu'à 256 Mo par seconde sur l’ensemble des disques. Actuellement, la machine virtuelle DS la plus puissante est STANDARD\_DS14, qui offre un débit maximum de 512 Mo par seconde sur l’ensemble des disques. La machine virtuelle de série GS DS la plus puissante est STANDARD\_GS5, qui offre un débit allant jusqu’à 2 000 Mo par seconde sur l’ensemble des disques.

	Notez que ces limites ne valent que pour le trafic de disques, pas pour les présences dans le cache ou le trafic réseau. Une bande passante distincte est disponible pour le trafic réseau des machines virtuelles. Il ne s’agit pas de la bande passante dédiée aux disques de stockage Premium.

	Pour obtenir les informations les plus récentes sur le nombre maximal d’opérations d’E/S par seconde et le débit (bande passante) maximal pour les machines virtuelles de série DS et GS, consultez [Tailles des machines virtuelles](../virtual-machines/virtual-machines-size-specs.md) Pour en savoir plus sur les disques Premium Storage et leurs limites d’E/S par seconde et de débit, consultez le tableau de la section [Objectifs de performances et d’évolutivité lors de l’utilisation de Premium Storage](#scalability-and-performance-targets-whfr-FRing-premium-storage) dans cet article.

> [AZURE.NOTE] Les présences dans le cache ne sont pas limitées par l’IOPS et le débit du disque. Autrement dit, quand vous utilisez un disque de données avec le paramètre de cache ReadOnly sur une machine virtuelle de série DS ou GS, les lectures effectuées dans le cache ne sont pas soumises aux limites des disques de stockage Premium. Il est donc possible d’obtenir un très haut débit à partir d'un disque, si la charge de travail concerne essentiellement des lectures. Notez que le cache est soumis à des limites IOPS/Débit séparées au niveau de la machine virtuelle, en fonction de la taille de celle-ci. Les machines virtuelles DS exécutent environ 4000 IOPS et ont un débit de 33 Mo/s par cœur pour les E/S du cache et du disque SSD local.

- Vous pouvez utiliser des disques de stockage Premium et Standard dans la même machine virtuelle de série DS ou GS.
- Avec le stockage Premium, vous pouvez configurer une machine virtuelle de série DS et attacher plusieurs disques de données persistants sur une machine virtuelle. Si nécessaire, vous pouvez répartir les données sur les disques pour augmenter la capacité et les performances du volume. Si vous équilibrez les disques de données de stockage Premium à l’aide des [espaces de stockage](http://technet.microsoft.com/library/hh831739.aspx), vous devez les configurer avec une colonne pour chaque disque utilisé. Dans le cas contraire, les performances globales du volume agrégé par bandes peuvent être limitées, en raison d'une distribution inégale du trafic sur les disques. Par défaut, l'interface utilisateur (IU) du Gestionnaire de serveur vous permet de créer des colonnes jusqu'à 8 disques. Mais si vous avez plus de 8 disques, vous devez utiliser PowerShell pour créer le volume et spécifier manuellement le nombre de colonnes. Dans le cas contraire, l'IU du Gestionnaire de serveur continue d'utiliser 8 colonnes, même si vous disposez de plusieurs disques. Par exemple, si vous disposez de 32 disques dans un agrégat unique, vous devez spécifier 32 colonnes. Vous pouvez utiliser le paramètre *NumberOfColumns* de l’applet de commande PowerShell [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) pour spécifier le nombre de colonnes utilisées par le disque virtuel. Pour plus d'informations, consultez [Vue d'ensemble des espaces de stockage](http://technet.microsoft.com/library/hh831739.aspx) et [Forum aux Questions sur les espaces de stockage](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
- Évitez d'ajouter des machines virtuelles DS à un service cloud comprenant des machines virtuelles autres que DS. L’autre solution consiste à migrer vos disques durs virtuels vers un nouveau service cloud n’exécutant que des machines virtuelles DS. Si vous souhaitez conserver la même adresse IP virtuelle (VIP) pour le nouveau service cloud qui héberge vos machines virtuelles DS, utilisez la fonctionnalité d’[adresses IP réservées](virtual-networks-configure-vnet-to-vnet-connection.md). Des machines virtuelles de la série GS peuvent être ajoutés à un service cloud existant exécutant seulement des machines virtuelles de la série GS.
- La série DS de machines virtuelles Azure peut être configurée pour utiliser un disque de système d'exploitation hébergé sur un compte de stockage Standard ou un compte de stockage Premium. Si vous utilisez le disque de système d'exploitation pour le démarrage, vous pouvez envisager d'utiliser un stockage Standard basé sur ce disque. Cela offre des avantages en termes de coûts et des performances similaires au stockage Premium après le démarrage. Si vous effectuez des tâches autres que le démarrage sur le disque de système d'exploitation, utilisez le stockage Premium car les performances sont supérieures. Par exemple, si votre application effectue des lectures/écritures sur le disque du système d'exploitation, l'utilisation du stockage Premium sur le disque du système d'exploitation offre de meilleures performances à votre machine virtuelle.
- Vous pouvez utiliser l’[interface de ligne de commande Azure](../xplat-cli-install.md) avec le stockage Premium. Pour modifier la stratégie de cache sur l'un de vos disques à l'aide de l’interface de ligne de commande Azure, exécutez la commande suivante :

	`$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>`

	Notez que l’option de la stratégie de mise en cache peut être ReadOnly, None ou ReadWrite. Pour les autres d'options, consultez l'aide en exécutant la commande suivante :

	`azure vm disk attach --help`


## Objectifs d'extensibilité et de performances avec le stockage Premium

Quand vous configurez un disque sur un compte de stockage Premium, le nombre d'opération d'E/S par seconde (IOPS) et le débit (bande passante) qu'il peut recevoir dépend de la taille du disque. Actuellement, il existe trois types de disques de stockage Premium : P10, P20 et P30. Chaque type présente des limites spécifiques en matière d'IOPS et de débit, comme indiqué dans le tableau suivant :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Type de disque de stockage Premium</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>Taille du disque</strong></td>
	<td>128 Go</td>
	<td>512 Go</td>
	<td>1024&#160;Go (1 To)</td>
</tr>
<tr>
	<td><strong>IOPS par disque</strong></td>
	<td>500</td>
	<td>2&#160;300</td>
	<td>5&#160;000</td>
</tr>
<tr>
	<td><strong>Débit par disque</strong></td>
	<td>100 Mo par seconde *</td>
	<td>150 Mo par seconde *</td>
	<td>200 Mo par seconde *</td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Vérifiez que la bande passante disponible sur votre machine est suffisante pour le trafic de disques, comme expliqué dans la section [Création et utilisation du compte de stockage Premium pour disques](#using-premium-storage-for-disks) plus haut dans cet article. Dans le cas contraire, le débit et l’IOPS du disque seront limitées à des valeurs inférieures basées sur les limites de la machine virtuelle, et non aux valeurs de disque mentionnées dans le tableau précédent.

Azure mappe la taille du disque (arrondie à la valeur supérieure) à l'option de disque de stockage Premium la plus proche, comme indiqué dans le tableau. Par exemple, un disque de 100 Go correspond à l’option P10 et peut prendre en charge jusqu'à 500 IOPS et un débit de 100 Mo par seconde. De même, un disque de 400 Go correspond à une option P20 et peut prendre en charge jusqu'à 2 300 IOPS et un débit de 150 Mo par seconde.

La taille d'une unité d'E/S est 256 Ko. Si la taille des données transférées est inférieure à 256 Ko, elles sont considérées comme une seule unité d'E/S. Les tailles d'E/S supérieures sont divisées en plusieurs unités d'E/S de 256 Ko. Par exemple, 1 100 Ko d'E/S correspond à cinq unités d'E/S.

La limite de débit comprend les écritures sur le disque ainsi que les lectures sur ce disque qui ne sont pas effectuées à partir du cache. Par exemple, la somme des opérations de lecture et d'écriture hors cache doit être inférieure ou égale à 100 Mo par seconde pour un disque P10. De même, un disque P10 peut gérer 100 Mo par seconde d'opérations de lecture hors cache ou 100 Mo par seconde d'opérations d'écriture, ou encore 60 Mo par seconde d'opérations de lecture avec 40 Mo par seconde d'opérations d'écriture, par exemple.

Quand vous créez vos disques dans Azure, sélectionnez l'offre de stockage Premium qui convient le mieux aux besoins de votre application en termes de capacité, de performances, d'extensibilité et de charge maximale.

> [AZURE.NOTE] Vous pouvez facilement augmenter la taille des disques existants. Par exemple, vous pouvez augmenter la taille d'un disque de 30 Go à 128 Go ou 1 To. Ou convertir votre disque P20 en disque P30, si vous avez besoin de davantage de capacités ou de plus d’IOPS et de débit. Vous pouvez augmenter la taille du disque à l'aide de l'applet de commande PowerShell « Update-AzureDisk » avec la propriété «-ResizedSizeInGB ». Pour effectuer cette action, le disque doit être détaché de la machine virtuelle ou cette dernière doit être arrêtée.

Le tableau suivant décrit les objectifs d'extensibilité des comptes de stockage Premium :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Capacité totale du compte</strong></td>
	<td><strong>Bande passante totale d’un compte de stockage localement redondant</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>Capacité du disque&#160;: 35&#160;To</li>
       <li type=round>Capacité d’instantanés&#160;: 10&#160;To</li>
    </ul>
	</td>
	<td>Jusqu'à 50&#160;Go par seconde pour les données entrantes/sortantes</td>
</tr>
</tbody>
</table>

- Les données entrantes sont toutes les données (demandes) envoyées à un compte de stockage.
- Les données sortantes sont toutes les données (réponses) reçues d'un compte de stockage.

Pour plus d'informations, consultez [Objectifs d'extensibilité et de performances d'Azure Storage](storage-scalability-targets.md).

## Limitation du stockage Premium
Vous pouvez constater une limitation de bande passante si l’IOPS ou le débit de votre application dépasse les limites allouées à un disque de stockage Premium ou si le trafic total de l’ensemble des disques de la machine virtuelle dépasse la limite de bande passante de disques disponible pour la machine virtuelle. Pour éviter cette situation, nous vous recommandons de limiter le nombre de demandes d'E/S en attente, en fonction des objectifs d'extensibilité et de performances du disque configuré et de la bande passante de disques disponible pour la machine virtuelle.

Quand votre application est conçue pour éviter les limitations, elle bénéficie d'une latence moindre. En revanche, si le nombre de demandes d'E/S en attente est trop faible, votre application ne peut pas bénéficier des niveaux maximum de débit et d'IOPS disponibles sur le disque.

Les exemples suivants montrent comment calculer les niveaux de limitation :

### Exemple 1 :
Votre application a effectué 495 E/S de 16 Ko (soit 495 unités d'E/S) en une seconde sur un disque P10. Si vous tentez une E/S de 2 Mo dans la même seconde, le total d'unités d'E/S est égal à 495 + 8. En effet, une E/S de 2 Mo équivaut à 2 048 Ko / 256 Ko = 8 unités d'E/S, avec une taille d'unité d'E/S égale à 256 Ko. Étant donné que la somme de 495 + 8 dépasse la limite de 500 IOPS du disque, il se produit une limitation.

### Remarque :
tous les calculs sont basés sur une taille d'unité d'E/S de 256 Ko.

### Exemple 2 :
Votre application a effectué 400 E/S de 256 Ko sur un disque P10. La bande passante totale consommée est (400 * 256) / 1024 = 100 Mo/s. Notez que la limite de débit d’un disque P10 est de 100 Mo par seconde. Si votre application tente d'effectuer davantage d'E/S dans la même seconde, elle sera limitée, car elle dépassera la limite allouée.

### Exemple 3 :
Vous avez une machine virtuelle DS4 avec deux disques P30. Chaque disque P30 peut gérer un débit de 200 Mo par seconde. Toutefois, une machine virtuelle DS4 a une bande passante disque maximale de 256 Mo par seconde. Par conséquent, vous ne pouvez pas obtenir un débit maximal sur tous les disques de cette machine virtuelle DS4 en même temps. La solution consiste à maintenir un trafic de 200 Mo par seconde sur un disque et un trafic de 56 Mo par seconde sur l'autre disque. Si la somme de votre trafic de disques excède 256 Mo par seconde, il est limité.

### Remarque :
si le trafic du disque consiste essentiellement en des E/S de petite taille, il est très probable que votre application atteindra la limite d'IOPS avant la limite de débit. En revanche, si le trafic du disque consiste essentiellement en des E/S de grande taille, il est très probable que votre application atteindra la limite de débit avant la limite d'IOPS. Vous pouvez optimiser la capacité de votre application en termes d'IOPS et de débit en utilisant des tailles d'E/S optimales et en limitant le nombre de demandes d'E/S en attente pour le disque.

## Captures instantanées et copie d'objets blob avec le stockage Premium
Vous pouvez créer une capture instantanée pour le stockage Premium de la même façon que pour le stockage Standard. Le stockage Premium étant localement redondant, nous vous recommandons de créer des captures instantanées et de les copier sur un compte de stockage Standard géo-redondant. Pour plus d'informations, consultez [Options de redondance du stockage Azure](storage-redundancy.md).

Si un disque est attaché à une machine virtuelle, certaines opérations d'API ne sont pas autorisées sur l'objet blob de pages utilisé par le disque. Par exemple, vous ne pouvez pas effectuer une opération [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) sur cet objet blob, tant que le disque est attaché à une machine virtuelle. À la place, commencez par créer une capture instantanée de cet objet blob à l'aide de la méthode [Snapshot Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) de l'API REST, puis exécutez l'opération [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) de la capture instantanée pour copier le disque attaché. Vous pouvez également détacher le disque et effectuer les opérations nécessaires sur l'objet blob sous-jacent.

### Remarques importantes :

- Le nombre de captures instantanées est limité à 100 par objet blob. Une capture instantanée peut être prise toutes les 10 minutes au maximum.
- La capacité maximale de captures instantanées par compte de stockage Premium est de 10 To. Notez que la capacité de captures instantanées fait référence à la quantité totale de données contenues dans les captures instantanées uniquement, et n'incluent pas les données de l'objet blob de base.
- Pour conserver des copies géo-redondantes de vos captures instantanées, vous pouvez copier des captures instantanées d'un compte de stockage Premium vers un compte de stockage Standard géo-redondant à l'aide des opérations AzCopy ou Copy Blob. Pour plus d'informations, consultez [Prise en main de l'utilitaire de ligne de commande AzCopy](storage-use-azcopy.md) et [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).
- Pour plus d'informations sur l'exécution d'opérations REST sur les objets blob de pages dans les comptes de stockage Premium, consultez [Utilisation des opérations de service blob avec le stockage Azure Premium](http://go.microsoft.com/fwlink/?LinkId=521969) dans la bibliothèque MSDN.

## Utilisation de machines virtuelles Linux avec le stockage Premium
Consultez les instructions importantes ci-dessous pour configurer vos machines virtuelles Linux sur un stockage Premium :

- Pour tous les disques de stockage Premium dont le paramètre de cache est « ReadOnly » ou « None », vous devez désactiver les « barrières » lors du montage du système de fichiers pour atteindre les objectifs d'évolutivité du stockage Premium. Ces barrières sont inutiles dans ce cas de figure, car les écritures sur les disques sauvegardés de stockage Premium sont pérennes avec ces paramètres de cache. Lorsque la demande d'écriture est terminée, les données sont stockées dans le magasin permanent. Utilisez les méthodes suivantes pour désactiver les « barrières » en fonction de votre système de fichiers :
	- Si vous utilisez **reiserFS**, désactivez les barrières à l'aide de l'option de montage « barrier=none ». (Pour activer les barrières, utilisez « barrier=flush ».)
	- Si vous utilisez **ext3/ext4**, désactivez les barrières à l'aide de l'option de montage « barrier=0 ». (Pour activer les barrières, utilisez « barrier=1 ».)
	- Si vous utilisez **XFS**, désactivez les barrières à l'aide de l'option de montage « nobarrier ». (Pour activer les barrières, utilisez l’option « barrier ».)

- Pour les disques de stockage Premium dont le paramètre de cache est « ReadWrite », les barrières doivent être activées pour pérenniser les écritures.
- Pour conserver les étiquettes de volume après le redémarrage de la machine virtuelle, vous devez mettre à jour /etc/fstab avec les références UUID aux disques. Consultez également [Association d’un disque de données à une machine virtuelle Linux](../virtual-machines/virtual-machines-linux-how-to-attach-disk.md).

Les distributions Linux suivantes ont été validées avec le stockage Premium. Nous vous recommandons de mettre vos machines virtuelles au niveau de l’une de ces versions (ou d’une version ultérieure) pour améliorer les performances et la stabilité du stockage Premium. De plus, certaines versions nécessitent également la dernière version de LIS (Linux Integration Services version 4.0 pour Microsoft Azure). Cliquez sur le lien ci-dessous pour effectuer le téléchargement et l'installation. Nous allons continuer à ajouter des images à la liste, à chaque validation supplémentaire. Veuillez noter que, selon nos validations, les performances varient pour ces images. Elles dépendent également des caractéristiques de la charge de travail et des paramètres des images. Chaque image est optimisée pour une charge de travail particulière. <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;"> <tbody> <tr> <td><strong>Distribution</strong></td> <td><strong>Version</strong></td> <td><strong>Noyau pris en charge</strong></td> <td><strong>Image prise en charge</strong></td> </tr> <tr> <td rowspan="4"><strong>Ubuntu</strong></td> <td>12.04</td> <td>3.2.0-75.110</td> <td>Ubuntu-12\_04\_5-LTS-amd64-server-20150119-fr-FR-30GB</td> </tr> <tr> <td>14.04</td> <td>3.13.0-44.73</td> <td>Ubuntu-14\_04\_1-LTS-amd64-server-20150123-fr-FR-30GB</td> </tr> <tr> <td>14.10</td> <td>3.16.0-29.39</td> <td>Ubuntu-14\_10-amd64-server-20150202-fr-FR-30GB</td> </tr> <tr> <td>15.04</td> <td>3.19.0-15</td> <td>Ubuntu-15\_04-amd64-server-20150422-fr-FR-30GB</td> </tr> <tr> <td><strong>SUSE</strong></td> <td>SLES 12</td> <td>3.12.36-38.1</td> <td>suse-sles-12-priority-v20150213<br>suse-sles-12-v20150213</td> </tr> <tr> <td><strong>CoreOS</strong></td> <td>584.0.0</td> <td>3.18.4</td> <td>CoreOS 584.0.0</td> </tr> <tr> <td rowspan="2"><strong>CentOS</strong></td> <td>6.5, 6.6, 6.7, 7.0</td> <td></td> <td> <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 obligatoire </a> </br> *Voir remarque ci-dessous </td> </tr> <tr> <td>7.1</td> <td>3.10.0-229.1.2.el7</td> <td> <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 recommandé </a> <br/> *Voir remarque ci-dessous </td> </tr>

<tr>
	<td rowspan="2"><strong>Oracle</strong></td>
	<td>6.4.</td>
	<td></td>
	<td><a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 Requise </a></td>
</tr>
<tr>
	<td>7.0</td>
	<td></td>
	<td>Contacter le Support technique pour plus d'informations</td>
</tr>
</tbody> </table>


### Pilotes LIS pour Openlogic CentOS

Les clients exécutant des machines virtuelles OpenLogic CentOS doivent exécuter la commande suivante pour installer les pilotes les plus récents :

	sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
	sudo yum install microsoft-hyper-v

Un redémarrage est nécessaire pour activer les nouveaux pilotes.



## Tarification et facturation du stockage Premium
Les considérations de facturation suivantes s'appliquent à l'utilisation du stockage Premium :

- La facturation pour un disque de stockage Premium dépend de la taille configurée du disque. Azure mappe la taille du disque (arrondie à la valeur supérieure) à l'option de disque de stockage Premium la plus proche, comme indiqué dans le tableau de la section [Objectifs d'extensibilité et de performances avec le stockage Premium](#scalability-and-performance-targets-whfr-FRing-premium-storage). La facturation de n'importe quel disque configuré est calculée au prorata horaire sur la base du tarif mensuel de l'offre de stockage Premium. Par exemple, si vous configurez un disque P10 et le supprimez au bout de 20 heures, vous êtes facturé 20 heures pour l'offre P10. Le montant facturé est indépendant de la quantité de données écrites sur le disque ou de la quantité de débit/IOPS utilisés.
- Les captures instantanées sur le stockage Premium sont facturées en fonction de la capacité supplémentaire utilisée par les captures instantanées. Pour plus d'informations sur les captures instantanées, consultez [Création d'un instantané d'objet blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).
- Les [transferts de données sortants](https://azure.microsoft.com/pricing/details/data-transfers/) (données sortant des centres de données Azure) sont facturés en fonction de la bande passante utilisée.

Pour plus d’informations sur la tarification du stockage Premium et des machines virtuelles de série DS et GS, consultez :

- [Tarification d’Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
- [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/)

## Créer et utiliser un compte de stockage Premium pour un disque de données de machine virtuelle

Cette section montre comment créer un compte Premium Storage à l’aide du portail Azure, d’Azure PowerShell et de l’interface de ligne de commande Azure (Azure CLI). Par ailleurs, elle montre un exemple de cas d'utilisation des comptes de stockage Premium : la création d'une machine virtuelle et l'attachement d'un disque de données à une machine virtuelle avec un stockage Premium.

### Créer une machine virtuelle Azure utilisant Premium Storage depuis le portail Azure

Cette section explique comment créer un compte Premium Storage depuis le portail Azure.

1.	Connectez-vous au [portail Azure](https://portal.azure.com). Consultez l'offre de [version d'évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/) si vous n'avez pas encore d'abonnement.

2.	Dans le menu Hub, cliquez sur **Nouveau**.

3.	Sous **Nouveau**, cliquez sur **Tout**. Sélectionnez **Stockage, cache, +sauvegarde**. Ensuite, cliquez sur **Stockage** puis sur **Créer**.

4.	Dans le volet Compte de stockage, tapez un nom pour votre compte de stockage. Cliquez sur **Niveau tarifaire**. Dans le panneau **Niveaux tarifaires recommandés**, cliquez sur **Parcourir tous les niveaux tarifaires**. Dans le panneau **Choisir votre niveau tarifaire**, choisissez **Premium localement redondant**. Cliquez sur **Sélectionner**. Notez que le panneau **Compte de stockage** affiche **Standard-GRS** comme **Niveau tarifaire** par défaut. Après avoir cliqué sur **Sélectionner**, le **Niveau tarifaire** affiché est **Premium-LRS**.

	![Niveau tarifaire][Image1]


5.	Dans le panneau **Compte de stockage**, conservez les valeurs par défaut des champs **Groupe de ressources**, **Abonnement**, **Emplacement** et **Diagnostics**. Cliquez sur **Create**.

Pour une description complète de la procédure pas à pas dans un environnement Azure, consultez [Créer une machine virtuelle Windows dans le portail Azure](../virtual-machines-windows-tutorial.md).

### Créer une machine virtuelle Azure utilisant le stockage Premium à l’aide d’Azure PowerShell
Cet exemple PowerShell montre comment créer un compte de stockage Premium et associer un disque de données utilisant ce compte à une nouvelle machine virtuelle Azure.

1. Configurez votre environnement PowerShell en suivant les étapes indiquées à la section [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md).
2. Démarrez la console PowerShell, connectez-vous à votre abonnement et exécutez l'applet de commande PowerShell suivante dans la fenêtre de console. Comme le montre cette instruction PowerShell, vous devez attribuer au paramètre **Type** la valeur **Premium\_LRS** lors de la création d’un compte de stockage Premium.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. Ensuite, créez une machine virtuelle de série DS et spécifiez que vous voulez un stockage Premium en exécutant les applets de commande PowerShell suivantes dans la fenêtre de console. Vous pouvez créer une machine virtuelle de série GS en utilisant la même procédure. Spécifiez la taille de machine virtuelle appropriée dans les commandes. Par exemple, pour Standard\_GS2 :

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

4. Si vous voulez davantage d’espace disque pour votre machine virtuelle, créez et attachez un disque de données à une machine virtuelle de série DS ou GS existante après sa création en exécutant les applets de commande PowerShell suivantes dans la fenêtre de console :

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

### Créer une machine virtuelle Azure utilisant le stockage Premium à l’aide de l’interface de ligne de commande Azure

L’[interface de ligne de commande Azure](../xplat-cli-install.md) (Azure CLI) fournit un ensemble de commandes interplateformes Open Source, utilisables sur la plateforme Azure. Les exemples suivants montrent comment utiliser cette interface Azure (versions 0.8.14 et ultérieures) pour créer un compte de stockage Premium, ainsi qu’une machine virtuelle, et connecter un nouveau disque de données à partir d'un compte de stockage Premium.

#### Créer un compte de stockage Premium

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### Créer une machine virtuelle DS

	azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
		"b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-fr-FR-30GB" -u "myusername" -p "passwd@123"

#### Afficher les informations sur la machine virtuelle

	azure vm show premium-test-vm

#### Associer un nouveau disque de données

	azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

#### Afficher les informations sur le nouveau disque de données

	azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

## Étapes suivantes

- [Utilisation des opérations de service blob avec le stockage Azure Premium](http://go.microsoft.com/fwlink/?LinkId=521969)
- [Migration vers le stockage Premium Azure](storage-migration-to-premium-storage.md)
- [Créer une machine virtuelle Windows dans le portail Azure](../virtual-machines-windows-tutorial.md)
- [Tailles de machines virtuelles](../virtual-machines/virtual-machines-size-specs.md)
- [Documentation du stockage](https://azure.microsoft.com/documentation/services/storage/)

[Image1]: ./media/storage-premium-storage/Azure_pricing_tier.png

<!---HONumber=AcomDC_0211_2016-->