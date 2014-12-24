<properties urlDisplayName="Get started with custom authentication" pageTitle="Prise en main de l'authentification personnalisée | Centre de développement mobile" metaKeywords="" description="Learn how to authenticate users with a username and password." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with custom authentication" authors="mahender" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="mahender" />

# Prise en main de l'authentification personnalisée

Cette rubrique explique comment authentifier les utilisateurs dans le service principal .NET d'Azure Mobile Services en émettant votre propre jeton d'authentification Mobile Services. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide en utilisant un nom d'utilisateur et un mot de passe personnalisés pour votre application.

>[WACOM.NOTE] Ce didacticiel présente une méthode avancée d'authentification de vos services mobiles avec des informations d'identification personnalisées. Le recours aux fournisseurs d'identité des réseaux sociaux intégrés sera mieux adapté pour de nombreuses applications, les utilisateurs pouvant se connecter via Facebook, Twitter, Google, un compte Microsoft ou Azure Active Directory. S'il s'agit de votre première authentification dans Mobile Services, consultez le didacticie [Prise en main des ] utilisateurs

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1. [Configuration de la table des comptes]
2. [Création du point de terminaison d'inscription]
3. [Création du LoginProvider]
4. [Création du point de terminaison de connexion]
5. [Configuration du service mobile afin d'exiger une authentification]
6. [Test du flux de connexion pour le client test]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel  [Prise en main de Mobile Services]. 

>[WACOM.NOTE] Le but de ce didacticiel est de vous montrer comment émettre un jeton d'authentification pour Mobile Services. Il ne doit pas être considéré comme un guide de sécurité. Pendant le développement de votre application, vous devez être conscient des implications du stockage des mots de passe en matière de sécurité et disposer d'une stratégie pour gérer les attaques en force brute.


## <a name="table-setup"></a>Configuration de la table des comptes

Dans la mesure où vous utilisez l'authentification personnalisée et ne dépendez d'aucun autre fournisseur d'identité, vous devez stocker les informations de connexion de vos utilisateurs. Dans cette section, vous allez créer une table pour vos comptes et configurer les mécanismes de sécurité de base. La table des comptes contiendra les noms d'utilisateur ainsi que les mots de passe salés et hachés, et vous pourrez également inclure d'autres informations utilisateur, si besoin est.

1. Dans le dossier `DataObjects` de votre projet principal, créez une nouvelle entité appelée `Compte` :

            public class Account : EntityData
            {
                public string Username { get; set; }
                public byte[] Salt { get; set; }
                public byte[] SaltedAndHashedPassword { get; set; }
            }
    
    Cette entité représente une ligne dans notre nouvelle table, contenant le nom d'utilisateur, la valeur salée de cet utilisateur et le mot de passe stocké de manière sécurisée.

2. Le dossier `Modèles` contient une classe `DbContext` nommée d'après votre service mobile. Dans la suite de ce didacticiel, nous allons utiliser `todoContext` comme exemple et vous allez devoir mettre à jour des extraits de code en conséquence. Ouvrez votre contexte et ajoutez la table de comptes à votre modèle de données en incluant le code suivant :

        public DbSet<Account> Accounts { get; set; }

3. Ensuite, vous allez configurer les fonctions de sécurité pour utiliser ces données. Vous aurez besoin d'un moyen de générer un nouveau sel long, de la possibilité de hacher un mot de passe salé et d'une solution sécurisée pour comparer deux hachages. Créez une classe appelée `CustomLoginProviderUtils` et ajoutez-y les méthodes suivantes :


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


## <a name="register-endpoint"></a>Création du point de terminaison d'inscription

À ce stade, vous avez tout ce qu'il faut pour commencer à créer des comptes utilisateur. Dans cette section, vous allez configurer un point de terminaison d'inscription pour traiter les nouvelles demandes d'inscription. C'est là où vous appliquerez de nouvelles stratégies de nom d'utilisateur et de mot de passe, et ferez en sorte que le nom d'utilisateur ne soit pas pris. Vous stockerez ensuite en toute sécurité les informations utilisateur dans votre base de données.

1. Créez un objet pour représenter une tentative d'inscription entrante :

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    Si vous voulez collecter d'autres informations au moment de l'inscription, vous pouvez les inclure ici.

1. Dans votre projet de service principal Mobile Services, ajoutez un nouveau contrôleur personnalisé nommé CustomRegistrationController et collez-le dans le code suivant :

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
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Username already exists");
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

    Veillez à autoriser tout le trafic vers ce point de terminaison en décorant le contrôleur avec :

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

## <a name="login-provider"></a>Création du LoginProvider

La `LoginProvider` est l'une des constructions fondamentales dans le pipeline d'authentification Mobile Services. Dans cette section, vous allez créer votre propre `CustomLoginProvider`. Il ne sera pas branché sur le pipeline comme les fournisseurs intégrés, mais vous offrira une fonctionnalité pratique.

1. Créez une nouvelle classe, `CustomLoginProvider`, qui dérive de `LoginProvider` :

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

       `LoginProvider` has three other abstract methods which you will implement later.

2. Créez une classe nommée `CustomLoginProviderCredentials`. Celle-ci représente des informations sur votre utilisateur et vous pourrez y accéder sur le serveur principal via `ServiceUser.getIdentitiesAsync()`. Si vous ajoutez des revendications personnalisées, assurez-vous qu'elles sont capturées dans cet objet.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

3. Ajoutez l'implémentation suivante de la méthode abstraite `ConfigureMiddleware` à `CustomLoginProvider`. Cette méthode est ici une instruction nulle (no-op), puisque `CustomLoginProvider` n'est pas intégré au pipeline d'authentification.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

4. Ajoutez l'implémentation suivante de la méthode abstraite `ParseCredentials` à `CustomLoginProvider`. Cette méthode autorisera le serveur principal à désérialiser les informations utilisateur d'un jeton d'authentification entrant.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }


5. Ajoutez l'implémentation suivante de la méthode abstraite `CreateCredentials` à `CustomLoginProvider`. Cette méthode traduit un objet `ClaimsIdentity` en un objet `ProviderCredentials` utilisé dans la phase d'émission du jeton d'authentification. Vous pouvez, ici aussi, capturer toutes les revendications supplémentaires.

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

## <a name="login-endpoint"></a>Création du point de terminaison de connexion

Vous allez ensuite créer un point de terminaison pour que vos utilisateurs puissent se connecter. Le nom d'utilisateur et le mot de passe que vous recevrez seront vérifiés par rapport à la base de données. Cette vérification consistera à demander d'abord le sel de l'utilisateur, à hacher le mot de passe et à s'assurer que la valeur entrante correspond à celle de la base de données. Si c'est le cas, vous pourrez créer un `ClaimsIdentity` et le transmettre au `CustomLoginProvider`. L'application cliente recevra alors un ID utilisateur et un jeton d'authentification pour accéder à votre service mobile.

1. Dans votre projet de service principal Mobile Services, créez un objet pour représenter une tentative de connexion entrante :

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

1. Ajoutez un nouveau contrôleur personnalisé appelé `CustomLoginController` et collez-le dans le code suivant :

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomLoginController : ApiController
        {
            public ApiServices Services { get; set; }
            public IServiceTokenHandler handler { get; set; }

            // POST api/CustomLogin
            public HttpResponseMessage Post(LoginRequest loginRequest)
            {
                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == loginRequest.username).SingleOrDefault();
                if (account != null)
                {
                    byte[] incoming = CustomLoginProviderUtils.hash(loginRequest.password, account.Salt);

                    if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
                    {
                        ClaimsIdentity claimsIdentity = new ClaimsIdentity();
                        claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
                        LoginResult loginResult = new CustomLoginProvider(handler).CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
                        return this.Request.CreateResponse(HttpStatusCode.OK, loginResult);
                    }
                }
                return this.Request.CreateResponse(HttpStatusCode.Unauthorized, "Invalid username or password");
            }
        }

    Veillez à autoriser tout le trafic vers ce point de terminaison en décorant le contrôleur avec :

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[WACOM.NOTE] Votre `CustomLoginController` destiné à la production doit également contenir une stratégie de détection en force brute. Sinon, votre solution de connexion risque d'être vulnérable aux attaques.

## <a name="require-authentication"></a>Configuration du service mobile afin d'exiger une authentification

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## <a name="test-login"></a>Test du flux de connexion pour le client test

Dans votre application cliente, vous devrez développer un écran de connexion personnalisé qui prend les noms d'utilisateurs et les mots de passe, puis les envoie dans une charge utile JSON à vos points de terminaison d'inscription et de connexion. Pour les besoins de ce didacticiel, vous allez vous contenter d'utiliser le client test intégré pour le service principal .NET de Mobile Services.

>[WACOM.NOTE] Les Kits de développement logiciel (SDK) Mobile Services communiquent avec le service via HTTPS. Si vous prévoyez d'accéder à ce point de terminaison via un appel REST direct, vous devez veiller à utiliser HTTPS pour appeler votre service mobile, car les mots de passe sont envoyés en clair.

1. Dans Visual Studio, démarrez une nouvelle instance de débogage de votre projet de service principal Mobile Services en cliquant avec le bouton droit sur le projet et en sélectionnant **Déboguer->Démarrer une nouvelle instance**

    ![][0]

2. Cliquez sur **Faire un essai**

    ![][1]

3. Sélectionnez votre point de terminaison d'inscription. La documentation de base concernant votre API s'affiche. Cliquez sur **Faire un essai**.

    ![][2]

4. Dans le corps, remplacez les chaînes de l'exemple par un nom d'utilisateur et un mot de passe qui satisfont aux critères que vous avez spécifiés précédemment. Cliquez ensuite sur **Envoyer**. La réponse est normalement **201/Créé**.

    ![][3]

5. Répétez la procédure pour votre point de terminaison de connexion. Après avoir envoyé le nom d'utilisateur et le mot de passe que vous avez inscrits précédemment, vous devez recevoir votre ID d'utilisateur et un jeton d'authentification.

    ![][4]


<!-- Anchors. -->
[Configuration de la table des comptes]: #table-setup
[Création du point de terminaison d'inscription]: #register-endpoint
[Création du LoginProvider]: #login-provider
[Création du point de terminaison de connexion]: #login-endpoint
[Configuration du service mobile afin d'exiger une authentification]: #require-authentication
[Test du flux de connexion pour le client test]: #test-login


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[Prise en main des utilisateurs]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
