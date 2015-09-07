<properties 
	pageTitle="Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines"
	description="Présente les meilleures pratiques pour optimiser les performances de SQL Server dans Microsoft Azure Virtual Machines."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/24/2015"
	ms.author="jroth"/>

# Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines

## Vue d’ensemble

Cette rubrique présente les meilleures pratiques pour optimiser les performances de SQL Server dans Microsoft Azure Virtual Machines. Lorsque vous exécutez SQL Server dans Microsoft Azure Virtual Machines, nous vous recommandons de continuer à utiliser les mêmes options de réglage des performances de base de données qui s’appliquent à SQL Server dans un environnement serveur local. Toutefois, les performances d’une base de données relationnelle dans un cloud public dépendent de nombreux facteurs, comme la taille de la machine virtuelle et la configuration des disques de données.

>[AZURE.NOTE]Ce document se concentre sur l’obtention de meilleures performances pour SQL Server sur des machines virtuelles Azure. Si votre charge de travail est moindre, vous n’aurez peut-être pas besoin de toutes les optimisations suivantes. Tenez compte de vos besoins de performances et de vos modèles de charges de travail lors de l’évaluation de ces recommandations.

Vous pouvez obtenir un guide de ressources relatives à cette rubrique en [téléchargeant le livre blanc suivant](http://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/Performance%20Guidance%20for%20SQL%20Server%20in%20Windows%20Azure%20Virtual%20Machines.docx). Veuillez noter que les informations contenues dans ce livre blanc sont obsolètes pour certains sujets. En effet, il a été écrit avant l’arrivée de Premium Storage, qui est maintenant recommandé pour de meilleurs résultats.

## Liste de vérification rapide

Voici une liste de vérification rapide pour optimiser les performances de SQL Server sur Azure Virtual Machines :

- Utiliser [Premium Storage](../storage/storage-premium-storage-preview-portal.md).

- Utiliser une [taille de machine virtuelle](virtual-machines-size-specs.md) DS3 ou supérieure pour SQL Enterprise et DS2 ou supérieure pour SQL Standard.

- Utiliser au moins 2 [disques P30](../storage/storage-premium-storage-preview-portal/#scalability-and-performance-targets-whfr-FRing-premium-storage) (1 pour les fichiers journaux ; 1 pour les fichiers de données et TempDB).

- Conserver le [compte de stockage](../storage/storage-create-storage-account.md) et la machine virtuelle SQL Server dans la même région.

- Désactiver le [stockage géo-redondant](../storage/storage-redundancy.md) (géo-réplication) sur le compte de stockage.

- Éviter d’utiliser des disques de système d’exploitation ou temporaires pour le stockage ou la journalisation des bases de données.

- Activer la mise en cache de lecture sur le ou les disques hébergeant les fichiers de données et TempDB.

- Ne pas activer la mise en cache sur le ou les disques hébergeant le fichier journal.

- Entrelacer plusieurs disques de données Azure pour obtenir un débit d’E/S plus élevé.

- Mettre en forme avec les tailles d’allocation documentées.

- Activer la compression de page de base de données.

- Activer l’initialisation de fichiers instantanée pour les fichiers de données.

- Limiter ou désactiver la croissance automatique sur la base de données.

- Désactiver la réduction automatique sur la base de données.

- Déplacer toutes les bases de données vers des disques de données, y compris les bases de données système.

- Déplacer les répertoires des journaux d’erreurs et des fichiers de trace SQL Server vers des disques de données.

- Appliquer des correctifs de performances SQL Server.

- Définir les emplacements par défaut.

- Activer les pages verrouillées.

- Sauvegarder directement dans le stockage d’objets blob.

Pour plus d’informations, suivez les instructions fournies dans les sous-sections suivantes.

## Considérations relatives à la taille des machines virtuelles et aux comptes de stockage

Pour les applications sensibles aux performances, il est recommandé d’utiliser les tailles de machine virtuelle suivantes :

- **SQL Server Enterprise Edition** : DS3 ou supérieur

- **SQL Server Standard Edition** : DS2 ou supérieur

Pour obtenir des informations actualisées sur les tailles de machine virtuelle prises en charge, consultez la page [Tailles de machine virtuelle et de service cloud pour Azure](virtual-machines-size-specs.md).

Par ailleurs, nous vous recommandons de créer votre compte de stockage Azure dans le même centre de données que vos machines virtuelles SQL Server afin de réduire les délais de transfert. Lors de la création d’un compte de stockage, désactivez la géo-réplication, étant donné que la cohérence de l’ordre d’écriture sur différents disques n’est pas garantie. Envisagez plutôt de configurer une technologie de récupération d’urgence de SQL Server entre deux centres de données Azure. Pour plus d’informations, consultez la page [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Éléments à prendre en considération pour les disques et les performances

Lorsque vous créez une machine virtuelle Azure, la plateforme attache au moins un disque à la machine virtuelle en tant que disque de système d’exploitation. Ce disque est un disque dur virtuel (VHD) stocké en tant qu’objet blob de pages dans le stockage. Vous pouvez également attacher des disques supplémentaires à votre machine virtuelle en tant que disques de données ; ces disques sont également stockés en tant qu’objets blob de pages. Il existe un autre disque dans Azure Virtual Machines, appelé « disque temporaire ». C’est un disque du nœud qui peut être utilisé comme un espace de travail temporaire.

### Disque de système d’exploitation

Un disque de système d’exploitation est un disque dur virtuel (VHD) que vous pouvez amorcer et monter comme version d’exécution d’un système d’exploitation. Il est désigné par la lettre de lecteur **C**.

La stratégie de mise en cache par défaut sur le disque du système d’exploitation est **Lecture/Écriture**. Pour les applications sensibles aux performances, nous vous recommandons d’utiliser le disque de données au lieu du disque du système d’exploitation. Reportez-vous à la section sur les disques de données ci-dessous.

### Disque temporaire

Le disque de stockage temporaire, désigné par le lecteur **D**:, n’est pas persistant dans le stockage d’objets blob Azure. Ne stockez pas vos données ni fichiers journaux sur le lecteur **D**:.

Stockez uniquement tempdb et/ou les extensions du pool de mémoires tampons sur le lecteur **D** quand vous utilisez les machines virtuelles de la série D ou G. Contrairement aux autres séries de machines virtuelles, le lecteur **D** des machines virtuelles de la série D et G est basé sur un stockage SSD. Cela peut améliorer les performances des charges de travail qui utilisent intensivement les objets temporaires ou qui disposent de plages de travail qui ne tiennent pas en mémoire. Pour plus d’informations, consultez la page [Utilisation des disques SSD dans les machines virtuelles Azure pour stocker les extensions de tempdb SQL Server et de pool de mémoires tampons](http://blogs.technet.com/b/dataplatforminsider/archive/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions.aspx).

### Disque de données

- **Nombre de disques de données** : utilisez au moins 2 [disques P30](../storage/storage-premium-storage-preview-portal.md), un pour contenir le ou les fichiers journaux, tandis que l’autre contient le ou les fichiers de données et le TempDB. Pour augmenter le débit, vous pouvez ajouter des disques de données supplémentaires. Pour déterminer le nombre de disques de données, vous devez analyser le nombre d’opérations d’E/S par seconde disponibles pour vos disques de données et de journaux. Pour plus d’informations, consultez les tableaux présentant la [taille de machine virtuelle](virtual-machines-size-specs.md) et la taille de disque dans l’article suivant : [Utilisation de Premium Storage pour les disques](../storage/storage-premium-storage-preview-portal.md). Si vous avez besoin de plus de bande passante, vous pouvez attacher des disques supplémentaires en utilisant l’entrelacement de disques. Si vous n’utilisez pas Premium Storage, nous vous recommandons d’ajouter le nombre maximum de disques de données pris en charge par votre [taille de machine virtuelle](virtual-machines-size-specs.md) et d’utiliser l’entrelacement de disques. Pour plus d’informations sur l’entrelacement de disques, consultez la section associée ci-dessous.

- **Stratégie de mise en cache** : activez uniquement la mise en cache de lecture sur les disques de données hébergeant vos fichiers de données et TempDB. Si vous n’utilisez pas Premium Storage, n’activez aucune mise en cache sur les disques de données. Pour obtenir des instructions sur la configuration de la mise en cache du disque, consultez les rubriques suivantes : [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) et [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx).

- **Taille d’unité d’allocation NTFS** : lors de la mise en forme du disque de données, il est recommandé d’utiliser une taille d’unité d’allocation de 64 Ko pour les fichiers de données et les fichiers journaux ainsi que TempDB.

- **Entrelacement de disques** : nous vous recommandons de suivre les instructions suivantes :

	- Pour Windows 8/Windows Server 2012 ou version ultérieure, utilisez des [espaces de stockage](https://technet.microsoft.com/library/hh831739.aspx). Définissez la taille de bande sur 64 Ko pour les charges de travail OLTP et sur 256 Ko pour les charges de travail d’entrepôt de données, afin d’éviter qu’un alignement incorrect de la partition n’affecte les performances. Définissez également le nombre de colonnes pour le faire correspondre au nombre de disques physiques. Pour configurer un espace de stockage avec plus de 8 disques, vous devez utiliser PowerShell (et non pas l’interface utilisateur du Gestionnaire de serveur) pour définir de façon explicite le nombre de colonnes pour le faire correspondre au nombre de disques. Pour plus d’informations sur la configuration des [espaces de stockage](https://technet.microsoft.com/library/hh831739.aspx), consultez la rubrique relative aux applets de commande des [espaces de stockage dans Windows PowerShell](https://technet.microsoft.com/library/jj851254.aspx).
	
	- Pour Windows 2008 R2 ou version antérieure, vous pouvez utiliser des disques dynamiques (volumes entrelacés de système d’exploitation). La taille de l’entrelacement restera 64 Ko. Remarque : cette option est déconseillée à partir des versions Windows 8/Windows Server 2012. Pour en savoir plus, consultez les instructions du support dans la rubrique relative à la [transition du service de disque virtuel vers l’API de gestion de stockage Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).
	
	- Si votre charge de travail ne génère pas intensivement de journaux et ne nécessite pas d’opérations d’E/S par seconde dédiées, vous pouvez configurer un seul pool de stockage. Sinon, créez deux pools de stockage, un pour les fichiers journaux et un autre pour les fichiers de données et TempDB. Déterminez le nombre de disques associés à chaque pool de stockage en fonction de vos attentes en matière de charge. N’oubliez pas que les différentes tailles de machines virtuelles autorisent différents nombres de disques de données attachés. Pour plus d’informations, consultez la page [Tailles des machines virtuelles](virtual-machines-size-specs.md).

## Considérations relatives aux performances d’E/S

- Pour obtenir les meilleurs résultats avec Premium Storage, vous devez paralléliser vos applications et vos demandes. Premium Storage est conçu pour les scénarios où la profondeur de file d’attente est supérieure à 1. Les améliorations seront donc minimes ou inexistantes pour les demandes de série à thread unique (même si elles sont gourmandes en stockage). Par exemple, cela peut affecter les résultats des tests à thread unique des outils d’analyses de performances, par exemple SQLIO.

- Envisagez d’utiliser la [compression de page de base de données](https://msdn.microsoft.com/library/cc280449.aspx), car elle peut améliorer les performances des charges de travail gourmandes en E/S. Toutefois, la compression de données peut augmenter la consommation d’UC sur le serveur de base de données.

- Envisagez de compresser tous les fichiers de données lors des transferts vers et depuis Azure.

- Envisagez d’activer l’initialisation instantanée des fichiers pour réduire le temps requis pour l’allocation initiale des fichiers. Pour tirer parti de l’initialisation instantanée des fichiers, vous devez accorder l’autorisation SE\_MANAGE\_VOLUME\_NAME au compte de service SQL Server (MSSQLSERVER) et l’ajouter à la stratégie de sécurité **Effectuer des tâches de maintenance sur les volumes**. Si vous utilisez une image de plateforme SQL Server pour Azure, le compte de service par défaut (NT Service\\MSSQLSERVER) n’est pas ajouté à la stratégie de sécurité **Effectuer des tâches de maintenance sur les volumes**. En d’autres termes, l’initialisation instantanée des fichiers n’est pas activée dans une image de plateforme SQL Server pour Azure. Après avoir ajouté le compte de service SQL Server à la stratégie de sécurité **Effectuer des tâches de maintenance sur les volumes**, redémarrez le service SQL Server. Des considérations de sécurité liées à l’utilisation de cette fonctionnalité peuvent exister. Pour plus d’informations, consultez la page [Initialisation des fichiers de base de données](https://msdn.microsoft.com/library/ms175935.aspx).

- La croissance automatique (**autogrow**) est considérée comme un simple plan d’urgence en cas de croissance plus rapide que prévu. Ne gérez pas la croissance de vos données et journaux quotidiennement avec la croissance automatique. En cas d’utilisation de la croissance automatique, augmentez préalablement le fichier à l’aide du commutateur Taille.

- Vérifiez que la fonctionnalité de réduction automatique (**autoshrink**) est désactivée afin d’éviter une surcharge inutile qui pourrait affecter les performances.

- Si vous exécutez SQL Server 2012, installez la mise à jour cumulative 10 Service Pack 1. Elle contient le correctif qui permet de résoudre les problèmes de performances d’E/S lorsque vous exécutez une sélection dans l’instruction de table temporaire dans SQL Server 2012. Pour plus d’informations, consultez cet [article de la Base de connaissances](http://support.microsoft.com/kb/2958012).

- En vue d’améliorer les performances, déplacez les bases de données système (telles que msdb et tempdb), les sauvegardes et les répertoires par défaut de données et de journaux de SQL Server vers des disques de données non mis en cache. Effectuez ensuite les actions suivantes :

	- Définissez les chemins d’accès aux fichiers XEvent et Trace.
	
	- Définissez le chemin d’accès du journal d’erreurs SQL.
	
	- Définissez le chemin d’accès de sauvegarde par défaut.
	
	- Définissez l’emplacement par défaut de la base de données.

- Déterminez des pages verrouillées pour réduire les activités d’E/S et de pagination.

## Considérations relatives aux performances propres aux fonctionnalités

Certains déploiements peuvent bénéficier de plus grands avantages en termes de performances à l’aide de techniques de configuration avancées. La liste suivante présente certaines fonctionnalités SQL Server qui peuvent vous aider à améliorer les performances :

- **Sauvegarde vers le stockage Azure** : lors de la création de sauvegardes pour SQL Server exécuté sur des machines virtuelles Azure, vous pouvez vous référer à l’article [Sauvegarde SQL Server vers une URL](https://msdn.microsoft.com/library/dn435916.aspx). Cette fonctionnalité est disponible à partir de SQL Server 2012 SP1 CU2, et recommandée pour la sauvegarde vers les disques de données attachés. Lors de la sauvegarde ou la restauration vers/depuis le stockage Azure, suivez les recommandations indiquées dans [Meilleures pratiques et résolution des problèmes pour la sauvegarde de SQL Server vers une URL et Restauration à partir d’une sauvegarde dans le stockage Azure](https://msdn.microsoft.com/library/jj919149.aspx). Vous pouvez également automatiser ces sauvegardes en utilisant la [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure](virtual-machines-sql-server-automated-backup.md).

	Pour les versions antérieures à SQL Server 2012, vous pouvez utiliser l’[outil de sauvegarde SQL Server vers Azure](https://www.microsoft.com/download/details.aspx?id=40740). Cet outil peut augmenter le débit de sauvegarde à l’aide de plusieurs cibles d’entrelacement de sauvegarde.

- **Fichiers de données SQL Server dans Azure** : cette nouvelle fonctionnalité, nommée [Fichiers de données SQL Server dans Azure](https://msdn.microsoft.com/library/dn385720.aspx), est disponible à partir de SQL Server 2014. L’exécution de SQL Server avec des fichiers de données dans Azure offre des caractéristiques de performances comparables à l’utilisation de disques de données Azure.

## Étapes suivantes

Si vous souhaitez obtenir davantage d’informations sur SQL Server et Premium Storage, consultez l’article [Utilisation du stockage Premium Azure avec SQL Server sur des machines virtuelles](virtual-machines-sql-server-use-premium-storage.md).

Pour accéder aux meilleures pratiques de sécurité, consultez la page [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-security-considerations.md).

Consultez d’autres rubriques relatives aux machines virtuelles avec SQL Server à la page [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-infrastructure-services.md)

<!---HONumber=August15_HO9-->