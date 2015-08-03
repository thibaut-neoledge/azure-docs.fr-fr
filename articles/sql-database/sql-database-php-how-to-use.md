<properties 
	pageTitle="Utilisation d’une base de données SQL (PHP) - Guides de fonctionnalités Azure" 
	description="Découvrez comment créer une base de données SQL Azure et comment s’y connecter à partir de PHP." 
	services="sql-database" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="jeffreyg" 
	editor="mollybos"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="tomfitz"/>

#Accès à la base de données SQL Azure à partir de PHP 

## Vue d’ensemble

Ce guide présente les principes de base concernant l’utilisation de la base de données SQL à partir de PHP. Les exemples sont écrits en PHP. Les scénarios traités incluent la **création d’une base de données SQL** et la **connexion à celle-ci**. Ce guide traite de la création d’une base de données SQL à partir du [portail de gestion][management-portal]. Pour plus d’informations sur la réalisation de ces tâches depuis le portail de production, consultez la page [Prise en main de PHP et de la base de données SQL][prod-portal-instructions]. Pour plus d’informations, consultez la section [Étapes suivantes](#NextSteps).

##Présentation de la base de données SQL

La base de données SQL fournit un système de gestion des bases de données relationnelles pour Azure. Elle est fondée sur la technologie SQL Server. La base de données SQL permet de configurer et de déployer facilement des solutions de bases de données relationnelles dans le cloud. Elle tire parti d’un centre de données distribué qui fournit haute disponibilité, extensibilité et sécurité aux entreprises, avec les avantages de la protection des données et de l’auto-adaptation intégrées.

##<a id="Concepts"></a>Concepts
La base de données SQL repose sur les technologies SQL Server. De ce fait, il existe beaucoup de similitudes entre l’accès à la base de données SQL à partir de PHP et l’accès à SQL Server à partir de PHP. Vous pouvez développer une application en local (à l’aide de SQL Server) et vous connecter ensuite à la base de données SQL en modifiant simplement la chaîne de connexion. Toutefois, les quelques différences qui existent entre la base de données SQL et SQL Server peuvent avoir une incidence sur votre application. Pour plus d'informations, consultez la page [Instructions et limitations (Base de données SQL)][limitations].

L'approche recommandée pour accéder à la base de données SQL à partir de PHP est d'utiliser les [pilotes Microsoft SQL Server pour PHP][download-drivers] (les exemples contenus dans cet article utilisent ces pilotes). Les pilotes Microsoft SQL Server pour PHP fonctionnent sur Windows uniquement.

##<a id="Setup"></a>Configuration de votre environnement

La méthode recommandée pour configurer votre environnement de développement est d’utiliser [Microsoft Web Platform Installer][wpi-installer]. Web Platform Installer vous permet de choisir les éléments de votre plateforme de développement Web, qui sont ensuite installés et configurés automatiquement. En téléchargeant Web Platform Installer et en décidant d'installer WebMatrix, PHP pour WebMatrix et SQL Server Express, vous obtenez un environnement de développement complet, configuré automatiquement.

Vous pouvez également choisir de configurer votre environnement manuellement :

* Installez PHP et configurez IIS : [http://php.net/manual/en/install.windows.iis7.php][manual-config].
* Téléchargez et installez SQL Server Express : [http://www.microsoft.com/download/details.aspx?id=29062][install-sql-express]
* Téléchargez et installez les [pilotes Microsoft pour PHP pour SQL Server][download-drivers].

##<a id="CreateServer"></a>Création d’une base de données SQL

Pour créer une base de données SQL Azure, procédez comme suit :

1. Connectez-vous au [portail de gestion Azure][management-portal].
2. Cliquez sur **New** dans le coin inférieur gauche du portail.

	![Créer un site Web Azure][new-website]

3. Cliquez sur **SERVICES DE DONNÉES**, **BASE DE DONNÉES SQL** et **CRÉATION RAPIDE**. Fournissez un nom pour la base de données, que ce soit pour utiliser un serveur de base de données existant ou un nouveau, une région, un nom d’administrateur et un mot de passe.

	![Créer une base de données SQL personnalisée][quick-create]


Pour afficher les informations relatives au serveur et à la base de données, cliquez sur **SQL Databases** dans le portail de gestion. Cliquez sur **BASES DE DONNÉES** ou **SERVEURS** pour afficher les informations associées.

![Afficher les informations relatives au serveur et à la base de données][sql-dbs-servers]

##<a id="ConnectionInfo"></a>Obtention des informations de connexion à la base de données SQL

Pour obtenir les informations de connexion à la base de données SQL, cliquez sur **SQL DATABASES** dans le portail, puis sur le nom de la base de données.

![Afficher les informations relatives à la base de données][go-to-db-info]

Cliquez ensuite sur **View SQL Database connection strings for ADO.NET, ODBC, PHP, and JDBC**.

![Afficher les chaînes de connexion][show-connection-string]

Dans la section PHP de la fenêtre qui s’ouvre, notez les valeurs des champs **SERVER**, **DATABASE** et **USERNAME**. Votre mot de passe est le même que celui que vous avez utilisé lorsque vous avez créé la base de données SQL.

##<a id="Connect"></a>Connexion à une instance de base de données SQL

Les exemples suivants montrent comment utiliser les extensions **SQLSRV** et **PDO_SQLSRV** pour se connecter à une base de données SQL appelée `testdb`. Pour plus d’informations sur les API **SQLSRV** et **PDO_SQLSRV**, consultez la [documentation relative aux pilotes Microsoft SQL Server pour PHP][driver-docs]. Vous aurez besoin des informations obtenues à la section précédente. Remplacez `SERVER_ID` par votre ID de serveur à 10 chiffres (c’est-à-dire, les 10 premiers caractères de la valeur SERVER obtenue à la section précédente), puis affectez les valeurs correctes (votre nom d’utilisateur et votre mot de passe) aux variables `$user` et `$pwd`.

#####SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	$conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

	if($conn === false){
		die(print_r(sqlsrv_errors()));
	}

#####PDO_SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	try{
		$conn = new PDO( "sqlsrv:Server= $server ; Database = $db ", $user, $pwd);
		$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
	}
	catch(Exception $e){
		die(print_r($e));
	}


##<a id="NextSteps"></a>Étapes suivantes
Comme indiqué précédemment, l’utilisation de la base de données SQL est semblable à celle de SQL Server. Une fois que vous avez établi une connexion avec une base de données SQL (comme indiqué plus haut), vous pouvez utiliser les API **SQLSRV** ou **PDO_SQLSRV** pour insérer, récupérer, mettre à jour et supprimer des données. Toutefois, les quelques différences qui existent entre la base de données SQL et SQL Server peuvent avoir une incidence sur votre application. Pour plus d’informations, consultez la page [Instructions et limitations (Base de données SQL)][limitations].

Un exemple montrant comment utiliser la base de données SQL avec PHP sur Azure est disponible à l’adresse <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure>.

[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/library/dd638075(SQL.10).aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[install-sql-express]: http://www.microsoft.com/download/details.aspx?id=29062
[management-portal]: https://manage.windowsazure.com
[prod-portal-instructions]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
[new-website]: ./media/sql-database-php-how-to-use/plus-new.png
[custom-create]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
[database-settings]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
[create-server]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-php-how-to-use/sql-dbs-portal.png
[wpi-installer]: http://go.microsoft.com/fwlink/?LinkId=253447
[go-to-db-info]: ./media/sql-database-php-how-to-use/go-to-db-info.png
[show-connection-string]: ./media/sql-database-php-how-to-use/show-connection-string-2.png
[quick-create]: ./media/sql-database-php-how-to-use/create-new-sql.png
 

<!---HONumber=July15_HO4-->