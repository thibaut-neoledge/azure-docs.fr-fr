<properties urlDisplayName="SQL Database" pageTitle="Utilisation d'une base de données SQL (PHP) - Guides de fonctionnalités Azure" metaKeywords="Base de données SQL Azure PHP, Base de données SQL PHP" description="Learn how to create and connect to an Azure SQL Database from PHP." metaCanonical="" services="sql-database" documentationCenter="PHP" title="How to Access Azure SQL Database from PHP" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/29/2014" ms.author="tomfitz" />

#Accès à la base de données SQL Azure à partir de PHP 

Ce guide présente les principes de base concernant l'utilisation de la base de données SQL Azure à partir de PHP. Les exemples sont écrits en PHP. Les scénarios traités incluent la **création d'une base de données SQL** et la **connexion à celle-ci**. Ce guide traite de la création d'une base de données SQL à partir du [portail de gestion][management-portal]. Pour plus d'informations sur la réalisation de ces tâches depuis le portail de production, consultez la page [Prise en main de PHP et de la base de données SQL][prod-portal-instructions]. Pour plus d'informations, consultez la section [Étapes suivantes](#NextSteps).

##Définition de la base de données SQL Azure

La base de données SQL Azure fournit un système de gestion des bases de données relationnelles pour Azure. Elle est basée sur la technologie SQL Server. La base de données SQL permet de configurer et de déployer facilement des solutions de bases de données relationnelles dans le cloud. Elle tire parti d'un centre de données distribué qui fournit haute disponibilité, extensibilité et sécurité aux entreprises, avec les avantages de la protection des données et de l'auto-adaptation intégrées.

##Sommaire

* [Concepts](#Concepts)
* [ Configuration de votre environnement](#Setup)
* [ Création d'une base de données SQL](#CreateServer)
* [ Obtention des informations de connexion à la base de données SQL](#ConnectionInfo)
* [ Connexion à une instance de base de données SQL](#Connect)
* [Étapes suivantes](#NextSteps)

##<a id="Concepts"></a>Concepts
La base de données SQL Azure repose sur les technologies SQL Server. De ce fait, il existe beaucoup de similitudes entre l'accès à la base de données SQL à partir de PHP et l'accès à SQL Server à partir de PHP. Vous pouvez développer une application en local (à l'aide de SQL Server) et vous connecter ensuite à la base de données SQL en modifiant simplement la chaîne de connexion. Toutefois, les quelques différences qui existent entre la base de données SQL et SQL Server peuvent avoir une incidence sur votre application. Pour plus d'informations, consultez la page [Instructions et limitations (Base de données SQL)][limitations].

L'approche recommandée pour accéder à la base de données SQL à partir de PHP est d'utiliser les [pilotes Microsoft SQL Server pour PHP][download-drivers]. (les exemples contenus dans cet article utilisent ces pilotes). Les pilotes Microsoft SQL Server pour PHP fonctionnent sur Windows uniquement.

##<a id="Setup"></a> Configuration de votre environnement

La méthode recommandée pour configurer votre environnement de développement est d'utiliser [Microsoft Web Platform Installer][wpi-installer]. Web Platform Installer vous permet de choisir les éléments de votre plateforme de développement Web, qui sont ensuite installés et configurés automatiquement. En téléchargeant Web Platform Installer et en décidant d'installer WebMatrix, PHP pour WebMatrix et SQL Server Express, vous obtenez un environnement de développement complet, configuré automatiquement.

Vous pouvez également choisir de configurer votre environnement manuellement :

* Installez PHP et configurez IIS : [http://php.net/manual/en/install.windows.iis7.php][manual-config].
* Téléchargez et installez SQL Server Express : [http://www.microsoft.com/fr-fr/download/details.aspx?id=29062][install-sql-express]
* Téléchargez et installez les pilotes Microsoft SQL Server pour PHP : [http://php.net/manual/en/sqlsrv.requirements.php][install-drivers].

##<a id="CreateServer"></a> Création d'une base de données SQL

Pour créer une base de données SQL Azure, procédez comme suit :

1. Connectez-vous au [portail de gestion][management-portal].
2. Cliquez sur **Nouveau** dans le coin inférieur gauche du portail.

	![Create New Azure Web Site][new-website]

3. Cliquez sur **DATA SERVICES**, **BASE DE DONNÉES SQL**, puis **﻿CRÉATION RAPIDE**. Indiquez un nom pour la base de données, utilisez une nouvelle base de données, ou une base de données existante, une région, ainsi qu'un nom et un mot de passe d'administrateur.

	![Custom Create a new SQL Database][quick-create]


Pour afficher les informations relatives au serveur et à la base de données, cliquez sur **Bases de données SQL** dans le portail de gestion. Cliquez sur **BASES DE DONNÉES** ou **SERVEURS** pour afficher les informations associées.

![View server and database information][sql-dbs-servers]

##<a id="ConnectionInfo"></a> Obtention des informations de connexion à la base de données SQL

Pour obtenir les informations de connexion à la base de données SQL, cliquez sur **﻿BASES DE DONNÉES SQL** dans le portail, puis sur le nom de la base de données.

![View database information][go-to-db-info]

Cliquez ensuite sur **Afficher les chaînes de connexion de la base de données SQL pour ADO .Net, ODBC, PHP et JDBC**.

![Show connection strings][show-connection-string]

Dans la section PHP de la fenêtre qui s'affiche, notez les valeurs des champs **SERVEUR**, **BASE DE ﻿﻿﻿DONNÉES** et **NOM D'UTILISATEUR**. Votre mot de passe est le même que celui que vous avez utilisé lorsque vous avez créé la base de données SQL.

##<a id="Connect"></a> Connexion à une instance de base de données SQL

Les exemples suivants montrent comment utiliser les extensions **SQLSRV** et **PDO_SQLSRV** pour se connecter à une base de données SQL appelée `testdb`. Vous aurez besoin des informations obtenues à la section précédente. Remplacez `SERVER_ID` par votre ID de serveur à 10 chiffres (c'est-à-dire, les 10 premiers caractères de la valeur SERVER obtenue à la section précédente), puis affectez les valeurs correctes (votre nom d'utilisateur et votre mot de passe) aux variables `$user` et `$pwd`.

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
Comme indiqué précédemment, l'utilisation de la base de données SQL est semblable à celle de SQL Server. Une fois que vous avez établi une connexion avec une base de données SQL (comme indiqué plus haut), vous pouvez utiliser les API **SQLSRV** ou **PDO\_SQLSRV** pour insérer, récupérer, mettre à jour et supprimer des données. Pour plus d'informations sur les API **SQLSRV** et **PDO\_SQLSRV**, consultez la [documentation relative aux pilotes Microsoft SQL Server pour PHP][driver-docs]. Toutefois, les quelques différences qui existent entre la base de données SQL et SQL Server peuvent avoir une incidence sur votre application. Pour plus d'informations, consultez la page [Instructions et limitations (Base de données SQL)][limitations].

Un exemple montrant comment utiliser la base de données SQL avec PHP sur Azure est disponible à l'adresse <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure>.

[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/fr-fr/library/dd638075(SQL.10).aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[install-sql-express]: http://www.microsoft.com/fr-fr/download/details.aspx?id=29062
[management-portal]: https://manage.windowsazure.com
[prod-portal-instructions]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
[new-website]: ./media/sql-database-php-how-to-use-sql-database/plus-new.png
[custom-create]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
[database-settings]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
[create-server]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png
[wpi-installer]: http://go.microsoft.com/fwlink/?LinkId=253447
[go-to-db-info]: ./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png
[show-connection-string]: ./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png
[quick-create]: ./media/sql-database-php-how-to-use-sql-database/create-new-sql.png
