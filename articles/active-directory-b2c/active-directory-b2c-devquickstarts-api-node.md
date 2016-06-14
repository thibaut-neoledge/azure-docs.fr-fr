<properties
	pageTitle="Version préliminaire B2C : sécuriser une API web à l’aide de Node.js | Microsoft Azure"
	description="Comment créer une API web Node.js qui accepte les jetons d’un client B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
  	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="hero-article"
	ms.date="05/31/2016"
	ms.author="brandwe"/>

# Version préliminaire B2C : sécuriser une API web à l’aide de Node.js

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]


> [AZURE.NOTE] Cet article ne couvre pas l’implémentation de la connexion, de l’inscription et de la gestion de profil avec Azure AD B2C. Il s’intéresse principalement à l’appel d’API web après que l’utilisateur s’est authentifié. Si ce n’est encore fait, commencez par consulter le [didacticiel sur la prise en main de l’application web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) pour en savoir plus sur les principes fondamentaux d’Azure Active Directory B2C.


> [AZURE.NOTE]	Cet exemple a été écrit pour être connecté à notre [exemple d’application iOS B2C.](active-directory-b2c-devquickstarts-ios.md) Effectuez d’abord cette procédure pas à pas avant de passer à cet exemple.

**Passport** est un intergiciel d’authentification pour Node.js. Extrêmement flexible et modulaire, Passport peut être installé discrètement dans n’importe quelle application web basée sur Express ou Restify. Une gamme complète de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et d’un mot de passe, de Facebook, de Twitter,etc. Nous avons développé une stratégie pour Azure Active Directory (Azure AD). Installez ce module, puis ajoutez le plug-in `passport-azure-ad` Azure AD.

Pour ce faire, procédez comme suit :

1. inscrivez une application auprès d’Azure AD ;
2. configurez votre application pour utiliser le plug-in `azure-ad-passport` Passport ;
3. configurez une application cliente pour appeler l’API web to-do list.

Le code associé à ce didacticiel [est stocké sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs). Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip) ou la cloner :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

L’application terminée est également fournie à la fin de ce didacticiel.

> [AZURE.WARNING] 	Pour la version préliminaire de B2C, vous devez utiliser les mêmes **ID client**/**ID d’application** et stratégies pour le serveur de la tâche API web et le client qui s’y connecte. Cela est également vrai pour les didacticiels iOS et Android. Si vous avez précédemment créé une application dans l’un de ces Démarrages rapides, utilisez ces valeurs au lieu d’en créer d’autres.


## Obtention d'un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes, etc. Si vous n’en possédez pas déjà un, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant d’aller plus loin.

## Création d'une application

Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. Dans ce cas, l’application cliente et l’API web seront toutes les deux représentées par un seul **ID d’application**, car elles constituent une application logique. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Veillez à effectuer les opérations suivantes :

- Inclure une **application web/API web** dans l’application.
- Entrer `http://localhost/TodoListService` comme **URL de réponse**. Il s’agit de l’URL par défaut pour cet exemple de code.
- Créer une **clé secrète d’application** pour votre application et la copier. Vous en aurez besoin ultérieurement. Vous en aurez besoin rapidement. Cette valeur doit être [placée dans une séquence d’échappement XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) avant son utilisation.
- Copiez l’**ID d’application** affecté à votre application. Vous en aurez besoin ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Création de vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cette application contient 3 expériences liées à l’identité : l’inscription, la connexion et la connexion avec Facebook. Vous devez créer une stratégie de chaque type, comme décrit dans l’[article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lors de la création de vos 3 stratégies, assurez-vous de :

- Choisir le **nom d’affichage** et d’autres attributs d’inscription dans votre stratégie d’inscription.
- Choisir les revendications **nom d’affichage** et **ID objet** comme revendications d’application pour chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
- Noter le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`. Vous aurez besoin des noms de ces stratégies ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos 3 stratégies créées, vous pouvez générer votre application.

Remarque : cet article n’explique pas comment utiliser les stratégies que vous venez de créer. Pour en savoir plus sur la façon dont les stratégies fonctionnent dans Azure AD B2C, commencez par le [didacticiel sur la prise en main de l’application web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Télécharger Node.js pour votre plateforme

Pour réussir à utiliser cet exemple, Node.js doit être installé correctement.

Installez Node.js à partir de [nodejs.org](http://nodejs.org).

## Installer MongoDB pour votre plateforme

Pour réussir à utiliser cet exemple, MongoDB doit être installé correctement. Vous allez utiliser MongoDB afin que votre API REST soit persistante sur plusieurs instances de serveur.

Installez MongoDB à partir de [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Cette procédure détaillée suppose que vous utilisez l’installation et les points de terminaison du serveur par défaut pour MongoDB, c’est-à-dire les suivants au moment de la rédaction : `mongodb://localhost`

## Installer les modules Restify sur votre API web

Vous allez utiliser Restify pour générer votre API REST. Restify est une infrastructure d’application Node.js minimale et flexible dérivée d’Express. Elle inclut un ensemble complet de fonctionnalités de création API REST sur Connect.

### Installation de Restify

À partir de la ligne de commande, modifiez votre répertoire en `azuread`. Si le répertoire `azuread` n’existe pas, créez-le.

`cd azuread` ou `mkdir azuread;`

Entrez la commande suivante :

`npm install restify`

Cette commande permet d’installer Restify.

#### Une erreur est-elle survenue ?

Dans certains systèmes d’exploitation, lorsque vous utilisez `npm`, vous pouvez recevoir l’erreur `Error: EPERM, chmod '/usr/local/bin/..'`, ainsi qu’une demande pour exécuter le compte en tant qu’administrateur. Dans ce cas, utilisez la commande `sudo` pour exécuter `npm` avec des privilèges plus élevés.

#### Avez-vous reçu une erreur DTrace ?

Lors de l’installation de Restify, vous risquez de voir ceci :

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

Le résultat de la commande doit ressembler à ceci :

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

## Installer Passport.dans votre API web

[Passport](http://passportjs.org/) est un intergiciel d’authentification pour Node.js. Extrêmement flexible et modulaire, Passport peut être installé discrètement dans n’importe quelle application web basée sur Express ou Restify. Une gamme complète de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et d’un mot de passe, de Facebook, de Twitter,etc. Nous avons développé une stratégie pour Azure AD. Installez ce module, puis ajoutez le plug-in de stratégie Azure AD.

À partir de la ligne de commande, modifiez votre répertoire en `azuread`, si ce n’est pas déjà fait.

Saisissez la commande suivante pour installer Passport :

`npm install passport`

Le résultat de la commande doit ressembler à ceci :

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## Ajouter passport-azuread à votre API web

Ensuite, ajoutez la stratégie OAuth à l’aide de `passport-azuread`, une suite de stratégies qui connectent Azure AD à Passport. Utilisez cette stratégie pour les jetons du porteur dans cet exemple d’API Rest.

> [AZURE.NOTE] Bien qu’OAuth2 fournisse une infrastructure dans laquelle tout type de jeton connu peut être émis, seuls certains types de jeton sont utilisés de manière généralisée. Les jetons protégeant les points de terminaison sont des jetons du porteur. Il s’agit du type de jeton le plus fréquemment émis dans OAuth2. De nombreuses implémentations partent du principe qu’il s’agit du seul type de jeton émis.

À partir de la ligne de commande, modifiez votre répertoire en `azuread`, si ce n’est pas déjà fait.

Entrez la commande suivante pour installer le module Passport `passport-azure-ad` :

`npm install passport-azure-ad`

Le résultat de la commande doit ressembler à ceci :

``
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
``

## Ajouter des modules MongoDB à votre API web

Vous allez utiliser MongoDB comme magasin de données. Pour cette raison, vous devez installer Mongoose, un plug-in fréquemment utilisé pour la gestion des modèles et des schémas, et le pilote de base de données de MongoDB, également appelé MongoDB.

* `npm install mongoose`
* `npm install mongodb`

## Installation de modules supplémentaires

Ensuite, installez les autres modules requis.

À partir de la ligne de commande, modifiez votre répertoire en `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Entrez les commandes suivantes pour installer les modules dans votre répertoire `node_modules` :

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## Créer un fichier server.js avec vos dépendances

Le fichier`server.js` fournira la plupart des fonctionnalités de votre serveur d’API web. La plupart du code sera ajouté à ce fichier. À des fins de production, vous devez refactoriser la fonctionnalité en fichiers plus petits, par exemple des itinéraires et des contrôleurs distincts. Pour les besoins de ce didacticiel, nous utiliserons server.js pour cette fonctionnalité.

À partir de la ligne de commande, modifiez votre répertoire en `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Créez un fichier `server.js` dans un éditeur. Ajoutez les informations suivantes :

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Enregistrez le fichier . Vous y reviendrez ultérieurement.

## Créer un fichier .js de configuration pour stocker vos paramètres Azure AD

Ce fichier de code transmet les paramètres de configuration de votre portail Azure AD vers le fichier `Passport.js`. Vous avez créé ces valeurs de configuration lorsque vous avez ajouté l’API web au portail, dans la première partie de la procédure détaillée. Copiez le code ; nous vous expliquerons ensuite ce qu’il faut placer dans les valeurs de ces paramètres.

À partir de la ligne de commande, modifiez votre répertoire en `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Créez un fichier `config.js` dans un éditeur. Ajoutez les informations suivantes :

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>',
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### Valeurs requises

`IdentityMetadata` : c’est ici que `passport-azure-ad` recherchera vos données de configuration pour le fournisseur d’identité, ainsi que les clés pour valider les jetons web JSON. Vous ne souhaitez probablement pas modifier cela si vous utilisez Azure AD.

`audience` : URI du portail qui identifie votre service. Notre exemple utilise : `http://localhost/TodoListService`.

`tenantName` : nom de votre client (par exemple, **contoso.onmicrosoft.com**).

`policyName` : stratégie que vous souhaitez utiliser pour valider les jetons atteignant votre serveur. Cette stratégie doit être la même que celle que vous utilisez sur l’application cliente pour vous connecter.

> [AZURE.NOTE] Pour notre aperçu B2C, utilisez les mêmes stratégies sur les installations client et serveur. Si vous avez déjà suivi une procédure pas à pas au cours de laquelle vous avez créé ces stratégies, vous n’avez pas besoin de répéter cette opération. Puisque vous avez suivi cette procédure, vous n’avez pas besoin de configurer de nouvelles stratégies pour les procédures pas à pas destinées au client sur le site.

## Ajouter des paramètres de configuration dans votre fichier server.js

Vous devez lire ces valeurs dans le fichier `config.js` que vous venez de créer dans votre application. Pour ce faire, ajoutez le fichier `.config` comme une ressource requise dans votre application, puis réglez les variables globales sur celles utilisées dans le document `config.js`.

À partir de la ligne de commande, modifiez votre répertoire en `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Ouvrez le fichier `server.js` dans un éditeur. Ajoutez les informations suivantes :

```Javascript
var config = require('./config');
```
Ensuite, ajoutez une nouvelle section à `server.js` qui inclut le code suivant :

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience
};
// array to hold logged-in users and the current logged-in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## Ajouter les informations du modèle et du schéma MongoDB à l’aide de Moongoose

La préparation anticipée portera ses fruits lorsque vous rassemblerez ces 3 fichiers dans un service API REST.

Dans le cadre de cette procédure pas à pas, utilisez MongoDB pour stocker vos tâches, comme indiqué précédemment.

Dans le fichier `config.js` que vous avez créé, appelez votre base de données **tasklist**. C’est également ce qui figure à la fin de l’URL de connexion `mongoose_auth_local`. Vous n’avez pas besoin de créer cette base de données au préalable dans MongoDB. La base de données sera créée pour vous lors de la première exécution de votre application serveur, si elle n’existe pas déjà.

Après avoir indiqué au serveur quelle base de données MongoDB utiliser, vous devez écrire du code supplémentaire pour créer le modèle et le schéma pour les tâches du serveur.

### Développer le modèle

Ce modèle de schéma est très simple. Vous pouvez le développer en fonction de vos besoins.

`name` : personne à qui la tâche est assignée. Il s’agit d’une **chaîne**.

`task` : la tâche elle-même. Il s’agit d’une **chaîne**.

`date`: la date d’échéance de la tâche. Il s’agit d’une valeur **datetime**.

`completed` : indique si la tâche est terminée ou non. Il s’agit d’une valeur **booléenne**.

### Créer le schéma dans le code

À partir de la ligne de commande, modifiez votre répertoire en `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Ouvrez le fichier `server.js` dans un éditeur. Ajoutez les informations suivantes sous l’entrée de configuration :

```Javascript
// MongoDB setup
// Set up some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Cela permet de se connecter au serveur MongoDB et d’obtenir un objet de schéma.

### Utiliser le schéma pour créer le modèle dans le code.

Sous le code que vous avez écrit précédemment, ajoutez le code suivant :

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Vous créez tout d’abord le schéma, puis un objet de modèle que vous utiliserez pour stocker vos données dans le code lorsque vous définissez vos **itinéraires**.

## Ajouter des itinéraires pour votre serveur de la tâche API REST

Maintenant que vous disposez d’un modèle de base de données, ajoutez les itinéraires que vous utiliserez pour le serveur API REST.

### À propos des itinéraires dans Restify

Dans Restify, les itinéraires fonctionnent exactement de la même façon que lorsqu’ils utilisent la pile Express. Vous définissez des itinéraires à l’aide de l’URI qui, selon vous, sera appelé par les applications clientes. En général, vous définissez vos itinéraires dans un fichier distinct. Pour les besoins de ce didacticiel, vous allez placer vos itinéraires dans le fichier `server.js`. Nous vous recommandons de les factoriser dans leur propre fichier s’ils sont destinés à la production.

Voici un exemple de modèle d’un itinéraire Restify :

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Il s’agit d’un modèle très basique. Restify et Express fournissent des fonctionnalités bien plus approfondies, comme la définition de types d’application et la création d’un routage complexe entre différents points de terminaison. Pour les besoins de ce didacticiel, nous utiliserons des itinéraires simples.

#### Ajouter des itinéraires par défaut à votre serveur

Vous allez à présent ajouter les itinéraires CRUD de base suivants : **créer**, **extraire**, **mettre à jour** et **supprimer**.

À partir de la ligne de commande, modifiez votre répertoire en `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Ouvrez le fichier `server.js` dans un éditeur. Ajoutez les informations suivantes en dessous des entrées de base de données créées précédemment :

```Javascript
/**
*
* APIs for our REST task server
*/
// Create a task
function createTask(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
// Create a new task model, fill it up and save it to Mongodb
var _task = new Task();
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
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
// Delete a task by name
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
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
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
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
log.info("listTasks was called for: ", owner);
Task.find({
owner: owner
}).limit(20).sort('date').exec(function(err, data) {
if (err)
return next(err);
if (data.length > 0) {
log.info(data);
}
if (!data.length) {
log.warn(err, "There is no tasks in the database. Add one!");
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

#### Ajouter la gestion des erreurs pour les itinéraires

Ajoutez la gestion des erreurs afin de rapporter de manière intelligible au client les problèmes que vous rencontrez.

Sous le code que vous avez écrit précédemment, ajoutez le code suivant :

```Javascript
///--- Errors for communicating something interesting back to the client
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


## Créer votre serveur

Vous avez maintenant défini votre base de données et mis vos itinéraires en place. La dernière chose à faire consiste à ajouter l’instance de serveur qui gérera vos appels.

Restify et Express permettent la personnalisation complète d’un serveur API REST, mais vous utiliserez ici la configuration la plus simple.

```Javascript
/**
* Our server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow five requests/second by IP, and burst to 10
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## Ajouter les itinéraires au serveur (sans authentification)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the passport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method like this:
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
// Register a default '/' handler
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
## Exécutez le serveur avant d’ajouter la prise en charge d’OAuth.

Testez votre serveur avant d’ajouter l’authentification

Le plus simple pour cela consiste à utiliser `curl` dans une ligne de commande. Avant cela, vous avez besoin d’un utilitaire simple qui vous permette d’analyser la sortie au format JSON. Tout d’abord, installez l’outil JSON.

`$npm install -g jsontool`

Cette opération installe totalement l’outil JSON. Après avoir installé l’outil JSON, testez le serveur :

Vérifiez que votre instance MongoDB est en cours d’exécution.

`$sudo mongodb`

Passez au répertoire `azuread` et utilisez `curl`.

`$ cd azuread` `$ node server.js`

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

Ajoutez une tâche :

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
Vous pouvez répertorier les tâches pour l’utilisateur « Brandon » de cette manière :

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Si tout fonctionne, vous êtes prêt à ajouter OAuth au serveur API REST.

Vous avez un serveur API REST avec MongoDB.

## Ajouter une authentification à votre serveur API REST

Maintenant que vous disposez d’un serveur API REST en cours d’exécution, vous pouvez l’utiliser dans Azure AD.

À partir de la ligne de commande, modifiez votre répertoire en `azuread`, si ce n’est pas déjà fait :

`cd azuread`

### Utiliser la stratégie OIDCBearerStrategy incluse avec passport-azure-ad

Jusqu’à présent, vous avez créé un serveur REST ToDo standard ne disposant d’aucune autorisation. Vous pouvez maintenant commencer à configurer l’autorisation.

Tout d’abord, vous devez indiquer que vous voulez utiliser Passport. Directement sous la configuration de l’autre serveur, ajoutez ceci :

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts Passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Lors de l’écriture d’API, vous devez toujours lier les données à un élément unique du jeton, dont un utilisateur ne peut pas usurper l’identité. Lorsque le serveur stocke les éléments ToDo, il le fait en fonction de l’**ID d’objet** de l’utilisateur dans le jeton (appelé via token.oid) placé dans le champ « propriétaire ». Cela garantit que l’utilisateur peut accéder à ses éléments ToDo, et que personne d’autre ne peut accéder aux éléments ToDo qui ont été entrés. Il n’y a pas d’exposition dans l’API du « propriétaire », afin qu’un utilisateur externe puisse demander les ToDo d’un tiers même s’ils sont authentifiés.

Ensuite, utilisez la stratégie du porteur livrée avec `passport-azure-ad`. (Nous allons simplement examiner le code pour le moment.) Placez ceci après ce que vous avez collé ci-dessus :

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementer.
/* Here we just autoregister any user and implement a FindById().
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
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
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
passport.use(oidcStrategy);
```

Passport utilise un modèle pour toutes ses stratégies (notamment Twitter et Facebook) similaire aux stratégies que respectent tous les enregistreurs de stratégie. Vous lui transmettez un `function()` qui dispose de `token` et `done` en tant que paramètres. La stratégie vous reviendra après avoir effectué toutes ses tâches. Vous devez ensuite stocker l’utilisateur et enregistrer le jeton pour ne pas avoir à le demander de nouveau.

> [AZURE.IMPORTANT]
Le code ci-dessus note tout utilisateur s’authentifiant sur votre serveur. C’est ce qu’on appelle l’enregistrement automatique. Dans les serveurs de production, il est préférable de faire passer tout utilisateur qui essaie de se connecter par un processus d’inscription de votre choix. C’est généralement le modèle des applications consommateur qui vous permettent de vous inscrire via Facebook, mais vous demandent ensuite de renseigner des informations supplémentaires. S’il ne s’agissait pas d’un programme de ligne de commande, nous aurions pu extraire l’adresse de messagerie à partir de l’objet de jeton retourné, avant d’inviter les utilisateurs à entrer des informations supplémentaires. Étant donné qu’il s’agit d’un serveur de test, nous les ajoutons simplement à la base de données en mémoire.

### Protéger les points de terminaison

Vous protégez les points de terminaison lorsque vous spécifiez l’appel `passport.authenticate()` en utilisant le protocole que vous souhaitez utiliser.

Vous pouvez modifier l’itinéraire dans le code de serveur afin d’effectuer quelque chose de plus intéressant :

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

## Exécuter à nouveau votre application de serveur et vous assurer de votre rejet

Vous pouvez de nouveau utiliser `curl` pour voir si vous disposez à présent de la protection OAuth2 sur vos points de terminaison. Effectuez ce contrôle avant d’exécuter l’un de vos Kits de développement logiciel (SDK) clients sur ce point de terminaison. Les en-têtes renvoyés doivent suffire pour vous indiquer que vous êtes sur la bonne voie.

Vérifiez que votre instance MongoDB est en cours d’exécution :

	$sudo mongodb

Passez au répertoire et utilisez `curl` :

	$ cd azuread
	$ node server.js

Essayez une commande basique, comme POST :

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Une erreur 401 est la réponse souhaitée. Elle indique que la couche Passport tente de rediriger vers le point de terminaison d’autorisation.


## Vous disposez maintenant d’un service API REST qui utilise OAuth2.

Vous êtes allé aussi loin que possible avec ce serveur sans utiliser un client compatible OAuth2. Pour ce faire, vous devez suivre une procédure pas à pas supplémentaire.

Si vous cherchez simplement des informations sur l’implémentation d’une API REST à l’aide de Restify et OAuth2, vous avez maintenant suffisamment de code pour continuer à développer votre service en tirant parti de cet exemple.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) [est fourni en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## Étapes suivantes

Vous pouvez maintenant aborder des rubriques plus avancées, telles que :

[Se connecter à une API web en utilisant iOS avec B2C](active-directory-b2c-devquickstarts-ios.md)

<!---HONumber=AcomDC_0608_2016-->