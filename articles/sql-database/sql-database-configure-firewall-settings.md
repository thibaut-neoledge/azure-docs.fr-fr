---
title: "Configurer une règle de pare-feu au niveau du serveur sur une base de données SQL | Microsoft Docs"
description: "Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent au serveur SQL Azure."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 867f06c1fae3715ab03ae4a3ff4ec381603e32f7
ms.openlocfilehash: 1479595fb7de2e0a37520c7d323624142e624af1


---
# <a name="create-and-manage-azure-sql-database-server-level-firewall-rules-using-the-azure-portal"></a>Créer et gérer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-database-firewall-configure.md)
> * [Portail Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API REST](sql-database-configure-firewall-settings-rest.md)
> 

Les règles de pare-feu au niveau du serveur permettent aux administrateurs d’accéder à un serveur SQL Database à partir d’une adresse IP spécifiée ou d’une plage d’adresses IP. Vous pouvez également utiliser des règles de pare-feu pour les utilisateurs quand plusieurs bases de données ont les mêmes exigences d’accès et que vous ne souhaitez pas les configurer une à une. Microsoft recommande d’utiliser, dans la mesure du possible, des règles de pare-feu au niveau de la base de données pour améliorer la sécurité et renforcer la portabilité de la base de données. Pour une vue d’ensemble des pare-feu SQL Database, consultez [Vue d’ensemble des règles de pare-feu de SQL Database](sql-database-firewall-configure.md).

> [!Note]
> Pour plus d’informations sur les bases de données portables dans le cadre de la continuité d’activité, consultez [Exigences d’authentification pour la récupération d’urgence](sql-database-geo-replication-security-config.md).
>

[!INCLUDE [Create SQL Database firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gérer des règles de pare-feu au niveau du serveur existantes via le portail Azure
Répétez les étapes pour gérer les règles de pare-feu au niveau du serveur.

* Pour ajouter l’ordinateur actuel, cliquez sur Ajouter une adresse IP cliente.
* Pour ajouter des adresses IP supplémentaires, remplissez les champs Nom de la règle, Adresse IP de début et Adresse IP de fin.
* Pour modifier une règle existante, cliquez sur les champs dans la règle et modifiez-les.
* Pour supprimer une règle existante, placez le pointeur sur la règle jusqu’à ce que le X s’affiche à la fin de la ligne. Cliquez sur X pour supprimer la règle.

Cliquez sur **Enregistrer** pour enregistrer les modifications.

## <a name="next-steps"></a>Étapes suivantes

- Pour trouver un didacticiel de prise en main, consultez [Didacticiel sur la base de données SQL : création d’un serveur, d’une règle de pare-feu de niveau serveur, d’un exemple de base de données, d’une règle de pare-feu de niveau base de données et connexion à SQL Server](sql-database-get-started.md).
- Pour trouver un didacticiel de prise en main de la sécurité, consultez [Prise en main de la sécurité](sql-database-get-started-security.md)
- Pour obtenir de l’aide afin de vous connecter à une base de données SQL Azure à partir d’applications open source ou tierces, consultez [Exemples de code de démarrage rapide client pour Base de données SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Pour comprendre comment créer des utilisateurs supplémentaires pouvant se connecter aux bases de données, consultez [Authentification et autorisation de base de données SQL : octroi de l’accès](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Sécurisation de votre base de données](sql-database-security.md)   
* [Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Dec16_HO2-->


