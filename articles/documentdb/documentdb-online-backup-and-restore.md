<properties
    pageTitle="Sauvegarde et restauration en ligne avec DocumentDB | Microsoft Azure"
    description="Découvrez comment sauvegarder et restaurer automatiquement des bases de données NoSQL avec Azure DocumentDB."
    keywords="sauvegarde et restauration, sauvegarde en ligne"
    services="documentdb"
    documentationCenter=""
    authors="RahulPrasad16"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="raprasa"/>


# <a name="automatic-online-backup-and-restore-with-documentdb"></a>Sauvegarde et restauration en ligne automatiques avec DocumentDB 

Azure DocumentDB sauvegarde automatiquement toutes vos données à intervalles réguliers. Les sauvegardes automatiques sont effectuées sans affecter les performances ou la disponibilité de vos opérations de base de données NoSQL. Toutes vos sauvegardes sont stockées séparément dans un autre service de stockage, et ces sauvegardes sont répliquées globalement pour garantir la résilience contre les sinistres régionaux. Les sauvegardes automatiques sont destinées aux scénarios où vous supprimez accidentellement votre collection DocumentDB et où vous avez besoin ultérieurement d’une solution de récupération de données ou de récupération d’urgence.  

Cet article commence par un récapitulatif sur la redondance et la disponibilité des données dans DocumentDB et traite ensuite des sauvegardes. 

## <a name="high-availability-with-documentdb---a-recap"></a>Récapitulatif de la haute disponibilité avec DocumentDB

DocumentDB est conçu pour être [globalement distribué](documentdb-distribute-data-globally.md) : il vous permet de mettre à l’échelle le débit dans plusieurs régions Azure, ainsi que le basculement indiqué par la stratégie et les API multihébergement transparentes. En tant que système de base de données offrant des [SLA de disponibilité à 99,99 %](https://azure.microsoft.com/support/legal/sla/documentdb/v1_0/), toutes les écritures dans DocumentDB sont validées durablement sur des disques locaux par un quorum de réplicas au sein d’un centre de données local avant d’accuser réception au client. Notez que la haute disponibilité de DocumentDB s’appuie sur le stockage local et ne dépend d’aucune technologie de stockage externe. En outre, si votre compte de base de données est associé à plusieurs régions Azure, vos écritures sont également répliquées entre les autres régions. Pour mettre à l’échelle votre débit, et accéder aux données à une latence faible, vous pouvez avoir autant de régions lues associées à votre compte de base de données que vous le souhaitez. Dans chaque région de lecture, les données (répliquées) sont rendues persistantes durablement sur un jeu de réplicas.  

Comme illustré dans le diagramme suivant, une seule collection DocumentDB est [partitionnée horizontalement](documentdb-partition-data.md). Une « partition » est indiquée par un cercle dans le diagramme suivant, et chaque partition est hautement disponible via un jeu de réplicas. Il s’agit de la distribution locale au sein d’une seule région Azure (indiquée par l’axe des abscisses). En outre, chaque partition (avec son jeu de réplicas correspondant) est ensuite globalement distribuée dans plusieurs régions liées à votre compte de base de données (par exemple, dans cette illustration, les trois régions : États-Unis de l’Est, États-Unis de l’Ouest et Centre de l’Inde). Le « jeu de partitions » est une entité globalement distribuée comprenant plusieurs copies de vos données dans chaque région (indiquée par l’axe des ordonnées). Vous pouvez affecter la priorité aux régions associées à votre compte de base de données ; DocumentDB basculera en toute transparence à la région suivante en cas de sinistre. Vous pouvez également simuler le basculement manuellement pour tester la disponibilité de bout en bout de votre application.  

L’image suivante illustre le degré élevé de redondance avec DocumentDB.

![Degré élevé de redondance avec DocumentDB](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-redundancy.png)


![Degré élevé de redondance avec DocumentDB](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-global-distribution.png)

## <a name="full,-automatic,-online-backups"></a>Sauvegardes complètes, automatiques, en ligne

Oups, j’ai supprimé ma collection ou une base de données ! Avec DocumentDB, vos données, ainsi que les sauvegardes de vos données sont rendues hautement redondantes et résilientes aux sinistres régionaux. Actuellement, ces sauvegardes automatisées sont effectuées environ toutes les quatre heures. 

Les sauvegardes sont effectuées sans affecter les performances ou la disponibilité de vos opérations de base de données. DocumentDB effectue la sauvegarde en arrière-plan sans consommer les RU approvisionnées ni affecter les performances et sans affecter la disponibilité de votre base de données NoSQL. 

Contrairement à vos données qui sont stockées dans DocumentDB, les sauvegardes automatiques sont stockées dans le service de stockage blob Azure. Pour garantir un chargement efficace et à faible latence, l’instantané de votre sauvegarde est chargé vers une instance de stockage blob Azure dans la même région que la région d’écriture en cours de votre compte de base de données DocumentDB. Pour assurer la résilience contre les sinistres régionaux, chaque instantané de vos données de sauvegarde dans le stockage blob Azure est à nouveau répliqué via le stockage géoredondant (GRS) vers une autre région. Le diagramme suivant montre que l’ensemble de la collection DocumentDB (avec les trois partitions principales dans la région États-Unis de l’Ouest, dans cet exemple) est sauvegardé dans un compte de stockage blob Azure distant dans la région États-Unis de l’Ouest, puis répliquée par GRS dans la région États-Unis de l’Est. 

L’image suivante illustre les sauvegardes complètes périodiques de toutes les entités DocumentDB dans le stockage Azure GRS.

![Sauvegardes complètes périodiques de toutes les entités DocumentDB dans le stockage Azure GRS](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-automatic-backup.png)


## <a name="retention-period-for-a-given-snapshot"></a>Période de rétention pour un instantané donné

Comme décrit ci-dessus, nous prenons régulièrement des instantanés de vos données et, conformément à nos obligations réglementaires, nous conservons le dernier instantané pendant 90 jours maximum avant qu’il ne soit finalement purgé. Si une collection ou un compte sont supprimés, DocumentDB stocke la dernière sauvegarde pendant 90 jours.

## <a name="restore-database-from-the-online-backup"></a>Restaurer la base de données à partir de la sauvegarde en ligne

En cas de suppression accidentelle de vos données, vous pouvez [émettre un ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou [appeler le support technique Azure](https://azure.microsoft.com/support/options/) pour restaurer les données à partir de la dernière sauvegarde automatique. Pour obtenir un instantané spécifique de votre sauvegarde à restaurer, DocumentDB nécessite que les données nous soient accessibles pendant au moins la durée du cycle de sauvegarde de cet instantané.

## <a name="next-steps"></a>Étapes suivantes

Pour répliquer votre base de données NoSQL dans plusieurs centres de données, consultez [distribuer vos données globalement avec DocumentDB](documentdb-distribute-data-globally.md). 

Pour contacter le support technique Azure, [émettez un ticket à partir du Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


<!--HONumber=Oct16_HO2-->


