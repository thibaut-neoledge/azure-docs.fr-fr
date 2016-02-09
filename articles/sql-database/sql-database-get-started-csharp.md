<properties 
	pageTitle="Essayer la base de données SQL : Utiliser C# pour créer une base de données SQL | Microsoft Azure" 
	description="Essayez la base de données SQL pour développer des applications SQL et C# et créez une base de données SQL Azure avec C# à l’aide de la bibliothèque de base de données SQL pour .NET." 
	keywords="essayer sql, sql c#"   
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="01/22/2016"
   ms.author="sstein"/>

# Essayer la base de données SQL : Utiliser C&#x23; pour créer une base de données SQL avec la bibliothèque de base de données SQL pour .NET 

**Base de données unique**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



Découvrez comment utiliser les commandes C# pour créer une base de données SQL Azure avec la [bibliothèque de base de données SQL Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).

Vous allez essayer la base de données SQL en créant une base de données unique à l’aide de SQL et C#. Pour créer des bases de données élastiques, voir [Créer un pool de bases de données élastiques](sql-database-elastic-pool-portal.md).

Les différents extraits de code sont fractionnés par souci de clarté, et un exemple d’application console réunit toutes les commandes dans la dernière section de cet article.

La bibliothèque de base de données SQL Azure pour .NET fournit une API basée sur [Azure Resource Manager](resource-group-overview.md) qui encapsule l’[API REST de base de données SQL basée sur Resource Manager](https://msdn.microsoft.com/library/azure/mt163571.aspx). Cette bibliothèque cliente suit le modèle commun pour les bibliothèques clientes basées sur Resource Manager. Resource Manager nécessite des groupes de ressources et l’authentification avec [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE] La bibliothèque SQL Database pour .NET est actuellement en version préliminaire.

<br>

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Visual Studio. Pour obtenir une copie gratuite de Visual Studio, consultez la page [Téléchargements Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Installation des bibliothèques nécessaires

Pour configurer une base de données SQL avec C#, obtenez les bibliothèques de gestion requises en installant les packages suivants à l’aide de la [console du gestionnaire de package](http://docs.nuget.org/Consume/Package-Manager-Console) dans Visual Studio (**Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**) :

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurer l’authentification avec Azure Active Directory

Vous devez d’abord autoriser votre application cliente à accéder au service Base de données SQL en configurant l’authentification requise.

Pour authentifier votre application cliente en fonction de l’utilisateur actuel, vous devez d’abord inscrire celle-ci dans le domaine AAD associé à l’abonnement sous lequel les ressources Azure ont été créées. Si votre abonnement Azure a été créé avec un compte Microsoft, plutôt qu’avec un compte professionnel ou scolaire, vous disposez déjà d’un domaine AAD par défaut.

Pour créer une application et l’inscrire dans le répertoire actif correct, procédez comme suit :

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/).
1. Sur la gauche, sélectionnez le service **Active Directory**, sélectionnez le répertoire pour authentifier votre application, puis cliquez sur son **Nom**.

    ![Essayer la base de données SQL : Configurer Azure Active Directory (AAD).][1]

2. Dans la page du répertoire, cliquez sur **APPLICATIONS**.

    ![Page de répertoire avec Applications.][5]

4. Cliquez sur **AJOUTER** pour créer une application.

5. Sélectionnez **Ajouter une application développée par mon organisation**.

5. Spécifiez un **NOM** pour l’application et sélectionnez **APPLICATION CLIENTE NATIVE**.

    ![Fournissez des informations sur votre application C# SQL.][7]

6. Fournissez un **URI DE REDIRECTION**. Il n’est pas nécessaire que celui-ci soit un point de terminaison réel ; un URI valide suffit.

    ![Ajoutez une URL de redirection pour votre application C# SQL.][8]

7. Terminez la création de l’application, cliquez sur **CONFIGURER**, puis copiez l’**ID CLIENT** (vous en aurez besoin dans votre code ultérieurement).

    ![Obtenez l’ID client pour votre application C# SQL.][9]


1. En bas de la page, cliquez sur **Ajouter une application**.
1. Sélectionnez **Applications Microsoft**.
1. Sélectionnez **API de gestion des services Azure**, puis terminez l’Assistant.
2. Vérifiez que cette API est sélectionnée puis, pour accorder les autorisations permettant d’y accéder, sélectionnez **Accéder à la gestion des services Azure (version préliminaire)**.

    ![Définissez des autorisations.][2]

2. Cliquez sur **ENREGISTRER**.



### Identifier le nom de domaine

Le nom de domaine est nécessaire pour votre code. Pour identifier aisément le nom de domaine correct, procédez comme suit :

1. Accédez au [portail Azure](http://portal.azure.com).
2. Pointez sur votre nom dans le coin supérieur droit et notez le domaine qui apparaît dans la fenêtre contextuelle.

    ![Identifiez le nom de domaine.][3]





**Ressources AAD supplémentaires**

Pour plus d’informations sur l’utilisation d’Azure Active Directory pour l’authentification, consultez [ce billet de blog](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Récupérer le jeton d’accès pour l’utilisateur actuel 

L’application cliente doit récupérer le jeton d’accès d’application pour l’utilisateur actuel. La première fois que le code est exécuté par un utilisateur, celui-ci est invité à entrer ses informations d’identification. Le jeton résultant est mis en cache localement. Les exécutions suivantes récupèrent le jeton du cache, et l’utilisateur n’est invité à se connecter que si le jeton a expiré.

Ce code retourne le résultat Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationResult dont vous aurez besoin dans les extraits de code ci-dessous.

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

Avec Resource Manager, toutes les ressources doivent être créées dans un groupe de ressources. Un groupe de ressources est un conteneur réunissant les ressources associées d’une application. Avec Base de données SQL Microsoft Azure, le serveur de base de données doit être créé dans un groupe de ressources existant.

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }


## Créer un serveur 

Les bases de données SQL se trouvent sur des serveurs. Le nom du serveur doit être unique globalement parmi tous les serveurs SQL Azure. Il se peut donc qu’une erreur soit signalée si le nom de serveur est déjà utilisé. Il est également à noter que l'exécution de cette commande peut prendre plusieurs minutes.

    static void CreateServer()
    {
        // Create a SQL Database management client
        SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

        // Create a server
        ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
        {
            Location = location,
            Properties = new ServerCreateOrUpdateProperties()
            {
                AdministratorLogin = administratorLogin,
                AdministratorLoginPassword = administratorPassword,
                Version = serverVersion
            }
        };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
    }


### Créer un administrateur de serveur externe


    // Create a server external admin
    ServerAdministratorCreateOrUpdateParameters aadAdminParameters = new ServerAdministratorCreateOrUpdateParameters()
    {
        Properties = new ServerAdministratorCreateOrUpdateProperties()
        {
            AdministratorType = "ActiveDirectory",
            Login = "<login>",
            Sid = new Guid("<Azure AD admin sid>"),
            TenantId = new Guid("<Azure AD tenant id>")
        }
    };

    var adminResult = sqlClient.ServerAdministrators.CreateOrUpdate(resourceGroupName, serverName, "ActiveDirectory", aadAdminParameters);
    var adminGetResult = sqlClient.ServerAdministrators.Get(resourceGroupName, serverName, "ActiveDirectory");




## Créer une règle de pare-feu de serveur pour autoriser l’accès au serveur

Par défaut, un serveur n’est pas accessible depuis n’importe quel emplacement. Pour vous connecter à un serveur ou à des bases de données sur le serveur, une [règle de pare-feu](https://msdn.microsoft.com/library/azure/ee621782.aspx) qui autorise l’accès à partir de l’adresse IP du client doit être définie.

L’exemple suivant crée une règle qui ouvre l’accès au serveur à partir de n’importe quelle adresse IP. En guise de défense principale contre les intrusions, nous vous recommandons de créer des connexions SQL et des mots de passe appropriés pour sécuriser votre base de données et de ne pas compter sur des règles de pare-feu.


        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server 
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }




Pour autoriser d’autres services Azure à accéder à un serveur, ajoutez une règle de pare-feu et définissez les paramètres StartIpAddress et EndIpAddress sur 0.0.0.0. Notez que cette configuration autorise le trafic Azure à accéder au serveur depuis *n’importe quel* abonnement Azure.


## Utiliser C&#x23; pour créer une base de données SQL

La commande C# suivante crée une base de données SQL si aucune base de données portant le même nom n’existe sur le serveur ; dans le cas contraire, la base de données existante est mise à jour.


        static void CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
        }



## Exemple C&#x23; d’application console

L’exemple suivant crée un groupe de ressources, un serveur, les règles de pare-feu et une base de données SQL. La section *Configurer l’authentification avec Azure Active Directory* située en haut de cet article explique où obtenir les valeurs correspondant aux variables clientId, redirectUri et domainName URI.


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    
    namespace SqlDbConsoleApp
    {
    class Program
    {
        // Get these values from the Azure portal
        static string subscriptionId = "<Azure subscription id>";
        static string clientId = "<Azure App clientId>";
        static string redirectUri = "<Azure App redirectURI>";
        static string domainName = "<domain>";

        // You create these values 
        static string resourceGroupName = "<your resource group name>";
        static string location = "<Azure data center location>";

        static string serverName = "<your server name>";
        static string administratorLogin = "<your server admin>";
        
        // store your password securely!
        static string administratorPassword = "<your server admin password>";
        static string serverVersion = "12.0";

        static string firewallRuleName = "<your firewall rule name>";

        static string databaseName = "dbfromcsharparticle";
        static string databaseEdition = "Basic"; // Basic, Standard, or Premium
        static string databasePerfLevel = "";

        static AuthenticationResult token;
        static Microsoft.Rest.TokenCredentials creds;

        static SqlManagementClient sqlClient;


        static void Main(string[] args)
        {
            Console.WriteLine("Logging in...");

            token = GetAccessToken();

            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Creating resource group...");
            CreateResourceGroup();

            sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            Console.WriteLine("Creating server...");
            CreateServer();

            Console.WriteLine("Creating firewall rule...");
            CreateFirewallRule();

            Console.WriteLine("Creating database...");

            DatabaseCreateOrUpdateResponse dbResponse = CreateDatabase();
            Console.WriteLine("Status: " + dbResponse.Status.ToString() 
                + " Code: " + dbResponse.StatusCode.ToString());

            Console.WriteLine("Press enter to exit...");
            Console.ReadLine();
        }

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }

        static void CreateServer()
        {
            // Create a SQL Database management client
            //SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = location,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = administratorLogin,
                    AdministratorLoginPassword = administratorPassword,
                    Version = serverVersion
                }
            };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
        }

        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server 
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    // replace with your client IP address
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }

        static DatabaseCreateOrUpdateResponse CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }

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
    }
    }




## Étapes suivantes
Maintenant que vous avez essayé la base de données SQL et configuré une base de données avec C#, vous êtes prêt pour les articles suivants :

- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)

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

<!---HONumber=AcomDC_0204_2016-->