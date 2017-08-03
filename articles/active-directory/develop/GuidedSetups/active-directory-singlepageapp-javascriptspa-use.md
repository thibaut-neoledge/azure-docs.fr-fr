
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Utiliser la bibliothèque d’authentification Microsoft (MSAL) afin d’obtenir un jeton pour l’API Microsoft Graph

Ajoutez le code suivant à votre `index.html` dans la balise `<body>` :

```html
<script type="text/javascript">

    var graphAPIMeEndpoint = "https://graph.microsoft.com/v1.0/me";
    var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

    // Initialize application
    var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID);

    // Set redirect URI
    userAgentApplication.redirectUri = msalconfig.redirectUri;

    displayUserInfo();

    function displayUserInfo() {
        var user = userAgentApplication.getUser();
        if (user) {
            // Display the user info
            var userInfoElement = document.getElementById("userInfo");
            userInfoElement.parentElement.classList.remove("hidden");
            userInfoElement.innerHTML = JSON.stringify(user, null, 4);

            // Show Sign-Out button
            document.getElementById("signOutButton").classList.remove("hidden");
        }
    }

    function callGraphAPI() {
        if (userAgentApplication.getAllUsers().length === 0) {
            userAgentApplication.loginPopup()
                .then(function (token) {
                    console.log(token);
                    displayUserInfo();
                    callGraphAPI();
                }, function (error) {
                    showError("login", error, document.getElementById("errorMessage"));
                });
        } else {
            var responseElement = document.getElementById("graphResponse");
            responseElement.parentElement.classList.remove("hidden");
            responseElement.innerText = "Calling Graph ...";
            callWebApiWithScope(graphAPIMeEndpoint,
                graphAPIScopes,
                responseElement,
                document.getElementById("errorMessage"),
                document.getElementById("accessToken"));
        }
    }

    function callWebApiWithScope(endpoint, scope, responseElement, errorElement, showTokenElement) {
        userAgentApplication.acquireTokenSilent(scope)
            .then(function (token) {
                callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
            }, function (error) {
                if (error.indexOf("interaction_required" !== -1)) {
                    userAgentApplication.acquireTokenPopup(scope).then(function(token) {
                            callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
                        },
                        function(error) {
                            showError(endpoint, error, errorElement);
                        });
                } else {
                    showError(endpoint, error, errorElement);
                }
            });
    }

    function showAPIResponse(data, token, responseElement, showTokenElement) {
        console.log(data);
        responseElement.innerHTML = JSON.stringify(data, null, 4);
        if (showTokenElement) {
            showTokenElement.parentElement.classList.remove("hidden");
            showTokenElement.innerHTML = token;
        }
    }

    function showError(endpoint, error, errorElement) {
        console.error(error);
        var formattedError = JSON.stringify(error, null, 4);
        if (formattedError.length < 3) {
            formattedError = error;
        }
        errorElement.innerHTML = "Error calling " + endpoint + ": " + formattedError;
    }
</script>
```

<!--start-collapse-->
### <a name="more-information"></a>Informations complémentaires
#### <a name="sign-in-the-user"></a>Connexion de l’utilisateur
Quand un utilisateur clique sur le bouton « Call Microsoft Graph API » pour la première fois, `loginPopup` est exécuté pour connecter l’utilisateur. Cette méthode affiche une fenêtre de navigateur invitant l’utilisateur à se connecter. Une fois l’utilisateur connecté, les informations utilisateur sont mises en cache par MSAL et un jeton est retourné. Ce jeton est le *jeton d’ID* et contient des informations de base sur l’utilisateur, telles que son nom d’affichage. Si vous envisagez d’utiliser les données fournies par ce jeton à toutes fins que ce soit, vous devez vous assurer que ce jeton est validé par le serveur principal afin de garantir qu’il a été émis pour un utilisateur valide de votre application.

L’application SPA créée dans ce guide n’utilise pas directement le jeton d’ID. En effet, elle appelle `acquireTokenSilent` et/ou `acquireTokenPopup` pour acquérir un *jeton d’accès* utilisé pour interroger l’API Microsoft Graph. Si vous avez besoin d’un exemple qui valide le jeton d’ID, regardez [cet exemple d’application](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "exemple Github active-directory-javascript-singlepageapp-dotnet-webapi-v2") dans GitHub. Cet exemple utilise une API web ASP.NET pour la validation du jeton.

#### <a name="getting-a-user-token-interactively"></a>Obtention d’un jeton d’utilisateur de manière interactive
L’appel de la méthode `acquireTokenPopup(scope)` affiche une fenêtre de navigateur invitant l’utilisateur à se connecter. En général, les applications obligent un utilisateur à se connecter de manière interactive la première fois qu’elles doivent accéder à une ressource protégée ou lorsqu’une opération silencieuse d’acquisition de jeton échoue (par exemple, en cas d’expiration du mot de passe de l’utilisateur).

#### <a name="getting-a-user-token-silently"></a>Obtention d’un jeton d’utilisateur en mode silencieux
La méthode ` acquireTokenSilent` gère les acquisitions et renouvellements de jetons sans aucune interaction de l’utilisateur. Quand `loginPopup` est exécuté pour la première fois, c’est en général la méthode `acquireTokenSilent` qui est utilisée pour obtenir les jetons permettant d’accéder aux ressources protégées pour les appels suivants, ainsi que pour les demandes ou renouvellements de jetons en mode silencieux.
`acquireTokenSilent` finira par échouer, si l’utilisateur a modifié son mot de passe sur un autre appareil, par exemple. Votre application peut gérer cette exception de deux manières :

1.  En appelant immédiatement `acquireTokenPopup`, suite à quoi l’utilisateur est invité à se connecter. Cette méthode est généralement employée avec les applications en ligne dans lesquelles aucun contenu hors connexion n’est disponible pour l’utilisateur. L’exemple créé avec cette installation guidée utilise ce modèle.
2. Les applications peuvent également signaler à l’utilisateur qu’une connexion interactive est requise afin qu’il puisse choisir le bon moment pour se connecter ou que l’application puisse réessayer d’exécuter `acquireTokenSilent` ultérieurement. Cette solution est généralement retenue lorsque l’utilisateur peut utiliser d’autres fonctionnalités de l’application sans être perturbé, notamment lorsque du contenu hors connexion est disponible dans l’application. Dans ce cas, l’utilisateur peut décider du moment où il se connecte pour accéder à la ressource protégée ou pour actualiser les informations obsolètes, ou votre application peut choisir de réexécuter `acquireTokenSilent` après une indisponibilité temporaire du réseau.

`acquireTokenSilent` va également échouer si l’utilisateur n’a pas encore donné son consentement pour l’étendue demandée par l’appel. Par exemple, la première fois qu’un utilisateur exécute l’application créée par ce guide, l’étendue *user.read* est fournie en tant que paramètre. Étant donné que l’utilisateur connecté n’a pas donné son consentement pour lire son profil, `acquireTokenSilent` génère une exception contenant `interaction_required`, ce qui indique généralement que `acquireTokenPopup` doit être appelé pour résoudre le problème. Dans ce cas, une fenêtre contextuelle est créée pour afficher l’écran de consentement de l’étendue demandée.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Appeler l’API Microsoft Graph à l’aide du jeton que vous venez d’obtenir

Ajoutez le code suivant à votre index.html dans la balise `<body>` :

```html
 <script type="text/javascript">
function callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    // Note that fetch API is not available in all browsers
    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showAPIResponse(data, token, responseElement, showTokenElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showError(endpoint, data, errorElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error, errorElement);
        });
}
</script>
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Plus d’informations sur l’envoi d’un appel REST à une API protégée

Dans l’exemple d’application créé par ce guide, la méthode `callWebApiWithToken()` est utilisée pour envoyer une demande HTTP `GET` à une ressource protégée qui nécessite un jeton, puis pour retourner le contenu à l’appelant. Cette méthode ajoute le jeton acquis à l’*en-tête d’autorisation HTTP*. Dans l’exemple d’application créé par ce guide, la ressource est le point de terminaison *me* de l’API Microsoft Graph, qui affiche les informations de profil de l’utilisateur.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Ajouter une méthode pour déconnecter l’utilisateur

Ajoutez le code suivant à votre index.html dans la balise `<body>` :

```html
 <script type="text/javascript">
    function signOut() {
        userAgentApplication.logout();
    }
</script>
```
