<properties 
   pageTitle="Créer une base de données Azure SQL Database avec C#" 
   description="Cet article vous montre comment créer une base de données SQL Azure à l’aide de la bibliothèque Azure SQL Database pour .NET." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="09/01/2015"
   ms.author="sstein"/>

# Création d’une base de données SQL avec C&#x23;

**Base de données unique**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



Cet article contient des commandes pour créer une base de données SQL Azure avec C# en utilisant la [bibliothèque Azure SQL Database pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).

Cet article montre comment créer une base de données unique. Pour créer des bases de données élastiques, consultez [Créer un pool de bases de données élastiques](sql-database-elastic-pool-portal.md).

Les différents extraits de code sont fractionnés par souci de clarté, et un exemple d’application console réunit toutes les commandes dans la dernière section de cet article.

La bibliothèque Azure SQL Database pour .NET fournit une API basée sur [Azure Resource Manager](resource-group-overview.md) qui encapsule l’[API REST de base de données SQL basée sur Resource Manager](https://msdn.microsoft.com/library/azure/mt163571.aspx). Cette bibliothèque cliente suit le modèle commun pour les bibliothèques clientes basées sur Resource Manager. Resource Manager nécessite des groupes de ressources et une authentification avec [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE]La bibliothèque SQL Database pour .NET est actuellement en version préliminaire.

<br>

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Visual Studio. Pour obtenir une copie gratuite de Visual Studio, consultez la page [Téléchargements Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Installation des bibliothèques nécessaires

Obtenez les bibliothèques de gestion requises en installant les packages suivants à l’aide de la [console du gestionnaire de package](http://docs.nuget.org/Consume/Package-Manager-Console) :

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurer l’authentification avec Azure Active Directory

Vous devez d’abord autoriser votre application cliente à accéder à l’API REST en configurant l’authentification nécessaire.

Les [API REST d’Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn948464.aspx) utilisent Azure Active Directory pour l’authentification.

Pour authentifier votre application cliente en fonction de l’utilisateur actuel, vous devez d’abord inscrire celle-ci dans le domaine AAD associé à l’abonnement sous lequel les ressources Azure ont été créées. Si votre abonnement Azure a été créé avec un compte Microsoft, plutôt qu’avec un compte professionnel ou scolaire, vous disposez déjà d’un domaine AAD par défaut. L’inscription de l’application peut être effectuée dans le [portail Azure](https://manage.windowsazure.com/).

Pour créer une application et l’inscrire dans le répertoire actif correct, procédez comme suit :

1. Faites défiler le menu situé à gauche pour localiser le service **Active Directory**, puis ouvrez ce dernier.

    ![AAD][1]

2. Sélectionnez l’annuaire pour authentifier votre application et cliquez sur son **Nom**.

    ![Annuaires][4]

3. Dans la page de l’annuaire, cliquez sur **APPLICATIONS**.

    ![Applications][5]

4. Cliquez sur **AJOUTER** pour ajouter une nouvelle application.

    ![Ajouter l’application][6]

5. Sélectionnez **Ajouter une application développée par mon organisation**.

5. Spécifiez un **NOM** pour l’application et sélectionnez **APPLICATION CLIENTE NATIVE**.

    ![Ajouter l’application][7]

6. Spécifiez un **URI DE REDIRECTION**. Il n’est pas nécessaire que celui-ci soit un point de terminaison réel ; un URI valide suffit.

    ![Ajouter l’application][8]

7. Terminez la création de l’application, cliquez sur **CONFIGURER**, puis copiez l’**ID CLIENT** (vous en aurez besoin dans votre code).

    ![Obtenir l’ID client][9]


1. En bas de la page, cliquez sur **Ajouter une application**.
1. Sélectionnez **Applications Microsoft**.
1. Sélectionnez **API de gestion des services Azure**, puis terminez l’Assistant.
2. Vérifiez que cette API est sélectionnée puis, pour accorder les autorisations permettant d’y accéder, sélectionnez **Accéder à la gestion des services Azure (version Preview)**.

    ![Autorisations][2]

2. Cliquez sur **ENREGISTRER**.



### Identifier le nom de domaine

Le nom de domaine est nécessaire pour votre code. Pour identifier aisément le nom de domaine correct, procédez comme suit :

1. Accédez au [portail Azure en version préliminaire](https://portal.azure.com).
2. Pointez sur votre nom dans le coin supérieur droit et notez le domaine qui apparaît dans la fenêtre contextuelle.

    ![Identifier le nom de domaine][3]





**Ressources AAD supplémentaires**

Pour plus d’informations sur l’utilisation d’Azure Active Directory pour l’authentification, consultez [ce billet de blog](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Récupérer le jeton d’accès pour l’utilisateur actuel 

L’application cliente doit récupérer le jeton d’accès d’application pour l’utilisateur actuel. La première fois que le code est exécuté par un utilisateur, celui-ci est invité à entrer ses informations d’identification. Le jeton résultant est mis en cache localement. Les exécutions suivantes récupèrent le jeton du cache, et l’utilisateur n’est invité à se connecter que si le jeton a expiré.

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
        new Uri(clientAppUri) /* redirect URI */,
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }



> [AZURE.NOTE]Les exemples de cet article utilisent une forme synchrone de chaque bloc et demande d’API jusqu’à la fin de l’appel REST sur le service sous-jacent. Des méthodes asynchrones sont disponibles.



## Créer un groupe de ressources

Avec Resource Manager, toutes les ressources doivent être créées dans un groupe de ressources. Un groupe de ressources est un conteneur réunissant les ressources associées d’une application. Avec Azure SQL Database, le serveur de base de données doit être créé dans un groupe de ressources existant, puis la base de données doit être créée sur le serveur. Ensuite, pour qu’une application puisse se connecter au serveur ou à la base de données, vous devez également créer une règle de pare-feu sur le serveur pour ouvrir l’accès à partir de l’adresse IP du client.


    // Create a resource management client 
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));
    
    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };
    
    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("ResourceGroupName", resourceGroupParameters);



## Créer un serveur 

Les bases de données SQL se trouvent sur des serveurs. Le nom du serveur doit être unique globalement parmi tous les serveurs SQL Azure. Il se peut donc qu’une erreur soit signalée si le nom de serveur est déjà utilisé. Il est également à noter que l'exécution de cette commande peut prendre plusieurs minutes.


    // Create a SQL Database management client
    SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken));

    // Create a server
    ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ServerCreateOrUpdateProperties()
        {
            AdministratorLogin = "ServerAdmin",
            AdministratorLoginPassword = securelyStoredPassword,
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("ResourceGroupName", "ServerName", serverParameters);




## Créer une règle de pare-feu de serveur pour autoriser l’accès au serveur

Par défaut, un serveur n’est pas accessible depuis n’importe quel emplacement. Pour vous connecter à un serveur ou à des bases de données sur le serveur, une [règle de pare-feu](https://msdn.microsoft.com/library/azure/ee621782.aspx) qui autorise l’accès à partir de l’adresse IP du client doit être définie.

L’exemple suivant crée une règle qui ouvre l’accès au serveur à partir de n’importe quelle adresse IP. En guise de défense principale contre les intrusions, nous vous recommandons de créer des connexions SQL et des mots de passe appropriés pour sécuriser votre base de données et de ne pas compter sur des règles de pare-feu.


    // Create a firewall rule on the server to allow TDS connection 
    FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
    {
        Properties = new FirewallRuleCreateOrUpdateProperties()
        {
            StartIpAddress = "0.0.0.0",
            EndIpAddress = "255.255.255.255"
        }
    };

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("ResourceGroupName", "ServerName", "FirewallRuleName", firewallParameters);




Pour autoriser d’autres services Azure à accéder à un serveur, ajoutez une règle de pare-feu et définissez les paramètres StartIpAddress et EndIpAddress sur 0.0.0.0. Notez que cette configuration autorise le trafic Azure à accéder au serveur depuis *n’importe quel* abonnement Azure.


## Créer une base de données

La commande suivante crée une base de données de base si aucune base de données portant le même nom n’existe sur le serveur ; dans le cas contraire, la base de données existante est mise à jour.

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("ResourceGroupName", "ServerName").Server;
 
        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Standard",
                RequestedServiceObjectiveName = "S0",
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("ResourceGroupNname", "ServerName", "Database1", newDatabaseParameters);






## Exemple d’application console


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    
    namespace CreateSqlDatabase
    {
     class Program
     {

        #region user variables
        
        // Azure subscription id
        private static string subscriptionId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";
     
        
        // application client ID from Azure Active Directory (AAD)
        private static string clientAppUri = "http://app1";
        private static string clientId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";

        private static string resourcegroupName = "rg1";

        private static string dataCenterLocation = "Japan West";

        private static string databaseName = "newDatabaseName";
        private static string databaseEdition = "Standard";
        private static string databasePerformanceLevel = "S0";

        private static string serverName = "serverName";
        private static string serverAdmin = "admin";
        private static string serverAdminPassword = securelyStoredPassword;
        private static string serverVersion = "12.0";

        private static string firewallRuleName = "rule1";
        private static string firewallRuleStartIp = "0.0.0.0";
        private static string firewallRuleEndIp = "255.255.255.255";



        private static string domainName = "microsoft.onmicrosoft.com";

        private static string serverLocation = "Japan West";

        #endregion


        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity: {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = dataCenterLocation
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourcegroupName, resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourcegroupName, serverName, serverParameters);

            var serverGetResult = sqlClient.Servers.Get(resourcegroupName, serverName);


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection 

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = firewallRuleStartIp,
                    EndIpAddress = firewallRuleEndIp
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourcegroupName, serverName, firewallRuleName, firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code: {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);

            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourcegroupName, serverName).Server;

            // Create a database
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerformanceLevel,
                }
            };

            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourcegroupName, serverName, databaseName, newDatabaseParameters);

            Console.WriteLine("Database {0} created with status code: {1}. Service Objective: {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);
           
            Console.WriteLine(System.Environment.NewLine + "Press any key to exit.");
            Console.ReadKey();
        }

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
            new Uri(clientAppUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
      }
    }


## Étapes suivantes

- [Se connecter et interroger une base de données SQL avec C#](sql-database-connect-query.md)
- [Se connecter avec SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)

## Ressources supplémentaires

- [Base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)





<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png

<!---HONumber=Nov15_HO2-->