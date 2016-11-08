---
title: La sauvegarde et la restauration de dépannage avec Azure SQL Database
description: Découvrez comment restaurer une base de données suite à des erreurs ou des bannes en utilisant des sauvegardes et des réplicas dans Azure SQL DatabaseBase de données SQL.
services: sql-database
documentationcenter: ''
author: dalechen
manager: felixwu
editor: ''

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: daleche

---
# Restaurer une base de données à un état antérieur, restaurer une base de données supprimée ou à la suite d’une panne du centre de données
Une base de données SQL conserve les réplicas de votre base de données pour vous permettre de les restaurer à la suite d’une panne ou d’une erreur utilisateur. Les options disponibles dépendent du niveau de service de base de données et des options que vous avez choisi. Consultez la section [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md) pour avoir plus de détails et des considérations en matière de conception.

## Pour restaurer une base de données à un état antérieur
1. Dans le [portail Azure](https://azure.microsoft.com/), cliquez sur **Bases de données SQL**.
2. Sélectionnez votre base de données dans la liste, puis cliquez sur **Restaurer**.
3. Saisissez un nouveau nom de base de données, choisissez la date et l’heure à restaurer, puis cliquez sur **Créer.**
4. Faites les réglages nécessaires pour faire référence à la base de données. Consultez la page [Récupération d’une base de données à un moment donné](sql-database-recovery-using-backups.md#point-in-time-restore).

## Pour restaurer une base de données supprimée accidentellement
1. Dans le [portail Azure](https://azure.microsoft.com/), cliquez sur **Serveurs SQL**.
2. Sélectionnez le serveur hébergeant la base de données dans la liste.
3. Dans le panneau du serveur, faites défiler et cliquez sur **Bases de données supprimées**.
4. Sélectionnez la base de données à restaurer, puis cliquez sur **Créer**.
5. Faites les réglages nécessaires pour faire référence à la base de données. Consultez [Récupérer une base de données supprimée](sql-database-recovery-using-backups.md#deleted-database-restore).

## Pour restaurer une base de données suite à une panne du centre de données régional
Avec les bases de données Standard et Premium, si vous avez configuré les serveurs secondaires géo-répliqué, vous pouvez assurer la restauration à l’aide de bases de données secondaires. Vous avez ainsi la possibilité de restaurer une base de données avec des possibilités de perte de données moindres. Consultez [Récupérer une base de données SQL Azure à l’aide des sauvegardes automatisées d’une base de données](sql-database-disaster-recovery.md) pour plus de détails.

Azure fournit également des sauvegardes de chaque base de données dans une autre région (sauvegarde géo-redondante). Vous pouvez créer une base de données à partir de ces sauvegardes (cette opération s’appelle géo-restauration). Cependant, une restauration de ce type peut provoquer la perte de données.

**Pour restaurer une base de données à l’aide d’une géo-restauration :**

* Dans le [portail Azure](https://azure.microsoft.com/), cliquez sur **Nouveau**, cliquez sur **Données et stockage**, sur **Base de données SQL**, puis sélectionnez **Sauvegarde** en tant que source de base de données. Voir [Restaurer une base de données SQL Azure à la suite d’une panne](sql-database-disaster-recovery.md) pour plus de détails.

<!---HONumber=AcomDC_0831_2016-->