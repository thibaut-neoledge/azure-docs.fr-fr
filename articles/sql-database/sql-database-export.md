<properties
	pageTitle="Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide du portail Azure"
	description="Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide du portail Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="08/15/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Archiver une base de données SQL Azure dans un fichier BACPAC à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

Cet article fournit des instructions pour archiver votre base de données SQL Azure à partir d’un fichier BACPAC (stocké dans Azure Blob Storage) à l’aide du [Portail Azure](https://portal.azure.com).

Lorsque vous avez besoin d’archiver une base de données SQL Azure, vous pouvez exporter le schéma et les données associés dans un fichier BACPAC. Un fichier BACPAC est un fichier ZIP avec l’extension BACPAC. Il peut être stocké ultérieurement dans Azure Blob Storage ou un stockage local dans un emplacement local, puis importé dans Azure SQL Database ou une installation locale SQL Server.

***Considérations***

- Pour qu’une archive soit cohérente au niveau transactionnel, vous devez vérifier qu’aucune activité d’écriture n’a lieu lors de l’exportation ou effectuer une exportation à partir d’une [copie cohérente au niveau transactionnel](sql-database-copy.md) de votre base de données SQL Azure.
- La taille maximale d’un fichier BACPAC archivé dans Azure Blob Storage est de 200 Go. Pour archiver un fichier BACPAC plus volumineux dans un stockage local, utilisez l’utilitaire d’invite de commande [SqlPackage](https://msdn.azure.microsoft.com/.com/library/hh550080.aspx). Cet utilitaire est fourni avec Visual Studio et SQL Server. Vous pouvez également [télécharger](https://msdn.azure.microsoft.com/.com/library/mt204009.aspx) la dernière version de SQL Server Data Tools pour obtenir cet utilitaire.
- L’archivage dans Azure Premium Storage à l’aide d’un fichier BACPAC n’est pas pris en charge.
- Si l’opération d’exportation dure plus de 20 heures, elle peut être annulée. Pour améliorer les performances lors de l’exportation, vous pouvez :
 - Augmentez temporairement votre niveau de service.
 - Interrompez toutes les activités de lecture et d’écriture lors de l’exportation.
 - Utilisez un [index cluster](https://msdn.azure.microsoft.com/.com/library/ms190457.aspx) avec des valeurs non nulles sur toutes les tables de grande taille. Sans index cluster, une exportation peut échouer si elle dure plus de 6 à 12 heures. Cela est dû au fait que les services d’exportation doivent effectuer une analyse complète de la table pour tenter de l’exporter en entier. Une bonne méthode pour déterminer si vos tables sont optimisées pour l’exportation consiste à exécuter **DBCC SHOW\_STATISTICS** et à vérifier que *RANGE\_HI\_KEY* n’est pas nul et que sa valeur a une bonne distribution. Pour plus d’informations, consultez [DBCC SHOW\_STATISTICS](https://msdn.azure.microsoft.com/.com/library/ms174384.aspx).


> [AZURE.NOTE] Les BACPAC ne sont pas conçus pour être utilisés pour les opérations de sauvegarde et de restauration. La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur. Pour plus d’informations, consultez [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md).

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure.
- Une base de données SQL Azure.
- Un [compte Azure Storage standard](../storage/storage-create-storage-account.md) avec un conteneur d’objets blob pour stocker le fichier BACPAC dans le stockage standard.

## Exporter votre base de données

Ouvrez le panneau Base de données SQL de la base de données que vous voulez exporter :

> [AZURE.IMPORTANT] Pour garantir un fichier BACPAC cohérent au niveau transactionnel, vous devez d’abord [créer une copie de votre base de données](sql-database-copy.md), puis exporter la copie de base de données.

1.	Accédez au [portail Azure](https://portal.azure.com).
2.	Cliquez sur **Bases de données SQL**.
3.	Cliquez sur la base de données à archiver.
4.	Dans le panneau Base de données SQL, cliquez sur **Exporter** pour ouvrir le panneau **Exporter la base de données** :

    ![bouton exporter][1]

5.  Cliquez sur **Stockage** et sélectionnez votre compte de stockage et le conteneur d’objets blob où sera stocké le fichier BACPAC :

    ![exporter une base de données][2]

6. Sélectionnez le type d’authentification.
7.  Entrez les informations d’identification appropriées pour le serveur SQL Azure qui contient la base de données que vous exportez.
8.  Cliquez sur **OK** pour archiver la base de données. En cliquant sur **OK**, une demande d’exportation de la base de données est créée et envoyée au service. La durée de l’exportation dépend de la taille et de la complexité de votre base de données, ainsi que de votre niveau de service. Vous recevez une notification.

    ![exporter la notification][3]

## Surveillez la progression de l’opération d’exportation

1.	Cliquez sur **Serveurs SQL**.
2.	Cliquez sur le serveur contenant la base de données (source) d’origine que vous venez d’archiver.
3.  Faites défiler vers Opérations.
4.	Dans le panneau du serveur SQL, cliquez sur **Historique d'Import/Export** :

    ![historique d’import export][4]

## Vérifiez que le fichier BACPAC se trouve dans votre conteneur de stockage

1.	Cliquez sur **Comptes de stockage**.
2.	Cliquez sur le compte de stockage où vous avez stocké l’archive BACPAC.
3.	Cliquez sur **Conteneurs** et sélectionnez le conteneur dans lequel vous avez exporté la base de données pour obtenir les détails (vous pouvez télécharger et enregistrer le fichier BACPAC ici).

    ![détails du fichier .bacpac][5]

## Étapes suivantes

- Pour en savoir plus sur l’importation d’un fichier BACPAC dans une base de données SQL Azure, consultez [Importer un fichier BACPCAC dans une base de données SQL Azure](sql-database-import.md)
- Pour en savoir plus sur l’importation d’un fichier BACPAC dans une base de données SQL Server, consultez [Importer un fichier BACPCAC dans une base de données SQL Server](https://msdn.azure.microsoft.com/.com/library/hh710052.aspx)



<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

<!---HONumber=AcomDC_0817_2016-->