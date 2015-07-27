<properties 
	pageTitle="Gestion de la base de données SQL" 
	description="Découvrez comment gérer la base de données SQL Azure." 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor="" 
	services="sql-database" 
	documentationCenter=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="jeffreyg"/>


# Gestion de la base de données SQL

Cet article montre comment effectuer des tâches de gestion simples dans la base de données SQL Azure.

## Connexion à la base de données SQL dans Azure à l’aide de Management Studio

Management Studio est un outil d’administration qui permet de gérer plusieurs serveurs et instances SQL Server dans un espace de travail unique. Si vous disposez déjà d’une instance SQL Server locale, vous pouvez ouvrir une connexion sur une instance locale et un serveur logique sur Azure afin d’effectuer des tâches côte à côte.

Management Studio comprend des fonctionnalités qui ne sont actuellement pas disponibles sur le portail de gestion, telles qu’un vérificateur de syntaxe et la capacité d’enregistrement de scripts et des requêtes nommées à des fins de réutilisation. La base de données SQL est simplement un point de terminaison TDS (Tabular Data Stream). Tous les outils qui fonctionnent avec TDS, notamment Management Studio, sont valides pour les opérations relatives à la base de données SQL. Les scripts que vous développez pour le serveur local s’exécuteront sur un serveur logique de la base de données SQL.

À l’étape suivante, vous allez utiliser Management Studio pour vous connecter à un serveur logique sur Azure. Cette étape requiert la version 2008 R2 ou 2012 de SQL Server Management Studio. Si vous avez besoin d’aide pour télécharger Management Studio ou vous y connecter, consultez la rubrique [Gestion de la base de données SQL à l’aide de Management Studio][], sur ce site.

Avant de pouvoir vous connecter, il est parfois nécessaire de créer une exception de pare-feu qui autorise des requêtes sortantes sur le port 1433 sur votre système local. Le port 1433 des ordinateurs qui sont sécurisés par défaut n’est généralement pas ouvert.

## Configuration du pare-feu pour le serveur local

1. Dans Pare-feu Windows avec fonctions avancées de sécurité, créez une règle sortante.

2. Choisissez **Port**, spécifiez TCP 1433, indiquez **Autoriser la connexion** et vérifiez que le profil **Public** est sélectionné.

3. Indiquez un nom explicite, tel que *WindowsAzureSQLDatabase (tcp-out) port 1433*.


## Connexion à un serveur logique

1. Dans la zone Se connecter au serveur de Management Studio, vérifiez que le moteur de base de données est sélectionné, puis saisissez le nom du serveur logique au format suivant : *nom_serveur*.database.widnows.net.

	Vous pouvez également obtenir le nom de serveur complet dans le portail de gestion, sur le tableau de bord du serveur et dans GÉRER L’URL.

2. Dans Authentification, choisissez **Authentification SQL Server**, puis entrez la connexion administrateur que vous avez créée lorsque vous avez configuré le serveur logique.

3. Cliquez sur **Options**.

4. Dans Connexion à une base de données, spécifiez **master**.


## Connexion à un serveur local

1. Dans la zone Se connecter au serveur de Management Studio, vérifiez que le moteur de base de données est sélectionné, puis saisissez le nom d’une instance locale au format suivant : *nom_serveur**nom_instance*. Si le serveur est local et une instance par défaut, entrez *localhost*.

2. Dans Authentification, choisissez **Authentification Windows**, puis entrez un compte Windows qui est membre du rôle administrateur système.


## Ajout de connexions et d’utilisateurs à la base de données SQL Azure

Après avoir déployé une base de données, vous devez configurer des connexions et attribuer des autorisations. Dans cette étape, vous allez exécuter deux scripts.

Dans le premier script, vous allez vous connecter à master et exécuter un script qui crée des connexions. Les connexions seront utilisées pour prendre en charge les opérations de lecture et d’écriture, ainsi que la délégation de tâches opérationnelles, telles que la capacité à exécuter les requêtes système sans autorisation « sa ».

Les connexions que vous créez doivent être des connexions d’authentification SQL Server. Si vous disposez déjà des scripts qui utilisent des identités d’utilisateur Windows ou des identités basées sur les revendications, ils ne fonctionneront pas sur la base de données SQL.

Le second script attribue les autorisations aux utilisateurs de la base de données. Dans ce script, vous allez vous connecter à une base de données déjà chargée sur Azure.

## Création de connexions

1. Dans Management Studio, connectez-vous à un serveur logique sur Azure, développez le dossier Bases de données, cliquez avec le bouton droit sur **master**, puis sélectionnez **Nouvelle requête**.

2. Utilisez les instructions Transact-SQL suivantes pour créer des connexions. Remplacez le mot de passe par un mot de passe valide. Sélectionnez chacun d’eux, puis cliquez sur **Exécuter**. Répétez l’action précédente pour les connexions restantes.

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- exécuter sur master, exécuter chaque ligne individuellement
    -- utiliser cette connexion pour gérer d’autres connexions sur ce serveur
    CREATE LOGIN sqladmin WITH password='&lt;ProvidePassword>'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- utiliser cette connexion pour créer ou copier une base de données
    CREATE LOGIN sqlops WITH password='&lt;ProvidePassword>';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
</pre></div>


## Création d'utilisateurs de base de données

1. Développez le dossier Bases de données, cliquez avec le bouton droit sur **school**, puis sélectionnez **Nouvelle requête**.

2. Utilisez les instructions Transact-SQL suivantes pour ajouter des utilisateurs de base de données. Remplacez le mot de passe par un mot de passe valide.

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- exécuter sur une base de données normale, exécuter chaque ligne séparément
    -- utiliser cette connexion pour les opérations de lecture
    CREATE LOGIN sqlreader WITH password='&lt;ProvidePassword>';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- utiliser cette connexion pour les opérations d’écriture
    CREATE LOGIN sqlwriter WITH password='&lt;ProvidePassword>';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- accorder des autorisations DMV sur la base de données school à 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
</pre></div>

## Affichage et test de connexions

1. Dans une nouvelle fenêtre de requête, connectez-vous à **master**, puis exécutez l’instruction suivante : 

        SELECT * from sys.sql_logins;

2. Dans Management Studio, cliquez avec le bouton droit sur la base de données **school**, puis sélectionnez **Nouvelle requête**.

3. Dans le menu Requête, pointez sur **Connexion**, puis cliquez sur **Modifier la connexion**.

4. Connectez-vous en tant que *sqlreader*.

5. Copiez et essayez d’exécuter l’instruction suivante. Vous recevrez une erreur indiquant que l’objet n’existe pas.

        INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6. Ouvrez une seconde fenêtre de requête, puis remplacez le contexte de connexion par *sqlwriter*. La même requête doit à présent s’exécuter correctement.

Vous avez maintenant créé et testé plusieurs connexions. Pour plus d’informations, consultez les pages [Gestion des bases de données et des connexions dans Base de données SQL Azure][] et [Contrôle de Base de données SQL Azure à l’aide de vues de gestion dynamique][].

[Gestion des bases de données et des connexions dans Base de données SQL Azure]: http://msdn.microsoft.com/library/windowsazure/ee336235.aspx
[Contrôle de Base de données SQL Azure à l’aide de vues de gestion dynamique]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
[Gestion de la base de données SQL à l’aide de Management Studio]: http://www.windowsazure.com/develop/net/common-tasks/sql-azure-management/





 

<!---HONumber=July15_HO2-->