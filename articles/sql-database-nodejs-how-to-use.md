<properties linkid="develop-node-how-to-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (Node.js) - Azure feature guide" metaKeywords="" description="Learn how to use Azure SQL Database from Node.js." metaCanonical="" services="sql-database" documentationCenter="nodejs" title="How to Access Azure SQL Database from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr" />

# Accès à la base de données SQL AZure à partir de Node.js

Ce guide présente les concepts de base concernant l'utilisation du pilote Microsoft SQL Server pour Node.JS afin d'accéder à une base de données SQL Azure. Les scénarios traités incluent la **création d'une base de données SQL** et la **connexion à celle-ci**. Ce guide décrit la création d'une base de données SQL à partir du [portail de gestion Azure][portail de gestion Azure].

## Sommaire

-   [Concepts][Concepts]
-   [Configuration de votre environnement][Configuration de votre environnement]
-   [Création d'une base de données SQL][Création d'une base de données SQL]
-   [Obtention des informations de connexion à la base de données SQL][Obtention des informations de connexion à la base de données SQL]
-   [Connexion à une instance de base de données SQL][Connexion à une instance de base de données SQL]
-   [Considérations relatives au déploiement dans Azure][Considérations relatives au déploiement dans Azure]
-   [Étapes suivantes][Étapes suivantes]

## <span id="Concepts"></span></a>Concepts

### Définition de la base de données SQL Azure

La base de données SQL Azure fournit un système de gestion des bases de données relationnelles pour Azure. Elle est basée sur la technologie SQL Server. La base de données SQL permet de configurer et de déployer facilement des solutions de bases de données relationnelles dans le cloud. Elle tire parti d'un centre de données distribué qui fournit haute disponibilité, extensibilité et sécurité aux entreprises, avec les avantages de la protection des données et de l'auto-adaptation intégrées.

## Définition du pilote Microsoft SQL Server pour Node.JS

Le pilote Microsoft SQL Server pour Node.JS permet aux développeurs d'accéder aux données stockées dans Microsoft SQL Server ou une base de données SQL Azure à partir d'une application Node.js. Il est actuellement disponible dans sa version préliminaire uniquement. Des fonctionnalités supplémentaires seront prochainement intégrées au projet. Pour plus d'informations sur le pilote, consultez la [page Github][page Github] du projet de pilote Microsoft SQL Server pour Node.JS et la page [Wiki][Wiki] associée.

<div class="dev-callout">
<b>Remarque</b>
<p>Le pilote Microsoft SQL Server pour Node.JS est actuellement disponible dans sa version pr&eacute;liminaire. Il repose sur des composants d'ex&eacute;cution qui sont disponibles uniquement sur les syst&egrave;mes d'exploitation Microsoft Windows et Azure.</p>
</div>

## <span id="Setup"></span></a> Configuration de votre environnement

### Installation du client natif SQL Server

Le pilote Microsoft SQL Server pour Node.js repose sur le client natif SQL Server. Ce dernier est automatiquement disponible lorsque l'application est déployée dans Azure. Il se peut qu'il soit absent de votre environnement de développement local. Vous pouvez installer le client natif SQL Server à partir de la page de téléchargement de [Microsoft SQL Server 2012 Feature Pack][Microsoft SQL Server 2012 Feature Pack].

<div class="dev-callout">
<b>Remarque</b>
<p>Le client natif SQL Server est disponible uniquement pour les syst&egrave;mes d'exploitation Microsoft Windows. Alors que le pilote est disponible de mani&egrave;re native sur Azure, vous ne pourrez pas tester votre application localement &agrave; l'aide des informations de cet article si vous proc&eacute;dez au d&eacute;veloppement sur un syst&egrave;me d'exploitation diff&eacute;rent de Microsoft Windows.</p>
</div>

### Installation de Node.js

Node.js peut être installé à partir de [][]<http://nodejs.org/#download></a>. Si aucun package d'installation n'est disponible pour votre système d'exploitation, vous pouvez générer Node.js à partir du code source.

## <span id="CreateServer"></span></a> Création d'une base de données SQL

Pour créer une base de données SQL Azure, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur l'icône **+ New** dans le coin inférieur gauche du portail.

    ![Créer un site Web Azure][Créer un site Web Azure]

3.  Cliquez sur **SQL Database**, puis sur **Custom Create**.

    ![Créer une base de données SQL personnalisée][Créer une base de données SQL personnalisée]

4.  Entrez une valeur pour le **nom** de la base de données, sélectionnez l'**édition** (Web ou Entreprise), la **taille maximale**, le **classement** et **NEW SQL Database Server**. Cliquez sur la flèche située en bas de la boîte de dialogue. Si vous avez déjà créé une base de données SQL, vous pouvez choisir un serveur existant dans le menu déroulant **Choose a server**.

    ![Renseigner les paramètres de la base de données SQL][Renseigner les paramètres de la base de données SQL]

5.  Entrez le nom de l'administrateur et son mot de passe (et confirmez ce dernier), choisissez la région dans laquelle sera créée la base de données SQL, puis cochez la case `Allow Azure Services to access the server`.

    ![Créer un serveur de base de données SQL][Créer un serveur de base de données SQL]

Pour afficher les informations relatives au serveur et à la base de données, cliquez sur **Bases de données SQL** dans le portail de gestion Azure. Cliquez sur **BASES DE DONNÉES** ou **SERVEURS** pour afficher les informations associées.

![Afficher les informations relatives au serveur et à la base de données][Afficher les informations relatives au serveur et à la base de données]

## <span id="ConnectionInfo"></span></a> Obtention des informations de connexion à la base de données SQL

Pour obtenir les informations de connexion à la base de données SQL, cliquez sur **SQL DATABASES** dans le portail, puis sur le nom de la base de données.

![Afficher les informations relatives à la base de données][Afficher les informations relatives à la base de données]

Cliquez ensuite sur **Show connection strings**.

![Afficher les chaînes de connexion][Afficher les chaînes de connexion]

Dans la section ODBC de la fenêtre qui s'ouvre, notez les valeurs associées à la chaîne de connexion. Vous utiliserez cette chaîne de connexion lors de la connexion à la base de données SQL à partir de votre application node. Votre mot de passe est le même que celui que vous avez utilisé lorsque vous avez créé la base de données SQL.

## <span id="Connect"></span></a> Connexion à une instance de base de données SQL

### Installation de node-sqlserver

Le pilote Microsoft SQL Server pour Node.JS est disponible en tant que module natif node-sqlserver. Une version binaire de ce module est disponible dans le [centre de téléchargement][centre de téléchargement]. Pour utiliser la version binaire, procédez comme suit :

1.  Décompressez l'archive binaire dans le répertoire **node\_modules** de votre application.
2.  Exécutez le fichier **node-sqlserver-install.cmd** décompressé. Cette opération permet de créer un sous-répertoire **node-sqlserver** sous **node\_modules** et de déplacer les fichiers du pilote dans la nouvelle structure de répertoires.
3.  Supprimez le fichier **node-sqlserver-install.cmd**, dès lors que vous n'en avez plus besoin.

<div class="dev-callout">
<b>Remarque</b>
<p>Vous pouvez &eacute;galement installer le module node-sqlserver &agrave; l'aide de l'utilitaire npm&nbsp;; toutefois, cela informera node-gyp de g&eacute;n&eacute;rer une version binaire du module sur votre syst&egrave;me.</p>
</div>

### Spécification de la chaîne de connexion

Pour utiliser node-sqlserver, vous devez le demander dans votre application et spécifier une chaîne de connexion. Cette dernière doit correspondre à la valeur ODBC renvoyée dans la section [Obtention des informations de connexion à la base de données SQL][Obtention des informations de connexion à la base de données SQL] de cet article. Le code qui apparaît doit être semblable au code suivant :

    var sql = require('node-sqlserver');
    var conn_str = "Driver={SQL Server Native Client 10.0};Server=tcp:{dbservername}.database.windows.net,1433;Database={database};Uid={username};Pwd={password};Encrypt=yes;Connection Timeout=30;";

### Interrogation de la base de données

Des requêtes peuvent être effectuées en spécifiant l'instruction Transact-SQL avec la méthode **query**. Le code suivant crée un serveur HTTP et renvoie les données des lignes **ID**, **Column1** et **Column2** de la table **Test** lorsque vous affichez la page Web :

    var http = require('http')
    var port = process.env.port||3000;
    http.createServer(function (req, res) {
        sql.query(conn_str, "SELECT * FROM TestTable", function (err, results) {
            if (err) {
                res.writeHead(500, { 'Content-Type': 'text/plain' });
                res.write("Got error :-( " + err);
                res.end("");
                return;
            }
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            for (var i = 0; i < results.length; i++) {
                res.write("ID: " + results[i].ID + " Column1: " + results[i].Column1 + " Column2: " + results[i].Column2);
            }
            res.end("; Done.");
        });
    }).listen(port);

Alors que l'exemple ci-dessus illustre comment renvoyer toutes les lignes en une seule fois dans la collection de résultats, vous pouvez également renvoyer un objet d'instruction qui vous permet de vous abonner aux événements. Cela vous permet de recevoir des lignes et colonnes spécifiques lorsque ces dernières sont renvoyées. L'exemple suivant en est la démonstration :

    var stmt = sql.query(conn_str, "SELECT * FROM TestTable");
    stmt.on('meta', function (meta) { console.log("We've received the metadata"); });
    stmt.on('row', function (idx) { console.log("We've started receiving a row"); });
    stmt.on('column', function (idx, data, more) { console.log(idx + ":" + data);});
    stmt.on('done', function () { console.log("All done!"); });
    stmt.on('error', function (err) { console.log("We had an error: " + err); });

## <span id="Deploy"></span></a>Considérations relatives au déploiement dans Azure

<div class="dev-callout">
<b>Remarque</b>
<p>Les informations suivantes sont bas&eacute;es sur une version pr&eacute;liminaire du pilote Microsoft SQL Server pour Node.JS. Les informations de cette section ne sont peut-&ecirc;tre pas les plus actualis&eacute;es concernant l'utilisation du module node-sqlserver avec Azure. Pour acc&eacute;der &agrave; des informations plus &agrave; jour, consultez la <a href="https://github.com/WindowsAzure/node-sqlserver">page du projet de pilote Microsoft SQL Server pour Node.JS</a> sur Github.</p>
</div>

Azure ne va pas installer de manière dynamique le module node-sqlserver au moment de l'exécution. Vous devez donc vous assurer que le déploiement de votre application inclut une version binaire du module. Pour cela, assurez-vous que la structure de répertoires suivante existe et contient les fichiers décrits ci-après :

    application directory
        node_modules
            node-sqlserver
                lib

Le répertoire **node-sqlserver** doit contenir un fichier **package.json**. Le répertoire **lib** doit contenir un fichier **sql.js** et un fichier **sqlserver.node**, qui constituent la version compilée du module node-sqlserver.

Pour plus d'informations sur le déploiement d'une application Node.js dans Azure, consultez les rubriques [Création et déploiement d'une application Node.js dans un site Web Azure][Création et déploiement d'une application Node.js dans un site Web Azure] et [Service cloud Node.js][Service cloud Node.js].

## <span id="NextSteps"></span></a>Étapes suivantes

-   [Présentation du pilote Microsoft SQL Server pour Node.JS][Présentation du pilote Microsoft SQL Server pour Node.JS]
-   [Pilote Microsoft SQL Server pour Node.js sur Github.com][page Github]

  [portail de gestion Azure]: https://manage.windowsazure.com
  [Concepts]: #Concepts
  [Configuration de votre environnement]: #Setup
  [Création d'une base de données SQL]: #CreateServer
  [Obtention des informations de connexion à la base de données SQL]: #ConnectionInfo
  [Connexion à une instance de base de données SQL]: #Connect
  [Considérations relatives au déploiement dans Azure]: #Deploy
  [Étapes suivantes]: #NextSteps
  [page Github]: https://github.com/WindowsAzure/node-sqlserver
  [Wiki]: https://github.com/WindowsAzure/node-sqlserver/wiki
  [Microsoft SQL Server 2012 Feature Pack]: http://www.microsoft.com/fr-fr/download/details.aspx?id=29065
  []: http://nodejs.org/#download
  [Créer un site Web Azure]: ./media/sql-database-nodejs-how-to-use/new_website.jpg
  [Créer une base de données SQL personnalisée]: ./media/sql-database-nodejs-how-to-use/create_custom_sql_db.jpg
  [Renseigner les paramètres de la base de données SQL]: ./media/sql-database-nodejs-how-to-use/new-sql-db.png
  [Créer un serveur de base de données SQL]: ./media/sql-database-nodejs-how-to-use/db-server-settings.png
  [Afficher les informations relatives au serveur et à la base de données]: ./media/sql-database-nodejs-how-to-use/sql-dbs-portal.png
  [Afficher les informations relatives à la base de données]: ./media/sql-database-nodejs-how-to-use/go-to-db-info.png
  [Afficher les chaînes de connexion]: ./media/sql-database-nodejs-how-to-use/show-connection-string.png
  [centre de téléchargement]: http://www.microsoft.com/fr-fr/download/details.aspx?id=29995
  [Création et déploiement d'une application Node.js dans un site Web Azure]: /fr-fr/develop/nodejs/tutorials/create-a-website-(mac)/
  [Service cloud Node.js]: /fr-fr/develop/nodejs/tutorials/getting-started/
  [Présentation du pilote Microsoft SQL Server pour Node.JS]: http://blogs.msdn.com/b/sqlphp/archive/2012/06/08/introducing-the-microsoft-driver-for-node-js-for-sql-server.aspx
