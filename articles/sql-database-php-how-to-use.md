<properties linkid="develop-php-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (PHP) - Azure feature guides" metaKeywords="Azure SQL Database PHP, SQL Database PHP" description="Learn how to create and connect to an Azure SQL Database from PHP." metaCanonical="" services="sql-database" documentationCenter="PHP" title="How to Access Azure SQL Database from PHP" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" videoId="" scriptId="" />

Accès à la base de données SQL Azure à partir de PHP
====================================================

Ce guide présente les principes de base concernant l'utilisation de la base de données SQL Azure à partir de PHP. Les exemples sont écrits en PHP. Les scénarios traités incluent la **création d'une base de données SQL** et la **connexion à celle-ci**. Ce guide traite de la création d'une base de données SQL à partir du [portail de gestion](https://manage.windowsazure.com). Pour plus d'informations sur la réalisation de ces tâches depuis le portail de production, consultez la page [Prise en main de PHP et de la base de données SQL](http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx). Pour plus d'informations, consultez la section [Étapes suivantes](#NextSteps).

Définition de la base de données SQL Azure
------------------------------------------

La base de données SQL Azure fournit un système de gestion des bases de données relationnelles pour Azure. Elle est basée sur la technologie SQL Server. La base de données SQL permet de configurer et de déployer facilement des solutions de bases de données relationnelles dans le cloud. Elle tire parti d'un centre de données distribué qui fournit haute disponibilité, extensibilité et sécurité aux entreprises, avec les avantages de la protection des données et de l'auto-adaptation intégrées.

Sommaire
--------

-   [Concepts](#Concepts)
-   [Configuration de votre environnement](#Setup)
-   [Création d'une base de données SQL](#CreateServer)
-   [Obtention des informations de connexion à la base de données SQL](#ConnectionInfo)
-   [Connexion à une instance de base de données SQL](#Connect)
-   [Étapes suivantes](#NextSteps)

Concepts
--------

La base de données SQL Azure repose sur les technologies SQL Server. De ce fait, il existe beaucoup de similitudes entre l'accès à la base de données SQL à partir de PHP et l'accès à SQL Server à partir de PHP. Vous pouvez développer une application en local (à l'aide de SQL Server) et vous connecter ensuite à la base de données SQL en modifiant simplement la chaîne de connexion. Toutefois, les quelques différences qui existent entre la base de données SQL et SQL Server peuvent avoir une incidence sur votre application. Pour plus d'informations, consultez la page [Instructions et limitations (Base de données SQL)](http://msdn.microsoft.com/en-us/library/windowsazure/ff394102.aspx).

L'approche recommandée pour accéder à la base de données SQL à partir de PHP est d'utiliser les [pilotes Microsoft SQL Server pour PHP](http://www.microsoft.com/download/en/details.aspx?id=20098) (les exemples contenus dans cet article utilisent ces pilotes). Les pilotes Microsoft SQL Server pour PHP fonctionnent sur Windows uniquement.

Configuration de votre environnement
------------------------------------

La méthode recommandée pour configurer votre environnement de développement est d'utiliser [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/?LinkId=253447). Web Platform Installer vous permet de choisir les éléments de votre plateforme de développement Web, qui sont ensuite installés et configurés automatiquement. En téléchargeant Web Platform Installer et en décidant d'installer WebMatrix, PHP pour WebMatrix et SQL Server Express, vous obtenez un environnement de développement complet, configuré automatiquement.

Vous pouvez également choisir de configurer votre environnement manuellement :

-   Installez PHP et configurez IIS : <http://php.net/manual/en/install.windows.iis7.php>.
-   Téléchargez et installez SQL Server Express : <http://www.microsoft.com/en-us/download/details.aspx?id=29062>
-   Téléchargez et installez les pilotes Microsoft SQL Server pour PHP : <http://php.net/manual/en/sqlsrv.requirements.php>.

Création d'une base de données SQL
----------------------------------

Pour créer une base de données SQL Azure, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2.  Cliquez sur **New** dans le coin inférieur gauche du portail.

    ![Créer un site Web Azure](./media/sql-database-php-how-to-use-sql-database/plus-new.png)

3.  Cliquez sur **DATA SERVICES**, **SQL DATABASE**, puis sur **CUSTOM CREATE**.

    ![Créer une base de données SQL personnalisée](./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png)

4.  Entrez une valeur pour le **nom** de la base de données, sélectionnez l'**édition** (Web ou Entreprise), la **taille maximale**, le **classement** et **NEW SQL Database Server**. Cliquez sur la flèche située en bas de la boîte de dialogue. Si vous avez déjà créé une base de données SQL, vous pouvez choisir un serveur existant dans le menu déroulant **Choose a server**.

    ![Renseigner les paramètres de la base de données SQL](./media/sql-database-php-how-to-use-sql-database/new-sql-db.png)

5.  Entrez le nom et le mot de passe de l'administrateur (et confirmez ce dernier), choisissez la région dans laquelle sera créée la base de données SQL, puis activez la case `Allow Azure Services to access the server`.

    ![Créer un serveur de base de données SQL](./media/sql-database-php-how-to-use-sql-database/db-server-settings.png)

Pour afficher les informations relatives au serveur et à la base de données, cliquez sur **SQL Databases** dans le portail de gestion. Cliquez sur **BASES DE DONNÉES** ou **SERVEURS** pour afficher les informations associées.

![Afficher les informations relatives au serveur et à la base de données](./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png)

Obtention des informations de connexion à la base de données SQL
----------------------------------------------------------------

Pour obtenir les informations de connexion à la base de données SQL, cliquez sur **SQL DATABASES** dans le portail, puis sur le nom de la base de données.

![Afficher les informations relatives à la base de données](./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png)

Cliquez ensuite sur **View SQL Database connection strings for ADO.NET, ODBC, PHP, and JDBC**.

![Afficher les chaînes de connexion](./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png)

Dans la section PHP de la fenêtre qui s'ouvre, notez les valeurs des champs **SERVER**, **DATABASE** et **USERNAME**. Votre mot de passe est le même que celui que vous avez utilisé lorsque vous avez créé la base de données SQL.

Connexion à une instance de base de données SQL
-----------------------------------------------

Les exemples suivants montrent comment utiliser les extensions **SQLSRV** et **PDO\_SQLSRV** pour se connecter à une base de données SQL appelée `testdb`. Vous aurez besoin des informations obtenues à la section précédente. Remplacez `SERVER_ID` par votre ID de serveur à 10 chiffres (c'est-à-dire, les 10 premiers caractères de la valeur SERVER obtenue à la section précédente), puis affectez les valeurs correctes (votre nom d'utilisateur et votre mot de passe) aux variables `$user` et `$pwd`.

##### SQLSRV

    $server = "tcp:<value of SERVER from section above>";
    $user = "<value of USERNAME from section above>"@SERVER_ID;
    $pwd = "mot de passe";
    $db = "testdb";

    $conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

    if($conn === false){
        die(print_r(sqlsrv_errors()));
    }

##### PDO\_SQLSRV

    $server = "tcp:<value of SERVER from section above>";
    $user = "<value of USERNAME from section above>"@SERVER_ID;
    $pwd = "mot de passe";
    $db = "testdb";

    try {
        $conn = new PDO( "sqlsrv:Server= $server ; Database = $db ", $user, $pwd);
        $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
    }
    catch(Exception $e){
        die(print_r($e));
    }

Étapes suivantes
----------------

Comme indiqué précédemment, l'utilisation de la base de données SQL est semblable à celle de SQL Server. Une fois que vous avez établi une connexion avec une base de données SQL (comme indiqué plus haut), vous pouvez utiliser les API **SQLSRV** ou **PDO\_SQLSRV** pour insérer, récupérer, mettre à jour et supprimer des données. Pour plus d'informations sur les API **SQLSRV** et **PDO\_SQLSRV**, consultez la [documentation relative aux pilotes Microsoft SQL Server pour PHP](http://msdn.microsoft.com/en-us/library/dd638075(SQL.10).aspx). Toutefois, les quelques différences qui existent entre la base de données SQL et SQL Server peuvent avoir une incidence sur votre application. Pour plus d'informations, consultez la page [Instructions et limitations (Base de données SQL)](http://msdn.microsoft.com/en-us/library/windowsazure/ff394102.aspx).

Un exemple montrant comment utiliser la base de données SQL avec PHP sur Azure est disponible à l'adresse <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure>.

