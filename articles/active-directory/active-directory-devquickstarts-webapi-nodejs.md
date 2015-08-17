<properties
	pageTitle="Prise en main d’Azure AD NodeJS | Microsoft Azure"
	description="Création d’une API web Node.js qui s’intègre avec Azure AD pour l’authentification."
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="07/17/2015"
	ms.author="brandwe"/>

# Prise en main de l’API web pour Node

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Cette procédure détaillée permet de configurer rapidement et facilement un service API REST intégré à Azure Active Directory pour la protection de l’API à l’aide du protocole OAuth2. L’exemple de serveur inclus dans le téléchargement est conçu pour fonctionner sur n’importe quelle plateforme, mais est principalement destiné à OSX et Linux.

À l’issue de cette procédure, vous serez en mesure de concevoir un serveur API REST fonctionnel avec les caractéristiques suivantes :

* Un serveur node.js exécutant une interface API REST avec JSON utilisant MongoDB comme stockage permanent
* Des API REST tirant parti de la protection de l’API OAuth2 avec des jetons porteurs à l’aide d’Azure Active Directory


Nous avons publié tout le code source de cet exemple dans GitHub sous une licence Apache 2.0. N’hésitez pas à cloner (ou mieux, à répliquer), à commenter et à soumettre des demandes d’extraction.

## À propos des modules Node.js

Nous utiliserons les modules Node.js au cours de cette procédure détaillée. Les modules sont des packages JavaScript chargeables qui fournissent une fonctionnalité spécifique à votre application. En général, vous installez un module à l’aide de l’outil en ligne de commande NPM Node.js. Néanmoins, certains modules, comme le module HTTP, sont intégrés au package Node.js principal. Les modules installés sont stockés dans le répertoire node\_modules, à la racine de votre répertoire Node.js. Chaque module du répertoire node\_modules conserve son propre répertoire node\_modules qui contient tous les modules dont il dépend. Chaque module requis dispose de son propre répertoire node\_modules. Cette structure de répertoire récursive représente la chaîne de dépendance.

Cette structure de chaîne de dépendance produit une application certes plus encombrante, mais elle garantit que toutes les dépendances sont vérifiées et que la version des modules utilisée dans le développement sera également utilisée en production. Le comportement de l’application de production est ainsi plus prévisible et cela évite les problèmes de version pouvant affecter les utilisateurs.

## Étape 1 : enregistrement d’un client Azure AD

Pour utiliser cet exemple, vous aurez besoin d’un client Azure Active Directory. Si vous n’êtes pas sûr de ce qu’est un client ou si vous ne savez pas comment en obtenir un, consultez la page [Obtention d’un client Azure AD](active-directory-howto-tenant.md).

## Étape 2 : ajout d’une API web pour votre client

Une fois que vous avez obtenu votre client Azure Active Directory, ajoutez cet exemple d’application à votre client afin de l’utiliser pour protéger l’API.

Pour autoriser l’authentification des utilisateurs par votre application, vous devez tout d’abord inscrire une nouvelle application dans votre client.

- Connectez-vous au portail de gestion Azure.
- Cliquez sur **Active Directory** dans la partie de gauche.
- Sélectionnez le client dans lequel vous souhaitez inscrire l’application.
- Cliquez sur l’onglet **Applications**, puis sur Ajouter dans le menu déroulant inférieur.
- Suivez les invites et créez une **Application Web et/ou API Web**.
    - Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.
    -	L’**URL de connexion** est l’URL de base de votre application. La valeur par défaut de la structure est `https://localhost:8888`.
    - Un **URI ID d’application** est un identificateur unique pour votre application. L’usage est d’utiliser `https://<tenant-domain>/<app-name>`, par exemple `https://contoso.onmicrosoft.com/my-first-aad-app`.
- Une fois l’inscription terminée, AAD affecte un identificateur client unique à votre application. Copiez cette valeur à partir de l’onglet Configurer, car vous en aurez besoin dans les sections suivantes.

## Étape 3 : téléchargement de node.js pour votre plateforme
Pour réussir à utiliser cet exemple, Node.js doit être installé correctement.

Installez Node.js à partir de l’adresse suivante : [http://nodejs.org](http://nodejs.org).

## Étape 4 : installation de MongoDB sur votre plateforme

Pour réussir à utiliser cet exemple, MongoDB doit être installé correctement. Nous allons utiliser MongoDB afin que notre API REST soit persistante sur plusieurs instances de serveur.

Installez MongoDB à partir de l’adresse suivante : [http://mongodb.org](http://www.mongodb.org).

**REMARQUE :** cette procédure détaillée suppose que vous utilisez l’installation et les points de terminaison du serveur par défaut pour MongoDB, c’est-à-dire les suivants au moment de la rédaction : mongodb://localhost


## Étape 5 : installation des modules Restify sur votre API web

Nous allons utiliser Restify pour concevoir notre API REST. Restify est une infrastructure d’application Node.js minimale et flexible dérivée d’Express, qui inclut un ensemble complet de fonctionnalités permettant de créer des API REST sur Connect.

### Installation de Restify

À partir de la ligne de commande, accédez au répertoire azuread. Si le répertoire **azuread** n’existe pas, créez-le.

`cd azuread - or- mkdir azuread; cd azuread`

Tapez la commande suivante :

`npm install restify`

Cette commande permet d’installer Restify.

#### UNE ERREUR EST-ELLE SURVENUE ?

Sur certains systèmes d’exploitation, NPM peut générer une erreur Error: EPERM, chmod ’/usr/local/bin/..’ et une demande pour essayer d’exécuter le compte en tant qu’administrateur. Si cette erreur survient, utilisez la commande sudo pour exécuter npm avec des privilèges plus élevés.

#### UNE ERREUR RELATIVE À DTRACE EST-ELLE SURVENUE ?

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


Restify fournit un mécanisme puissant pour suivre les appels REST utilisant DTrace. Toutefois, de nombreux systèmes d’exploitation ne disposent pas de DTrace. Vous pouvez ignorer ces erreurs.


Le résultat de cette commande doit ressembler à ceci :


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


## Étape 6 : installation de Passport.js dans votre API web

[Passport](http://passportjs.org/) est un intergiciel d’authentification pour Node.js. Extrêmement flexible et modulaire, Passport peut discrètement intervenir dans n’importe quelle application web basée sur Express ou Restify. Une gamme complète de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et d’un mot de passe, de Facebook, de Twitter et bien d’autres. Nous avons développé une stratégie pour Azure Active Directory. Nous installerons ce module, puis nous y ajouterons le plug-in stratégique Active Directory Azure.

À partir de la ligne de commande, accédez au répertoire azuread.

Saisissez la commande suivante pour installer passport.js.

`npm install passport`

Le résultat de la commande doit ressembler à ceci :

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## Étape 7 : ajout de la prise en charge du jeton porteur de Passport.js à votre API web

Ensuite, ajoutez la stratégie de porteur à l’aide de passport-bearer-http, un gestionnaire de porteur pour [Passport](http://passportjs.org/). Ajoutez également la prise en charge du gestionnaire de jetons JWT à l’aide de node-jwt.

**REMARQUE :** bien qu’OAuth2 fournisse une infrastructure dans laquelle tout type de jeton connu peut être émis, seuls certains types de jeton sont utilisés de manière généralisée. Pour protéger les points de terminaison qui se sont révélés être des jetons porteurs. Les jetons porteurs sont les jetons les plus largement émis dans OAuth2, et de nombreuses implémentations considèrent qu’ils sont le seul type de jeton émis.

Depuis la ligne de commande, accédez au répertoire **azuread**.

Saisissez la commande suivante pour installer les modules Passport.js :

- `npm install passport-oauth`
- `npm install passport-http-bearer`
- `npm install node-jwt`

Le résultat de la commande doit ressembler à ceci :

	ms-passport-wsfed-saml2@0.3.8 node_modules\passport-oauth  
	├── xtend@2.0.3
	├── xml-crypto@0.0.9
	├── xmldom@0.1.13
	└── xml2js@0.1.14 (sax@0.5.2)


## Étape 8 : ajout des modules MongoDB à votre API web

Nous allons utiliser MongoDB comme magasin de données. Par conséquent, nous devons installer le plug-in le plus largement utilisé pour gérer les modèles et schémas, appelé Mongoose, ainsi que le pilote de base de données de MongoDB, également appelé MongoDB.


* `npm install mongoose`
* `npm install mongodb`

## Étape 9 : installation de modules supplémentaires

Ensuite, nous devons installer les autres modules requis.


Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`


Entrez les commandes suivantes pour installer les modules suivants dans le répertoire node\_modules :

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


## Étape 10 : création d’un server.js avec vos dépendances

Le fichier server.js fournit la majorité des fonctionnalités du serveur de l’API web. La plupart du code sera ajouté à ce fichier. À des fins de production, vous souhaitez refactoriser la fonctionnalité en fichiers plus petits, par exemple des itinéraires et des contrôleurs distincts. Pour les besoins de cette démonstration, nous utiliserons server.js pour cette fonctionnalité.

Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

Créez un fichier `server.js` dans votre éditeur favori et ajoutez les informations suivantes :

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
```

Enregistrez le fichier . Nous y reviendrons rapidement.

## Étape 11 : création d’un fichier de configuration pour stocker vos paramètres Azure AD

Ce fichier de code transmet les paramètres de configuration de votre portail Azure Active Directory vers Passport.js. Vous avez créé ces valeurs de configuration lorsque vous avez ajouté l’API web au portail, dans la première partie de la procédure détaillée. Copiez le code ; nous vous expliquerons ensuite ce qu’il faut placer dans les valeurs de ces paramètres.


Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

Créez un fichier `config.js` dans votre éditeur favori et ajoutez les informations suivantes :

```Javascript
// Don't commit this file to your public repos
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
    openid_configuration: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
    openid_keys: 'https://login.microsoftonline.com/common/discovery/keys', // For using Microsoft you should never need to change this. If absent will attempt to get from openid_configuration
}

```



**REMARQUE :** vous n’aurez probablement jamais besoin de modifier ces valeurs.

**REMARQUE :** nous remplaçons régulièrement et fréquemment nos clés. Vérifiez que votre extraction est toujours effectuée à partir de l’URL « openid\_keys » et que l’application peut accéder à Internet.


## Étape 12 : ajout de configuration à votre fichier server.js

Nous avons besoin de lire ces valeurs dans le fichier de configuration que vous venez de créer dans notre application. Pour ce faire, nous ajoutons simplement le fichier .config comme une ressource requise dans notre application, puis nous réglons les variables globales sur celles utilisées dans le document config.js.

Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

Ouvrez votre fichier `server.js` dans votre éditeur favori et ajoutez les informations suivantes :

```Javascript
var config = require('./config');
```
Ensuite, ajoutez une nouvelle section à `server.js` avec le code suivant :

```Javascript
/**
* Setup some configuration
*/
var mongoose = require('mongoose/');
var serverPort = process.env.PORT || 8888;
var serverURI = ( process.env.PORT ) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

```
## Étape 13 : création d’un fichier d’assistance metadata.js pour aider à l’analyse des métadonnées/jetons

L’objectif étant de conserver uniquement la logique d’application dans le fichier server.js, il est judicieux de placer quelques méthodes d’assistance dans un fichier distinct. Ces méthodes nous aident simplement à analyser les métadonnées OpenID Connect et ne concernent pas le scénario de base. Il est préférable de les déplacer. Nous ajouterons davantage d’éléments à ce fichier au fur et à mesure de la procédure détaillée.

***REMARQUE :*** notez que le fichier metadata.js analyse le code XML à la recherche de SAML et WS-Fed ainsi que JSON à la recherche de OpenID Connect. Il s’agit d’une analyse par défaut, car vous utiliserez également ce fichier dans nos autres exemples. Vous pouvez l’ignorer.

Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

Créez un fichier `metadata.js` dans votre éditeur favori et ajoutez les informations suivantes :

```Javascript

'use strict';

var xml2js = require('xml2js');
var request = require('request');
var aadutils = require('./aadutils');
var async = require('async');

// Logging

var bunyan = require('bunyan');
var log = bunyan.createLogger({name: 'Microsoft OpenID Connect Passport Strategy'});

var Metadata = function (url, authtype) {


  if(!url) {
    throw new Error("Metadata: url is a required argument");
  }
  if(!authtype) {
    throw new Error('OIDCBearerStrategy requires an authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"');
  }

  this.url = url;
  this.metadata = null;
  this.authtype = authtype;
  log.info(authtype, 'Metadata requested for authentication type');
};

Object.defineProperty(Metadata, 'url', {
  get: function () {
    return this.url;
  }
});

Object.defineProperty(Metadata, 'saml', {
  get: function () {
    return this.saml;
  }
});

Object.defineProperty(Metadata, 'wsfed', {
  get: function () {
    return this.wsfed;
  }
});

Object.defineProperty(Metadata, 'oidc', {
  get: function () {
    return this.oidc;
  }
});


Object.defineProperty(Metadata, 'metadata', {
  get: function () {
    return this.metadata;
  }
});

Metadata.prototype.updateSamlMetadata = function(doc, next) {
  log.info('Request to update the SAML Metadata');
  try {

    this.saml = {};

    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var idp = aadutils.getElement(entity, 'IDPSSODescriptor');
    var signOn = aadutils.getElement(idp[0], 'SingleSignOnService');
    var signOff = aadutils.getElement(idp[0], 'SingleLogoutService');
    var keyDescriptor = aadutils.getElement(idp[0], 'KeyDescriptor');
    this.saml.loginEndpoint = signOn[0].$.Location;
    this.saml.logoutEndpoint = signOff[0].$.Location;

    // copy the x509 certs from the metadata
    this.saml.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.saml.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid SAMLP Federation Metadata ' + e.message));
  }
};

Metadata.prototype.updateOidcMetadata = function(doc, next) {
  log.info('Request to update the Open ID Connect Metadata');
  try {
    this.oidc = {};

    var issuer = doc['issuer'];
    var keyDescriptor = aadutils.getElement(idp[0], 'keys');

    // copy the x509 certs from the metadata
    this.oidc.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.oidc.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid Open ID Connect Federation Metadata ' + e.message));
  }
};


Metadata.prototype.updateWsfedMetadata = function(doc, next) {
  log.info('Request to update the WS Federation Metadata');
  try {
    this.wsfed = {};
    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var roles = aadutils.getElement(entity, 'RoleDescriptor');
    for(var i = 0; i < roles.length; i++) {
      var role = roles[i];
      if(role['fed:SecurityTokenServiceEndpoint']) {
        var endpoint = role['fed:SecurityTokenServiceEndpoint'];
        var endPointReference = aadutils.getFirstElement(endpoint[0],'EndpointReference');
        this.wsfed.loginEndpoint = aadutils.getFirstElement(endPointReference,'Address');

        var keyDescriptor = aadutils.getElement(role, 'KeyDescriptor');
        // copy the x509 certs from the metadata
        this.wsfed.certs = [];
        for (var j=0;j<keyDescriptor.length;j++) {
          this.wsfed.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
        }
        break;
      }
    }

    return next(null);
  } catch (e) {
    next(new Error('Invalid WSFED Federation Metadata ' + e.message));
  }
};

Metadata.prototype.fetch = function(callback) {
  var self = this;
  log.info("Fetching metadata from the provided metadata URL: " + self.url);
  async.waterfall([
    // fetch the Federation metadata for the AAD tenant
    function(next){
      request(self.url, function (err, response, body) {
        if(err) {
          next(err);
        } else if(response.statusCode !== 200) {
          next(new Error("Error:" + response.statusCode +  " Cannot get AAD Federation metadata from " + self.url));
        } else {
          log.info(body, "retreived");
          next(null, body);
        }
      });
    },
    function(body, next){
      // parse the AAD Federation metadata xml

      if(self.authtype == "saml" || self.authtype == "wsfed") {
      log.info(body, "Parsing XML retreived from the endpoint");
      var parser = new xml2js.Parser({explicitRoot:true});
      // Note: xml responses from Azure AAD have a leading \ufeff which breaks xml2js parser!
      parser.parseString(body.replace("\ufeff", ""), function (err, data) {
        self.metatdata = data;
        next(err);

      });
    } else if(self.authtype == "oidc") {
      log.info(body, "Parsing JSON retreived from the endpoint");
      JSON.parse(body, function (err, data) {
        self.metatdata = data;
        next(err);
      });

    } else {

       next(new Error("Error: No Authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"));
    }

    },

    function(next){
      if(self.authtype = "saml") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateSamlMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "wsfed") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateWsfedMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "oidc") {
      self.updateOidcMetadata(self.metadata, next);
    }},
  ], function (err) {
    // return err or success (err === null) to callback
    callback(err);
  });
};

exports.Metadata = Metadata;
```
Comme vous pouvez le voir à partir du code, l’URL openid est simplement transmise dans `config.js`, puis analysée pour obtenir des d’informations que nous utiliserons dans le fichier `server.js`. N’hésitez pas à examiner ce code et à le développer si nécessaire.

### Chargez le fichier metadata.js dans votre fichier server.js.

Nous devons indiquer à notre serveur où obtenir les méthodes que vous venez de rédiger.

Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

Ouvrez votre fichier `server.js` dans votre éditeur favori et ajoutez les informations suivantes :

```Javascript
var metadata = require('./metadata);
```
Ensuite, ajoutez à la fin de la section `Configuration` cet appel pour envoyer le document de métadonnées dans notre `config.js`, vers l’analyseur que nous avons rédigé :

```Javascript
this.aadutils = new var Metadata = require('./metadata').Metadata;
```

## Étape 14 : ajout des informations du modèle et du schéma MongoDB à l’aide de Moongoose

Toute cette préparation va maintenant prouver son utilité : associez ces trois fichiers dans un service API REST.

Pour cette procédure détaillée, nous utilisons MongoDB pour stocker les tâches, comme indiqué dans l’***étape 4***.

Si vous vous rappelez le contenu du fichier `config.js` que nous avons créé dans l’***étape 11***, vous savez que nous avons appelé notre base de données `tasklist`, car c’était ce que nous avions placé à la fin de l’URL de connexion mogoose\_auth\_local. Vous n’avez pas besoin de créer cette base de données au préalable dans MongoDB ; il la crée lors de la première exécution de notre application de serveur (à condition qu’elle n’existe pas déjà).

Maintenant que nous avons indiqué au serveur quelle base de données MongoDB nous souhaitons utiliser, nous devons écrire du code supplémentaire pour créer le modèle et le schéma pour les tâches du serveur.

#### Considérations sur le modèle

Notre modèle de schéma est très simple et vous pouvez le développer en fonction de vos besoins.

NOM : le nom de la personne à qui la tâche est assignée. ***Chaîne***

TÂCHE : la tâche elle-même. ***Chaîne***

DATE : la date d’échéance de la tâche. ***DATEHEURE***

TERMINÉ : indique si la tâche est terminée ou non. ***BOOLÉEN***

#### Création du schéma dans le code


Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

Ouvrez votre fichier `server.js` dans votre éditeur favori et ajoutez les informations suivantes sous l’entrée de configuration :

```Javascript
/**
*
* Connect to MongoDB
*/

global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;  
```
Cela permet de se connecter au serveur MongoDB et d’obtenir un objet de schéma.

#### En utilisant le schéma, créez le modèle dans le code.

Sous le code que vous avez écrit précédemment, ajoutez le code suivant :

```Javascript
/**
/ Here we create a schema to store our tasks. Pretty simple schema for now.
*/

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
Comme vous avez pu le constater dans le code, nous créons notre schéma, puis un objet de modèle que nous utilisons pour stocker nos données dans le code lors du paramétrage de nos ***itinéraires***.

## Étape 15 : ajout de nos itinéraires pour notre serveur de tâche de l’API REST

Maintenant que nous disposons d’un modèle de base de données, ajoutons les itinéraires que nous utiliserons pour notre serveur API REST.

### À propos des itinéraires dans Restify

Dans Restify, les itinéraires fonctionnent de la même façon que dans la pile Express. Vous définissez des itinéraires à l’aide de l’URI qui, selon vous, sera appelé par les applications clientes. En général, vous définissez vos itinéraires dans un fichier distinct. Pour cet exemple, nous placerons nos itinéraires dans le fichier server.js. Nous vous recommandons de les factoriser dans leur propre fichier s’ils sont destinés à la production.

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


Il s’agit d’un modèle très basique. Restify (et Express) fournissent des fonctionnalités bien plus approfondies, comme la définition de types d’application et la création d’un routage complexe entre différents points de terminaison. Dans le cade de cet exemple, nous allons créer des itinéraires très simples.

#### Ajout d’itinéraires par défaut à notre serveur

Nous allons à présent ajouter les itinéraires CRUD de base suivants : Créer, Extraire, Mettre à jour et Supprimer.

Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

Ouvrez votre fichier `server.js` dans votre éditeur favori et ajoutez les informations suivantes en-dessous des entrées de base de données créées précédemment :

```Javascript

/**
 *
 * APIs
 */

function createTask(req, res, next) {

	// Resitify currently has a bug which doesn't allow you to set default headers
  	// This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
  var _task = new Task();

        if (!req.params.task) {
                req.log.warn('createTask: missing task');
                next(new MissingTaskError());
                return;
        }


  _task.owner = req.params.owner;
   _task.task = req.params.task;
   _task.date = new Date();

  _task.save(function (err) {
  	if (err) {
        req.log.warn(err, 'createTask: unable to save');
        next(err);
    } else {
    res.send(201, _task);

			}
  });

  return next();

}


/**
 * Deletes a Task by name
 */
function removeTask(req, res, next) {

        Task.remove( { task:req.params.task }, function (err) {
                if (err) {
                        req.log.warn(err,
                                     'removeTask: unable to delete %s',
                                     req.params.task);
                        next(err);
                } else {
                        res.send(204);
                        next();
                }
        });
}

/**
 * Deletes all Tasks. A wipe
 */
function removeAll(req, res, next) {
        Task.remove();
        res.send(204);
        return next();
}    });
}


/**
 *
 *
 *
 */
function getTask(req, res, next) {


        Task.find(req.params.name, function (err, data) {
                if (err) {
                        req.log.warn(err, 'get: unable to read %s', req.params.name);
                        next(err);
                        return;
                }

                res.json(data);
        });

        return next();
}


/**
 * Simple returns the list of TODOs that were loaded.
 *
 */

function listTasks(req, res, next) {
  // Resitify currently has a bug which doesn't allow you to set default headers
  // This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

  console.log("server getTasks");

  Task.find().limit(20).sort('date').exec(function (err,data) {

    if (err)
      return next(err);

    if (data.length > 0) {
            console.log(data);
        }

    if (!data.length) {
            console.log('there was a problem');
            console.log(err);
            console.log("There is no tasks in the database. Did you initalize the database as stated in the README?");
        }

    else {

        res.json(data);

        }
  });

  return next();
}
```

### Ajout de la gestion des erreurs pour les itinéraires

Il est judicieux d’ajouter une gestion des erreurs afin que nous puissions communiquer au client le problème que nous avons rencontré et ce, de manière intelligible.

Ajoutez le code suivant sous le code que vous avez écrit précédemment :

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


function TaskExistsError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 409,
                restCode: 'TaskExists',
                message: name + ' already exists',
                constructorOpt: TaskExistsError
        });

        this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 404,
                restCode: 'TaskNotFound',
                message: name + ' was not found',
                constructorOpt: TaskNotFoundError
        });

        this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## Étape 16 : création de votre serveur

Nous avons défini notre base de données et nos itinéraires sont en place. La dernière étape est d’ajouter l’instance de serveur qui va gérer nos appels.

Restify (et Express) disposent de nombreuses options de personnalisation approfondie applicables à un serveur API REST, mais à nouveau, nous allons utiliser la configuration la plus simple pour cet exemple.

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
        name: "Azure Active Directroy TODO Server",
    version: "1.0.0",
    formatters: {
        'application/json': function(req, res, body){
            if(req.params.callback){
                var callbackFunctionName = req.params.callback.replace(/[^A-Za-z0-9_\.]/g, '');
                return callbackFunctionName + "(" + JSON.stringify(body) + ");";
            } else {
                return JSON.stringify(body);
            }
        },
        'text/html': function(req, res, body){
            if (body instanceof Error)
                        return body.stack;

                      if (Buffer.isBuffer(body))
                        return body.toString('base64');

                return util.inspect(body);
        },
        'application/x-www-form-urlencoded': function(req, res, body){
            if (body instanceof Error) {
                    res.statusCode = body.statusCode || 500;
                    body = body.message;
            } else if (typeof (body) === 'object') {
                body = body.task || JSON.stringify(body);
            } else {
                body = body.toString();
            }

        res.setHeader('Content-Length', Buffer.byteLength(body));
        return (body);
        }
    }
});

        // Ensure we don't drop data on uploads
        server.pre(restify.pre.pause());

        // Clean up sloppy paths like //todo//////1//
        server.pre(restify.pre.sanitizePath());

        // Handles annoying user agents (curl)
        server.pre(restify.pre.userAgentConnection());

        // Set a per request bunyan logger (with requestid filled in)
        server.use(restify.requestLogger());

        // Allow 5 requests/second by IP, and burst to 10
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

        // This lets us push JSON to the REST API endpoint as well. Maps x: y as /name:value

        server.use(restify.bodyParser({ mapParams: false }));

        /// Now the real handlers. Here we just CRUD

        server.get('/tasks', listTasks);
        server.head('/tasks', listTasks);
        server.get('/tasks/:name', getTask);
        server.head('/tasks/:name', getTask);
        server.post('/tasks/:name/:task', createTask);
        server.del('/tasks/:name/:task', removeTask);
        server.del('/tasks/:name', removeTask);
        server.del('/tasks', removeAll, function respond(req, res, next) { res.send(204); next(); });


        // Register a default '/' handler

        server.get('/', function root(req, res, next) {
                var routes = [
                        'GET     /',
                        'POST    /tasks/:name/:task',
                        'GET     /tasks',
                        'PUT     /tasks/:name',
                        'GET     /tasks/:name',
                        'DELETE  /tasks/:name/:task'
                ];
                res.send(200, routes);
                next();
        });

  server.listen(serverPort, function() {

  var consoleMessage = '\n Azure Active Directory Tutorial'
  consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++'
  consoleMessage += '\n %s server is listening at %s';
  consoleMessage += '\n Open your browser to %s/tasks\n';
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n'
  consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n'
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n'  

  console.log(consoleMessage, server.name, server.url, server.url, server.url);

});
```

## Étape 17 : exécution du serveur avant l’ajout de la prise en charge d’OAuth

Il est important de vérifier qu’il n’y a aucune erreur avant de continuer vers la partie OAuth de la procédure.

Le plus simple pour cela consiste à utiliser `curl` dans une ligne de commande. Avant cela, nous avons besoin d’un utilitaire simple qui nous permette d’analyser la sortie au format JSON. Pour ce faire, vous devez installer l’outil [json](https://github.com/trentm/json), car tous les exemples ci-dessous l’utilisent.

	$npm install -g jsontool

Cette opération installe totalement l’outil JSON. Maintenant que l’outil JSON est installé, allons jouer avec le serveur :

Tout d’abord, vérifiez que votre instance MongoDB est en cours d’exécution.

	$sudo mongod

Accédez ensuite au répertoire et commencez à utiliser curl.

	$ cd azuread
	$ node server.js

	$ curl -isS http://127.0.0.1:8888 | json
	HTTP/1.1 200 OK
	Connection: close
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 145
	Date: Wed, 29 Jan 2014 03:41:24 GMT

	[
  	"GET     /",
  	"POST    /tasks/:owner/:task",
  	"GET     /tasks",
  	"DELETE  /tasks",
  	"PUT     /tasks/:owner",
  	"GET     /tasks/:owner",
  	"DELETE  /tasks/:task"
	]

Nous pouvons à présent ajouter une tâche de la manière suivante :

	$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello

La réponse doit être la suivante :

	HTTP/1.1 201 Created
	Connection: close
	Access-Control-Allow-Origin: *
	Access-Control-Allow-Headers: X-Requested-With
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 5
	Date: Tue, 04 Feb 2014 01:02:26 GMT

	Hello

Et nous pouvons répertorier des tâches pour Brandon de cette manière :

	$ curl -isS http://127.0.0.1:8888/tasks/brandon/

Si tout cela fonctionne, nous sommes prêts à ajouter OAuth au serveur API REST.

## Étape 18 : ajout du code Passport.js à notre serveur API REST

Maintenant que nous avons une API REST en cours d’exécution (félicitations d’ailleurs !), offrons-lui une utilité dans Azure AD.

Depuis la ligne de commande, accédez au dossier **azuread** si vous ne vous y trouvez pas déjà :

`cd azuread`

### Étape 1 : ajout de nos modules Passport

Ouvrez votre fichier `server.js` dans votre éditeur favori et ajoutez les informations suivantes en-dessous de l’indication du lieu où charger les modules, mentionné précédemment. Cette information se situe dans la partie supérieure du fichier et doit être juste après l’importation `var aadutils = require('./aadutils');`.

```Javascript
/*
*
* Load our old friend Passport for OAuth2 flows
*/

var passport = require('passport')
  , OAuth2Strategy = require('passport-oauth').OAuth2Strategy;
```

### 2\. Indiquez au serveur que nous utilisons l’authentification.

Ouvrez votre fichier `server.js` dans votre éditeur favori et ajoutez les informations suivantes **sous l’instruction server.get()** où vous avez défini vos itinéraires, mais au-dessus de la méthode **server.listen()**.


Nous devons indiquer à Restify de commencer à utiliser ses `authorizationParser()` et d’examiner le contenu de l’en-tête d’autorisation.

```Javascript
        server.use(restify.authorizationParser());


```


### 3\. Ajoutez le module Passport OAuth2 à notre code.

Ici, nous utilisons les paramètres propres à OAuth2 que nous avons ajoutés au fichier config.js. Si notre fichier `aadutils.js` a fait son travail d’analyse de notre document de métadonnées de fédération, toutes ces valeurs doivent être remplies, même si elles sont vides dans le fichier config.js.

```Javascript
// Now our own handlers for authentication/authorization
// Here we only use Oauth2 from Passport.js

passport.use('provider', new OAuth2Strategy({
    authorizationURL: authEndpoint,
    tokenURL: tokenEndpoint,
    clientID: clientID,
    clientSecret: clientSecret,
    callbackURL: callbackURL
  },
  function(accessToken, refreshToken, profile, done) {
    User.findOrCreate({ UserId: profile.id }, function(err, user) {
      done(err, user);
    });
  }
));

// Let's start using Passport.js

server.use(passport.initialize());

```
### Étape 4 : ajout des itinéraires pour l’authentification OAuth

```Javascript
// Redirect the user to the OAuth 2.0 provider for authentication.  When
// complete, the provider will redirect the user back to the application at
//     /auth/provider/callback

server.get('/auth/provider', passport.authenticate('provider'));

// The OAuth 2.0 provider has redirected the user back to the application.
// Finish the authentication process by attempting to obtain an access
// token.  If authorization was granted, the user will be logged in.
// Otherwise, authentication has failed.

server.get('/auth/provider/callback',
  passport.authenticate('provider', { successRedirect: '/',
                                      failureRedirect: '/login' }));
```

### Étape 5 : ajout d’une méthode d’assistance IsAuthenticated() aux itinéraires

```Javascript
// Simple route middleware to ensure user is authenticated.
//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.

var ensureAuthenticated = function(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/login');
};

```

### Étape 6 : ajout d’un mécanisme de mise en cache pour les cookies

```Javascript
// Passport session setup.
//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});
```
### Étape 7 : protection de certains points de terminaison (étape finale)

Pour protéger les points de terminaison, spécifiez l’appel passport.authenticate() avec le protocole que vous souhaitez utiliser.

Nous allons modifier notre code d’itinéraire pour faire quelque chose de plus intéressant :

```Javascript
server.get('/tasks', passport.authenticate('provider', { session: false }), listTasks);
```


## Étape 19 : ré-exécution de votre application serveur et contrôle du rejet

Nous allons de nouveau utiliser `curl` pour voir si nous disposons à présent de la protection OAuth2 sur nos points de terminaison. Nous effectuons ce contrôle avant d’exécuter l’un de nos Kits de développement logiciel (SDK) clients sur ce point de terminaison. Les en-têtes renvoyés doivent être suffisants pour nous dire que nous sommes sur le bon chemin d’accès.

Tout d’abord, vérifiez que votre instance MongoDB est en cours d’exécution.

	$sudo mongod

Accédez ensuite au répertoire et commencez à utiliser curl.

	$ cd azuread
	$ node server.js

Essayez une commande basique, comme GET :

	$ curl -isS http://127.0.0.1:8888/tasks/
	HTTP/1.1 302 Moved Temporarily
	Connection: close
	Location: https://login.windows.net/468a75f4-f9a7-4dc4-a527-4f4522734790/oauth2/authorize?response_type=code&redirect_uri=&client_id=123
	Content-Length: 0
	Date: Tue, 04 Feb 2014 02:15:14 GMT


Un 302 est la réponse que vous cherchez ici, car cela indique que la couche de Passport tente de rediriger vers le point de terminaison d’autorisation, ce qui est exactement ce que vous souhaitez.

## Félicitations ! Vous avez un service d’API REST utilisant OAuth2 !

Vous êtes allé aussi loin que possible avec ce serveur sans utiliser un client compatible OAuth2. Vous devrez suivre une autre procédure détaillée.

Si vous recherchiez simplement des informations concernant l’implémentation d’une API REST à l’aide de Restify et d’OAuth2, vous avez plus de code que nécessaire pour poursuivre le développement de votre service et l’apprentissage de la création à partir de cet exemple.

Si vous êtes intéressé par les étapes suivantes dans votre parcours de la bibliothèque ADAL, voici quelques clients de la bibliothèque ADAL pris en charge sur lesquels nous vous recommandons de continuer à travailler :

Clonez simplement sur votre ordinateur de développement et configurez comme indiqué dans la procédure détaillée.

[Bibliothèque ADAL pour iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[Bibliothèque ADAL pour Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[Bibliothèque ADAL pour .Net](http://msdn.microsoft.com/library/windowsazure/jj573266.aspx)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
 

<!---HONumber=August15_HO6-->