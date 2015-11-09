<properties
	pageTitle="Créer et exporter un fichier BACPAC à partir d'une base de données SQL Azure"
	description="Créer et exporter un fichier BACPAC à partir d'une base de données SQL Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/23/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Créer et exporter un fichier BACPAC à partir d'une base de données SQL Azure

**Base de données unique**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

Cet article fournit des instructions pour l’exportation d’un fichier BACPAC de votre base de données SQL Azure à l’aide du [portail Azure en version préliminaire](https://portal.azure.com).

Un [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) est un fichier .bacpac qui contient un schéma de base de données et des données. Un BACPAC est principalement utilisé pour déplacer une base de données d’un serveur à un autre, [migrer une base de données locale dans le cloud](sql-database-cloud-migrate.md) et archiver une base de données existante dans un format ouvert.

> [AZURE.NOTE]Les BACPAC ne sont pas conçus pour être utilisés pour les opérations de sauvegarde et de restauration. La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur. Pour plus d’informations, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).


Le fichier BACPAC est exporté dans un conteneur d'objets blob de stockage Azure que vous pouvez télécharger une fois l'opération terminée.

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Une base de données SQL Azure. Si vous n’avez pas de base de données SQL, créez-en une en suivant les étapes figurant dans cet article : [Créer votre première base de données SQL Azure](sql-database-get-started.md).
- Un [compte Azure Storage](storage-create-storage-account.md) avec un conteneur d’objets blob pour stocker le fichier BACPAC. Actuellement, le compte de stockage doit utiliser le modèle de déploiement classique : choisissez par conséquent **Classique** lors de la création d’un compte de stockage. 


## Exporter votre base de données

Ouvrez le panneau Base de données SQL de la base de données que vous voulez exporter :

> [AZURE.IMPORTANT]Pour garantir un fichier BACPAC cohérent au niveau transactionnel, vous devez d’abord [créer une copie de votre base de données](sql-database-copy.md), puis exporter la copie de base de données.

1.	Accédez au [portail Azure en version préliminaire](https://portal.azure.com).
2.	Cliquez sur **PARCOURIR TOUT**.
3.	Cliquez sur **Bases de données SQL**.
2.	Cliquez sur la base de données à exporter en tant que BACPAC.
3.	Dans le panneau Base de données SQL, cliquez sur **Exporter** pour ouvrir le panneau **Exporter la base de données** :

    ![bouton exporter][1]

1.  Cliquez sur **Stockage** et sélectionnez votre compte de stockage et le conteneur d’objets blob où sera stocké le fichier BACPAC :

    ![exporter une base de données][2]

1.  Entrez la **Connexion de l’administrateur du serveur** et le **Mot de passe** du serveur SQL Azure qui contient la base de données que vous exportez.
1.  Cliquez sur **Créer** pour exporter la base de données.

En cliquant sur **Créer**, une demande d’exportation de la base de données est créée et envoyée au service. Selon la taille de votre base de données, l'opération d'exportation peut prendre plus ou moins longtemps.

## Surveillez la progression de l’opération d’exportation

2.	Cliquez sur **PARCOURIR TOUT**.
3.	Cliquez sur **Serveurs SQL**.
2.	Cliquez sur le serveur contenant la base de données (source) d'origine que vous venez d'exporter.
3.	Dans le panneau du serveur SQL, cliquez sur **Historique d’importation/exportation** :

    ![historique d’import export][3] ![historique d’import export][4]

## Vérifiez que le fichier BACPAC se trouve dans votre conteneur de stockage

2.	Cliquez sur **PARCOURIR TOUT**.
3.	Cliquez sur **Comptes de stockage (classiques)**.
2.	Cliquez sur le compte de stockage où vous avez stocké le fichier BACPAC.
3.	Cliquez sur **Conteneurs** et sélectionnez le conteneur dans lequel vous avez exporté la base de données pour obtenir les détails (vous pouvez télécharger et enregistrer le fichier BACPAC ici).

    ![détails du fichier .bacpac][5]


## Étapes suivantes

- [Importation d'une base de données SQL Azure](sql-database-import.md)



## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Exercices de récupération d'urgence](sql-database-disaster-recovery-drills.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-history.png
[4]: ./media/sql-database-export/export-status.png
[5]: ./media/sql-database-export/bacpac-details.png

<!---HONumber=Nov15_HO1-->