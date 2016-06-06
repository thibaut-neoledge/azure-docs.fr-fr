<properties
   pageTitle="Guide technique de la résilience Azure pour la récupération de l’environnement local vers Azure | Microsoft Azure"
   description="Cet article permet de comprendre et concevoir des systèmes de récupération de l’infrastructure locale vers Azure"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Guide technique de la résilience Azure - Récupération de l’environnement local vers Azure

Azure offre un ensemble complet de services conçus pour étendre un centre de données local vers Azure afin de faciliter la récupération d’urgence et de garantir une haute disponibilité :

  * __Réseau__ : l’utilisation d’un réseau privé virtuel vous permet d’étendre en toute sécurité votre réseau local vers le cloud.
  * __Calcul__ : les clients qui utilisent Hyper-V en local peuvent « élever et déplacer » des machines virtuelles existantes vers Azure.
  * __Stockage__ : StorSimple étend votre système de fichiers à Azure Storage. Le service Azure Backup assure la sauvegarde des fichiers et des bases de données SQL Azure dans Azure Storage.
  * __Réplication de base de données__ : grâce aux groupes de disponibilité SQL 2014 (ou version ultérieure), vous pouvez garantir la haute disponibilité et la récupération d’urgence de vos données locales.

##Mise en réseau
Le réseau virtuel Azure vous permet de créer une section isolée logiquement dans Azure et de la connecter en toute sécurité à votre centre de données local ou à un seul ordinateur client à l’aide d’une connexion IPsec. Grâce au réseau virtuel, vous pouvez aisément tirer parti de l’infrastructure évolutive et à la demande d’Azure tout en assurant la connectivité aux données et aux applications locales, y compris aux systèmes s’exécutant sur Windows Server, les ordinateurs centraux et les systèmes UNIX. Pour plus d’informations, consultez la [documentation réseau d’Azure](../virtual-network/virtual-networks-overview.md).

##Calcul
Si vous utilisez Hyper-V en local, vous pouvez « élever et déplacer » des machines virtuelles existantes vers Azure et vers des fournisseurs de services exécutant Windows Server 2012 (ou version ultérieure), sans avoir à apporter de modifications à la machine virtuelle ni à la convertir dans un autre format. Pour plus d’informations, consultez l’article [À propos des disques et des VHD pour les machines virtuelles Azure](../virtual-machines/virtual-machines-linux-about-disks-vhds.md).

##Azure Site Recovery
Si vous souhaitez bénéficier d’une récupération d’urgence en tant que service (DRaaS), vous pouvez utiliser [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/). Azure Site Recovery offre une protection complète pour les serveurs VMware, Hyper-V et physiques. Azure Site Recovery vous permet de tirer parti d’un autre serveur local ou d’utiliser Azure comme site de récupération. Pour plus d’informations sur Azure Site Recovery, consultez la [documentation d’Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery/).

##Storage
Pour utiliser Azure comme site de sauvegarde pour les données locales, vous disposez de plusieurs options.

###StorSimple
StorSimple intègre le stockage cloud en toute sécurité et en toute transparence pour les applications locales et propose une appliance unique conçue pour garantir un stockage en local et dans le cloud hiérarchisé et performant, tout en offrant des fonctions d’archivage dynamique, de protection des données dans le cloud et de récupération d’urgence. Pour plus d’informations, consultez la [page produit StorSimple](https://azure.microsoft.com/services/storsimple/).

###Azure Backup
Azure Backup exécute des sauvegardes dans le cloud à l’aide des outils de sauvegarde classiques de Windows Server 2012 (ou version ultérieure), de Windows Server 2012 Essentials (ou version ultérieure) et de System Center 2012 Data Protection Manager (ou version ultérieure). Ces outils établissent pour la gestion des sauvegarde un workflow totalement indépendant de l’emplacement de stockage des sauvegardes, qu’il s’agisse d’un disque local ou d’Azure Storage. Une fois les données sauvegardées dans le cloud, les utilisateurs autorisés peuvent facilement restaurer les sauvegardes sur un serveur.

Avec les sauvegardes incrémentielles, seules les modifications des fichiers sont transférées dans le cloud. Cela permet d’utiliser efficacement le stockage, de réduire la consommation de bande passante et d’assurer une récupération à tout moment des différentes versions des données. Vous pouvez également choisir d’utiliser des fonctionnalités supplémentaires, telles que les stratégies de rétention des données, la compression des données et la limitation du transfert de données. L’utilisation d’Azure comme emplacement de sauvegarde a l’avantage d’offrir des sauvegardes qui sont automatiquement exécutées « hors site ». Cette approche vous évite d’avoir à sécuriser et protéger vos supports de sauvegarde sur site. Pour plus d’informations, consultez les pages [Qu’est-ce qu’Azure Backup ?](../backup/backup-introduction-to-azure-backup.md) et [Configuration d’une sauvegarde Azure pour des données DPM](https://technet.microsoft.com/library/jj728752.aspx).

##Base de données
Disposez d’une solution de récupération d’urgence pour vos bases de données SQL Server dans un environnement informatique hybride utilisant des groupes de disponibilité AlwaysOn, la mise en miroir de bases de données, la copie des journaux de transaction et la sauvegarde et la restauration avec le stockage d’objets blob Azure. Toutes ces solutions utilisent une implémentation SQL Server exécutée sur Azure Virtual Machines. Les groupes de disponibilité AlwaysOn peuvent être utilisés dans un environnement informatique hybride comportant des réplicas de base de données à la fois en local et dans le cloud. Cet aspect est illustré dans le schéma suivant, extrait de l’article [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Groupes de disponibilité AlwaysOn dans une architecture de cloud hybride](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png "Groupes de disponibilité AlwaysOn dans une architecture de cloud hybride")

La mise en miroir de base de données peut également s’étendre à la fois à des serveurs locaux et au cloud dans une installation basée sur un certificat. Le schéma suivant illustre ce concept.

![Mise en miroir de la base de données SQL dans une architecture de cloud hybride](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png "Mise en miroir de la base de données SQL dans une architecture de cloud hybride")

L’envoi de journaux peut être utilisé pour synchroniser une base de données locale avec une base de données SQL Server dans une machine virtuelle Azure.

![Envoi de journaux SQL dans une architecture de cloud hybride](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png "Envoi de journaux SQL dans une architecture de cloud hybride")

Vous pouvez enfin sauvegarder une base de données locale directement dans Azure Blob Storage.

![Sauvegarde de SQL Server dans un objet blob Azure Storage dans une architecture de cloud hybride](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png "Sauvegarde de SQL Server dans un objet blob Azure Storage dans une architecture de cloud hybride")

Pour plus d’informations, consultez les articles [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md) et [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).

##Listes de vérification pour la récupération des données locales dans Microsoft Azure

##Liste de vérification du réseau
  1. Consultez la section [Réseau](#networking) de ce document
  2. Utilisez un réseau virtuel pour connecter en toute sécurité votre environnement local au cloud

##Liste de vérification de calcul
  1. Consultez la section [Calcul](#compute) de ce document
  2. Déplacez des machines virtuelles entre Hyper-V et Azure

##Liste de vérification du stockage
  1. Consultez la section [Stockage](#storage) de ce document
  2. Utilisez les services StorSimple pour tirer parti du stockage cloud
  3. Utilisez les services Azure Backup

##Liste de vérification de la base de données
  1. Consultez la section [Base de données](#database) de ce document
  2. Envisagez d’utiliser comme sauvegarde une instance SQL Server exécutée sur des machines virtuelles Azure
  3. Configurez les groupes de disponibilité AlwaysOn
  4. Configurez la mise en miroir de base de données basée sur un certificat
  5. Utilisez l’envoi de journaux
  6. Sauvegardez la base de données locale dans Azure Blob Storage

##Étapes suivantes
Cet article fait partie d’une série intitulée [Guide technique de la résilience Azure](./resiliency-technical-guidance.md). L’article suivant de cette série traite de [la récupération des données corrompues](./resiliency-technical-guidance-recovery-data-corruption.md).

<!---HONumber=AcomDC_0525_2016-->