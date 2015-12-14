<properties 
	pageTitle="Utilisation de la base de données SQL (.NET) | Microsoft Azure" 
	description="Prise en main d’une base de données SQL. Découvrez comment créer une instance de base de données SQL et vous y connecter à l’aide d’ADO.NET, d’ODBC et du fournisseur EntityClient." 
	services="sql-database" 
	documentationCenter=".net" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="jeffreyg"/>


# Utilisation d’une base de données SQL Azure dans des applications .NET

Ce guide vous explique comment créer une instance de base de données et de serveur logique sur une base de données SQL Azure et se connecter à la base de données à l’aide des technologies de fournisseurs de données .NET Framework suivantes : ADO.NET, ODBC et EntityClient Provider.


## Présentation de la base de données SQL

La base de données SQL fournit un système de gestion des bases de données relationnelles pour Azure. Elle est fondée sur la technologie SQL Server. Une instance de base de données SQL permet d’approvisionner et de déployer facilement des solutions de base de données relationnelle dans le cloud. Elle tire parti d’un centre de données distribué qui fournit haute disponibilité, extensibilité et sécurité aux entreprises, avec les avantages de la protection des données et de l’auto-adaptation intégrées.



## Connexion à Azure

La base de données SQL fournit des services de stockage de données relationnelles, d’accès aux données et de gestion sur Azure. Pour utiliser ces services, vous avez besoin d’un abonnement Azure.

1. Ouvrez un navigateur Web et accédez à la page [http://azure.microsoft.com/](http://azure.microsoft.com). Pour commencer avec un compte gratuit, cliquez sur Version d’évaluation gratuite dans le coin supérieur droit, puis suivez la procédure.

2. Votre compte est maintenant créé. Vous êtes prêt à commencer.


## Création et configuration d’une base de données SQL

Ensuite, créez et configurez un serveur et une base de données. Le portail Azure Classic propose une refonte des workflows qui vous permettent de commencer par la création d’une base de données avant de déployer un serveur.

**Créer une instance de base de données et un serveur logique :**

1. Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com).

2. Cliquez sur **NOUVEAU** en bas de la page.

3. Cliquez sur **Services de données**.

4. Cliquez sur **Base de données SQL**.

5. Cliquez sur **Création personnalisée**.

6. Dans Nom, entrez un nom de base de données.

7. Choisissez une édition, une taille maximale et un classement. Dans le cadre de ce guide, vous pouvez utiliser les valeurs par défaut.

	La base de données SQL propose trois éditions de base de données : De base, Standard et Premium.

	Le paramètre MAXSIZE est spécifié lors de la création de la base de données et peut être modifié ultérieurement à l’aide de l’instruction ALTER DATABASE. MAXSIZE offre la possibilité de limiter la taille de la base de données.

	Pour chaque base de données SQL créée sur Azure, il existe réellement trois réplicas de cette base de données. Ces derniers garantissent une haute disponibilité. Le basculement est transparent et fait partie du service.

8. Dans Serveur, sélectionnez **Nouveau serveur de base de données SQL**.

9. Cliquez sur la flèche pour passer à la page suivante.

10. Dans Paramètres du serveur, entrez un nom de connexion d'authentification SQL Server.

	La base de données SQL utilise l'authentification SQL sur une connexion chiffrée. Une nouvelle connexion d'authentification SQL Server attribuée au rôle serveur administrateur système fixe est créée avec le nom indiqué.

	L'ID de connexion ne peut pas être une adresse de messagerie, un compte d'utilisateur Windows ou un Windows Live ID. L'authentification Windows et l'authentification basée sur les revendications ne sont pas prises en charge sur la base de données SQL.

11. Fournissez un mot de passe fort de plus de huit caractères, combinant des majuscules et des minuscules, et comportant un chiffre ou un symbole.

12. Choisissez une région. La région détermine l'emplacement géographique du serveur. Choisissez une région qui est pertinente pour ce serveur, car il n'est pas possible de basculer facilement d'une région à l'autre. Choisissez un emplacement le plus proche possible de vous. Vous économisez sur les coûts de la bande passante de sortie et réduisez la latence des données en maintenant l'application et la base de données Azure dans la même région.

13. Veillez à ce que l’option **Allow Azure Services to access the server** reste activée pour pouvoir vous connecter à cette base de données avec le portail Classic pour la base de données SQL, les services de stockage et d’autres services sur Azure.

14. Une fois que vous avez terminé, cliquez sur la coche en bas de la page.

Comme vous pouvez le remarquer, vous n'avez spécifié aucun nom de serveur. La base de données SQL génère automatiquement le nom du serveur pour garantir l'absence d'entrées DNS en double. Le nom du serveur se présente sous la forme d'une chaîne alphanumérique de dix caractères. Vous ne pouvez pas changer le nom de votre serveur de base de données SQL.

Une fois la base de données créée, cliquez dessus pour ouvrir son tableau de bord. Ce dernier fournit des chaînes de connexion que vous pouvez copier et utiliser dans le code de l'application. Il affiche également l’URL de gestion que vous devez spécifier si vous vous connectez à la base de données à partir de Management Studio ou d’un autre outil d’administration.


![Tableau de bord de base de données SQL](./media/sql-database-dotnet-how-to-use/SQLDbDashboard.PNG)


À l'étape suivante, vous allez configurer le pare-feu de telle sorte que les connexions des applications exécutées sur votre réseau soient autorisées à y accéder.

**Configuration du pare-feu pour le serveur logique**

1. Cliquez sur **Bases de données SQL**, sur **Serveurs** en haut de la page, puis sur le serveur que vous venez de créer.

	![Configuration d’un pare-feu](./media/sql-database-dotnet-how-to-use/SQLDBFirewall.PNG)

2. Cliquez sur **Configurer**.

3. Copiez l'adresse IP actuelle du client. Si vous vous connectez depuis un réseau, il s'agit de l'adresse IP sur laquelle votre routeur ou serveur proxy est à l'écoute. La base de données SQL détecte l'adresse IP utilisée par la connexion actuelle afin que vous puissiez créer une règle de pare-feu pour accepter les demandes de connexion provenant de cet appareil.

4. Collez l'adresse IP dans les deux adresses, START IP ADDRESS et END IP ADDRESS, pour déterminer les adresses de la plage autorisée pour accéder au serveur. Plus tard, si vous êtes confronté à des erreurs de connexion indiquant que la plage est trop étroite, vous pourrez modifier cette règle pour élargir la plage.

	Si les ordinateurs clients utilisent des adresses IP attribuées dynamiquement, vous devez indiquer une plage suffisamment large afin d'inclure les adresses IP attribuées aux ordinateurs de votre réseau. Optez pour une plage restreinte au départ, puis étendez-la selon vos besoins.

5. Entrez un nom pour la règle de pare-feu, par exemple, le nom de l'ordinateur ou de l'entreprise.

6. Cliquez sur la coche en regard de la règle pour l’enregistrer.

	![Plage d’adresses IP des paramètres de pare-feu](./media/sql-database-dotnet-how-to-use/SQLDBIPRange.PNG)

7. Cliquez sur **Enregistrer** en bas de la page pour achever cette étape. Si **Enregistrer** n'est pas visible, actualisez la page du navigateur.

Vous disposez à présent d’une instance de base de données, d’un serveur logique, d’une règle de pare-feu qui autorise les connexions entrantes provenant de votre adresse IP et d’une connexion administrateur. Vous êtes désormais prêt à vous connecter à la base de données par programme.


## Connexion à la base de données SQL

Cette section montre comment se connecter à une instance de base de données SQL à l’aide de différents fournisseurs de données .NET Framework. Pour obtenir des recommandations sur la connexion à un serveur de base de données SQL et à une base de données, consultez :


- [Connexions à la base de données SQL : recommandations centrales](../sql-database-connect-central-recommendations.md).


Si vous optez pour Visual Studio et que votre configuration ne comprend pas une application web Azure en tant qu’application principale, aucun outil supplémentaire ni Kit de développement logiciel (SDK) n’a besoin d’être installé sur l’ordinateur de développement. Vous êtes prêt à développer votre application.

Tous les outils de conception dans Visual Studio peuvent vous servir de la même façon avec la base de données SQL ou avec SQL Server. L'Explorateur de serveurs vous permet d'afficher (mais pas d'éditer) les objets de base de données. Visual Studio Entity Data Model Designer est complètement fonctionnel. Vous pouvez l’utiliser pour créer des modèles de base de données SQL à utiliser avec Entity Framework.

## Utilisation du fournisseur de données .NET Framework pour SQL Server

L’espace de noms **System.Data.SqlClient** représente le fournisseur de données .NET Framework pour SQL Server.

La chaîne de connexion standard ressemble à ceci :

    Server=tcp:.database.windows.net;
    Database=;
    User ID=@;
    Password=;
    Trusted_Connection=False;
    Encrypt=True;

La classe **SQLConnectionStringBuilder** permet de générer une chaîne de connexion comme illustré dans l’exemple de code suivant :

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder();
    csBuilder.DataSource = xxxxxxxxxx.database.windows.net;
    csBuilder.InitialCatalog = testDB;
    csBuilder.Encrypt = true;
    csBuilder.TrustServerCertificate = false;
    csBuilder.UserID = MyAdmin;
    csBuilder.Password = pass@word1;

Si les éléments d’une chaîne de connexion sont connus à l’avance, ils peuvent être stockés dans un fichier de configuration et extraits lors de l’exécution afin de construire une chaîne de connexion. Voici un exemple de chaîne de connexion dans un fichier de configuration :

    <connectionStrings>
      <add name="ConnectionString" 
           connectionString ="Server=tcp:xxxxxxxxxx.database.windows.net;Database=testDB;User ID=MyAdmin@xxxxxxxxxx;Password=pass@word1;Trusted_Connection=False;Encrypt=True;" />
    </connectionStrings>

Pour extraire la chaîne de connexion dans un fichier de configuration, vous utilisez la classe **ConfigurationManager** :

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder(ConfigurationManager.ConnectionStrings["ConnectionString"].ConnectionString);
    After you have built your connection string, you can use the SQLConnection class to connect the SQL Database server:
    SqlConnection conn = new SqlConnection(csBuilder.ToString());
    conn.Open();

## Utilisation du fournisseur de données pour ODBC

L’espace de noms **System.Data.Odbc** représente le fournisseur de données .NET Framework pour ODBC. Voici un exemple de chaîne de connexion ODBC :

    Driver={SQL Server Native Client 10.0};
    Server=tcp:.database.windows.net;
    Database=;
    Uid=@;
    Pwd=;
    Encrypt=yes;

La classe **OdbcConnection** représente une connexion ouverte vers une source de données. Voici un exemple de code pour l’ouverture d’une connexion :

    string cs = "Driver={SQL Server Native Client 10.0};" +
                "Server=tcp:xxxxxxxxxx.database.windows.net;" +
                "Database=testDB;"+
                "Uid=MyAdmin@xxxxxxxxxx;" +
                "Pwd=pass@word1;"+
                "Encrypt=yes;";

    OdbcConnection conn = new OdbcConnection(cs);
    conn.Open();

Si vous voulez générer la chaîne de connexion lors de l’exécution, vous pouvez utiliser la classe **OdbcConnectionStringBuilder**.

## Utilisation du fournisseur EntityClient

L’espace de noms **System.Data.EntityClient** représente le fournisseur de données .NET Framework pour Entity Framework.

Entity Framework permet aux développeurs de créer des applications d'accès aux données en programmant selon un modèle d'application conceptuel plutôt que selon un schéma de stockage relationnel. Entity Framework crée au-dessus des fournisseurs de données ADO.NET dédiés au stockage en mettant l'objet **EntityConnection** à disposition d'un fournisseur de données sous-jacent et d'une base de données relationnelle.

Pour construire un objet **EntityConnection**, vous devez référencer un ensemble de métadonnées qui contient le mappage et les modèles nécessaires, ainsi qu’un nom de fournisseur de données dédié au stockage et une chaîne de connexion. Une fois **EntityConnection** en place, il est possible d'accéder aux entités via les classes générées à partir du modèle conceptuel.

Voici par exemple la chaîne de connexion suivante :

    metadata=res://*/SchoolModel.csdl|res://*/SchoolModel.ssdl|res://*/SchoolModel.msl;provider=System.Data.SqlClient;provider connection string="Data Source=xxxxxxxxxx.database.windows.net;Initial Catalog=School;Persist Security Info=True;User ID=MyAdmin;Password=***********"

Pour plus d’informations, consultez la page [Fournisseur EntityClient pour Entity Framework](http://msdn.microsoft.com/library/bb738561.aspx).

## Étapes suivantes

Maintenant que vous avez appris les principes de base de la connexion à une base de données SQL, consultez [Développement de base de données SQL Azure : Rubriques de procédures](http://msdn.microsoft.com/library/windowsazure/ee621787.aspx).
 

<!---HONumber=AcomDC_1203_2015-->