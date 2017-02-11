---
title: "Sauvegarder des bases de données Stretch | Microsoft Docs"
description: "Découvrez comment sauvegarder des bases de données Stretch."
services: sql-server-stretch-database
documentationcenter: 
author: Antvgski
manager: johnmac
editor: douglasl
ms.assetid: a196f858-ef8f-47b5-b9db-bb7db98d48bd
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 68aeee33dbbba67e2a24300db62bf6bcdc627ea5


---
# <a name="backup-stretch-enabled-databases"></a>Sauvegarder des bases de données Stretch
Les sauvegardes de bases de données vous permettent d’effectuer une récupération à partir de nombreux types d’échecs, d’erreurs et de sinistres.  

* Vous devez sauvegarder vos bases de données Stretch SQL Server.  
* Microsoft Azure sauvegarde automatiquement les données distantes que Stretch Database a migrées de SQL Server vers Azure.  

> [!NOTE]
> La sauvegarde n’est qu’une partie d’une solution complète de haute disponibilité et de continuité d’activité. Pour plus d’informations sur la haute disponibilité, consultez [Solutions de haute disponibilité](https://msdn.microsoft.com/library/ms190202.aspx).
> 
> 

## <a name="back-up-your-sql-server-data"></a>Sauvegarder vos données SQL Server
Pour sauvegarder vos bases de données Stretch SQL Server, vous pouvez continuer à appliquer les méthodes de sauvegarde SQL Server que vous appliquez actuellement. Pour plus d’informations, consultez [Sauvegarder et restaurer des bases de données SQL Server](https://msdn.microsoft.com/library/ms187048.aspx).

Les sauvegardes de bases de données Stretch SQL Server contiennent uniquement les données locales et les données éligibles à la migration au moment où la sauvegarde s’exécute. \(Les données éligibles sont des données qui n’ont pas encore été migrées, mais qui seront migrées vers Azure conformément aux paramètres de migration des tables.\) Il s’agit d’une sauvegarde **superficielle** qui n’inclut pas les données déjà migrées vers Azure.  

## <a name="back-up-your-remote-azure-data"></a>Sauvegarder vos données Azure distantes
Microsoft Azure sauvegarde automatiquement les données distantes que Stretch Database a migrées de SQL Server vers Azure.  

### <a name="azure-reduces-the-risk-of-data-loss-with-automatic-backup"></a>Azure réduit le risque de perte de données avec la sauvegarde automatique
Le service SQL Server Stretch Database sur Azure protège vos bases de données distantes avec des instantanés de stockage automatiques au moins toutes les huit heures. Il conserve chaque instantané pendant sept jours pour vous fournir un éventail de points de restauration possibles.  

### <a name="azure-reduces-the-risk-of-data-loss-with-geo-redundancy"></a>Azure réduit le risque de perte de données avec la géoredondance
Les sauvegardes de base de données Azure sont stockées sur un espace Stockage Azure géoredondant (RA\-GRS) et sont donc géoredondantes par défaut. Le stockage géoredondant réplique vos données vers une région secondaire à des centaines de kilomètres de la région primaire. Au sein des régions principales et secondaires, vos données sont répliquées trois fois, dans les domaines d’erreur et les domaines de mise à niveau. Ainsi, vous êtes assuré que les données restent disponibles, même en cas de panne régionale intégrale ou de sinistre rendant l’une des régions Azure indisponible.

### <a name="a-namestretchrpoastretch-database-reduces-the-risk-of-data-loss-for-your-azure-data-by-retaining-migrated-rows-temporarily"></a><a name="stretchRPO"></a>Stretch Database réduit le risque de perte de vos données Azure en conservant temporairement les lignes migrées
Une fois que Stretch Database a migré les lignes éligibles de SQL Server vers Azure, il conserve ces lignes dans la table intermédiaire pendant au minimum huit heures. Si vous restaurez une sauvegarde de votre base de données Azure, Stretch Database utilise les lignes enregistrées dans la table intermédiaire pour rapprocher les bases de données SQL Server et Azure.

Après avoir restauré une sauvegarde de vos données Azure, vous devez exécuter la procédure stockée [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) pour reconnecter la base de données Stretch SQL Server à la base de données Azure distante. Lorsque vous exécutez **sys.sp_rda_reauthorize_db**, Stretch Database rapproche automatiquement les bases de données SQL Server et Azure.

Pour augmenter le nombre d’heures pendant lesquelles Stretch Database conserve temporairement les données migrées dans la table intermédiaire, exécutez la procédure stockée [sys.sp_rda_set_rpo_duration](https://msdn.microsoft.com/library/mt707766.aspx) et spécifiez un nombre supérieur à huit heures. Pour déterminer la quantité de données à conserver, tenez compte des facteurs suivants :

* La fréquence des sauvegardes Azure automatiques (au moins toutes les huit heures).
* Le temps nécessaire après un problème pour l’identifier et décider de restaurer une sauvegarde.
* La durée de l’opération de restauration Azure.

> [!NOTE]
> Augmenter la quantité de données que Stretch Database conserve temporairement dans la table intermédiaire augmente l’espace requis sur le serveur SQL Server.
> 
> 

Pour vérifier le nombre d’heures pendant lesquelles Stretch Database conserve temporairement les données dans la table intermédiaire actuellement, exécutez la procédure stockée [sys.sp_rda_get_rpo_duration](https://msdn.microsoft.com/library/mt707767.aspx).

## <a name="see-also"></a>Voir aussi
[Gérer et dépanner Stretch Database](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Sauvegarder et restaurer des bases de données SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)




<!--HONumber=Nov16_HO3-->


