<properties
	pageTitle="Comment utiliser le plug-in Apache Cordova pour Azure Mobile Apps"
	description="Comment utiliser le plug-in Apache Cordova pour Azure Mobile Apps"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="adrianha"/>

# Comment utiliser la bibliothèque cliente Apache Cordova pour Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide indique le déroulement de scénarios courants dans le cadre de l’utilisation du dernier [plug-in Apache Cordova pour Azure Mobile Apps]. Si vous ne connaissez pas Azure Mobile Apps, consultez d’abord la section [Démarrage rapide d’Azure Mobile Apps] pour créer un serveur principal, créer une table et télécharger un projet Apache Cordova prédéfini. Dans ce guide, nous nous concentrons sur le plug-in Apache Cordova côté client.

##<a name="Setup"></a>Configuration et conditions préalables

Ce guide part du principe que vous avez créé un serveur principal avec une table. Ce guide suppose que la table a le même schéma que les tables dans ces didacticiels. Ce guide suppose également que vous avez ajouté le plug-in Apache Cordova à votre code. Si ce n’est pas le cas, vous pouvez l’ajouter à votre projet depuis la ligne de commande :

```
cordova plugin add ms-azure-mobile-apps
```

Pour plus d’informations sur la création de [votre première application Apache Cordova], consultez la documentation officielle.

##<a name="create-client"></a>Création du client

Créez une connexion client en créant un objet `WindowsAzure.MobileServicesClient`. Remplacez `appUrl` par l’URL de votre application mobile.

```
var client = WindowsAzure.MobileServicesClient(appUrl);
```

##<a name="table-reference"></a>Procédure : création d'une référence de table

Pour accéder aux données ou les mettre à jour, créez une référence à la table principale. Remplacez `tableName` par le nom de votre table.

```
var table = client.getTable(tableName);
```

##<a name="querying"></a>Procédure : interrogation d’une référence de table

Une fois que vous disposez d’une référence de table, vous pouvez l’utiliser pour rechercher des données sur le serveur. Les requêtes sont effectuées dans un langage de type LINQ. Pour retourner toutes les données de la table, utilisez la syntaxe suivante :

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

La fonction success est appelée avec les résultats. Ne recourez pas à `for (var i in results)` dans la fonction success, car cette action entraîne une itération sur les informations contenues dans les résultats quand d’autres fonctions de requête (telles que `.includeTotalCount()`) sont utilisées.

Pour plus d’informations sur la syntaxe de requête, consultez la [documentation de l’objet Query].

### Filtrage des données sur le serveur

Vous pouvez utiliser une clause `where` sur la référence de table :

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Vous pouvez également utiliser une fonction qui filtre l’objet. Dans ce cas, la variable `this` est affectée à l’objet en cours de filtrage. La syntaxe suivante est équivalente à l’exemple précédent sur le plan fonctionnel :

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

### Pagination des données

Utilisez les méthodes take() et skip(). Par exemple, si vous souhaitez fractionner la table en enregistrements de 100 lignes :

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

La méthode `.includeTotalCount()` est utilisée pour ajouter un champ totalCount à l’objet results. Le champ totalCount est rempli avec le nombre total d’enregistrements qui est retourné si aucune pagination n’est utilisée.

Vous pouvez ensuite utiliser la variable pages et des boutons d’interface utilisateur pour fournir une liste de pages ; utilisez loadPage() pour charger les nouveaux enregistrements pour chaque page. Vous devez implémenter une sorte de mise en cache pour accélérer l’accès aux enregistrements qui ont déjà été chargés.


###<a name="sorting-data"></a>Procédure : renvoi de données triées

Utilisez les méthodes de requête .orderBy() ou .orderByDescending() :

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Pour plus d’informations sur l’objet Query, consultez la [documentation de l’objet Query].

##<a name="inserting"></a>Procédure : insertion de données

Créez un objet JavaScript avec la date appropriée et appelez table.insert() de façon asynchrone :

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Une fois l’insertion correctement effectuée, l’élément inséré est retourné avec les champs supplémentaires qui sont nécessaires pour les opérations de synchronisation. Vous devez mettre à jour votre propre cache avec ces informations en vue des mises à jour ultérieures.

Notez que le Kit de développement logiciel (SDK) de serveur Node.js Azure Mobile Apps prend en charge le schéma dynamique à des fins de développement. Dans le cas du schéma dynamique, le schéma de la table est mis à jour à la volée ; vous pouvez ainsi ajouter des colonnes à la table simplement en les spécifiant dans une opération d’insertion ou de mise à jour. Nous vous recommandons de désactiver le schéma dynamique avant de déplacer votre application vers un environnement de production.

##<a name="modifying"></a>Procédure : modification des données

Comme dans le cas de la méthode .insert(), vous devez créer un objet de mise à jour, puis appeler .update(). L’objet de mise à jour doit contenir l’ID de l’enregistrement à mettre à jour, obtenu au moment de la lecture de l’enregistrement ou de l’appel de .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

##<a name="deleting"></a>Procédure : suppression de données

Appelez la méthode .del() pour supprimer un enregistrement. Transmettez l’ID d’une référence d’objet :

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

##<a name="auth"></a>Procédure : authentification des utilisateurs

Azure App Service prend en charge l’authentification et l’autorisation des utilisateurs d’applications par le biais de divers fournisseurs d’identité externes : Facebook, Google, compte Microsoft et Twitter. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l'identité des utilisateurs authentifiés pour implémenter des règles d'autorisation dans les scripts serveur. Pour plus d'informations, consultez la page [Prise en main de l'authentification].

Quand vous utilisez l’authentification dans une application Apache Cordova, les plug-ins Cordova suivants doivent être réunis :

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Deux flux d’authentification sont pris en charge : un flux serveur et un flux client. Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface d'authentification Web du fournisseur. Le flux client permet une intégration approfondie avec les fonctionnalités propres aux appareils, telles que l'authentification unique, car il repose sur des Kits de développement logiciel (SDK) propres aux appareils et aux fournisseurs.

##<a name="server-auth"></a>Procédure : authentification auprès d’un fournisseur (flux serveur)

Pour que Mobile Services gère le processus d’authentification dans votre application Windows Store ou HTML5, vous devez inscrire votre application auprès de votre fournisseur d’identité. Ensuite, dans Azure App Service, vous devez configurer l’ID d’application et le secret fournis par votre fournisseur. Pour plus d’informations, consultez le didacticiel [Ajout de l’authentification à votre application].

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

##<a name="register-for-push"></a>Procédure : inscription aux notifications Push

Installez le plug-in [plug-in phonegap-push] pour gérer les notifications Push. Vous pouvez l’ajouter facilement à l’aide de la commande `cordova plugin add` sur la ligne de commande, ou par le biais du programme d’installation de plug-in Git dans Visual Studio. Le code suivant dans votre application Apache Cordova inscrit votre appareil aux notifications Push :

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Utilisez le Kit de développement logiciel (SDK) Notification Hubs pour envoyer des notifications Push à partir du serveur. Vous ne devez jamais envoyer de notifications Push directement à partir de clients, car cela peut favoriser le déclenchement d’une attaque par déni de service contre Notification Hubs ou le PNS.

<!-- URLs. -->
[Démarrage rapide d’Azure Mobile Apps]: app-service-mobile-cordova-get-started.md
[Prise en main de l'authentification]: app-service-mobile-cordova-get-started-users.md
[Ajout de l’authentification à votre application]: app-service-mobile-cordova-get-started-users.md

[plug-in Apache Cordova pour Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[votre première application Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[plug-in phonegap-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[documentation de l’objet Query]: https://msdn.microsoft.com/fr-FR/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0224_2016-->