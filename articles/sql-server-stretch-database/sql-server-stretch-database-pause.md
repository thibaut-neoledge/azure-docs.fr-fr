---
title: "Suspension et reprise de la migration des données (Stretch Database) | Microsoft Docs"
description: "Découvrez comment suspendre ou reprendre la migration des données vers Azure."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: d45c05ad-254e-4950-a652-3d5cc40ed967
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f8b5f4461a20c1407ba2ce834ec6dd970f4e7544


---
# <a name="pause-and-resume-data-migration-stretch-database"></a>Suspension et reprise de la migration des données (Stretch Database)
Pour suspendre ou reprendre la migration des données vers Azure, sélectionnez **Stretch** pour une table dans SQL Server Management Studio, puis sélectionnez **Suspendre** pour suspendre la migration des données ou **Reprendre** pour reprendre la migration de données. Vous pouvez également utiliser Transact\-SQL pour suspendre ou reprendre la migration des données.

Suspendez la migration des données sur différentes tables pour résoudre les problèmes sur le serveur local ou optimiser la bande passante réseau disponible.

## <a name="pause-data-migration"></a>Suspension de la migration des données
### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>SQL Server Management Studio permet de suspendre la migration des données
1. Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la table compatible Stretch pour laquelle vous souhaitez suspendre la migration des données.
2. Cliquez avec le bouton droit et sélectionnez **Stretch**, puis sélectionnez **Suspendre**.

### <a name="use-transact-sql-to-pause-data-migration"></a>Utiliser Transact\-SQL pour suspendre la migration des données
Exécutez la commande ci-dessous.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>Reprendre la migration des données
### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>SQL Server Management Studio permet de reprendre la migration des données
1. Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la table compatible Stretch pour laquelle vous souhaitez reprendre la migration des données.
2. Cliquez avec le bouton droit et sélectionnez **Stretch**, puis sélectionnez **Reprendre**.

### <a name="use-transact-sql-to-resume-data-migration"></a>Utiliser Transact\-SQL pour reprendre la migration des données
Exécutez la commande ci-dessous.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>Vérifier si la migration est active ou en pause
### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>Utilisez SQL Server Management Studio pour vérifier si la migration est active ou en pause
Dans SQL Server Management Studio, ouvrez **Surveillance de Stretch Database** et vérifiez la valeur de la colonne **État de la migration**. Pour plus d’informations, voir [Surveillance et dépannage de la migration de données](sql-server-stretch-database-monitor.md).

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Utiliser Transact-SQL pour vérifier si la migration est active ou en pause
Interrogez l’affichage catalogue **sys.remote_data_archive_tables** et vérifiez la valeur de la colonne **is_migration_paused**. Pour plus d’informations, consultez [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="see-also"></a>Voir aussi
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[Surveillance et dépannage de la migration de données](sql-server-stretch-database-monitor.md)




<!--HONumber=Nov16_HO3-->


