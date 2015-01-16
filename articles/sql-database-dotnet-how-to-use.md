<properties urlDisplayName="SQL Database" pageTitle="Utilisation de la base de données SQL (.NET) - Guide des fonctionnalités Azure" metaKeywords="Get started SQL Azure, Getting started SQL Azure, SQL Azure database connection, SQL Azure ADO.NET, SQL Azure ODBC, SQL Azure EntityClient" description="Prise en main d'une base de données SQL. Découvrez comment créer une instance de base de données SQL et vous-y connecter à l'aide d'ADO.NET, d'ODBC et du fournisseur EntityClient." metaCanonical="" services="sql-database" documentationCenter=".NET" title="How to use Azure SQL Database in .NET applications" authors="jeffreyg" solutions="" manager="jeffreyg" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/31/2015" ms.author="jeffreyg" />







# Utilisation d'une base de données SQL Azure dans des applications .NET

Ce guide montre comment créer un serveur logique et une instance de base de données sur la base de données SQL Azure et vous connecter à la base de données à l'aide des technologies du fournisseur de données .NET Framework : ADO.NET, ODBC et le fournisseur EntityClient.


<h2><a name="Whatis"></a>Définition de la base de données SQL</h2>

La base de données SQL fournit un système de gestion des bases de données relationnelles pour Azure. Elle est fondée sur la technologie SQL Server. Une instance de base de données SQL permet d'approvisionner et de déployer facilement des solutions de base de données relationnelle dans le cloud. Elle tire parti d'un centre de données distribué qui fournit haute disponibilité, extensibilité et sécurité aux entreprises, avec les avantages de la protection des données et de l'auto-adaptation intégrées.

## Sommaire

- [Se connecter à Azure](#PreReq1)
- [Création et configuration d'une base de données SQL](#PreReq2)
- [Connexion à la base de données SQL](#connect-db) 
- [Connexion à l'aide d'ADO.NET](#using-sql-server)
- [Connexion à l'aide d'ODBC](#using-ODBC)
- [Connexion à l'aide du fournisseur EntityClient](#using-entity)
- [Étapes suivantes](#next-steps)

<h2><a name="PreReq1"></a>Se connecter à Azure</h2>

La base de données SQL fournit des services de stockage de données relationnelles, d'accès aux données et de gestion sur Azure. Pour utiliser ces services, vous avez besoin d'un abonnement Azure.

1. Ouvrez un navigateur web et accédez à la page [http://azure.microsoft.com/fr-fr/](http://www.windowsazure.com). Pour commencer avec un compte gratuit, cliquez sur Version d'évaluation gratuite dans le coin supérieur droit, puis suivez la procédure.

2. Votre compte est maintenant créé. Vous êtes prêt à commencer.


<h2><a name="PreReq2"></a>Création et configuration d'une base de données SQL</h2>

Ensuite, créez et configurez un serveur et une base de données. Le portail de gestion Azure propose une refonte des workflows qui vous permettent de commencer par la création d'une base de données avant de déployer un serveur. 

<h3 name="createsrvr">Création d'une instance de base de données et d'un serveur logique</h3>

1. Connectez-vous au [Portail de gestion Azure][].

2. Cliquez sur **+NOUVEAU** en bas de la page.

3. Cliquez sur **Services de données**.

4. Cliquez sur **Base de données SQL**.

5. Cliquez sur **Création personnalisée**. 

6. Dans Nom, entrez un nom de base de données.

7. Choisissez une édition, une taille maximale et un classement. Dans le cadre de ce guide, vous pouvez utiliser les valeurs par défaut. 

	Trois éditions de la base de données SQL sont disponibles : De base, Standard et Premium.

	Le paramètre MAXSIZE est spécifié lors de la création de la base de données et peut être modifié ultérieurement à l'aide de l'instruction ALTER DATABASE. MAXSIZE offre la possibilité de limiter la taille de la base de données.

	Pour chaque base de données SQL créée sur Azure, il existe réellement trois réplicas de cette base de données. Ces derniers garantissent une haute disponibilité. Le basculement est transparent et fait partie du service. Le [contrat de niveau de service (SLA)][] garantit 99,9 % de disponibilité pour la base de données SQL.

8. Dans Serveur, sélectionnez **Nouveau serveur de base de données SQL**. 

9. Cliquez sur la flèche pour passer à la page suivante.

10. Dans Paramètres du serveur, entrez un nom de connexion d'authentification SQL Server.

	La base de données SQL utilise l'authentification SQL sur une connexion chiffrée. Une nouvelle connexion d'authentification SQL Server attribuée au rôle serveur administrateur système fixe est créée avec le nom indiqué. 

	L'identifiant de connexion ne peut pas correspondre à une adresse électronique, à un compte d'utilisateur Windows, ni à un Windows Live ID. Les revendications et l'authentification Windows ne sont pas prises en charge sur la base de données SQL.

11. Fournissez un mot de passe fort de plus de huit caractères, combinant des majuscules et des minuscules, et comportant un chiffre ou un symbole.

12. Choisissez une région. La région détermine l'emplacement géographique du serveur. Choisissez une région qui est pertinente pour ce serveur, car il n'est pas possible de basculer facilement d'une région à l'autre. Choisissez un emplacement le plus proche possible de vous. Vous économisez sur les coûts de la bande passante de sortie et réduisez la latence des données en maintenant l'application et la base de données Azure dans la même région.

13. Veillez à ce que l'option **Autoriser les services Microsoft Azure à accéder au serveur** reste activée pour pouvoir vous connecter à cette base de données avec le portail de gestion pour la base de données SQL, les services de stockage et d'autres services sur Azure. 

14. Une fois que vous avez terminé, cliquez sur la coche en bas de la page.

Comme vous pouvez le remarquer, vous n'avez spécifié aucun nom de serveur. La base de données SQL génère automatiquement le nom du serveur pour garantir l'absence d'entrées DNS en double. Le nom du serveur se présente sous la forme d'une chaîne alphanumérique de dix caractères. Vous ne pouvez pas changer le nom de votre serveur de base de données SQL.

Une fois la base de données créée, cliquez dessus pour ouvrir son tableau de bord. Ce dernier fournit des chaînes de connexion que vous pouvez copier et utiliser dans le code de l'application. Il affiche également l'URL de gestion que vous devez spécifier si vous vous connectez à la base de données à partir de Management Studio ou d'un autre outil d'administration.


![image](./media/sql-database-dotnet-how-to-use/SQLDbDashboard.PNG)


À l'étape suivante, vous allez configurer le pare-feu de telle sorte que les connexions des applications exécutées sur votre réseau soient autorisées à y accéder.

<h3 name="configFWLogical">Configuration du pare-feu pour le serveur logique</h3>

1. Cliquez sur **Bases de données SQL**, sur **Serveurs** en haut de la page, puis sur le serveur que vous venez de créer.

	![Image2](./media/sql-database-dotnet-how-to-use/SQLDBFirewall.PNG)

2. Cliquez sur **Configurer**. 

3. Copiez l'adresse IP actuelle du client. Si vous vous connectez depuis un réseau, il s'agit de l'adresse IP sur laquelle votre routeur ou serveur proxy est à l'écoute. La base de données SQL détecte l'adresse IP utilisée par la connexion actuelle afin que vous puissiez créer une règle de pare-feu pour accepter les demandes de connexion provenant de cet appareil. 

4. Collez l'adresse IP dans les deux adresses (ADRESSE IP DE DÉBUT et ADRESSE IP DE FIN, pour déterminer la plage d'adresses autorisée pour accéder au serveur. Plus tard, si vous êtes confronté à des erreurs de connexion indiquant que la plage est trop étroite, vous pourrez modifier cette règle pour élargir la plage.

	Si les ordinateurs clients utilisent des adresses IP attribuées dynamiquement, vous devez indiquer une plage suffisamment large afin d'inclure les adresses IP attribuées aux ordinateurs de votre réseau. Optez pour une plage restreinte au départ, puis étendez-la selon vos besoins.

5. Entrez un nom pour la règle de pare-feu, par exemple, le nom de l'ordinateur ou de l'entreprise.

6. Cliquez sur la coche en regard de la règle pour l'enregistrer.

	![Image3](./media/sql-database-dotnet-how-to-use/SQLDBIPRange.PNG)

7. Cliquez sur **Enregistrer** en bas de la page pour achever cette étape. Si **Enregistrer** n'est pas visible, actualisez la page du navigateur.

Vous disposez à présent d'une instance de base de données, d'un serveur logique, d'une règle de pare-feu qui autorise les connexions entrantes provenant de votre adresse IP et d'une connexion administrateur. Vous êtes désormais prêt à vous connecter à la base de données par programme.


<h2><a name="Connect-DB"></a>Connexion à la base de données SQL</h2>

Cette section montre comment se connecter à une instance de base de données SQL à l'aide de différents
fournisseurs de données .NET Framework.

Si vous optez pour Visual Studio et que votre configuration ne comprend pas une application web Azure en tant qu'application frontale, aucun outil supplémentaire ni Kit de développement logiciel (SDK) n'a besoin d'être installé sur l'ordinateur de développement. Vous êtes prêt à développer votre application.

Tous les outils de conception dans Visual Studio peuvent vous servir de la même façon avec la base de données SQL ou avec SQL Server. L'Explorateur de serveurs vous permet d'afficher (mais pas d'éditer) les objets de base de données. Visual Studio Entity Data Model Designer est complètement fonctionnel. Vous pouvez l'utiliser pour créer des modèles de base de données SQL à utiliser avec Entity Framework.

### <a name="using-sql-server"></a>Utilisation du fournisseur de données .NET Framework pour SQL Server

L'espace de noms **System.Data.SqlClient** est le fournisseur de données .NET Framework
pour SQL Server.

La chaîne de connexion standard ressemble à ceci :

    Server=tcp:.database.windows.net;
    Database=;
    User ID=@;
    Password=;
    Trusted_Connection=False;
    Encrypt=True;

La classe **SQLConnectionStringBuilder** permet de générer une chaîne de connexion comme illustré dans l'exemple de code suivant :

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder();
    csBuilder.DataSource = xxxxxxxxxx.database.windows.net;
    csBuilder.InitialCatalog = testDB;
    csBuilder.Encrypt = true;
    csBuilder.TrustServerCertificate = false;
    csBuilder.UserID = MyAdmin;
    csBuilder.Password = pass@word1;

Si les éléments d'une chaîne de connexion sont connus à l'avance, ils peuvent être stockés dans un fichier de configuration et extraits au moment de l'exécution afin de construire une chaîne de connexion. Voici un exemple de chaîne de connexion dans un fichier de configuration :

    <connectionStrings>
      <add name="ConnectionString" 
           connectionString ="Server=tcp:xxxxxxxxxx.database.windows.net;Database=testDB;User ID=MyAdmin@xxxxxxxxxx;Password=pass@word1;Trusted_Connection=False;Encrypt=True;" />
    </connectionStrings>

To retrieve the connection string in a configuration file, you use the
**ConfigurationManager** class:

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder(ConfigurationManager.ConnectionStrings["ConnectionString"].ConnectionString);
    After you have built your connection string, you can use the SQLConnection class to connect the SQL Database server:
    SqlConnection conn = new SqlConnection(csBuilder.ToString());
    conn.Open();

### <a name="using-ODBC"></a>Utilisation du fournisseur de données .NET Framework pour ODBC

L'espace de noms **System.Data.Odbc** représente le fournisseur de données .NET Framework pour ODBC. Voici un exemple de chaîne de connexion ODBC :

    Driver={SQL Server Native Client 10.0};
    Server=tcp:.database.windows.net;
    Database=;
    Uid=@;
    Pwd=;
    Encrypt=yes;

La classe **OdbcConnection** représente une connexion ouverte vers une source de données. Voici un exemple de code sur l'ouverture d'une connexion :

    string cs = "Driver={SQL Server Native Client 10.0};" +
                "Server=tcp:xxxxxxxxxx.database.windows.net;" +
                "Database=testDB;"+
                "Uid=MyAdmin@xxxxxxxxxx;" +
                "Pwd=pass@word1;"+
                "Encrypt=yes;";

    OdbcConnection conn = new OdbcConnection(cs);
    conn.Open();

Si vous voulez générer la chaîne de connexion lors de l'exécution, vous pouvez utiliser la classe **OdbcConnectionStringBuilder**.

### <a name="using-entity"></a>À l'aide du fournisseur EntityClient

L'espace de noms **System.Data.EntityClient** représente le fournisseur de données .NET Framework Data Provider pour Entity Framework.

Entity Framework permet aux développeurs de créer des applications d'accès aux données en programmant selon un modèle d'application conceptuel plutôt que selon un schéma de stockage relationnel. Entity Framework crée au-dessus des fournisseurs de données ADO.NET dédiés au stockage en mettant l'objet **EntityConnection** à disposition d'un fournisseur de données sous-jacent et d'une base de données relationnelle.

Pour construire un objet **EntityConnection**, vous devez référencer un ensemble de métadonnées qui contient le mappage et les modèles nécessaires, ainsi qu'un nom de fournisseur de données dédié au stockage et une chaîne de connexion. Une fois **EntityConnection** en place, il est possible d'accéder aux entités via les classes générées à partir du modèle conceptuel.

Voici par exemple la chaîne de connexion suivante :

    metadata=res://*/SchoolModel.csdl|res://*/SchoolModel.ssdl|res://*/SchoolModel.msl;provider=System.Data.SqlClient;provider connection string="Data Source=xxxxxxxxxx.database.windows.net;Initial Catalog=School;Persist Security Info=True;User ID=MyAdmin;Password=***********"

Pour plus d'informations, consultez la page [Fournisseur EntityClient pour Entity
Framework][].

## <a name="next-steps"></a>Étapes suivantes

Vous avez découvert les notions de base de la connexion à la base de données SQL. Consultez à présent les ressources suivantes pour obtenir plus d'informations sur la base de données SQL.

-   [Développement : rubriques de procédures (Base de données SQL)][]
-   [Base de données SQL][]


  [Définition de la base de données SQL]: #WhatIs
  [Se connecter à Azure]: #PreReq1
  [Création et configuration d'une base de données SQL]: #PreReq2
  [Connexion à la base de données SQL]: #connect-db
  [Connexion à l'aide d'ADO.NET]: #using-sql-server
  [Connexion à l'aide d'ODBC]: #using-ODBC
  [Connexion à l'aide du fournisseur EntityClient]: #using-entity
  [Étapes suivantes]: #next-steps
  [Version d'évaluation gratuite d'Azure] : {localLink:2187} " Version d'évaluation gratuite "
  [Portail de gestion Azure]: http://manage.windowsazure.com
  [Création d'un serveur de base de données SQL]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-create-a-sql-azure-server.aspx
  [Portail de gestion pour la base de données SQL]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg442309.aspx
  [Pare-feu de base de données SQL]: http://social.technet.microsoft.com/wiki/contents/articles/sql-azure-firewall.aspx
  [Prise en charge des outils et utilitaires de la Base de données SQL Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee621784.aspx
  [Création d'une base de données SQL sur Azure]: http://social.technet.microsoft.com/wiki/contents/articles/how-to-create-a-sql-azure-database.aspx
  [Contrat de niveau de service] : {localLink:1132} " SLA "
  [Fournisseur EntityClient pour Entity Framework]: http://msdn.microsoft.com/fr-fr/library/bb738561.aspx
  [Développement : rubriques de procédures (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee621787.aspx
  [Base de données SQL]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336279.aspx
