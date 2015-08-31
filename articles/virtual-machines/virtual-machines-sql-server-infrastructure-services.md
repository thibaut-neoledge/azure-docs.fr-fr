<properties 
	pageTitle="Vue d’ensemble de SQL Server sur les machines virtuelles Azure" 
	description="Cet article fournit une vue d’ensemble de l’hébergement de SQL Server sur des Machines virtuelles Azure IaaS. Il comporte des liens vers des contenus approfondis." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="08/18/2015"
	ms.author="jroth"/>

# Vue d’ensemble de SQL Server sur les machines virtuelles Azure

## Vue d'ensemble
Vous pouvez héberger [SQL Server sur des Machines virtuelles Azure](http://azure.microsoft.com/services/virtual-machines/sql-server/) de diverses manières allant de la configuration d’un serveur de base de données unique à la configuration de plusieurs machines à l’aide de groupes de disponibilité AlwaysOn et d’un réseau virtuel Azure. Cette rubrique tente de vous orienter vers quelques-unes des meilleures ressources pour la prise en main de l’exécution de SQL Server sur une machine virtuelle Azure.

>[AZURE.NOTE]L’exécution de SQL Server sur une machine virtuelle Azure est une option permettant de stocker des données relationnelles dans Azure. Vous pouvez également utiliser le service Base de données SQL Azure. Pour plus d’informations, consultez [Présentation de Base de données SQL Azure et de SQL Server dans les machines virtuelles Azure](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).
 
## Déploiement d’une instance de SQL Server sur une seule machine virtuelle

Le moyen le plus simple de déployer une machine virtuelle SQL Server dans Azure consiste à [approvisionner une image de la galerie de machines SQL Server du portail de gestion Azure](virtual-machines-provision-sql-server.md). Ces images comprennent les frais de licence de SQL Server dans la tarification relative à la machine virtuelle.

Vous pouvez toutefois également [créer une machine virtuelle Azure](virtual-machines-windows-tutorial.md) sans que SQL Server soit préinstallé. Vous pouvez installer n’importe quelle instance de SQL Server pour laquelle vous disposez d’une licence.

Ces premières étapes d’approvisionnement et de configuration englobent les tâches courantes suivantes :

- [Vérification des meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](https://msdn.microsoft.com/library/azure/dn133149.aspx)
- [Vérification des meilleures pratiques relatives à la sécurité de SQL Server sur les machines virtuelles Azure](https://msdn.microsoft.com/library/azure/dn133147.aspx)
- [Configuration de la connectivité](virtual-machines-sql-server-connectivity.md)

## Déploiement d’une configuration hautement disponible avec plusieurs machines virtuelles

Vous pouvez rendre SQL Server hautement disponible à l’aide de groupes de disponibilité AlwaysOn SQL Server. Cela implique la présence de plusieurs machines virtuelles Azure dans un réseau virtuel. Le portail Azure en version préliminaire dispose d’un modèle qui définit cette configuration pour vous. Pour plus d’informations, consultez [Offre AlwaysOn SQL Server dans la galerie du portail Microsoft Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

Si vous souhaitez configurer manuellement votre groupe de disponibilité et l’écouteur associé, consultez les articles suivants :

- [Configuration de groupes de disponibilité AlwaysOn dans Azure (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Configuration d'un écouteur à équilibrage de charge interne pour des groupes de disponibilité AlwaysOn dans Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [Extension des groupes de disponibilité AlwaysOn locaux à Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

Pour plus d’informations sur la haute disponibilité, consultez [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Exécution des charges de travail décisionnelles, d’entreposage de données et OLTP dans Azure   
Vous pouvez exécuter des charges de travail SQL Server courantes sur des Machines virtuelles Azure. SQL Server possède plusieurs images de machines virtuelles optimisées dans la galerie. Il existe notamment des images pour :

- [Business Intelligence](https://msdn.microsoft.com/library/azure/jj992719.aspx)
- [Data Warehousing](https://msdn.microsoft.com/library/azure/dn387396.aspx)
- [OLTP](https://msdn.microsoft.com/library/azure/dn387396.aspx)

## Migration de vos données

Une fois que votre machine virtuelle SQL Server est en cours d'exécution, vous pouvez souhaiter migrer des bases de données existantes vers la machine. Il existe plusieurs techniques, mais l'Assistant de déploiement dans SQL Server Management Studio fonctionne bien pour la plupart des scénarios. Pour en savoir plus sur les scénarios et obtenir un didacticiel de l’Assistant, consultez [Migration d’une base de données vers SQL Server sur une machine virtuelle Azure](virtual-machines-migrate-onpremises-database.md).

## Sauvegarde et restauration
Pour les bases de données sur site, Azure peut agir comme un centre de données secondaire pour stocker les fichiers de sauvegarde SQL Server. Pour une vue d’ensemble des options de sauvegarde et de restauration, consultez la page [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](virtual-machines-sql-server-backup-and-restore.md).

La [sauvegarde SQL Server dans l’URL](https://msdn.microsoft.com/library/dn435916.aspx) stocke les fichiers de sauvegarde Azure dans le stockage d’objets blob Azure. La [sauvegarde gérée par SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) vous permet de planifier la sauvegarde et la conservation dans Azure. Ces services peuvent être utilisés avec des instances de SQL Server sur site ou avec SQL Server s’exécutant sur des machines virtuelles Azure. Les machines virtuelles Azure peuvent également tirer parti de la [sauvegarde automatisée](virtual-machines-sql-server-automated-backup.md) et de la [mise à jour corrective automatisée](virtual-machines-sql-server-automated-patching.md) pour SQL Server.

<!---HONumber=August15_HO8-->