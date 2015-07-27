<properties
	pageTitle="Connexion à une base de données SQL Azure avec SSMS" metaKeywords=""
	description="Découvrez comment vous connecter à une base de données SQL Azure à l&#39;aide de SSMS"
	services="sql-database"
	documentationCenter=""
	authors="sidneyh" solutions=""
	manager="jhubbard" editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="06/25/2015"
	ms.author="sidneyh" />

# Connexion à une base de données SQL Azure avec SQL Server Management Studio

Voici la procédure à suivre pour vous connecter à une base de données SQL Microsoft Azure à l’aide de SQL Server Management Studio (SSMS).

## Conditions préalables
* Une base de données SQL Azure configurée et en cours d'exécution. Pour créer une base de données SQL, consultez [Prise en main de Base de données SQL Microsoft Azure](sql-database-get-started.md).
* Le nom et le mot de passe de l'administrateur pour la base de données SQL.
* SQL Server Management Studio 2014. Pour obtenir l'outil, consultez [Télécharger SQL Express](http://www.hanselman.com/blog/DownloadSQLServerExpress.aspx).
* Configurer les paramètres de pare-feu pour la base de données. Voir la rubrique [Configuration des paramètres du pare-feu (Base de données SQL Azure)](sql-database-configure-firewall-settings.md)

## Connexion à une instance de base de données SQL
1. Connectez-vous au [portail de gestion Azure](https://portal.azure.com).
2. Cliquez sur le bouton **Parcourir**, puis cliquez sur **Bases de données SQL** (b).

	![Cliquez sur Parcourir et la base de données SQL][1]
3. Avec l’option **Bases de données SQL** sélectionnée (a), cliquez sur le nom de la base de données sur le serveur à laquelle vous souhaitez vous connecter (b).

	![Cliquez sur le nom d'une base de données][2]
4. Une fois le nom sélectionné (a), cliquez sur Propriétés (b). Copiez le nom du serveur (c) et le nom de l'administrateur (d). Le nom et le mot de passe de l’administrateur sont créés lors de la création de la base de données SQL. Vous devez avoir le mot de passe pour passer à l'étape suivante.

	![Cliquez sur SQL Server, Paramètres et Propriété][3]
5. Ouvrez SQL Server Management Studio 2014.
6. Dans la boîte de dialogue Se connecter au serveur, collez le nom du serveur dans la zone **Nom du serveur** (a). Définissez l'authentification sur **Authentification SQL Server** (b). Collez le nom de l'administrateur du serveur dans la zone **Connexion** (c), puis entrez son mot de passe (d). Ensuite, cliquez sur **Options** (e).

	![Boîte de dialogue de connexion SSMS][4]
7. Dans l'onglet Propriétés de connexion, définissez la zone **Connexion à une base de données** sur **master** (ou toute autre base de données à laquelle vous souhaitez vous connecter). Ensuite, cliquez sur **Connecter**.

	![Valeur maître sélectionnée et cliquez sur Connecter][5]

## Résolution des problèmes de connexion

En cas de problème, consultez [Résolution des problèmes de connexion à la base de données SQL Azure](https://support.microsoft.com/kb/2980233/). Pour obtenir la liste des problèmes possibles et des réponses associées, consultez [Dépannage de la connectivité de la base de données SQL Microsoft Azure](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1).


## Étapes suivantes
Vous pouvez utiliser les instructions Transact-SQL pour créer ou gérer les bases de données. Consultez [CREATE DATABASE (base de données SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) et [Gestion de la base de données SQL Azure au moyen de SQL Server Management Studio](sql-database-manage-azure-ssms.md). Vous pouvez également enregistrer les événements dans le stockage Azure. Consultez [Prise en main de l'audit de base de données SQL](sql-database-auditing-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/browse-vms.png
[2]: ./media/sql-database-connect-to-database/sql-databases.png
[3]: ./media/sql-database-connect-to-database/blades.png
[4]: ./media/sql-database-connect-to-database/ssms-connect-to-server.png
[5]: ./media/sql-database-connect-to-database/ssms-master.png
 

<!---HONumber=July15_HO2-->