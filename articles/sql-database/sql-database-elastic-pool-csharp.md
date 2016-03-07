<properties
    pageTitle="Développement de base de données C# : pools de bases de données élastiques | Microsoft Azure"
    description="Utilisez les techniques de développement de bases de données C# pour créer un pool de base de données élastique SQL Azure afin de pouvoir partager des ressources entre plusieurs bases de données."
    services="sql-database"
    keywords="base de données c#,développement sql"
    documentationCenter=""
    authors="stevestein"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="02/23/2016"
    ms.author="sstein"/>

# C&#x23; développement de base de données : créer et configurer un pool de base de données élastique pour une base de données SQL

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


Cet article vous montre comment créer un [pool de base de données élastique](sql-database-elastic-pool.md) pour des bases de données SQL à partir d’une application en utilisant des techniques de développement de base de données C#.

> [AZURE.NOTE] Les pools élastiques de bases de données sont actuellement en version préliminaire et uniquement disponibles avec des serveurs de base de données SQL V12. Si vous disposez d’un serveur de base de données SQL V11, vous pouvez [utiliser PowerShell pour effectuer une mise à niveau vers V12 et créer un pool](sql-database-upgrade-server-powershell.md) en une seule étape.

Les exemples utilisent la [bibliothèque de base de données SQL Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Les différents extraits de code sont fractionnés par souci de clarté, et un exemple d’application console réunit toutes les commandes dans la dernière section de cet article.


> [AZURE.NOTE] La bibliothèque SQL Database pour .NET est actuellement en version préliminaire.



Si vous n’avez pas d’abonnement Azure, cliquez simplement sur **VERSION D’ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article. Pour une copie gratuite de Visual Studio, consultez la page [Téléchargements Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).

## Installation des bibliothèques nécessaires

Obtenez les bibliothèques de gestion requises en installant les packages suivants à l’aide de la [console du gestionnaire de package](http://docs.nuget.org/Consume/Package-Manager-Console) pour le développement sur SQL :

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurer l’authentification avec Azure Active Directory

Avant de commencer le développement SQL en C#, vous devez effectuer certaines tâches dans le portail Azure. Commencez par autoriser votre application à accéder à l’API REST en configurant l’authentification nécessaire.

Les [API REST Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn948464.aspx) utilisent Azure Active Directory pour l’authentification, plutôt que les certificats utilisés par les API REST de gestion des services Azure antérieures.

Pour authentifier votre application cliente en fonction de l’utilisateur actuel, vous devez d’abord inscrire celle-ci dans le domaine AAD associé à l’abonnement sous lequel les ressources Azure ont été créées. Si votre abonnement Azure a été créé avec un compte Microsoft, plutôt qu’avec un compte professionnel ou scolaire, vous disposez déjà d’un domaine AAD par défaut. L’inscription de l’application peut être effectuée dans le [Portail Classic](https://manage.windowsazure.com/).

Pour créer une application et l’inscrire dans le répertoire actif correct, procédez comme suit :

1. Faites défiler le menu situé à gauche pour localiser le service **Active Directory**, puis ouvrez ce dernier.

    ![Développement de base de données SQL C# : configuration d’Active Directory][1]

2. Sélectionnez le répertoire pour authentifier votre application et cliquez sur son **Nom**.

    ![Sélectionnez un répertoire.][4]

3. Dans la page du répertoire, cliquez sur **APPLICATIONS**.

    ![Cliquez sur Applications.][5]

4. Cliquez sur **AJOUTER** pour créer une application.

    ![Cliquez sur le bouton Ajouter : créer une application C#.][6]

5. Sélectionnez **Ajouter une application développée par mon organisation**.

5. Spécifiez un **NOM** pour l’application et sélectionnez **APPLICATION CLIENTE NATIVE**.

    ![Ajouter l’application][7]

6. Fournissez un **URI DE REDIRECTION**. Il n’est pas nécessaire que celui-ci soit un point de terminaison réel ; un URI valide suffit.

    ![Ajouter l’application][8]

7. Terminez la création de l’application, cliquez sur **CONFIGURER**, puis copiez l’**ID CLIENT** (vous en aurez besoin dans votre code).

    ![Obtenir l’ID client][9]


1. En bas de la page, cliquez sur **Ajouter une application**.
1. Sélectionnez **Applications Microsoft**.
1. Sélectionnez **API de gestion des services Azure**, puis terminez l’Assistant.
2. Vérifiez que cette API est sélectionnée puis, pour accorder les autorisations permettant d’y accéder, sélectionnez **Accéder à la gestion des services Azure (version préliminaire)**.

    ![Définir des autorisations][2]

2. Cliquez sur **ENREGISTRER**.



### Identifier le nom de domaine

Le nom de domaine est nécessaire pour votre code. Pour identifier aisément le nom de domaine correct, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Pointez sur votre nom dans le coin supérieur droit et notez le domaine qui apparaît dans la fenêtre contextuelle. Remplacez **domain.onmicrosoft.com** dans l'extrait de code ci-dessous avec la valeur de votre compte.

    ![Identifier le nom de domaine][3]



**Ressources AAD supplémentaires**

Pour plus d’informations sur l’utilisation d’Azure Active Directory pour l’authentification, consultez [ce billet de blog](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Récupérer le jeton d’accès pour l’utilisateur actuel

L’application cliente doit récupérer le jeton d’accès d’application pour l’utilisateur actuel. La première fois que le code est exécuté par un utilisateur, celui-ci est invité à entrer ses informations d’identification et le jeton résultant est mis en cache localement. Les exécutions suivantes récupèrent le jeton du cache, et l’utilisateur n’est invité à se connecter que si le jeton a expiré.


    private static AuthenticationResult GetAccessToken()
    {
        AuthenticationContext authContext = new AuthenticationContext
            ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

        AuthenticationResult token = authContext.AcquireToken
            ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                clientId,
        new Uri(redirectUri) /* redirect URI */,
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }



> [AZURE.NOTE] Les exemples de cet article utilisent une forme synchrone de chaque bloc et demande d’API jusqu’à la fin de l’appel REST sur le service sous-jacent. Des méthodes asynchrones sont disponibles.



## Créer un groupe de ressources

Avec Resource Manager, toutes les ressources doivent être créées dans un groupe de ressources. Un groupe de ressources est un conteneur réunissant les ressources associées d’une application. Pour créer un pool de base de données élastique, vous avez besoin d'un serveur de base de données SQL Azure dans un groupe de ressources existant. Exécutez le code C# suivant pour créer un groupe de ressources :


    // Create a resource management client
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));

    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };

    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);



## Créer un serveur

Les pools de base de données élastiques sont contenus dans des serveurs de base de données SQL Azure afin de l'étape suivante consiste à créer un serveur. Le nom du serveur doit être unique globalement parmi tous les serveurs SQL Azure. Il se peut donc qu’une erreur soit signalée si le nom de serveur est déjà utilisé. Il est également à noter que l'exécution de cette commande peut prendre plusieurs minutes. Pour qu'une application puisse se connecter au serveur, vous devez également créer une règle de pare-feu sur le serveur pour permettre l'accès à partir de l'adresse IP du client.


    // Create a SQL Database management client
    SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken));

    // Create a server
    ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ServerCreateOrUpdateProperties()
        {
            AdministratorLogin = "ServerAdmin",
            AdministratorLoginPassword = "P@ssword1",
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);




## Créer une règle de pare-feu de serveur pour autoriser l’accès au serveur

Par défaut, un serveur n'est pas doté de règles de pare-feu pour qu'il ne puisse pas être connecté depuis n'importe quel emplacement. Pour vous connecter à un serveur ou à des bases de données quelconques sur le serveur, une [règle de pare-feu](sql-database-firewall-configure.md) qui autorise l'accès à partir de l'adresse IP du client doit être définie.

L'exemple suivant crée une règle de pare-feu de serveur qui permet un accès au serveur à partir de n'importe quelle adresse IP. En guise de défense principale contre les intrusions, nous vous recommandons de créer des connexions SQL et des mots de passe appropriés pour sécuriser votre base de données et de ne pas compter sur des règles de pare-feu. Pour plus d'informations, consultez [Gestion des bases de données et des connexions dans la base de données Azure SQL](sql-database-manage-logins.md)


    // Create a firewall rule on the server to allow TDS connection
    FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
    {
        Properties = new FirewallRuleCreateOrUpdateProperties()
        {
            StartIpAddress = "0.0.0.0",
            EndIpAddress = "255.255.255.255"
        }
    };

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);




Pour autoriser d’autres services Azure à accéder à un serveur, ajoutez une règle de pare-feu et définissez les paramètres StartIpAddress et EndIpAddress sur 0.0.0.0. Notez que cette configuration autorise le trafic Azure à accéder au serveur depuis *n’importe quel* abonnement Azure.


## Créer une base de données

L'exemple suivant crée une nouvelle base de données de base. Si une base de données portant le même nom existe sur le serveur, la base de données existante sera actualisée.

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("resourcegroup-name", "server-name").Server;

        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Basic",
                RequestedServiceObjectiveName = "Basic",
                MaxSizeBytes = 2147483648,
                Collation = "SQL_Latin1_General_CP1_CI_AS"
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);



## Créer un pool de base de données élastique

L'exemple suivant crée un pool de base de données élastique :



    // Create elastic pool: configure create or update parameters and properties explicitly
    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 100,  // alternatively set StorageMB, if both are specified they must agree based on the eDTU:storage ratio of the edition
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## Mettre à jour un pool de base de données élastique

L'exemple suivant met à jour les caractéristiques de performances d'un pool de base de données élastique existant :

    // Retrieve existing pool properties
    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);



## Déplacer une base de données existante vers un pool élastique de bases de données

*Après la création d’un pool, vous pouvez également utiliser Transact-SQL pour déplacer des bases de données existantes dans et hors d’un pool. Pour plus d’informations, voir [Référence du pool de base de données élastique - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

L'exemple suivant déplace une base de données SQL Azure existante vers un pool :


    // Update database service objective to add the database to a pool

    // Retrieve current database properties
    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);




## Créer une base de données dans un pool de base de données élastique

*Après la création d’un pool, vous pouvez également utiliser Transact-SQL pour la création de bases de données élastiques dans le pool. Pour plus d’informations, voir [Référence du pool de base de données élastique - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

L'exemple suivant crée une base de données directement dans un pool :


    // Create a new database in the pool

    // Create a database: configure create or update parameters and properties explicitly
    DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentServer.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = 268435456000, // 250 GB,
            Collation = "SQL_Latin1_General_CP1_CI_AS"
        }
    };

    var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);



## Répertorier toutes les bases de données dans un pool de base de données élastique

L'exemple suivant répertorie toutes les bases de données dans un pool :

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }




## Exemple d’application console


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Security;

    namespace AzureSqlDatabaseRestApiExamples
    {
    class Program
    {
        /// <summary>
        /// Prompts for user credentials when first run or if the cached credentials have expired.
        /// </summary>
        /// <returns>The access token from AAD.</returns>
        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }

        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + "YOU.onmicrosoft.com" /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* application client ID from AAD*/,
                userCredential);

            return token;
        }
        private static SecureString convertToSecureString(string secret)
        {
            var secureStr = new SecureString();
            if (secret.Length > 0)
            {
                foreach (var c in secret.ToCharArray()) secureStr.AppendChar(c);
            }
            return secureStr;
        }

        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity is {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = "South Central US"
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = "South Central US",
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = "ServerAdmin",
                    AdministratorLoginPassword = "P@ssword1",
                    Version = "12.0"
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);

            var serverGetResult = sqlClient.Servers.Get("resourcegroup-name", "server-name");


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);


            var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);


           // Create an elastic database pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update pool: retrieve existing pool properties
            var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

            // Update pool: configure create or update parameters with existing property values, override those to be changed.
            ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentPool.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = currentPool.Properties.Edition,
                    DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
                    DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
                    Dtu = (int)currentPool.Properties.Dtu,
                    StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
                }
            };
            newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update a databases service objective to add the database to a pool

            // Update database: retrieve current database properties
            currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

            // Update database: configure create or update parameters with existing property values, override those to be changed.
            DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentDatabase.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "ElasticPool",
                    ElasticPoolName = "ElasticPool1",
                    MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
                    Collation = currentDatabase.Properties.Collation,
                }
            };


            // Create a new database in the pool

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "ElasticPool",
                    ElasticPoolName = "ElasticPool1",
                    MaxSizeBytes = 268435456000, // 250 GB,
                    Collation = "SQL_Latin1_General_CP1_CI_AS"
                }
            };

            var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2}({3}) ", poolDbResponse.Database.Name, poolDbResponse.StatusCode, poolDbResponse.Database.Properties.ServiceObjective, poolDbResponse.Database.Properties.ElasticPoolName);
      }
    }







## Ressources supplémentaires


[Base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

[API de gestion des ressources Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Référence du pool de base de données élastique](sql-database-elastic-pool-reference.md).


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-csharp/aad.png
[2]: ./media/sql-database-elastic-pool-csharp/permissions.png
[3]: ./media/sql-database-elastic-pool-csharp/getdomain.png
[4]: ./media/sql-database-elastic-pool-csharp/aad2.png
[5]: ./media/sql-database-elastic-pool-csharp/aad-applications.png
[6]: ./media/sql-database-elastic-pool-csharp/add.png
[7]: ./media/sql-database-elastic-pool-csharp/add-application.png
[8]: ./media/sql-database-elastic-pool-csharp/add-application2.png
[9]: ./media/sql-database-elastic-pool-csharp/clientid.png

<!---HONumber=AcomDC_0224_2016-->