<properties 
	pageTitle="SQL Server sur les machines virtuelles Azure" 
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
	ms.date="04/17/2015"
	ms.author="jroth"/>

# SQL Server sur les machines virtuelles Azure

## Vue d'ensemble
Vous pouvez héberger [SQL Server sur des Machines virtuelles Azure][sqlvmlanding] de diverses manières allant de la configuration d’un serveur de base de données unique à la configuration de plusieurs machines à l’aide de groupes de disponibilité AlwaysOn et d’un réseau virtuel Azure.

> [AZURE.NOTE]L’exécution de SQL Server sur une machine virtuelle Azure est une option permettant de stocker des données relationnelles dans Azure. Vous pouvez également utiliser le service Base de données SQL Azure. Pour plus d’informations, consultez [Présentation de Base de données SQL Azure et de SQL Server dans les machines virtuelles Azure][sqldbcompared].
 
## Déploiement d’une instance de SQL Server sur une seule machine virtuelle
Une fois que vous avez [créé une machine virtuelle Azure via le portail Azure][createvmportal] ou par un processus automatisé, vous pouvez installer n’importe quelle instance de SQL Server pour lequel vous disposez d’une licence. Toutefois, vous devez prendre des mesures supplémentaires pour [configurer la connectivité][setupconnectivity] entre la machine SQL Server et les autres machines clientes.
 
Vous pouvez également installer une des nombreuses différentes images de machine virtuelle SQL Server à partir de la galerie. Ces images comprennent les frais de licence de SQL Server dans la tarification relative à la machine virtuelle. Pour plus d’informations, consultez la page [Configuration d’une machine virtuelle SQL Server sur Azure][provisionsqlvm].

## Déploiement d’une configuration hautement disponible avec plusieurs machines virtuelles
Vous pouvez rendre SQL Server hautement disponible à l’aide de groupes de disponibilité AlwaysOn SQL Server. Cela implique la présence de plusieurs machines virtuelles Azure dans un réseau virtuel. Le portail Azure en version préliminaire dispose d’un modèle qui définit cette configuration pour vous. Pour plus d’informations, consultez [Offre AlwaysOn SQL Server dans la galerie du portail Microsoft Azure][sqlalwaysonportal]. Vous avez également la possibilité de [configurer manuellement un groupe de disponibilité AlwaysOn][sqlalwaysonmanual]. Pour plus d’informations sur la haute disponibilité, consultez [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure][sqlhadr].

## Exécution des charges de travail décisionnelles, d’entreposage de données et OLTP dans Azure   
Vous pouvez exécuter des charges de travail SQL Server courantes sur des Machines virtuelles Azure. SQL Server possède plusieurs images de machines virtuelles optimisées dans la galerie. Il s’agit notamment d’images relatives à [Business Intelligence][sqlbi], à l’[entreposage de données][sqldw] et à [OLTP][sqloltp].

## Migration de vos données
Il existe plusieurs façons de migrer vos données vers des machines virtuelles Azure exécutant SQL Server. Commencez par configurer une machine virtuelle SQL Server à l’aide du portail Azure, de l’automatisation PowerShell ou de l’assistant de déploiement dans SQL Server Management Studio. Les images SQL Server optimisées comprennent les frais liés à la licence dans leur modèle de tarification. Toutefois, vous pouvez aussi installer SQL Server en utilisant votre propre licence. Pour migrer vos données, il existe plusieurs options,telles que l’assistant de déploiement ou la migration d’un disque de données vers la machine virtuelle cible. Pour plus d’informations, consultez [Se préparer à migrer vers SQL Server dans les machines virtuelles Azure][migratesql].

## Sauvegarde et restauration
Pour les bases de données sur site, Azure peut agir comme un centre de données secondaire pour stocker les fichiers de sauvegarde SQL Server. La [sauvegarde SQL Server dans l’URL][backupurl] stocke les fichiers de sauvegarde Azure dans le stockage d’objets blob Azure. La [sauvegarde gérée par SQL Server][managedbackup] vous permet de planifier la sauvegarde et la conservation dans Azure. Ces services peuvent être utilisés avec des instances de SQL Server sur site ou avec SQL Server s’exécutant sur des machines virtuelles Azure. Les machines virtuelles Azure peuvent également tirer parti de la [sauvegarde automatisée][autobackup] et de la [mise à jour corrective automatisée][autopatching] pour SQL Server. Pour plus d’informations, consultez [Tâches de gestion relatives à SQL Server sur les machines virtuelles Azure][managementtasks].

## Ressources supplémentaires :
[SQL Server sur les machines virtuelles Azure][sqlmsdnlanding]

[Mise en route de SQL Server sur les machines virtuelles Azure][sqlvmgetstarted]

[Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure][sqlperf]

[Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure][sqlsecurity]

[Articles techniques relatifs aux performances de SQL Server sur les machines virtuelles Azure][technicalarticles]

[sqlvmlanding]: http://azure.microsoft.com/services/virtual-machines/sql-server/
[sqldbcompared]: http://azure.microsoft.com/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas
[createvmportal]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
[setupconnectivity]: https://msdn.microsoft.com/library/azure/dn133152.aspx
[provisionsqlvm]: http://azure.microsoft.com/documentation/articles/virtual-machines-provision-sql-server/
[sqlalwaysonportal]: http://go.microsoft.com/fwlink/?LinkId=526941
[sqlalwaysonmanual]: https://msdn.microsoft.com/library/azure/dn249504.aspx
[sqlhadr]: https://msdn.microsoft.com/library/azure/jj870962.aspx
[sqlbi]: https://msdn.microsoft.com/library/azure/jj992719.aspx
[sqldw]: https://msdn.microsoft.com/library/azure/dn387396.aspx
[sqloltp]: https://msdn.microsoft.com/library/azure/eb0188e2-5569-48ff-b92c-1f6c0bf79620#about
[migratesql]: https://msdn.microsoft.com/library/azure/dn133142.aspx
[backupurl]: https://msdn.microsoft.com/library/dn435916(v=sql.120).aspx
[managedbackup]: https://msdn.microsoft.com/library/dn449496.aspx
[autobackup]: https://msdn.microsoft.com/library/azure/dn906091.aspx
[autopatching]: https://msdn.microsoft.com/library/azure/dn961166.aspx
[managementtasks]: https://msdn.microsoft.com/library/azure/dn906886.aspx
[sqlmsdnlanding]: https://msdn.microsoft.com/library/azure/jj823132.aspx
[sqlvmgetstarted]: https://msdn.microsoft.com/library/azure/dn133151.aspx
[sqlperf]: https://msdn.microsoft.com/library/azure/dn133149.aspx
[sqlsecurity]: https://msdn.microsoft.com/library/azure/dn133147.aspx
[technicalarticles]: https://msdn.microsoft.com/library/azure/dn248435.aspx
<!--HONumber=52-->
 