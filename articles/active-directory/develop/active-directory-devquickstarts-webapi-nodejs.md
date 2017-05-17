---
title: "Prise en main d’Azure AD Node.js | Microsoft Docs"
description: "Création d’une API web REST Node.js qui s’intègre à Azure AD pour l’authentification."
services: active-directory
documentationcenter: nodejs
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: xerners
ms.translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 707bb2b7cd9796e0e05234aa08002bbc1820f871
ms.contentlocale: fr-fr
ms.lasthandoff: 03/18/2017


---
# <a name="get-started-with-web-apis-for-nodejs"></a>Prise en main d’API web pour Node.js
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

*Passport* est un intergiciel d’authentification pour Node.js. Flexible et modulaire, Passport peut discrètement intervenir dans n’importe quelle application web basée sur Express ou Restify. Une gamme complète de stratégies prend en charge l’authentification avec un nom d’utilisateur et un mot de passe, Facebook, Twitter et bien d’autres. Nous avons développé une stratégie pour Microsoft Azure Active Directory (Azure AD). Nous installons ce module, puis nous y ajoutons le plug-in `passport-azure-ad` Microsoft Azure Active Directory.

Pour cela, vous devez procéder comme suit :

1. inscrivez une application auprès d’Azure AD ;
2. configurez votre application pour utiliser le plug-in `passport-azure-ad` Passport ;
3. configurez une application cliente pour appeler l’API web To Do List.

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/Azure-Samples/active-directory-node-webapi).

> [!NOTE]
> Cet article ne couvre pas l’implémentation de la connexion, de l’inscription ou de la gestion de profil avec Azure AD B2C. Il s'intéresse principalement à l'appel d'API web après que l'utilisateur s'est authentifié.  Nous recommandons de commencer par le document [How to integrate with Azure Active Directory](active-directory-how-to-integrate.md) (Intégration à Azure Active Directory) pour en savoir plus sur les notions de base d’Azure Active Directory.
>
>

Nous avons publié tout le code source de cet exemple dans GitHub sous une licence MIT. N’hésitez pas à cloner (ou mieux, à répliquer), à commenter et à soumettre des demandes d’extraction.

## <a name="about-nodejs-modules"></a>À propos des modules Node.js
Nous utilisons des modules Node.js au cours de cette procédure détaillée. Les modules sont des packages JavaScript chargeables qui fournissent une fonctionnalité spécifique à votre application. Vous installez généralement des modules à l’aide de Node.js, un outil de ligne de commande NPM dans le répertoire d’installation NPM. Toutefois, certains modules, comme le module HTTP, sont inclus dans le package Node.js principal.

Les modules installés sont stockés dans le répertoire **node_modules**, à la racine de votre répertoire Node.js. Chaque module du répertoire **node_modules** conserve son propre répertoire **node_modules** qui contient tous les modules dont il dépend. Chaque module requis dispose également de son propre répertoire **node_modules**. Cette structure de répertoire récursive représente la chaîne de dépendance.

Cette structure de chaîne de dépendance augmente l’encombrement de l’application. Mais elle garantit également que toutes les dépendances sont satisfaites et que la version des modules utilisée dans le développement est également utilisée en production. Le comportement de l’application de production est ainsi plus prévisible et cela évite les problèmes de version pouvant affecter les utilisateurs.

## <a name="step-1-register-an-azure-ad-tenant"></a>Étape 1 : inscription d’un client Azure AD
Pour utiliser cet exemple, vous avez besoin d’un client Azure Active Directory. Si vous n’êtes pas sûr de ce qu’est un locataire ou si vous ne savez pas comment en obtenir un, consultez la page [Obtention d’un client Azure Active Directory](active-directory-howto-tenant.md).

## <a name="step-2-create-an-application"></a>Étape 2 : création d’une application
Vous créez ensuite dans votre répertoire une application fournissant à Azure AD les informations nécessaires pour communiquer de manière sécurisée avec votre application.  L’application cliente et l’API web sont toutes les deux représentées par un seul **ID d’application** dans ce cas, car elles constituent une application logique.  Pour créer une application, suivez [ces instructions](active-directory-how-applications-are-added.md). Si vous générez une application métier, [ces instructions supplémentaires peuvent être utiles](../active-directory-applications-guiding-developers-for-lob-applications.md).

Pour créer une application :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu supérieur, sélectionnez votre compte. Puis, dans la liste **Répertoire**, choisissez le client Active Directory auprès duquel vous voulez inscrire votre application.

3. Dans le menu à gauche, sélectionnez **Plus de services**, puis **Azure Active Directory**.

4. Sélectionnez **Inscriptions d’applications**, puis **Ajouter**.

5. Suivez les invites pour créer une **application web et/ou une API web**.

      * Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.

      * L’ **URL de connexion** est l’URL de base de votre application.  L’URL par défaut de l’exemple de code est `https://localhost:8080`.

6. Une fois l’application enregistrée, Azure AD lui affecte un ID d’application unique. Copiez cette valeur à partir de la page de l’application, car vous en aurez besoin dans les sections suivantes.

7. À partir de la page **Paramètres** -> **Propriétés** de votre application, mettez à jour l’URI ID d’application. Un **URI ID d’application** est un identificateur unique pour votre application. L’usage est d’utiliser `https://<tenant-domain>/<app-name>`, par exemple : `https://contoso.onmicrosoft.com/my-first-aad-app`.

8. Créez une **clé** pour votre application dans la page **Paramètres** et notez-la. Vous en aurez besoin rapidement.

## <a name="step-3-download-nodejs-for-your-platform"></a>Étape 3 : téléchargement de Node.js pour votre plateforme
Pour réussir à utiliser cet exemple, Node.js doit être installé correctement.

Installez Node.js à partir de l’adresse suivante : [http://nodejs.org](http://nodejs.org).

## <a name="step-4-install-mongodb-on-your-platform"></a>Étape 4 : installation de MongoDB sur votre plateforme
Pour réussir à utiliser cet exemple, MongoDB doit être installé correctement. Vous utilisez MongoDB afin que votre API REST soit persistante sur les instances de serveur.

Installez MongoDB à partir de l’adresse suivante : [http://mongodb.org](http://www.mongodb.org).

> [!NOTE]
> Cette procédure détaillée suppose que vous utilisez l’installation et les points de terminaison du serveur par défaut pour MongoDB, c’est-à-dire les suivants au moment de la rédaction : mongodb://localhost.
>
>

## <a name="step-5-install-the-restify-modules-in-your-web-api"></a>Étape 5 : installation des modules Restify sur votre API web
Nous utilisons Restify pour concevoir notre API REST. Restify est une infrastructure d’application Node.js minimale et flexible qui est dérivée d’Express. Elle inclut un ensemble complet de fonctionnalités de création API REST sur Connect.

### <a name="install-restify"></a>Installation de Restify
1. Depuis la ligne de commande, accédez au répertoire **azuread**. Si le répertoire **azuread** n’existe pas, créez-le.

        `cd azuread - or- mkdir azuread; cd azuread`

2. Tapez la commande suivante :

    `npm install restify`

    Cette commande permet d’installer Restify.

#### <a name="did-you-get-an-error"></a>Une erreur est-elle survenue ?
Lorsque vous utilisez NPM sur certains systèmes d’exploitation, vous pouvez obtenir le message d’erreur suivant : **Error: EPERM, chmod '/usr/local/bin/..'** et la suggestion d’essayer d’exécuter le compte en tant qu’administrateur. Si cette erreur survient, utilisez la commande sudo pour exécuter NPM avec des privilèges plus élevés.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>Une erreur relative à DTRACE est-elle survenue ?
Lors de l’installation de Restify, vous risquez de voir une erreur comme celle-ci :

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```
Restify fournit un mécanisme puissant pour suivre les appels REST à l’aide de DTrace. Toutefois, de nombreux systèmes d’exploitation ne disposent pas de DTrace. Vous pouvez ignorer ces erreurs.

Le résultat de cette commande doit ressembler au résultat suivant :

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)


## <a name="step-6-install-passportjs-in-your-web-api"></a>Étape 6 : installation de Passport.js dans votre API web
[Passport](http://passportjs.org/) est un intergiciel d’authentification pour Node.js. Flexible et modulaire, Passport peut discrètement intervenir dans n’importe quelle application web basée sur Express ou Restify. Une gamme complète de stratégies prend en charge l’authentification avec un nom d’utilisateur et un mot de passe, Facebook, Twitter et bien d’autres.

Nous avons développé une stratégie pour Azure Active Directory. Nous installons ce module, puis nous y ajoutons le plug-in stratégique Active Directory Azure.

1. Depuis la ligne de commande, accédez au répertoire **azuread**.

2. Pour installer passport.js, saisissez la commande suivante :

    `npm install passport`

    Le résultat de la commande doit ressembler à ceci :

``
        passport@0.1.17 node_modules\passport
        ├── pause@0.0.1
        └── pkginfo@0.2.3
``

## <a name="step-7-add-passport-azure-ad-to-your-web-api"></a>Étape 7 : ajout de Passport-Azure-AD à votre API web
Nous ajoutons alors la stratégie OAuth à l’aide de `passport-azure-ad`, une suite de stratégies qui connectent Azure Active Directory à Passport. Nous utilisons cette stratégie pour les jetons du porteur dans cet exemple d’API REST.

> [!NOTE]
> Bien qu’OAuth2 fournisse une infrastructure dans laquelle tout type de jeton connu peut être émis, seuls certains types de jeton sont couramment utilisés. Les jetons du porteur sont les jetons les plus couramment utilisés pour la protection des points de terminaison. Il s’agit du type de jeton le plus fréquemment émis dans OAuth2. De nombreuses implémentations partent du principe qu’il s’agit du seul type de jeton émis.
>
>

Depuis la ligne de commande, accédez au répertoire **azuread**.

Saisissez la commande suivante pour installer le Passport.js `passport-azure-ad module` :

`npm install passport-azure-ad`

Le résultat de la commande doit ressembler au résultat suivant :


    passport-azure-ad@1.0.0 node_modules/passport-azure-ad
    ├── xtend@4.0.0
    ├── xmldom@0.1.19
    ├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
    ├── underscore@1.8.3
    ├── async@1.3.0
    ├── jsonwebtoken@5.0.2
    ├── xml-crypto@0.5.27 (xpath.js@1.0.6)
    ├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
    ├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
    ├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    └── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)



## <a name="step-8-add-mongodb-modules-to-your-web-api"></a>Étape 8 : ajout des modules MongoDB à votre API web
Nous utilisons MongoDB comme magasin de données. Pour cette raison, nous devons installer le plug-in largement utilisé appelé Mongoose pour gérer les modèles et schémas. Nous devons également installer le pilote de base de données de MongoDB (qui est également appelé MongoDB).

 `npm install mongoose`

## <a name="step-9-install-additional-modules"></a>Étape 9 : installation de modules supplémentaires
Ensuite, nous installons les autres modules requis.

1. Depuis la ligne de commande, accédez au dossier **azuread** si vous n’y êtes pas déjà.

    `cd azuread`

2. Entrez les commandes suivantes pour installer ces modules dans le répertoire **node_modules** :

    * `npm install assert-plus`
    * `npm install bunyan`
    * `npm update`

## <a name="step-10-create-a-serverjs-with-your-dependencies"></a>Étape 10 : création d’un server.js avec vos dépendances
Le fichier server.js fournit la plupart des fonctionnalités de notre serveur d’API web. Nous ajoutons la plupart du code à ce fichier. À des fins de production, nous vous recommandons de refactoriser la fonctionnalité en fichiers plus petits, par exemple des itinéraires et des contrôleurs distincts. Dans cette démonstration, nous utilisons server.js pour cette fonctionnalité.

1. Depuis la ligne de commande, accédez au dossier **azuread** si vous n’y êtes pas déjà.

    `cd azuread`

2. Créez un fichier `server.js` dans votre éditeur favori, puis ajoutez les informations suivantes :

    ```Javascript
        'use strict';

        /**
         * Module dependencies.
         */

        var fs = require('fs');
        var path = require('path');
        var util = require('util');
        var assert = require('assert-plus');
        var bunyan = require('bunyan');
        var getopt = require('posix-getopt');
        var mongoose = require('mongoose/');
        var restify = require('restify');
        var passport = require('passport');
      var BearerStrategy = require('passport-azure-ad').BearerStrategy;
    ```

3. Enregistrez le fichier . Nous y reviendrons rapidement.

## <a name="step-11-create-a-config-file-to-store-your-azure-ad-settings"></a>Étape 11 : création d’un fichier de configuration pour stocker vos paramètres Azure AD
Ce fichier de code transmet les paramètres de configuration de votre portail Azure Active Directory vers Passport.js. Vous avez créé ces valeurs de configuration lorsque vous avez ajouté l’API web au portail, dans la première partie de la procédure détaillée. Copiez le code. Après quoi, nous vous expliquerons ce qu’il convient de placer dans les valeurs de ces paramètres.

1. Depuis la ligne de commande, accédez au dossier **azuread** si vous n’y êtes pas déjà.

    `cd azuread`

2. Créez un fichier `config.js` dans votre éditeur favori, puis ajoutez les informations suivantes :

    ```Javascript
         exports.creds = {
             mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
             clientID: 'your client ID',
             audience: 'your application URL',
            // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
          // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
             identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
             validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
             passReqToCallback: false,
             loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

         };
    ```
3. Enregistrez le fichier .

## <a name="step-12-add-configuration-values-to-your-serverjs-file"></a>Étape 12 : ajout de valeurs de configuration à votre fichier server.js
Nous avons besoin de lire ces valeurs dans le fichier .config que vous avez créé dans notre application. Pour cela, nous ajoutons le fichier .config en tant que ressource requise dans notre application. Nous définissons ensuite les variables globales pour qu’elles correspondent aux variables dans le document config.js.

1. Depuis la ligne de commande, accédez au dossier **azuread** si vous n’y êtes pas déjà.

    `cd azuread`

2. Ouvrez votre fichier `server.js` dans votre éditeur favori, puis ajoutez les informations suivantes :

    ```Javascript
    var config = require('./config');
    ```
3. Ensuite, ajoutez une nouvelle section à `server.js` avec le code suivant :

    ```Javascript
    var options = {
        // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
        identityMetadata: config.creds.identityMetadata,
        clientID: config.creds.clientID,
        validateIssuer: config.creds.validateIssuer,
        audience: config.creds.audience,
        passReqToCallback: config.creds.passReqToCallback,
        loggingLevel: config.creds.loggingLevel

    };

    // Array to hold logged in users and the current logged in user (owner).
    var users = [];
    var owner = null;

    // Our logger.
    var log = bunyan.createLogger({
        name: 'Azure Active Directory Bearer Sample',
             streams: [
            {
                stream: process.stderr,
                level: "error",
                name: "error"
            },
            {
                stream: process.stdout,
                level: "warn",
                name: "console"
            }, ]
    });

      // If the logging level is specified, switch to it.
      if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    ```

4. Enregistrez le fichier .

## <a name="step-13-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Étape 13 : ajout des informations du modèle et du schéma MongoDB à l’aide de Mongoose
Toute cette préparation va maintenant prouver son utilité : associons ces trois fichiers dans un service API REST.

Pour cette procédure détaillée, nous utilisons MongoDB pour stocker les tâches, comme indiqué dans l’étape 4.

Dans le fichier `config.js` que nous avons créé dans l’étape 11, nous avons appelé notre base de données `tasklist`, car c’était ce que nous avions placé à la fin de l’URL de connexion **mogoose_auth_local**. Vous n’avez pas besoin de créer cette base de données au préalable dans MongoDB. MongoDB le fait pour nous au moment de la première exécution de l’application serveur (en supposant que la base de données n’existe pas déjà).

Maintenant que nous avons indiqué au serveur quelle base de données MongoDB nous souhaitons utiliser, nous devons écrire du code supplémentaire pour créer le modèle et le schéma pour les tâches du serveur.

### <a name="discussion-of-the-model"></a>Considérations sur le modèle
Notre modèle de schéma est simple. Vous le développez en fonction de vos besoins.

NOM : le nom de la personne à qui la tâche est assignée. **Chaîne**.

TÂCHE : la tâche elle-même. **Chaîne**.

DATE : la date d’échéance de la tâche. **DATEHEURE**.

TERMINÉ : indique si la tâche est terminée ou non. **BOOLÉEN**.

### <a name="creating-the-schema-in-the-code"></a>Création du schéma dans le code
1. Depuis la ligne de commande, accédez au dossier **azuread** si vous n’y êtes pas déjà.

    `cd azuread`

2. Ouvrez votre fichier `server.js` dans votre éditeur favori, puis ajoutez les informations suivantes sous l’entrée de configuration :

    ```Javascript
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');

    // Here we create a schema to store our tasks and users. It's a fairly simple schema for now.
    var TaskSchema = new Schema({
        owner: String,
        task: String,
        completed: Boolean,
        date: Date
    });

    // Use the schema to register a model.
    mongoose.model('Task', TaskSchema);
    var Task = mongoose.model('Task');
    ```
Comme vous pouvez le voir dans le code, nous créons notre schéma en premier. Nous créons ensuite un objet de modèle que nous utilisons pour stocker nos données dans le code lorsque nous définissons nos **itinéraires**.

## <a name="step-14-add-our-routes-for-our-task-rest-api-server"></a>Étape 14 : ajout de nos itinéraires pour notre serveur de tâche de l’API REST
Maintenant que nous disposons d’un modèle de base de données, ajoutons les itinéraires que nous allons utiliser pour notre serveur API REST.

### <a name="about-routes-in-restify"></a>À propos des itinéraires dans Restify
Dans Restify, les itinéraires fonctionnent de la même façon que dans la pile Express. Vous définissez des itinéraires à l’aide de l’URI qui, selon vous, sera appelé par les applications clientes. En général, vous définissez vos itinéraires dans un fichier distinct. Pour cet exemple, nous plaçons nos itinéraires dans le fichier server.js. Nous vous recommandons de factoriser ces itinéraires dans leur propre fichier s’ils sont destinés à la production.

Un exemple de modèle d’un itinéraire Restify se présente comme suit :

```Javascript
function createObject(req, res, next) {

// Do work on object.

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // Keep the server going.
}

....

server.post('/service/:add/:object', createObject); // Calls createObject on routes that match this.

```


Il s’agit d’un modèle très basique. Restify (et Express) fournit des fonctionnalités bien plus approfondies, comme la définition de types d’application et la fourniture d’un routage complexe entre différents points de terminaison. Pour nos objectifs, nous conservons ces itinéraires simples.

### <a name="add-default-routes-to-our-server"></a>Ajout d’itinéraires par défaut à notre serveur
Nous ajoutons à présent les itinéraires CRUD de base suivants : Créer, Extraire, Mettre à jour et Supprimer.

1. Depuis la ligne de commande, accédez au dossier **azuread** si vous n’y êtes pas déjà :

    `cd azuread`

2. Ouvrez le fichier `server.js` dans votre éditeur favori, puis ajoutez les informations suivantes en-dessous des entrées de base de données précédentes que vous avez créées :

```Javascript

/**
 *
 * APIs for our REST Task server.
 */

// Create a task.

function createTask(req, res, next) {

    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it, and save it to Mongodb.
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.task = req.params.task;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}


// Delete a task by name.

function removeTask(req, res, next) {

    Task.remove({
        task: req.params.task,
        owner: owner
    }, function(err) {
        if (err) {
            req.log.warn(err,
                'removeTask: unable to delete %s',
                req.params.task);
            next(err);
        } else {
            log.info('Deleted task:', req.params.task);
            res.send(204);
            next();
        }
    });
}

// Delete all tasks.

function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
}


// Get a specific task based on name.

function getTask(req, res, next) {

    log.info('getTask was called for: ', owner);
    Task.find({
        owner: owner
    }, function(err, data) {
        if (err) {
            req.log.warn(err, 'get: unable to read %s', owner);
            next(err);
            return;
        }

        res.json(data);
    });

    return next();
}

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initialize the database as stated in the README?");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}

```

### <a name="add-error-handling-in-our-apis"></a>Ajouter la gestion des erreurs dans nos API
```

///--- Errors for communicating something interesting back to the client.

function MissingTaskError() {
    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'MissingTask',
        message: '"task" is a required parameter',
        constructorOpt: MissingTaskError
    });

    this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);


function TaskExistsError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'TaskExists',
        message: owner + ' already exists',
        constructorOpt: TaskExistsError
    });

    this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 404,
        restCode: 'TaskNotFound',
        message: owner + ' was not found',
        constructorOpt: TaskNotFoundError
    });

    this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="step-15-create-your-server"></a>Étape 15 : création de votre serveur
Nous avons défini notre base de données et nos itinéraires sont en place. La dernière étape consiste à ajouter l’instance de serveur qui gérera vos appels.

Dans Restify (et Express), vous disposez de nombreuses options de personnalisation approfondie pour un serveur API REST, mais à nouveau, nous allons utiliser la configuration la plus simple pour cet exemple.

```Javascript
/**
 * Our server.
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads.
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());

// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in).
server.use(restify.requestLogger());

// Allow five requests per second by IP, and burst to 10.
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want.
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allow for JSON mapping to REST.
```

## <a name="step-16-add-the-routes-to-the-server-without-authentication-for-now"></a>Étape 16 : ajout des itinéraires au serveur (sans authentification à ce stade)
```Javascript
/// Now the real handlers. Here we just CRUD.
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'.
/* In the pasport.authenticate() method. We set 'session: false' because REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method as follows:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler.
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```

## <a name="step-17-run-the-server-before-adding-oauth-support"></a>Étape 17 : exécution du serveur (avant l’ajout de la prise en charge d’OAuth)
Testez votre serveur avant d’ajouter l’authentification.

Pour tester votre serveur, le plus simple consiste à utiliser curl dans une ligne de commande. Avant cela, nous avons besoin d’un utilitaire qui nous permette d’analyser la sortie au format JSON.

1. Installez l’outil JSON suivant (les exemples suivants utilisent cet outil) :

    `$npm install -g jsontool`

    Cette opération installe totalement l’outil JSON. Maintenant que l’outil JSON est installé, amusons-nous avec le serveur :

2. Vérifiez tout d’abord que votre instance mongoDB est en cours d’exécution :

    `$sudo mongod`

3. Accédez ensuite au répertoire et commencez à utiliser curl :

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 200 OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4. Nous pouvons à présent ajouter une tâche de la manière suivante :

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    La réponse doit être la suivante :

        ```Shell
        HTTP/1.1 201 Created
        Connection: close
        Access-Control-Allow-Origin: *
        Access-Control-Allow-Headers: X-Requested-With
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 5
        Date: Tue, 04 Feb 2014 01:02:26 GMT
        Hello
        ```
    Et nous pouvons répertorier des tâches pour Brandon de cette manière :

        `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Si tout cela fonctionne, nous sommes prêts à ajouter OAuth au serveur API REST.

Vous avez un serveur d’API REST avec MongoDB !

## <a name="step-18-add-authentication-to-our-rest-api-server"></a>Étape 18 : ajout d’une authentification à notre serveur API REST
Maintenant que nous disposons d’une API REST en cours d’exécution, commençons à l’utiliser avec Azure AD.

Depuis la ligne de commande, accédez au dossier **azuread** si vous n’y êtes pas déjà.

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Utiliser la stratégie OIDCBearerStrategy incluse avec passport-azure-ad
Jusqu’à présent, nous avons créé un serveur REST TODO standard ne disposant d’aucune autorisation. Nous allons commencer à tout rassembler.

1. Tout d’abord, nous devons indiquer que nous voulons utiliser Passport. Placez cela juste après la configuration de l’autre serveur :

    ```Javascript
            // Let's start using Passport.js.

            server.use(passport.initialize()); // Starts passport.
            server.use(passport.session()); // Provides session support.
    ```
    > [!TIP]
    > Lors de l’écriture d’API, nous vous recommandons de toujours lier les données à un élément unique du jeton, dont un utilisateur ne peut pas usurper l’identité. Lorsque ce serveur stocke les éléments TODO, il le fait en fonction de l’ID d’objet de l’utilisateur dans le jeton (appelé via token.oid) que nous avons placé dans le champ « propriétaire ». Cela permet de s’assurer que seul cet utilisateur peut accéder à ses éléments TODO. Il n’y a pas d’exposition dans l’API du « propriétaire », afin qu’un utilisateur externe puisse demander les éléments TODO d’un tiers même s’ils sont authentifiés.                    

2. Maintenant, utilisons la stratégie du porteur fournie avec `passport-azure-ad`. Examinez le code pour l’instant, nous fournirons des explications sous peu. Placez ceci après ce que vous avez collé ci-dessus :

```Javascript
    /**
    /*
    /* Calling the OIDCBearerStrategy and managing users.
    /*
    /* Passport pattern provides the need to manage users and info tokens
    /* with a FindorCreate() method that must be provided by the implementor.
    /* Here we just auto-register any user and implement a FindById().
    /* You'll want to do something smarter.
    **/

    var findById = function(id, fn) {
        for (var i = 0, len = users.length; i < len; i++) {
            var user = users[i];
            if (user.sub === id) {
                log.info('Found user: ', user);
                return fn(null, user);
            }
        }
        return fn(null, null);
    };


    var bearerStrategy = new BearerStrategy(options,
        function(token, done) {
            log.info('verifying the user');
            log.info(token, 'was the token retreived');
            findById(token.sub, function(err, user) {
                if (err) {
                    return done(err);
                }
                if (!user) {
                    // "Auto-registration"
                    log.info('User was added automatically as they were new. Their sub is: ', token.sub);
                    users.push(token);
                    owner = token.sub;
                    return done(null, token);
                }
                owner = token.sub;
                return done(null, user, token);
            });
        }
    );

    passport.use(bearerStrategy);
```

Passport utilise un modèle semblable pour toutes ses stratégies (Twitter, Facebook, etc.), que respectent tous les enregistreurs de stratégie. Comme vous pouvez le voir dans la stratégie, nous transmettons une fonction dont les paramètres sont un jeton et un done. La stratégie nous revient une fois le travail effectué. Nous stockons l’utilisateur et le jeton afin de ne pas avoir à les redemander.

> [!IMPORTANT]
> Le code précédent note tout utilisateur s’authentifiant sur notre serveur. C’est ce qu’on appelle l’enregistrement automatique. Dans les serveurs de production, nous vous recommandons de faire passer toute personne qui essaie de se connecter par un processus d’inscription de votre choix. C’est généralement le modèle des applications consommateur qui vous permettent de vous inscrire via Facebook, mais vous demandent ensuite de renseigner des informations supplémentaires. S’il ne s’agissait pas d’un programme de ligne de commande, nous aurions pu extraire l’adresse de messagerie à partir de l’objet de jeton retourné, avant d’inviter l’utilisateur à entrer des informations supplémentaires. Étant donné qu’il s’agit d’un serveur de test, nous les ajoutons simplement à la base de données en mémoire.
>
>

### <a name="protect-some-endpoints"></a>Protéger certains points de terminaison
Pour protéger les points de terminaison, spécifiez l’appel `passport.authenticate()` avec le protocole que vous souhaitez utiliser.

Pour que notre code de serveur effectue quelque chose de plus intéressant, modifions l’itinéraire.

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="step-19-run-your-server-application-again-and-ensure-it-rejects-you"></a>Étape 19 : ré-exécution de votre application serveur et contrôle du rejet
Nous allons de nouveau utiliser `curl` pour voir si nous disposons à présent de la protection OAuth2 sur nos points de terminaison. Nous faisons ce contrôle avant d’exécuter l’un de nos Kits de développement logiciel (SDK) clients sur ce point de terminaison. Les en-têtes renvoyés doivent être suffisants pour nous dire si nous sommes sur le bon chemin d’accès.

1. Vérifiez tout d’abord que votre instance mongoDB est en cours d’exécution :

    `$sudo mongod`

2. Accédez ensuite au répertoire et commencez à utiliser curl.

      `$ cd azuread`
      `$ node server.js`

3. Essayez une commande basique, comme POST.

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

Une erreur 401 est la réponse que vous cherchez ici. Cette réponse indique que la couche de Passport tente de rediriger vers le point de terminaison autorisé, ce qui est exactement ce que vous souhaitez.

## <a name="next-steps"></a>Étapes suivantes
Vous êtes allé aussi loin que possible avec ce serveur sans utiliser un client compatible OAuth2. Vous devrez suivre une autre procédure détaillée.

Vous savez maintenant comment implémenter une API REST à l’aide de Restify et d’OAuth2. Vous disposez également de plus de code pour continuer à développer votre service et à apprendre comment créer sur la base de cet exemple.

Si vous êtes intéressé par les étapes suivantes dans votre parcours de la bibliothèque ADAL, voici quelques clients de la bibliothèque ADAL pris en charge que nous vous recommandons de continuer à utiliser.

Clonez sur votre ordinateur de développement et configurez comme décrit dans la procédure détaillée.

[Bibliothèque ADAL pour iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[Bibliothèque ADAL pour Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

