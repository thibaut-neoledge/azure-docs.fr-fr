<properties
   pageTitle="Guide technique de la résilience Azure pour la récupération suite à une corruption de données ou à une suppression accidentelle | Microsoft Azure"
   description="Article dédié à la description de la récupération suite à une corruption de données ou à une suppression accidentelle de données et à la conception d’applications résilientes, hautement disponibles et tolérantes aux pannes, ainsi qu’à la planification de la récupération d’urgence"
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

#Guide technique de la résilience Azure - Récupération suite à une corruption de données ou à une suppression accidentelle

Un plan solide de continuité d’activité englobe une portion dédiée à la récupération suite aux corruptions ou aux suppressions de données. Vous trouverez ci-après des informations sur la récupération suite à une corruption ou à une suppression accidentelle de vos données en raison d’erreurs applicatives ou d’erreurs de l’opérateur.

##Machines virtuelles
Pour protéger votre système Azure Virtual Machines (parfois appelé sous la forme « machines virtuelles IaaS) contre les erreurs applicatives ou les suppressions accidentelles, utilisez [Azure Backup](https://azure.microsoft.com/services/backup/). Azure Backup prend en charge la création de sauvegardes cohérentes sur plusieurs disques de machine virtuelle. En outre, l’archivage de sauvegarde peut être répliqué entre les régions, afin d’assurer la prise en charge de la récupération suite à une perte de région.

##Storage
Notez que si Azure Storage assure la résilience des données via des réplicas automatisés, cela n’empêche aucunement la corruption des données par votre code d’application (ou les développeurs/utilisateurs) par le biais d’une suppression accidentelle ou involontaire, une mise à jour, etc. La conservation de l’intégrité des données sous la menace d’erreurs des utilisateurs et des applications nécessite des techniques plus avancées, comme la copie des données vers un emplacement de stockage secondaire avec un journal d’audit. Les développeurs peuvent tirer parti de la [fonctionnalité d’instantané d’objet blob](https://msdn.microsoft.com/library/azure/ee691971.aspx), qui peut créer des instantanés en lecture seule d’objets blob. Elle peut être utilisée comme solution de base dédiée à l’intégrité des données des objets blob Azure Storage.

###Sauvegarde d’objets blob et Table Storage
Les objets blob et les tables sont hautement durables, mais ils représentent toujours l’état actuel des données. La récupération de modifications ou de suppressions non souhaitées des données peut nécessiter leur restauration vers un état précédent. Pour ce faire, vous pouvez profiter des fonctionnalités fournies par Azure pour stocker et conserver des copies ponctuelles.

Pour les objets blob Azure, vous pouvez effectuer des sauvegardes ponctuelles à l’aide de la [fonctionnalité d’instantané blob](https://msdn.microsoft.com/library/ee691971.aspx). Pour chaque instantané, vous êtes facturé pour le stockage requis pour stocker les différences identifiées sur l’objet blob depuis le dernier état d’instantané. Les instantanés dépendant de l’existence de l’objet blob d’origine sur lequel ils sont basés, nous vous recommandons de copier les données sur un autre objet blob voire sur un autre compte de stockage. Dès lors, vous garantissez la protection des données de sauvegarde contre tout risque de suppression accidentelle. Pour les tables Azure, vous pouvez générer des copies ponctuelles vers une autre table ou vers des objets blob Azure. Pour consulter des recommandations détaillées et des exemples de sauvegardes de tables et d’objets blob au niveau de l’application, consultez la page :

  * [Protecting Your Tables Against Application Errors](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/) (Protection de vos tables contre les erreurs des applications)
  * [Protecting Your Blobs Against Application Errors](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/) (Protection de vos objets blob contre les erreurs des applications)

##Base de données
Plusieurs options de [continuité d’activité](../sql-database/sql-database-business-continuity.md) (sauvegarde, restauration) sont disponibles pour la base de données SQL Azure. Les bases de données peuvent être copiées via la fonctionnalité de [copie de base de données](../sql-database/sql-database-copy.md) ou via l’[exportation](../sql-database/sql-database-export.md) et l’[importation](https://msdn.microsoft.com/library/hh710052.aspx) d’un fichier .bacpac SQL Server. La fonctionnalité de copie de base de données offre des résultats transactionnels cohérents, ce qui n’est pas le cas du fichier bacpac (via le service d’importation/d’exportation). Ces deux options sont exécutées en tant que services basés sur file d’attente au sein du centre de données. Aucun contrat de niveau de service de durée d’exécution n’est actuellement fourni.

>[AZURE.NOTE]Les options de copie de base de données et d’importation/exportation placent un niveau de charge considérable sur la base de données source ; elles peuvent déclencher la contention des ressources ou des événements de limitation.

###Sauvegarde de base de données SQL
Les sauvegardes dans le temps des bases de données SQL Microsoft Azure sont accomplies via la [copie de votre base de données Azure SQL](../sql-database/sql-database-copy.md). Vous pouvez utiliser cette commande pour créer une copie cohérente sur le plan transactionnel d’une base de données sur le même serveur logique de base de données ou sur un serveur différent. Dans les deux cas, la copie de base de données est pleinement fonctionnelle et entièrement indépendante de la base de données source. Chaque copie créée représente une option de récupération dans le temps. Pour récupérer intégralement l’état de base de données, attribuez à la nouvelle base de données le nom de la base de données source. Sinon, vous pouvez récupérer un sous-ensemble spécifique des données de la nouvelle base. Pour ce faire, exécutez des requêtes Transact-SQL. Pour plus d’informations sur la base de données SQL, consultez [Continuité des activités cloud et récupération d’urgence d’une base de données avec SQL Database](../sql-database/sql-database-business-continuity.md).

###Sauvegarde SQL Server sur les machines virtuelles
Quand SQL Server est utilisé avec l’infrastructure Azure sous la forme de machines virtuelles de service (souvent appelées modèles IaaS ou machines virtuelles IaaS), vous disposez de deux options : les sauvegardes traditionnelles et la copie des journaux de transaction. Le recours aux sauvegardes traditionnelles vous permet de restaurer les données à un point spécifique dans le temps, mais le processus de récupération est lent. Avec la restauration de sauvegardes traditionnelles, vous devez démarrer par une sauvegarde initiale complète, puis appliquer toutes les sauvegardes effectuées ultérieurement. La seconde option consiste en la configuration d’une session de copie des journaux de transaction pour reporter la restauration des sauvegardes des journaux (par exemple, de deux heures). Vous disposez ainsi d’une fenêtre permettant de récupérer des erreurs identifiées sur la sauvegarde primaire.

##Autres services de plateforme Azure
Certains services de plateforme Azure stockent des informations dans un compte de stockage contrôlé par l’utilisateur ou une base de données SQL Azure. Si le compte ou la ressource de stockage est supprimé ou corrompu, de graves erreurs peuvent être signalées sur le service. Dans ces cas, il est important de conserver des sauvegardes qui peuvent être utilisées pour recréer ces ressources en cas de suppression ou de corruption.

Pour les Sites Web Azure et Azure Mobile Services, vous devez sauvegarder et mettre à jour les bases de données associées. For Azure Media Service et Virtual Machines, vous devez gérer le compte Azure Storage associé et l’ensemble des ressources de ce compte. Par exemple, pour Virtual Machines, vous devez sauvegarder et gérer les disques de machines virtuelles dans le stockage d’objets blob Azure.

##Listes de contrôle pour la corruption ou la suppression accidentelle des données

##Liste de contrôle des machines virtuelles
  1. Consultez la section [Machines virtuelles](#virtual-machines) de ce document.
  2. Sauvegardez et gérer les disques de machines virtuelles avec Azure Backup (ou votre propre système de sauvegarde à l’aide du stockage d’objets blob Azure et d’instantanés VHD).

##Liste de contrôle du stockage
  1. Consultez la section [Stockage](#storage) de ce document.
  2. Sauvegarder régulièrement les ressources de stockage critiques
  3. Envisager d’utiliser la fonctionnalité d’instantané pour les objets blob

##Liste de contrôle de la base de données
  1. Consultez la section [Base de données](#database) de ce document.
  2. Créer des sauvegardes dans le temps à l’aide de la commande de copie de base de données

##Liste de contrôle de la sauvegarde SQL Server sur les machines virtuelles
  1. Consultez la section [Sauvegarde SQL Server sur les machines virtuelles](#sql-server-on-virtual-machines-backup) de ce document.
  2. Utiliser des techniques traditionnelles de sauvegarde et de restauration
  3. Créer une session reportée de copie des journaux de transaction

##Liste de contrôle des applications web
  1. Sauvegarder et gérer la base de données associée, le cas échéant

##Liste de contrôle de Media Services
  1. Sauvegarder et gérer les ressources de stockage associées

##Informations complémentaires
Pour plus d’informations sur les fonctionnalités de sauvegarde et de restauration d’Azure, consultez la page [Scénarios de stockage, sauvegarde et récupération](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/).

##Étapes suivantes
Cet article fait partie d’une série intitulée [Guide technique de la résilience Azure](./resiliency-technical-guidance.md). Si vous cherchez à accroître les ressources de résilience,de récupération d’urgence et de haute disponibilité, consultez les [ressources supplémentaires](./resiliency-technical-guidance.md#additional-resources) du Guide technique de la résilience Azure.

<!---HONumber=AcomDC_0525_2016-->