<properties
	pageTitle="Prise en main de l'authentification personnalisée | Microsoft Azure"
	description="Découvrez comment authentifier les utilisateurs à l'aide d'un nom d'utilisateur et d'un mot de passe."
	documentationCenter="Mobile"
	authors="mattchenderson"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="12/28/2015"
	ms.author="mahender"/>

# Prise en main de l'authentification personnalisée

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


## Vue d'ensemble
Cette rubrique explique comment authentifier les utilisateurs dans le backend .NET d'Azure Mobile Services en émettant votre propre jeton d'authentification Mobile Services. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide en utilisant un nom d'utilisateur et un mot de passe personnalisés pour votre application.

>[AZURE.NOTE]Ce didacticiel présente une méthode avancée d'authentification de vos services mobiles avec des informations d'identification personnalisées. Le recours aux fournisseurs d'identité des réseaux sociaux intégrés sera mieux adapté pour de nombreuses applications, les utilisateurs pouvant se connecter via Facebook, Twitter, Google, un compte Microsoft ou Azure Active Directory. S'il s'agit de votre première authentification dans Mobile Services, consultez le didacticiel [Ajout de l'authentification à votre application].

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services].

>[AZURE.IMPORTANT]Le but de ce didacticiel est de vous montrer comment émettre un jeton d'authentification pour Mobile Services. Il ne doit pas être considéré comme un guide de sécurité. Pendant le développement de votre application, vous devez être conscient des implications du stockage des mots de passe en matière de sécurité et disposer d'une stratégie pour gérer les attaques en force brute.

## Configuration de la table des comptes

Dans la mesure où vous utilisez l'authentification personnalisée et ne dépendez d'aucun autre fournisseur d'identité, vous devez stocker les informations de connexion de vos utilisateurs. Dans cette section, vous allez créer une table pour vos comptes et configurer les mécanismes de sécurité de base. La table des comptes contiendra les noms d'utilisateur ainsi que les mots de passe salés et hachés, et vous pourrez également inclure d'autres informations utilisateur, si besoin est.

1. Dans le dossier **DataObjects** de votre projet de backend, ajoutez une nouvelle entité appelée `Account`.

2. Ajoutez l'instruction `using` suivante :

		using Microsoft.WindowsAzure.Mobile.Service;

3. Remplacez la définition de la classe par le code suivant :

	    public class Account : EntityData
	    {
	        public string Username { get; set; }
	        public byte[] Salt { get; set; }
	        public byte[] SaltedAndHashedPassword { get; set; }
	    }

    Cela représente une ligne dans une nouvelle table Account, qui contient le nom d'utilisateur, le sel de cet utilisateur et le mot de passe stocké de manière sécurisée.

2. Le dossier **Modèles** contient une classe dérivée **DbContext** nommée d'après votre service mobile. Ouvrez votre contexte et ajoutez la table de comptes à votre modèle de données en incluant le code suivant :

        public DbSet<Account> Accounts { get; set; }

	>[AZURE.NOTE]Les extraits de code dans ce didacticiel utilisent `todoContext` comme nom de contexte. Vous devez mettre à jour les extraits de code pour le contexte de votre projet. Ensuite, vous allez configurer les fonctions de sécurité pour utiliser ces données.

5. Créez une classe appelée `CustomLoginProviderUtils` et ajoutez l'instruction `using` suivante :

		using System.Security.Cryptography;

6. Ajoutez les méthodes de code suivantes à la nouvelle classe :


        public static byte[] hash(string plaintext, byte[] salt)
        {
            SHA512Cng hashFunc = new SHA512Cng();
            byte[] plainBytes = System.Text.Encoding.ASCII.GetBytes(plaintext);
            byte[] toHash = new byte[plainBytes.Length + salt.Length];
            plainBytes.CopyTo(toHash,0);
            salt.CopyTo(toHash, plainBytes.Length);
            return hashFunc.ComputeHash(toHash);
        }

        public static byte[] generateSalt()
        {
            RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();
            byte[] salt = new byte[256];
            rng.GetBytes(salt);
            return salt;
        }

        public static bool slowEquals(byte[] a, byte[] b)
        {
            int diff = a.Length ^ b.Length;
            for (int i = 0; i < a.Length && i < b.Length; i++)
            {
                diff |= a[i] ^ b[i];
            }
            return diff == 0;
        }

	Cela vous permet de générer un nouveau sel long, de hacher un mot de passe salé et fournit une solution sécurisée pour comparer deux hachages.

## Création du point de terminaison d'inscription

À ce stade, vous avez tout ce qu'il faut pour commencer à créer des comptes utilisateur. Dans cette section, vous allez configurer un point de terminaison d'inscription pour traiter les nouvelles demandes d'inscription. C'est là où vous appliquerez de nouvelles stratégies de nom d'utilisateur et de mot de passe, et ferez en sorte que le nom d'utilisateur ne soit pas pris. Vous stockerez ensuite en toute sécurité les informations utilisateur dans votre base de données.

1. Créez la classe suivante pour représenter une tentative d'inscription entrante :

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    Si vous avez besoin de collecter et de stocker d'autres informations pendant l'inscription, vous devez le faire ici.

2. Dans votre projet de backend du service mobile, cliquez avec le bouton droit sur **Contrôleurs**, cliquez sur **Ajouter** et **Contrôleur**, créez un **contrôleur personnalisé Microsoft Azure Mobile Services** nommé `CustomRegistrationController`, puis ajoutez les instructions `using` suivantes :

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Text.RegularExpressions;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

	Dans le code ci-dessus, remplacez l'espace réservé par l'espace de noms de votre projet.

4. Remplacez la définition de la classe par le code suivant :

	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomRegistrationController : ApiController
	    {
	        public ApiServices Services { get; set; }

	        // POST api/CustomRegistration
	        public HttpResponseMessage Post(RegistrationRequest registrationRequest)
	        {
	            if (!Regex.IsMatch(registrationRequest.username, "^[a-zA-Z0-9]{4,}$"))
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid username (at least 4 chars, alphanumeric only)");
	            }
	            else if (registrationRequest.password.Length < 8)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid password (at least 8 chars required)");
	            }

	            todoContext context = new todoContext();
	            Account account = context.Accounts.Where(a => a.Username == registrationRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "That username already exists.");
	            }
	            else
	            {
	                byte[] salt = CustomLoginProviderUtils.generateSalt();
	                Account newAccount = new Account
	                {
	                    Id = Guid.NewGuid().ToString(),
	                    Username = registrationRequest.username,
	                    Salt = salt,
	                    SaltedAndHashedPassword = CustomLoginProviderUtils.hash(registrationRequest.password, salt)
	                };
	                context.Accounts.Add(newAccount);
	                context.SaveChanges();
	                return this.Request.CreateResponse(HttpStatusCode.Created);
	            }
	        }
	    }

    N'oubliez pas de remplacer la variable *todoContext* par le nom du **DbContext** de votre projet. Notez que ce contrôleur utilise l'attribut suivant pour autoriser tout le trafic vers ce point de terminaison :

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]Ce point de terminaison d'inscription est accessible par n'importe quel client via HTTP. Avant de publier ce service dans un environnement de production, vous devez implémenter un schéma pour valider les enregistrements, tel qu'une vérification par SMS ou courrier électronique. Cela peut aider à empêcher un utilisateur malveillant de créer des inscriptions frauduleuses.

## Création du LoginProvider

**LoginProvider** est l'une des constructions fondamentales dans le pipeline d'authentification Mobile Services. Dans cette section, vous allez créer votre propre `CustomLoginProvider`. Il ne sera pas branché sur le pipeline comme les fournisseurs intégrés, mais vous offrira une fonctionnalité pratique. Si vous utilisez Visual Studio 2013, vous devrez peut-être installer le package nuget `WindowsAzure.MobileServices.Backend.Security` pour ajouter les références à la classe `LoginProvider`.

1. Créez une classe, `CustomLoginProvider`, qui dérive de **LoginProvider**, et ajoutez les instructions `using` suivantes :

	    using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Newtonsoft.Json.Linq;
		using Owin;
		using System.Security.Claims;

3. Remplacez la définition de la classe **CustomLoginProvider** par le code suivant :

        public class CustomLoginProvider : LoginProvider
        {
            public const string ProviderName = "custom";

            public override string Name
            {
                get { return ProviderName; }
            }

            public CustomLoginProvider(IServiceTokenHandler tokenHandler)
                : base(tokenHandler)
            {
                this.TokenLifetime = new TimeSpan(30, 0, 0, 0);
            }

        }

       Toute tentative de générer le projet maintenant est vouée à l'échec. `LoginProvider` a trois méthodes abstraites que vous devez implémenter, opération que vous effectuerez plus tard.

2. Créez une classe nommée `CustomLoginProviderCredentials` dans le même fichier de code.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

	Celle-ci représente des informations sur votre utilisateur et vous pourrez y accéder sur le backend via [GetIdentitiesAsync](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.serviceuser.getidentitiesasync.aspx). Si vous ajoutez des revendications personnalisées, assurez-vous qu'elles sont capturées dans cet objet.

3. Ajoutez l'implémentation suivante de la méthode abstraite `ConfigureMiddleware` à **CustomLoginProvider**.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

	Cette méthode n'est pas implémentée, car **CustomLoginProvider** n'intègre pas le pipeline d'authentification.

4. Ajoutez l'implémentation suivante de la méthode abstraite `ParseCredentials` à **CustomLoginProvider**.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }

	Cette méthode autorisera le backend à désérialiser les informations utilisateur d'un jeton d'authentification entrant.

5. Ajoutez l'implémentation suivante de la méthode abstraite `CreateCredentials` à **CustomLoginProvider**.

        public override ProviderCredentials CreateCredentials(ClaimsIdentity claimsIdentity)
        {
            if (claimsIdentity == null)
            {
                throw new ArgumentNullException("claimsIdentity");
            }

            string username = claimsIdentity.FindFirst(ClaimTypes.NameIdentifier).Value;
            CustomLoginProviderCredentials credentials = new CustomLoginProviderCredentials
            {
                UserId = this.TokenHandler.CreateUserId(this.Name, username)
            };

            return credentials;
        }

	Cette méthode traduit un objet [ClaimsIdentity] en un objet [ProviderCredentials] utilisé dans la phase d'émission du jeton d'authentification. Vous pouvez, ici aussi, capturer toutes les revendications supplémentaires dans cette méthode.

6. Ouvrez le fichier de projet WebApiConfig.cs dans le dossier App\_Start et la ligne de code suivante une fois que **ConfigOptions** est créé :

		options.LoginProviders.Add(typeof(CustomLoginProvider));



## Création du point de terminaison de connexion

Vous allez ensuite créer un point de terminaison pour que vos utilisateurs puissent se connecter. Le nom d'utilisateur et le mot de passe que vous recevrez sont vérifiés par rapport à la base de données. Cette vérification consiste à demander d'abord le sel de l'utilisateur, à hacher le mot de passe et à s'assurer que la valeur entrante correspond à celle de la base de données. Si c'est le cas, vous pourrez créer un [ClaimsIdentity] et le transmettre au **CustomLoginProvider**. L'application cliente reçoit un ID utilisateur et un jeton d'authentification pour accéder à votre service mobile.

1. Dans votre projet de backend du service mobile, créez la classe `LoginRequest` suivante :

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

	Cette classe représente une tentative de connexion entrante.

2. Créez la classe `CustomLoginResult` suivante :

	    public class CustomLoginResult
	    {
	        public string UserId { get; set; }
	        public string MobileServiceAuthenticationToken { get; set; }

	    }

	Cette classe représente une connexion établie avec l'ID utilisateur et le jeton d'authentification. Notez que cette classe a la même forme que la classe MobileServiceUser sur le client, ce qui facilite la transmission de la réponse de connexion sur un client fortement typé.

2. Cliquez avec le bouton droit sur **Contrôleurs**, cliquez sur **Ajouter** et **Contrôleur**, créez un **contrôleur personnalisé Microsoft Azure Mobile Services** nommé `CustomLoginController`, puis ajoutez les instructions `using` suivantes :

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Security.Claims;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

3. Remplacez la définition de la classe **CustomLoginController** par le code suivant :

	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomLoginController : ApiController
	    {
	        public ApiServices Services { get; set; }
	        public IServiceTokenHandler handler { get; set; }

	        // POST api/CustomLogin
	        public HttpResponseMessage Post(LoginRequest loginRequest)
	        {
	            todoContext context = new todoContext();
	            Account account = context.Accounts
	                .Where(a => a.Username == loginRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                byte[] incoming = CustomLoginProviderUtils
	                    .hash(loginRequest.password, account.Salt);

	                if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
	                {
	                    ClaimsIdentity claimsIdentity = new ClaimsIdentity();
	                    claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
	                    LoginResult loginResult = new CustomLoginProvider(handler)
	                        .CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
	                    var customLoginResult = new CustomLoginResult()
	                    {
	                        UserId = loginResult.User.UserId,
	                        MobileServiceAuthenticationToken = loginResult.AuthenticationToken
	                    };
	                    return this.Request.CreateResponse(HttpStatusCode.OK, customLoginResult);
	                }
	            }
	            return this.Request.CreateResponse(HttpStatusCode.Unauthorized,
	                "Invalid username or password");
	        }
	    }

       N'oubliez pas de remplacer la variable *todoContext* par le nom du **DbContext** de votre projet. Notez que ce contrôleur utilise l'attribut suivant pour autoriser tout le trafic vers ce point de terminaison :

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]Votre `CustomLoginController` destiné à la production doit également contenir une stratégie de détection en force brute. Sinon, votre solution de connexion risque d'être vulnérable aux attaques.

## Configuration du service mobile afin d'exiger une authentification

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## Test du flux de connexion pour le client test

Dans votre application cliente, vous devez développer un écran de connexion personnalisé qui prend les noms d'utilisateurs et les mots de passe, puis les envoie dans une charge utile JSON à vos points de terminaison d'inscription et de connexion. Pour les besoins de ce didacticiel, vous allez vous contenter d'utiliser le client test intégré pour le backend .NET Mobile Services.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet de service mobile, puis cliquez sur **Déboguer** et **Démarrer une nouvelle instance**.

	Cette opération démarre une nouvelle instance de débogage de votre projet de backend du service mobile. Une fois le service correctement démarré, une page de démarrage apparaît, indiquant que **ce service mobile est désormais opérationnel**.

2. Dans la page de démarrage du service, cliquez sur **Essayer**, puis tapez le mot de passe que vous avez défini pour le paramètre d'application **MS\_ApplicationKey** dans le fichier web.config avec un nom d'utilisateur vide dans la boîte de dialogue d'authentification.

3. Dans la page d'aide, cliquez sur le point de terminaison **CustomRegistration**, puis cliquez sur **Essayer**.

    ![][2]

4. Dans le corps, remplacez les chaînes de l'exemple par un nom d'utilisateur et un mot de passe qui satisfont aux critères que vous avez spécifiés précédemment, puis cliquez sur **Envoyer**.

    ![][3]

	La réponse est normalement **201/Créé**.

5. Cliquez sur le bouton Précédent du navigateur et répétez les étapes 2 et 3 pour le point de terminaison **CustomLogin**, en utilisant les nom d'utilisateur et mot de passe que vous avez enregistrés à l'étape précédente.

    ![][4]

	Vous devez obtenir un message de réponse dont le corps contient un objet JSON **user** qui possède à la fois le *userId* et un *authenticationToken*, jeton d'authentification Mobile Services généré par votre authentification personnalisée. Ce jeton est suffisant pour accorder l'accès de l'application cliente au point de terminaison TodoItem.

	Effectuez une copie de la valeur *authenticationToken*. Vous l'utiliserez pour accéder au point de terminaison TodoItem restreint.

6. Cliquez sur le bouton Précédent du navigateur, puis dans la page de documentation de l'API, cliquez sur **GetTables**, puis sur **Essayer**.

7. Dans la boîte de dialogue de requête GET, cliquez sur le signe plus à côté d'**En-têtes**, tapez la valeur `X-ZUMO-AUTH` dans la zone de gauche, collez la valeur *authenticationToken* copiée dans la zone de droite, puis cliquez sur **Envoyer**.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-endpoint.png)

	Le service mobile doit accorder l'accès au point de terminaison et retourner un état **200/OK** ainsi qu'une liste d'éléments TodoItems dans la table.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-success.png)

>[AZURE.IMPORTANT]Si vous choisissez de publier aussi ce projet de service mobile sur Azure à des fins de test, n'oubliez pas que vos fournisseurs d'authentification et de connexion sont vulnérables aux attaques. Assurez-vous qu'ils sont convenablement renforcés ou que les données de test protégées ne sont pas importantes pour vous. Faites preuve de beaucoup de prudence avant d'utiliser un schéma d'authentification personnalisé pour sécuriser un service de production.

## Connexion à l'aide de l'authentification personnalisée à partir du client

Cette section décrit les étapes nécessaires pour accéder aux points de terminaison d'authentification personnalisés à partir du client afin d'obtenir le jeton d'authentification permettant d'accéder au service mobile. Étant donné que le code client spécifique dont vous avez besoin dépend de votre client, les instructions fournies ici sont indépendantes de la plateforme.

>[AZURE.NOTE]Les bibliothèques clientes Mobile Services communiquent avec le service via HTTPS. Étant donné que cette solution vous oblige à envoyer des mots de passe en texte brut, vous devez vous assurer que vous utilisez HTTPS quand vous appelez ces points de terminaison à l'aide de demandes REST directes.

1. Créez les éléments d'interface utilisateur requis dans votre application cliente pour permettre aux utilisateurs d'entrer un nom d'utilisateur et un mot de passe.

2. Utilisez la méthode **invokeApi** appropriée sur le **MobileServiceClient** dans la bibliothèque cliente pour appeler le point de terminaison **CustomRegistration**, en passant le nom d'utilisateur et le mot de passe fournis par le runtime dans le corps du message.

	Vous ne devez appeler le point de terminaison **CustomRegistration** qu'une seule fois pour créer un compte pour un utilisateur donné, tant que vous conservez les informations de connexion de l'utilisateur dans la table Accounts. Pour obtenir des exemples montrant comment appeler une API personnalisée sur les différentes plateformes clientes prises en charge, consultez l'article [Custom API in Azure Mobile Services – client SDKs](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

	> [AZURE.IMPORTANT]Étant donné que cette étape de configuration de l'utilisateur se produit une seule fois, vous devez envisager de créer le compte d'utilisateur de manière « hors bande ». Pour un point de terminaison d'inscription public, vous devez également envisager de mettre en œuvre un processus de vérification par courrier électronique ou SMS ou une autre mesure pour empêcher la génération de comptes frauduleux. Vous pouvez utiliser Twilio pour envoyer des messages SMS à partir de Mobile Services. Vous pouvez également utiliser SendGrid pour envoyer des messages électroniques à partir de Mobile Services. Pour plus d’informations sur l’utilisation de SendGrid, consultez [Envoi de courrier électronique à partir de Mobile Services avec SendGrid](store-sendgrid-mobile-services-send-email-scripts.md).

3. Réutilisez la méthode **invokeApi** appropriée, cette fois pour appeler le point de terminaison **CustomLogin**, en passant le nom d'utilisateur et le mot de passe fournis par le runtime dans le corps du message.

	Cette fois, vous devez capturer les valeurs *userId* et *authenticationToken* retournées dans l'objet de réponse après une connexion réussie.

4. Utilisez les valeurs *userId* et *authenticationToken* retournées pour créer un objet **MobileServiceUser** et le définir en tant qu'utilisateur actuel pour votre instance **MobileServiceClient**, comme indiqué dans la rubrique [Ajout de l'authentification à une application existante](mobile-services-dotnet-backend-ios-get-started-users.md). Étant donné que le résultat CustomLogin présente la même forme que l'objet **MobileServiceUser**, vous devez être en mesure d'effectuer un cast direct du résultat.

C'est ici que s'achève ce didacticiel.


<!-- Anchors. -->


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[Ajout de l'authentification à votre application]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

[ClaimsIdentity]: https://msdn.microsoft.com/library/system.security.claims.claimsidentity(v=vs.110).aspx
[ProviderCredentials]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.providercredentials.aspx

<!---HONumber=AcomDC_0107_2016-->