<properties 
    pageTitle="Copier une base de données SQL Azure à l’aide de Transact-SQL | Microsoft Azure" 
    description="Création d'une copie d'une base de données SQL Azure à l'aide de Transact-SQL" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Copier une base de données SQL Azure à l’aide de Transact-SQL


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)



Les étapes suivantes vous montrent comment copier une base de données SQL avec Transact-SQL. L'opération de copie de base de données copie une base de données SQL dans une nouvelle base de données en utilisant l'instruction [CREATE DATABASE](). La copie est une sauvegarde instantanée de votre base de données que vous créez sur le même serveur ou sur un autre serveur.


> [AZURE.NOTE] La base de données SQL Azure [crée automatiquement des sauvegardes](sql-database-automated-backups.md) pour chaque base de données utilisateur que vous pouvez restaurer, et en assure la maintenance.


Lorsque le processus de copie est terminé, la nouvelle base de données est une base de données entièrement fonctionnelle qui est indépendante de la base de données source. La nouvelle base de données est cohérente au niveau transactionnel avec la base de données source au moment où la copie s'achève. Le niveau de service et le niveau de performances (niveau de tarification) de la copie de base de données sont les mêmes que ceux de la base de données source. Une fois la copie terminée, la copie devient une base de données indépendante et entièrement fonctionnelle. Les connexions, les utilisateurs et les autorisations peuvent être gérés indépendamment.


Lorsque vous copiez une base de données sur le même serveur logique, les mêmes connexions peuvent être utilisées sur les deux bases de données. L'élément principal de sécurité que vous utilisez pour copier la base de données devient le propriétaire de la base de données (DBO) sur la nouvelle base de données. Tous les utilisateurs de la base de données, leurs autorisations et leurs identificateurs de sécurité (SID) sont copiés vers la copie de base de données.


Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Une base de données SQL Azure. Si vous n’avez pas de base de données SQL, créez-en une en suivant les étapes figurant dans cet article : [Créer votre première base de données SQL Azure](sql-database-get-started.md).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Si vous n'avez pas SSMS ou si les fonctionnalités décrites dans cet article ne sont pas disponibles, [téléchargez la dernière version](https://msdn.microsoft.com/library/mt238290.aspx).




## Copie de votre base de données SQL

Connectez-vous à la base de données maître à l'aide de la connexion principale niveau serveur ou la connexion qui a créé la base de données que vous souhaitez copier. Les connexions autres que la connexion principale niveau serveur doivent être membres du rôle dbmanager afin de copier des bases de données. Pour plus d'informations sur les connexions et la connexion au serveur, consultez Gestion des bases de données, des connexions et des utilisateurs dans Azure SQL Database et Développement de base de données SQL Azure : rubriques Procédures, respectivement.

Démarrez la copie de la base de données source avec l'instruction CREATE DATABASE. L'exécution de cette instruction initie le processus de copie de la base de données. Étant donné que la copie d'une base de données est un processus asynchrone, l'instruction CREATE DATABASE est retournée avant la fin de la copie de la base de données.


### Copie d'une base de données SQL sur le même serveur

Connectez-vous à la base de données maître à l'aide de la connexion principale niveau serveur ou la connexion qui a créé la base de données que vous souhaitez copier. Les connexions autres que la connexion principale niveau serveur doivent être membres du rôle dbmanager afin de copier des bases de données.

Cette commande copie Database1 dans une nouvelle base de données nommée Database2 sur le même serveur. Selon la taille de votre base de données, l'opération de copie peut prendre plus ou moins longtemps.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### Copie d'une base de données SQL vers un autre serveur

Connectez-vous à la base de données maître du serveur de destination, le serveur Azure SQL Database dans lequel la nouvelle base de données doit être créée. Utilisez une connexion qui a les mêmes nom et mot de passe que le propriétaire de la base de données (DBO) source sur le serveur Azure SQL Database source. La connexion sur le serveur de destination doit également être membre du rôle dbmanager ou être la connexion principale niveau serveur.

Cette commande copie Database1 sur server1- dans une nouvelle base de données nommée Database2 sur server2. Selon la taille de votre base de données, l'opération de copie peut prendre plus ou moins longtemps.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## Contrôle de la progression de l'opération de copie

Contrôlez le processus de copie en interrogeant les vues sys.databases et sys.dm\_database\_copies. Pendant que la copie est en cours, la colonne state\_desc de la vue sys.databases pour la nouvelle base de données est définie sur COPYING.


- Si la copie échoue, la colonne state\_desc de la vue sys.databases pour la nouvelle base de données est définie sur SUSPECT. Dans ce cas, exécutez l'instruction DROP sur la nouvelle base de données et réessayez ultérieurement.
- Si la copie réussit, la colonne state\_desc de la vue sys.databases pour la nouvelle base de données est définie sur ONLINE. Dans ce cas, la copie est terminée et la nouvelle base de données est une base de données normale, qui peut être modifiée indépendamment de la base de données source.



## Étapes suivantes


- Si vous décidez d'annuler la copie pendant qu'elle est en cours, exécutez l'instruction [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) sur la nouvelle base de données. Vous pouvez également exécuter l'instruction DROP DATABASE sur la base de données source pour annuler le processus de copie.
- Une fois que la nouvelle base de données est en ligne sur le serveur de destination, utilisez l'instruction [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) pour remapper les utilisateurs de la nouvelle base de données avec des connexions sur le serveur de destination. Tous les utilisateurs de la nouvelle base de données conservent les autorisations qu'ils avaient dans la base de données source. L'utilisateur qui a initié la copie de la base de données devient le propriétaire de celle-ci et reçoit un nouvel identificateur de sécurité (SID). Une fois la copie réussie et avant que les autres utilisateurs ne soient remappés, seule la connexion qui a initié la copie, le propriétaire de la base de données (DBO), peuvent se connecter à la nouvelle base de données.




## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Exercices de récupération d'urgence](sql-database-disaster-recovery-drills.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0615_2016-->