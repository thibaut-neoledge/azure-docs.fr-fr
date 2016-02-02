<properties
	pageTitle="Copie d’une base de données SQL Azure | Microsoft Azure"
	description="Création d'une copie d'une base de données SQL Azure"
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


# Copie d'une base de données SQL Azure

**Base de données unique**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)

Les étapes suivantes vous montrent comment copier une base de données SQL avec le [portail Azure](https://portal.azure.com). L'opération de copie de base de données crée une nouvelle base de données SQL. La copie est une sauvegarde instantanée de votre base de données que vous créez sur le même serveur ou sur un autre serveur.

> [AZURE.NOTE]La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur que vous pouvez restaurer, et en assure la maintenance. Pour plus d'informations, consultez [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md).

Lorsque le processus de copie est terminé, la nouvelle base de données est une base de données entièrement fonctionnelle qui est indépendante de la base de données source. La nouvelle base de données est cohérente au niveau transactionnel avec la base de données source au moment où la copie s'achève. Le niveau de service et le niveau de performances (niveau de tarification) de la copie de base de données sont les mêmes que ceux de la base de données source. Une fois la copie terminée, la copie devient une base de données indépendante et entièrement fonctionnelle. Les connexions, les utilisateurs et les autorisations peuvent être gérés indépendamment.


Lorsque vous copiez une base de données sur le même serveur logique, les mêmes connexions peuvent être utilisées sur les deux bases de données. L'élément principal de sécurité que vous utilisez pour copier la base de données devient le propriétaire de la base de données (DBO) sur la nouvelle base de données. Tous les utilisateurs de la base de données, leurs autorisations et leurs identificateurs de sécurité (SID) sont copiés vers la copie de base de données.


Pour copier une base de données SQL, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Une base de données SQL à copier. Si vous n’avez pas de base de données SQL, créez-en une en suivant les étapes figurant dans cet article : [Créer votre première base de données SQL Azure](sql-database-get-started.md).



## Copie de votre base de données SQL

Ouvrez le panneau SQL Database de la base de données que vous voulez copier :

1.	Accédez au [portail Azure](https://portal.azure.com).
2.	Accédez à la base de données que vous souhaitez copier : Parcourir > Bases de données SQL
3.	Dans le panneau de la base de données SQL, cliquez sur **Copier** pour ouvrir le panneau **Copier** :

    ![copier une base de données][1]

1.  Entrez un nom pour la copie de la base de données. Un nom par défaut est fourni, mais vous pouvez le modifier si vous le souhaitez.
2.  Sélectionner un **serveur cible**. Le serveur cible est celui où la copie de la base de données sera créée. Vous pouvez créer un nouveau serveur ou sélectionnez un serveur existant dans la liste.
3.  Cliquez sur **OK** pour démarrer le processus de copie.

    ![nom et serveur de la base de données][2]




## Contrôle de la progression de l'opération de copie

2.	Après le démarrage de la copie, cliquez sur la notification du portail pour plus d'informations.


    ![notification][3]

 
    ![monitor][4]





## Vérifiez que la base de données est en ligne sur le serveur

- Cliquez sur **PARCOURIR** > **Bases de données SQL** et vérifiez que la nouvelle base de données est **En ligne**.



## Étapes suivantes

- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exporter la base de données vers un fichier BACPAC](sql-database-export.md)



## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-copy/copy.png
[2]: ./media/sql-database-copy/copy-ok.png
[3]: ./media/sql-database-copy/copy-notification.png
[4]: ./media/sql-database-copy/monitor-copy.png

<!---HONumber=AcomDC_0121_2016-->