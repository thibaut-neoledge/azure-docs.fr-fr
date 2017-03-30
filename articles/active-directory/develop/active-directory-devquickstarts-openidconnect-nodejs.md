---
title: "Prise en main de la connexion et de la déconnexion d’Azure AD à l’aide de Node.js | Microsoft Docs"
description: "Découvrez comment créer une application web Node.js Express MVC qui s’intègre avec Azure AD pour la connexion."
services: active-directory
documentationcenter: nodejs
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 27f9c5a18b85c0cc2f918ccefeb063f58cc967c6
ms.lasthandoff: 03/18/2017


---
# <a name="nodejs-web-app-sign-in-and-sign-out-with-azure-ad"></a>Connexion et déconnexion d’Azure AD à l’aide d’une application web Node.js
Nous utilisons Passport pour :

* connecter l’utilisateur à l’application avec Azure Active Directory (Azure AD) ;
* afficher les informations sur l’utilisateur ;
* déconnecter l’utilisateur de l’application.

Passport est un intergiciel d’authentification pour Node.js. Flexible et modulaire, Passport peut discrètement intervenir dans n’importe quelle application web basée sur Express ou Restify. Une gamme complète de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et d’un mot de passe, de Facebook, de Twitter et bien d’autres. Nous avons développé une stratégie pour Microsoft Azure Active Directory. Nous installons ce module, puis nous y ajoutons le plug-in `passport-azure-ad` Microsoft Azure Active Directory.

Pour cela, suivez les étapes ci-dessous :

1. inscrire une application ;
2. configurer votre application pour utiliser la stratégie `passport-azure-ad` ;
3. utiliser Passport pour émettre des demandes de connexion et de déconnexion dans Azure AD ;
4. imprimer les données relatives à l’utilisateur.

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS).  Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) ou la cloner :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

L'application terminée est également fournie à la fin de ce didacticiel.

## <a name="step-1-register-an-app"></a>Étape 1 : enregistrement d’une application
1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu situé en haut de la page, sélectionnez votre compte. Dans la liste **Répertoire**, choisissez le locataire Active Directory auprès duquel vous voulez inscrire votre application.

3. Sélectionnez **Plus de services** dans le menu sur la gauche de l’écran, puis sélectionnez **Azure Active Directory**.

4. Sélectionnez **Inscriptions d’applications**, puis **Ajouter**.

5. Suivez les invites et créez une **application web** et/ou une **API web**.
  * Le **nom** de l’application doit décrire votre application aux utilisateurs.

  * L’ **URL de connexion** est l’URL de base de votre application.  La valeur par défaut de la structure est « http://localhost:3000/auth/openid/return ».

6. Une fois l’application enregistrée, Azure AD lui affecte un ID d’application unique. Copiez cette valeur à partir de la page de l’application, car vous en aurez besoin dans les sections suivantes.
7. À partir de la page **Paramètres** -> **Propriétés** de votre application, mettez à jour l’URI ID d’application. Un **URI ID d’application** est un identificateur unique pour votre application. La convention consiste à utiliser le format `https://<tenant-domain>/<app-name>`, par exemple : `https://contoso.onmicrosoft.com/my-first-aad-app`.

## <a name="step-2-add-prerequisites-to-your-directory"></a>Étape 2 : ajout d’éléments requis au répertoire
1. Dans la ligne de commande, placez les répertoires dans votre dossier racine s’ils n’y sont pas encore, puis exécutez les commandes suivantes :

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. De plus, vous avez besoin de `passport-azure-ad` :
    * `npm install passport-azure-ad`

Cela installe les bibliothèques dont dépend `passport-azure-ad`.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Étape 3 : configuration de votre application pour utiliser la stratégie passport-nod-js
Ici, nous configurons Express pour utiliser le protocole d’authentification OpenID Connect.  Passport est utilisé pour plusieurs choses, notamment pour émettre des demandes de connexion et de déconnexion, gérer la session utilisateur et obtenir des informations concernant l’utilisateur.

1. Pour commencer, ouvrez le fichier `config.js` dans la racine du projet, puis entrez les valeurs de configuration de votre application dans la section `exports.creds`.

  * L’élément `clientID` est l’**ID d’application** affecté à votre application dans le portail d’inscription.

  * L’élément `returnURL` est l’**URI de redirection** que vous avez saisie dans le portail.

  * L’élément `clientSecret` est la clé secrète que vous avez générée dans le portail.

2. Ouvrez ensuite le fichier `app.js` à la racine du projet. Ajoutez l’appel suivant pour appeler la stratégie `OIDCStrategy` qui accompagne `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. Après cela, utilisez la stratégie référencée pour gérer les demandes de connexion.

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
        // asynchronous verification, for effect...
        process.nextTick(function () {
        findByEmail(profile.email, function(err, user) {
            if (err) {
            return done(err);
            }
            if (!user) {
            // "Auto-registration"
            users.push(profile);
            return done(null, profile);
            }
            return done(null, user);
        });
        });
    }
    ));
    ```
Passport utilise un modèle semblable pour toutes ses stratégies (Twitter, Facebook, etc.), que respectent tous les enregistreurs de stratégie. Comme vous pouvez le voir dans la stratégie, nous transmettons une fonction dont les paramètres sont un jeton et un done. La stratégie nous revient une fois le travail effectué. Il est alors intéressant de stocker l’utilisateur et le jeton afin de ne pas avoir à les redemander.

    > [!IMPORTANT]
    > The previous code takes any user that happens to authenticate to our server. This is known as auto-registration. We recommend that you don't let anyone authenticate to a production server without first having them register via a process that you decide on. This is usually the pattern you see in consumer apps, which allow you to register with Facebook but then ask you to provide additional information. If this weren't a sample application, we could have extracted the user's email address from the token object that is returned and then asked the user to fill out additional information. Because this is a test server, we add them to the in-memory database.
>
>

4. Ensuite, nous allons ajouter les méthodes qui assurent le suivi des utilisateurs connectés, comme requis par Passport. Ces méthodes incluent la sérialisation et la désérialisation des informations d’utilisateur.

    ```JavaScript

            // Passport session setup. (Section 2)

            //   To support persistent sign-in sessions, Passport needs to be able to
            //   serialize users into the session and deserialize them out of the session. Typically,
            //   this is done simply by storing the user ID when serializing and finding
            //   the user by ID when deserializing.
            passport.serializeUser(function(user, done) {
            done(null, user.email);
            });

            passport.deserializeUser(function(id, done) {
            findByEmail(id, function (err, user) {
                done(err, user);
            });
            });

            // array to hold signed-in users
            var users = [];

            var findByEmail = function(email, fn) {
            for (var i = 0, len = users.length; i < len; i++) {
                var user = users[i];
            log.info('we are using user: ', user);
                if (user.email === email) {
                return fn(null, user);
                }
            }
            return fn(null, null);
            };
            ```

5.  Next, let's add the code to load the Express engine. Here we use the default /views and /routes pattern that Express provides.

    ```JavaScript

        // configure Express (section 2)

            var app = express();
            app.configure(function() {
          app.set('views', __dirname + '/views');
          app.set('view engine', 'ejs');
          app.use(express.logger());
          app.use(express.methodOverride());
          app.use(cookieParser());
          app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
          app.use(bodyParser.urlencoded({ extended : true }));
          // Initialize Passport!  Also use passport.session() middleware, to support
          // persistent login sessions (recommended).
          app.use(passport.initialize());
          app.use(passport.session());
          app.use(app.router);
          app.use(express.static(__dirname + '/../../public'));
        });

        ```

6. Finally, let's add the routes that hand off the actual sign-in requests to the `passport-azure-ad` engine:

```JavaScript

        // Our Auth routes (section 3)

        // GET /auth/openid
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. The first step in OpenID authentication involves redirecting
        //   the user to their OpenID provider. After authenticating, the OpenID
        //   provider redirects the user back to this application at
        //   /auth/openid/return.
        app.get('/auth/openid',
        passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
        function(req, res) {
            log.info('Authentication was called in the Sample');
            res.redirect('/');
        });

            // GET /auth/openid/return
            //   Use passport.authenticate() as route middleware to authenticate the
            //   request. If authentication fails, the user is redirected back to the
            //   sign-in page. Otherwise, the primary route function is called,
            //   which, in this example, redirects the user to the home page.
            app.get('/auth/openid/return',
              passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
              function(req, res) {
                log.info('We received a return from AzureAD.');
                res.redirect('/');
              });

            // POST /auth/openid/return
            //   Use passport.authenticate() as route middleware to authenticate the
            //   request. If authentication fails, the user is redirected back to the
            //   sign-in page. Otherwise, the primary route function is called,
            //   which, in this example, redirects the user to the home page.
            app.post('/auth/openid/return',
              passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
              function(req, res) {
                log.info('We received a return from AzureAD.');
                res.redirect('/');
              });
          ```


## Step 4: Use Passport to issue sign-in and sign-out requests to Azure AD
Your app is now properly configured to communicate with the endpoint by using the OpenID Connect authentication protocol.  `passport-azure-ad` has taken care of all the details of crafting authentication messages, validating tokens from Azure AD, and maintaining user sessions. All that remains is giving your users a way to sign in and sign out, and gathering additional information about the signed-in users.

1. First, let's add the default, sign-in, account, and sign-out methods to our `app.js` file:

    ```JavaScript

        //Routes (section 4)

        app.get('/', function(req, res){
          res.render('index', { user: req.user });
        });

        app.get('/account', ensureAuthenticated, function(req, res){
          res.render('account', { user: req.user });
        });

        app.get('/login',
          passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
          function(req, res) {
            log.info('Login was called in the Sample');
            res.redirect('/');
        });

        app.get('/logout', function(req, res){
          req.logout();
          res.redirect('/');
        });

    ```

2.  Examinons-les en détail :

  * L’itinéraire `/` redirige vers la vue index.ejs en transmettant l’utilisateur dans la demande (le cas échéant).
  * L’itinéraire `/account` *s’assure d’abord que nous sommes authentifiés* (nous implémentons cela dans l’exemple suivant), puis transmet l’utilisateur dans la demande afin que nous puissions obtenir des informations supplémentaires sur l’utilisateur.
  * L’itinéraire `/login` appelle notre authentificateur azuread-openidconnect à partir de `passport-azuread`. En cas d’échec, il redirige l’utilisateur vers la page de connexion.
  * L’itinéraire `/logout` appelle simplement logout.ejs (et l’itinéraire) qui efface les cookies, puis renvoie l’utilisateur à index.ejs.

3. Pour la dernière partie de `app.js`, ajoutons la méthode **EnsureAuthenticated** utilisée dans `/account`, comme indiqué ci-dessus.

    ```JavaScript

        // Simple route middleware to ensure user is authenticated. (section 4)

        //   Use this route middleware on any resource that needs to be protected. If
        //   the request is authenticated (typically via a persistent sign-in session),
        //   the request proceeds. Otherwise, the user is redirected to the
        //   sign-in page.
        function ensureAuthenticated(req, res, next) {
          if (req.isAuthenticated()) { return next(); }
          res.redirect('/login')
        }
    ```

4. Enfin, créons le serveur proprement dit dans `app.js` :

```JavaScript

        app.listen(3000);

```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Étape 5 : création de vues et d’itinéraires dans Express pour afficher notre utilisateur dans le site web
`app.js` est désormais terminé. Il suffit d'ajouter les itinéraires et les vues qui affichent les informations que nous obtenons de l'utilisateur et traitent les itinéraires `/logout` et `/login` que nous avons créés.

1. Créez l’itinéraire `/routes/index.js` sous le répertoire racine.

    ```JavaScript
                /*
                 * GET home page.
                 */

                exports.index = function(req, res){
                  res.render('index', { title: 'Express' });
                };
                ```

2. Create the `/routes/user.js` route under the root directory.

                ```JavaScript
                /*
                 * GET users listing.
                 */

                exports.list = function(req, res){
                  res.send("respond with a resource");
                };
        ```

 These pass along the request to our views, including the user if present.

3. Create the `/views/index.ejs` view under the root directory. This is a simple page that calls our login and logout methods and enables us to grab account information. Notice that we can use the conditional `if (!user)` as the user being passed through in the request is evidence we have a signed-in user.

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

4. Créez la vue `/views/account.ejs` sous le répertoire racine, afin d’afficher les informations supplémentaires que `passport-azuread` a placées dans la demande de l’utilisateur.

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. Améliorons-en l’apparence à l’aide d’une mise en page. Créez l’affichage « /views/layout.ejs » sous le répertoire racine.

    ```HTML

    <!DOCTYPE html>
    <html>
        <head>
            <title>Passport-OpenID Example</title>
        </head>
        <body>
            <% if (!user) { %>
                <p>
                <a href="/">Home</a> |
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

##<a name="next-steps"></a>Étapes suivantes
Enfin, générez et exécutez votre application. Exécutez `node app.js`, puis accédez à `http://localhost:3000`.

Connectez-vous avec un compte Microsoft personnel ou un compte professionnel ou scolaire, et notez comment l’identité de l’utilisateur est indiquée dans la liste de comptes. Vous disposez désormais d’une application web sécurisée à l’aide de protocoles standard et pouvant authentifier les utilisateurs avec leurs comptes personnels et professionnels/scolaires.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) [est fourni en tant que fichier .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Vous pouvez maintenant aborder des rubriques plus sophistiquées. Par exemple :

[Sécurisation d’une API web avec Azure AD](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

