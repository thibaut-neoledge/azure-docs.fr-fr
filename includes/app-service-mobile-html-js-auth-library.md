##<a name="server-auth"></a>Procédure : authentification auprès d’un fournisseur (flux serveur)

Pour que les Mobile Services gèrent le processus d’authentification dans votre application, vous devez inscrire votre application auprès de votre fournisseur d’identité. Ensuite, dans Azure App Service, vous devez configurer l’ID d’application et le secret fournis par votre fournisseur. Pour plus d’informations, consultez le didacticiel [Ajout de l’authentification à votre application].

Une fois que vous avez inscrit votre fournisseur d’identité, appelez simplement la méthode .login() avec le nom de votre fournisseur. Par exemple, pour vous connecter avec Facebook, utilisez le code suivant.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Si vous utilisez un fournisseur d’identité différent de Facebook, remplacez la valeur transmise à la méthode login ci-dessus par l’une des valeurs suivantes : `microsoftaccount`, `facebook`, `twitter`, `google` ou `aad`.

Dans ce cas, Azure App Service gère le flux d’authentification OAuth 2.0 en affichant la page de connexion du fournisseur sélectionné et en générant un jeton d’authentification App Service après avoir établi une connexion avec le fournisseur d’identité. La fonction login, quand elle est utilisée, renvoie un objet JSON (user) qui expose l’ID utilisateur et le jeton d’authentification App Service dans les champs userId et authenticationToken, respectivement. Ce jeton peut être mis en cache et réutilisé jusqu'à ce qu'il arrive à expiration.

##<a name="client-auth"></a>Procédure : authentification auprès d’un fournisseur (flux client)

Votre application peut également contacter le fournisseur d’identité de manière indépendante, puis fournir le jeton renvoyé à App Service à des fins d’authentification. Le flux client permet de proposer l'authentification unique aux utilisateurs ou de récupérer d'autres données utilisateur auprès du fournisseur d'identité.

### Exemple de base de l’authentification sociale

Cet exemple utilise le SDK client Facebook pour l'authentification :

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
Cet exemple part du principe que le jeton fourni par le Kit de développement logiciel (SDK) propre au fournisseur est stocké dans une variable token.

### Exemple de compte Microsoft

L'exemple suivant utilise le Kit de développement logiciel (SDK) Live, qui prend en charge l'authentification unique pour les applications Windows Store à l'aide d'un compte Microsoft :

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

Cet exemple récupère un jeton de Live Connect, qui est fourni à App Service en appelant la fonction login.

##<a name="auth-getinfo"></a>Procédure : obtention des informations sur l’utilisateur authentifié

Les informations d’authentification sur l’utilisateur actuel peuvent être récupérées du point de terminaison `/.auth/me` à l’aide de toute méthode AJAX. Par exemple, pour utiliser l’API d’extraction :

```
var url = client.applicationUrl + '/.auth/me';
fetch(url)
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Vous pouvez également utiliser jQuery ou une autre API AJAX pour extraire les informations. Les données sont reçues sous la forme d’un objet JSON.

<!--------HONumber=AcomDC_0309_2016-->
