<properties 
   pageTitle="Consignes et limitations générales de base de données SQL Azure"
   description="Cette page décrit certaines limitations générales de la base de données SQL Azure, ainsi que les zones d’interopérabilité et de prise en charge."
   services="sql-database"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/28/2015"
   ms.author="jroth" />

# Consignes et limitations générales de base de données SQL Azure

Cette rubrique fournit des instructions et présente les limitations générales applicables à la base de données Azure SQL. Pour mieux comprendre les notions de quotas, de gestion des ressources et de prise en charge, consultez les [ressources supplémentaires](#additional-guidelines) référencées à la fin de cette rubrique.

## Connectivité

 - L’authentification Windows n’est pas prise en charge. Voir [Gestion des bases de données et des connexions dans la base de données Azure SQL](sql-database-manage-logins.md) 

 - La base de données SQL Microsoft Azure prend en charge les versions 7.3 et ultérieures du client de protocole TDS (Tabular Data Stream).

 - Seules les connexions TCP/IP sont autorisées.

 - Le navigateur de SQL Server 2008 n’est pas pris en charge, car la base de données SQL Microsoft Azure ne possède pas de ports dynamiques, mais uniquement le port 1433.

## Agents/tâches SQL Server

La base de données SQL Microsoft Azure ne prend pas en charge l’Agent ou les tâches SQL Server. Vous pouvez toutefois exécuter l’agent SQL Server sur votre serveur SQL local et vous connecter à la base de données SQL Microsoft Azure.

## Transactions

La base de données SQL Azure ne prend pas en charge les transactions distribuées, c’est-à-dire les transactions qui affectent plusieurs ressources. Pour plus d’informations, consultez [Transactions distribuées (ADO.NET)](https://msdn.microsoft.com/library/ms254973.aspx). Entre deux basculements, la base de données SQL peut ne pas conserver les valeurs d’horodatage non validées de la base de données actuelle (DBTS).

> [AZURE.NOTE]Dans certaines situations, une transaction peut être automatiquement promue vers une transaction distribuée. Pour plus d’informations, consultez [Intégration de System.Transactions à SQL Server](https://msdn.microsoft.com/library/ms172070.aspx).

## Prise en charge du classement SQL Server

Le classement de base de données par défaut utilisé par la base de données SQL Microsoft Azure est **SQL\_LATIN1\_GENERAL\_CP1\_CI\_AS**, où **LATIN1\_GENERAL** correspond à l’anglais (États-Unis) et **CP1** à la page de code 1252. La propriété **CI** indique que le classement n’est pas sensible à la casse, et **AS** qu’il respecte les accents.

Lorsque vous utilisez un serveur SQL local, vous pouvez définir des classements au niveau du serveur, de la base de données, de la colonne ou de l’expression. La base de données SQL Microsoft Azure n’autorise pas la définition du classement au niveau du serveur. Pour utiliser un classement autre que celui défini par défaut avec la base de données SQL Microsoft Azure, définissez le classement à l’aide de l’option Create Database Collate (Créer un classement de base de données) ou utilisez un classement au niveau de la colonne ou de l’expression. La base de données SQL ne prend pas en charge l’option de classement avec la commande Alter Database. Par défaut, dans la base de données SQL, les données temporaires ont le même classement que celui de la base de données. Pour plus d’informations sur la façon de définir le classement, consultez [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## Conventions de dénomination

Certains noms d’utilisateur ne sont pas autorisés pour des raisons de sécurité. Vous ne pouvez pas utiliser les noms suivants :

 - **admin** 
 - **administrator** 
 - **guest** 
 - **root** 
 - **sa** 

Les noms de tous les nouveaux objets doivent être conformes aux règles des identificateurs SQL Server. Pour plus d’informations, consultez [Identificateurs](https://msdn.microsoft.com/library/ms175874.aspx).

En outre, les noms de connexion et d’utilisateur ne peuvent pas contenir le caractère \\ (l’authentification Windows n’est pas prise en charge).

## Conseils supplémentaires

- Outre les limitations générales décrites dans cet article, la base de données SQL impose des quotas de ressource et des limitations spécifiques selon votre [niveau de service](sql-database-service-tiers.md). Pour obtenir une description détaillée des limites de niveau de service, consultez la rubrique relative aux [limites et capacités de niveau de service](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

- Pour connaître les autres limites de la base de données SQL, consultez [Limites de ressources de la base de données SQL](sql-database-limits.md).

- Pour des informations sur la sécurité, voir [Instructions et limitations de sécurité d’Azure SQL Database](sql-database-security-guidelines.md).

- Un autre domaine connexe entoure la compatibilité de la base de données SQL Azure avec les versions locales de SQL Server, comme SQL Server 2014. La dernière version (V12) de la base de données SQL Azure a introduit de nombreuses améliorations dans ce domaine. Pour plus d’informations, consultez [Nouveautés de SQL Database V12](sql-database-v12-whats-new.md).

- Pour plus d’informations sur la disponibilité des pilotes et sur la prise en charge de la base de données SQL, consultez [Bibliothèques de connexions de la base de données SQL et de SQL Server](sql-database-libraries.md).

<!---HONumber=Nov15_HO1-->