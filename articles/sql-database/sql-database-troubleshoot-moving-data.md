---
title: "Déplacez des bases de données entre des serveurs, entre des abonnements, au sein et hors d’Azure."
description: "Étapes à suivre pour copier, déplacer et migrer des données et des bases de données dans Azure SQL Database."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
ms.assetid: dea01868-5757-41e2-826c-ec26361a339c
ms.service: sql-database
ms.custom: migrate and move
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bc88bb49c601f525c62c4dac4716a3f77a30172e


---
# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>Déplacer des bases de données entre des serveurs, entre des abonnements, au sein et hors d’Azure
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Pour déplacer une base de données sur un autre serveur dans le même abonnement
* Dans le [portail Azure](https://portal.azure.com), cliquez sur **Bases de données SQL**, sélectionnez une base de données dans la liste, puis cliquez sur **Copier**. Pour plus d’informations, consultez [Copier une base de données SQL Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Pour déplacer une base de données entre des abonnements
* Dans le [portail Azure](https://portal.azure.com), cliquez sur **Serveurs SQL** , puis sélectionnez le serveur qui héberge votre base de données dans la liste. Cliquez sur **Déplacer**, puis sélectionnez les ressources à déplacer et l’abonnement cible.

## <a name="to-migrate-a-sql-database-into-azure"></a>Pour migrer une base de données SQL dans Azure
* Déterminez la compatibilité de la base de données, puis choisissez la méthode de migration adaptée à vos besoins. Suivez les instructions et les options indiquées dans [Migration d’une base de données SQL Server](sql-database-cloud-migrate.md).

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>Pour créer une copie d’une base de données en vue d’une utilisation hors d’Azure
* [Exportez un fichier BACPAC.](sql-database-export.md)




<!--HONumber=Nov16_HO3-->


