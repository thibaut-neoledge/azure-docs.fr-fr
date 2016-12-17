---
title: "Restaurer des bases de données Stretch | Microsoft Docs"
description: "Découvrez comment restaurer des bases de données Stretch."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 97fdf8d4-1d91-409f-bfce-755e15c79498
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7f892604d863225625c8d3b05bb4c8b985be7d5f


---
# <a name="restore-stretch-enabled-databases"></a>Restaurer des bases de données Stretch
Restaurez une base de données sauvegardée lorsque c’est nécessaire pour effectuer une récupération à partir de nombreux types d’échecs, d’erreurs et de sinistres.

Pour plus d’informations sur la sauvegarde, consultez [Sauvegarder des bases de données Stretch](sql-server-stretch-database-backup.md).

> [!NOTE]
> La sauvegarde n’est qu’une partie d’une solution complète de haute disponibilité et de continuité d’activité. Pour plus d’informations sur la haute disponibilité, consultez [Solutions de haute disponibilité](https://msdn.microsoft.com/library/ms190202.aspx).
> 
> 

## <a name="restore-your-sql-server-data"></a>Restaurer vos données SQL Server
Pour récupérer à partir d’une défaillance ou d’un endommagement matériel, restaurez la base de données\-Stretch SQL Server à partir d’une sauvegarde. Vous pouvez continuer à utiliser les méthodes de restauration SQL Server que vous utilisez actuellement. Pour plus d’informations, consultez [Vue d’ensemble de la récupération et de la restauration](https://msdn.microsoft.com/library/ms191253.aspx).

Après avoir restauré la base de données SQL Server, vous devez exécuter la procédure stockée **sys.sp_rda_reauthorize_db** pour restaurer la connexion entre la base de données\-Stretch SQL Server et la base de données Azure distante. Pour plus d’informations, consultez [Restaurer la connexion entre la base de données SQL Server et la base de données Azure distante](#restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database).

## <a name="restore-your-remote-azure-data"></a>Restaurer vos données Azure distantes
### <a name="recover-a-live-azure-database"></a>Restaurer une base de données Azure active
Le service SQL Server Stretch Database sur Azure crée des instantanés de toutes les données actives au moins toutes les huit heures, à l’aide d’instantanés Azure Storage. Ces instantanés sont conservés pendant 7 jours. Ainsi, vous pouvez restaurer les données à 21 instants différents au cours des 7 derniers jours, jusqu’au moment de la prise du dernier instantané.

Pour restaurer une base de données Azure active à un point antérieur dans le temps à l’aide du portail Azure, procédez comme suit.

1. Connectez-vous au portail Azure.
2. Sur le côté gauche de l’écran, sélectionnez l’option **PARCOURIR**, puis choisissez **Bases de données SQL**.
3. Accédez à votre base de données et sélectionnez-la.
4. En haut du panneau de la base de données, cliquez sur **Restaurer**.
5. Spécifiez un nouveau **nom de base de données**, sélectionnez un **point de restauration**, puis cliquez sur **Créer**.
6. Le processus de restauration de la base de données commence. Vous pouvez le surveiller dans **NOTIFICATIONS**.

### <a name="recover-a-deleted-azure-database"></a>Récupérer une base de données Azure supprimée
Avant qu’une base de données ne soit supprimée, le service SQL Server Stretch Database sur Azure en effectue une capture instantanée et la conserve pendant sept jours. Une fois cette opération achevée, il ne conserve plus les instantanés de la base de données active. Cela vous permet de restaurer une base de données supprimée dans son état au moment de sa suppression.

Pour restaurer une base de données Azure supprimée au moment de sa suppression à l’aide du portail Azure, procédez comme suit.

1. Connectez-vous au portail Azure.
2. Sur le côté gauche de l’écran, sélectionnez **PARCOURIR**, puis choisissez **Serveurs SQL**.
3. Accédez à votre serveur et sélectionnez-le.
4. Dans le panneau du serveur, faites défiler jusqu’à Opérations, puis cliquez sur la vignette **Bases de données supprimées** .
5. Sélectionnez la base de données supprimée que vous souhaitez restaurer.
6. Spécifiez un nouveau **nom pour la base de données** et cliquez sur **Créer**.
7. Le processus de restauration de la base de données commence. Vous pouvez le surveiller dans **NOTIFICATIONS**.

## <a name="restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database"></a>Restaurer la connexion entre la base de données SQL Server et la base de données Azure distante
1. Si vous souhaitez vous connecter à une base de données Azure restaurée avec un nom différent ou dans une région différente, exécutez la procédure stockée [sys.sp_rda_deauthorize_db](https://msdn.microsoft.com/library/mt703716.aspx) pour vous déconnecter de la base de données Azure précédente.  
2. Exécutez la procédure stockée [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) pour reconnecter la base de données\-Stretch locale à la base de données Azure.  
   
   * Fournissez les informations d’identification de portée de base de données sous forme de valeur varchar\(128\) ou sysname. \((N’utilisez pas varchar\(max\).\) Vous pouvez rechercher le nom d’identification dans la vue **sys.database\_scoped\_credentials**.  
   * Indiquez s’il est nécessaire d’effectuer une copie des données distantes et de s’y connecter (recommandé).  
   
   ```tsql  
   USE <Stretch-enabled database name>;
   GO
   EXEC sp_rda_reauthorize_db
       @credential = N'<existing_database_scoped_credential_name>',
       @with_copy = 1 ;  
   GO
   ```  

## <a name="see-also"></a>Voir aussi
[Gérer et dépanner Stretch Database](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Sauvegarder et restaurer des bases de données SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)




<!--HONumber=Nov16_HO3-->


