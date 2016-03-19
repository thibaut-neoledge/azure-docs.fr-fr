<properties
	pageTitle="Importer un fichier BACPAC pour créer une base de données SQL Azure | Microsoft Azure"
	description="Créer une base de données SQL Azure en important un fichier BACPAC existant"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="01/20/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Importer un fichier BACPAC pour créer une nouvelle base de données SQL Azure


**Base de données unique**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

Cet article fournit des instructions pour créer une nouvelle base de données SQL Azure à partir d’un fichier BACPAC à l’aide du [portail Azure](https://portal.azure.com).

Un BACPAC est un fichier .bacpac qui contient un schéma de base de données et des données. Pour plus d'informations, consultez Backup Package (.bacpac) dans [Applications de la couche Données](https://msdn.microsoft.com/library/ee210546.aspx).

La base de données est créée à partir d'un fichier BACPAC importé depuis un conteneur d'objets blob de stockage Azure. Si vous n’avez pas de fichier .bacpac dans le stockage Azure, vous pouvez en créer un en suivant la procédure décrite dans [Créer et exporter un fichier BACPAC à partir d’une base de données SQL Azure](sql-database-export.md).


> [AZURE.NOTE]La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur que vous pouvez restaurer, et en assure la maintenance. Pour plus d’informations, consultez [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md).


Pour importer une base de données SQL à partir d'un fichier .bacpac, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Un serveur V12 de base de données SQL Azure. Si vous n’avez pas de serveur V12, créez-en un en suivant les étapes figurant dans cet article : [Créer votre première base de données SQL Azure](sql-database-get-started.md).
- Un fichier .bacpac de la base de données que vous souhaitez importer dans un conteneur d’objets blob de [Compte Azure Storage (classique)](storage-create-storage-account.md).


## Sélectionnez le serveur qui contiendra la base de données

Ouvrez le panneau SQL Server de la base de données à importer :

1.	Accédez au [portail Azure](https://portal.azure.com).
2.	Cliquez sur **PARCOURIR TOUT**.
3.	Cliquez sur **Serveurs SQL**.
2.	Cliquez sur le serveur dans lequel la base de données doit être restaurée.
3.	Dans le panneau SQL Server, cliquez sur **Importer la base de données** pour ouvrir le panneau **Importer la base de données** :

    ![importer une base de données][1]

1.  Cliquez sur **Stockage** et sélectionnez votre compte de stockage, le conteneur blob et le fichier .bacpac, puis cliquez sur **OK**.

    ![configurer les options de stockage][2]

1.  Sélectionnez le niveau de tarification de la nouvelle base de données et cliquez sur **Sélectionner**.

    ![sélectionner un niveau de tarification][3]

1.  Saisissez un **NOM DE BASE DE DONNÉES**.
2.  Entrez le **Nom d’utilisateur** et le **Mot de passe** administrateur du serveur SQL Azure sur lequel vous importez la base de données.
1.  Cliquez sur **Créer** pour créer la base de données à partir du fichier BACPAC.

    ![créer une base de données][4]

La commande **Créer** envoie une demande d’importation de la base de données au service. Selon la taille de votre base de données, l'opération d'importation peut prendre plus ou moins longtemps.

## Surveillez la progression de l’opération d’importation

2.	Cliquez sur **PARCOURIR TOUT**.
3.	Cliquez sur **Serveurs SQL**.
2.	Cliquez sur le serveur sur lequel vous effectuez la restauration.
3.	Dans le panneau du serveur SQL, cliquez sur **Historique d'Import/Export** :

    ![historique d’import export][5] ![historique d’import export][6]





## Vérifiez que la base de données est en ligne sur le serveur

2.	Cliquez sur **PARCOURIR TOUT**.
3.	Cliquez sur **Bases de données SQL** et vérifiez que la nouvelle base de données est **En ligne**.



## Étapes suivantes

- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)



## Ressources supplémentaires

- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

<!---HONumber=AcomDC_0121_2016-->