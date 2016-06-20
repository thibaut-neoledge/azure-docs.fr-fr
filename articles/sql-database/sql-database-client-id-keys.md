<properties
   pageTitle="Inscrire votre application et obtenir l’ID client et la clé pour la connexion à une base de données SQL à partir du code | Microsoft Azure"
   description="Obtenez l’ID client et la clé pour l’accès à la base de données SQL à partir du code."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="06/06/2016"
   ms.author="sstein"/>

# Obtenir l’ID client et la clé pour la connexion à une base de données SQL à partir du code

Pour créer et gérer une base de données SQL à partir du code, vous devez inscrire votre application dans le domaine Azure Active Directory (AAD) associé à l’abonnement où vos ressources Azure ont été créées. Quand vous inscrivez l’application, Azure génère un ID client et une clé dont vous aurez besoin dans votre code pour authentifier votre application. Pour plus d’informations, consultez [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

## Inscrire une application cliente native et obtenir l’ID client

Pour créer et inscrire une application, procédez comme suit :

1. Connectez-vous au [portail Classic](https://manage.windowsazure.com/) (actuellement, l’inscription des applications doit être effectuée dans le portail Classic).
1. Recherchez **Active Directory** dans le menu, puis sélectionnez-le.

    ![AAD][1]

2. Sélectionnez le répertoire pour authentifier votre application et cliquez sur son **Nom**.

    ![Annuaires][4]

3. Dans la page du répertoire, cliquez sur **APPLICATIONS**.

    ![Applications][5]

4. Cliquez sur **AJOUTER** pour créer une application.

    ![Ajouter l’application][6]

5. Spécifiez un **NOM** pour l’application et sélectionnez **APPLICATION CLIENTE NATIVE**.

    ![Ajouter l’application][7]

6. Fournissez un **URI DE REDIRECTION**. Il n’est pas nécessaire que celui-ci soit un point de terminaison réel ; un URI valide suffit.

    ![Ajouter l’application][8]

7. Terminez la création de l’application, cliquez sur **CONFIGURER**, puis copiez l’**ID CLIENT** (vous aurez besoin de cette valeur dans votre code).

    ![Obtenir l’ID client][9]


1. Faites défiler la page vers le bas et cliquez sur **Ajouter une application**.
1. Sélectionnez **Applications Microsoft**.
1. Sélectionnez **API Gestion des services Microsoft Azure**, puis terminez l’Assistant.
2. Dans la section **Autorisations pour d’autres applications**, recherchez **API Gestion des services Microsoft Azure** et cliquez sur **Autorisations déléguées**.
3. Sélectionnez **Accéder à la gestion des services Azure...**.

    ![Autorisations][2]

2. Cliquez sur **ENREGISTRER** en bas de la page.



## Inscrire une application web (ou API web) et obtenir l’ID client et la clé

Pour créer une application et l’inscrire dans le répertoire actif correct, procédez comme suit :

1. Connectez-vous au [portail Classic](https://manage.windowsazure.com/).
1. Recherchez **Active Directory** dans le menu, puis sélectionnez-le.

    ![AAD][1]

2. Sélectionnez le répertoire pour authentifier votre application et cliquez sur son **Nom**.

    ![Annuaires][4]

3. Dans la page du répertoire, cliquez sur **APPLICATIONS**.

    ![Applications][5]

4. Cliquez sur **AJOUTER** pour créer une application.

    ![Ajouter l’application][6]

5. Spécifiez un **NOM** pour l’application et sélectionnez ** APPLICATION WEB ET/OU API WEB**.

    ![Ajouter l’application][10]

6. Indiquez une **URL DE CONNEXION** et un **URI ID D’APPLICATION**. Il n’est pas nécessaire que celui-ci soit un point de terminaison réel ; un URI valide suffit.

    ![Ajouter l’application][11]

7. Terminez la création de l’application, puis cliquez sur **CONFIGURER**.

    ![configurer][12]

8. Faites défiler l’écran jusqu’à la section **Clés** et sélectionnez **1 an** dans la liste **Sélectionner une durée**. Comme la valeur de la clé s’affiche après l’enregistrement, nous reviendrons copier la clé ultérieurement.

    ![définir la durée des clés][13]



1. Faites défiler la page vers le bas et cliquez sur **Ajouter une application**.
1. Sélectionnez **Applications Microsoft**.
1. Recherchez et sélectionnez **API Gestion des services Microsoft Azure**, puis terminez l’Assistant.
2. Dans la section **Autorisations pour d’autres applications**, recherchez **API Gestion des services Microsoft Azure** et cliquez sur **Autorisations déléguées**.
3. Sélectionnez **Accéder à la gestion des services Azure...**.

    ![Autorisations][2]

2. Cliquez sur **ENREGISTRER** en bas de la page.
3. Une fois l’enregistrement terminé, recherchez et enregistrez l’ID CLIENT et la clé :

    ![secrets d’application web][14]



## Obtenir votre nom de domaine

Le nom de domaine est parfois nécessaire pour votre code d’authentification. Pour identifier aisément le nom de domaine correct, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Pointez sur votre nom dans le coin supérieur droit et notez le domaine qui apparaît dans la fenêtre contextuelle.

    ![Identifier le nom de domaine][3]




## Exemple d’application console


Obtenez la bibliothèque de gestion requise en installant les packages suivants à l’aide de la [console du Gestionnaire de package](http://docs.nuget.org/Consume/Package-Manager-Console) dans Visual Studio (**Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**) :


    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


Créez une application console nommée **SqlDbAuthSample** et remplacez le contenu de **Program.cs** par le code suivant :

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbAuthSample
    {
    class Program
    {
        static void Main(string[] args)
        {
            token = GetAccessTokenforNativeClient();
            // token = GetAccessTokenUsingUserCredentials(new UserCredential("<email address>"));
            // token = GetAccessTokenForWebApp();

            Console.WriteLine("Signed in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Press Enter to continue...");
            Console.ReadLine();
        }


        // authentication variables (native)
        static string nclientId = "<your client id>";
        static string nredirectUri = "<your redirect URI>";
        static string ndomainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        private static AuthenticationResult GetAccessTokenforNativeClient()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + ndomainName /* Tenant ID or AAD domain */);

            token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    nclientId,
            new Uri(nredirectUri),
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }


        // authentication variables (web)
        static string wclientId = "<your client id>";
        static string wkey = "<your key>";
        static string wdomainName = "<i.e. microsoft.onmicrosoft.com>";

        private static AuthenticationResult GetAccessTokenForWebApp()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + wdomainName /* Tenant ID or AAD domain */);

            ClientCredential cc = new ClientCredential(wclientId, wkey);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                cc);

            return token;
        }


        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + ndomainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                nclientId /* application client ID from AAD*/,
                new Uri(nredirectUri) /* redirect URI */,
                PromptBehavior.Auto,
                new UserIdentifier(userCredential.UserName, UserIdentifierType.RequiredDisplayableId));

            return token;
        }
    }
    }


Pour obtenir des exemples de code spécifiques associés à l’authentification Azure AD, consultez la section [Blog de sécurité de SQL Server](http://blogs.msdn.com/b/sqlsecurity/) sur MSDN.

## Voir aussi

- [Créer une base de données SQL avec C#](sql-database-get-started-csharp.md)
- [Connexion à SQL Database avec l’authentification Azure Active Directory](sql-database-aad-authentication.md)



<!--Image references-->
[1]: ./media/sql-database-client-id-keys/aad.png
[2]: ./media/sql-database-client-id-keys/permissions.png
[3]: ./media/sql-database-client-id-keys/getdomain.png
[4]: ./media/sql-database-client-id-keys/aad2.png
[5]: ./media/sql-database-client-id-keys/aad-applications.png
[6]: ./media/sql-database-client-id-keys/add.png
[7]: ./media/sql-database-client-id-keys/add-application.png
[8]: ./media/sql-database-client-id-keys/add-application2.png
[9]: ./media/sql-database-client-id-keys/clientid.png
[10]: ./media/sql-database-client-id-keys/add-application-web.png
[11]: ./media/sql-database-client-id-keys/add-application-app-properties.png
[12]: ./media/sql-database-client-id-keys/configure.png
[13]: ./media/sql-database-client-id-keys/key-duration.png
[14]: ./media/sql-database-client-id-keys/web-secrets.png

<!---HONumber=AcomDC_0608_2016-->