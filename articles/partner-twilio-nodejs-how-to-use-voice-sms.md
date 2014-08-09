<properties linkid="dev-nodejs-how-to-twilio-voice-sms-service" urlDisplayName="Twilio Voice and SMS Service" pageTitle="Using Twilio for Voice, VoIP, and SMS Messaging in Azure" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="Node.js" title=" VoIP" authors="" solutions="" manager="" editor="" />

Utilisation de Twilio pour les fonctionnalités vocales, VoIP et de messagerie SMS dans Azure
============================================================================================

Ce guide montre comment générer des applications qui communiquent avec Twilio et node.js sur Azure.

Sommaire
--------

-   [Présentation de Twilio](#whatis)
-   [Inscription à Twilio (remise Microsoft)](#signup)
-   [Création et déploiement d'un site Web Azure node.js](#azuresite)
-   [Configuration du module Twilio](#twiliomodule)
-   [Exécution d'un appel téléphonique](#makecall)
-   [Envoi d'un SMS](#sendmessage)
-   [Étapes suivantes](#nextsteps)

## Présentation de Twilio

Twilio est une plateforme API qui facilite pour les développeurs le passage et la réception d'appels téléphoniques, l'envoi et la réception de SMS. Elle incorpore l'appel VoIP dans des applications basées sur un navigateur et mobiles natives. Survolons rapidement le fonctionnement de Twilio avant de l'étudier plus en détail.

### Réception d'appels et de SMS

Twilio permet aux développeurs d'[acheter des numéros de téléphone programmables](https://www.twilio.com/user/account/phone-numbers/available/local) qui peuvent être utilisés à la fois pour envoyer et recevoir des appels et des SMS. Lorsqu'un numéro Twilio reçoit un appel ou un SMS, il envoie à votre application Web une requête HTTP POST ou GET, vous demandant des instructions sur la gestion de l'appel ou du SMS. Votre serveur répond à la requête HTTP de Twilio avec [TwiML](https://www.twilio.com/docs/api/twiml), un jeu simple de balises XML qui contient des instructions sur la gestion d'un appel ou d'un SMS. Nous vous présenterons des exemples de TwiML un peu plus loin.

### Passage d'appels et envoi de SMS

En émettant des requêtes HTTP vers l'API de service Web Twilio, les développeurs peuvent envoyer des SMS et passer des appels téléphoniques. Pour les appels, le développeur doit également spécifier une URL qui renvoie des instructions TwiML relatives à la gestion de l'appel une fois qu'il est connecté.

### Incorporation des fonctionnalités VoIP dans un code de l'interface utilisateur (JavaScript, iOS ou Android)

Twilio offre un Kit de développement logiciel (SDK) côté client qui peut transformer n'importe quel navigateur Web, application iOS ou Android en un téléphone VoIP. Dans cet article, nous nous concentrerons sur l'utilisation de l'appel VoIP dans le navigateur. En plus du Kit de développement logiciel (SDK) JavaScript Twilio s'exécutant dans le navigateur, une application côté serveur (notre application node.js) doit être utilisée pour émettre un « jeton de fonctionnalité » au client JavaScript. Vous trouverez plus d'informations sur l'utilisation de VoIP avec node.js [sur le blog des développeurs Twilio](http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html).

## Inscription à Twilio (remise Microsoft)

Avant d'utiliser les services Twilio, vous devez [créer un compte](http://ahoy.twilio.com/azure). Les clients Microsoft Azure reçoivent une remise spéciale. Pour y avoir droit, [veillez à vous inscrire ici](http://ahoy.twilio.com/azure) !

## Création et déploiement d'un site Web Azure node.js

Ensuite, vous allez devoir créer un site Web node.js s'exécutant sur Azure. [La documentation officielle relative à cette création est située ici](http://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/create-a-website-(mac)/). À un niveau supérieur, vous réaliserez les tâches suivantes :

-   Création d'un compte Azure si vous n'en possédez pas déjà un
-   Utilisation de la console d'administration pour créer un site Web
-   Ajout d'une prise en charge du contrôle de code source (nous présumons que vous avez utilisé git)
-   Création d'un fichier `server.js` avec une application Web node.js simple
-   Déploiement de cette application simple sur Azure

## Configuration du module Twilio

Ensuite, nous commencerons à rédiger une simple application node.js qui utilise l'API Twilio. Avant de commencer, nous devons configurer les informations d'identification de notre compte Twilio.

### Configuration des informations d'identification Twilio dans des variables d'environnement système

Afin d'émettre des requêtes authentifiées au serveur principal Twilio, nous avons besoin de notre SID de compte et de notre jeton d'authentification, qui fonctionnent en tant que nom d'utilisateur et mot de passe pour notre compte Twilio. Le moyen le plus sécurisé de configurer ces deux éléments pour une utilisation avec le module Node dans Azure est d'utiliser les variables d'environnement système, que vous pouvez définir directement dans la console d'administration d'Azure.

Sélectionnez votre site Web node.js et cliquez sur le lien « CONFIGURER ». Si vous faites un peu défiler vers le bas, vous verrez une zone dans laquelle vous pouvez définir les propriétés de configuration de votre application. Entrez les informations d'identification de votre compte Twilio ([disponibles dans le tableau de bord Twilio](https://www.twilio.com/user/account)) comme illustré : assurez-vous de les nommer « TWILIO\_ACCOUNT\_SID » et « TWILIO\_AUTH\_TOKEN », respectivement :

![Console d'administration Azure](./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png)

Une fois que vous avez configuré ces variables, redémarrez votre application dans la console Azure.

### Déclaration du module Twilio dans package.json

Ensuite, nous devons créer un fichier package.json pour gérer nos dépendances de module Node via [npm](http://npmjs.org). Au même niveau que le fichier « server.js » que vous avez créé dans le didacticiel Azure/node.js, créez un fichier nommé « package.json ». Placez-y les éléments suivants :

  {
	"name": "application-name",
	"version": "0.0.1",
	"private": true,
	"scripts": { 
	  "start": "node server" 
	}, 
	"dependencies": { 
	  "express": "3.1.0", 
	  "ejs": "*", 
	  "twilio":"*" 
	} 
  }

Cette instruction déclare le module Twilio en tant que dépendance ainsi que la [structure Web Express](http://expressjs.com) courante et le moteur de modèles EJS. Tout est maintenant prêt et nous pouvons passer à l'écriture du code.

## Exécution d'un appel téléphonique

Créons un simple formulaire qui passera un appel vers un numéro que nous choisissons. Ouvrez server.js et saisissez le code suivant. Lorsque le code indique "CHANGE\_ME", placez le nom de votre site Web Azure à cet endroit :

  // Dépendances du module 
  var express = require('express'), 
	  path = require('path'), 
	  http = require('http'), 
	  twilio = require('twilio');

  // Créez l'application Web Express 
  var app = express();

  // Configuration Express 
  app.configure(function(){ 
	  app.set('port', process.env.PORT || 3000); 
	  app.set('views', \_\_dirname + '/views'); 
	  app.set('view engine', 'ejs'); 
	  app.use(express.favicon()); 
	  app.use(express.logger('dev')); 
	  app.use(express.bodyParser()); 
	  app.use(express.methodOverride()); 
	  app.use(app.router); 
	  app.use(express.static(path.join(\_\_dirname, 'public'))); 
  }); 
  app.configure('development', function(){ 
	  app.use(express.errorHandler()); 
  });

  // Effectuez le rendu d'une interface utilisateur HTML pour la page d'accueil de l'application 
  app.get('/', function(request, response) { 
	  response.render('index'); 
  });

  // Gérez la méthode POST du formulaire pour passer un appel 
  app.post('/call', function(request, response) { 
	  var client = twilio(); 
	  client.makeCall({ 
		  // Effectuez un appel vers ce numéro 
		  to:request.body.number,

          // Indiquez le numéro Twilio que vous avez acheté - voir :
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // Une URL dans notre application qui génère TwiML
          // Remplacez "CHANGE_ME" par le nom de votre application
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Revenez à la page d'accueil
          response.redirect('/');
      });
  });

  // Générez TwiML pour traiter un appel sortant 
  app.post('/outbound\_call', function(request, response) { 
	  var twiml = new twilio.TwimlResponse();

      // Laissez un message au destinataire de l'appel 
      twiml.say('Bonjour, merci d'avoir consulté Twilio et Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());
  });

  // Démarrez le serveur 
  http.createServer(app).listen(app.get('port'), function(){ 
	console.log("Express server listening on port " + app.get('port')); 
  });

Ensuite, créez un répertoire appelé « views » et créez-y un fichier nommé « index.ejs » avec le contenu suivant :

&lt;!DOCTYPE html\> 
&lt;html\> 
	&lt;head\> 
		&lt;title\>Twilio Test&lt;/title\> 
		&lt;style\> input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; } 
		&lt;/style\> 
	&lt;/head\> 
	&lt;body\> 
		&lt;h1\>Twilio Test&lt;/h1\> 
		&lt;form action="/call" method="POST"\> 
			&lt;input placeholder="Entrez un numéro de téléphone" name="number"/\> 
			&lt;br/\> 
			&lt;input type="submit" value="Appelez le numéro ci-dessus"/\> 
		&lt;/form\> 
	&lt;/body\> 
&lt;/html\>

Maintenant, déployez votre site Web sur Azure et ouvrez votre page d'accueil. Vous devez être en mesure de saisir votre numéro de téléphone dans le champ de texte et de recevoir un appel depuis votre numéro Twilio !

## Envoi d'un SMS

À présent, configurons une interface utilisateur et une logique de gestion pour envoyer un SMS. Ouvrez « server.js » et ajoutez le code suivant après le dernier appel à « app.post » :

	app.post('/sms', function(request, response) { 
		var client = twilio(); 
		client.sendSms({ 
			// Envoyez un SMS à ce numéro 
			to:request.body.number,

          	// Un numéro Twilio que vous avez acheté - voir :
          	// https://www.twilio.com/user/account/phone-numbers/incoming
          	from:'+15558675309',

			// Le corps du SMS
			body: request.body.message
          
      }, function(error, data) {
          // Revenez à la page d'accueil
          response.redirect('/');
      });
	});

Dans « views/index.ejs », ajoutez un autre formulaire sous le premier afin de soumettre un numéro et un SMS :

&lt;form action="/sms" method="POST"\> 
	&lt;input placeholder="Entrez un numéro de téléphone" name="number"/\> 
	&lt;br/\> 
	&lt;input placeholder="Entrez un SMS à envoyer" name="message"/\> 
	&lt;br/\> 
	&lt;input type="submit" value="Envoyez le SMS au numéro ci-dessus"/\> 
&lt;/form\>

Redéployez votre application sur Azure. Vous devriez désormais être en mesure d'envoyer ce formulaire et d'envoyer à vous-même (ou à un ami proche) un SMS !

## Étapes suivantes

Vous avez appris les bases d'utilisation de node.js et de Twilio pour générer des applications qui communiquent. Mais ces exemples ne représentent d'une infime partie des possibilités offertes par Twilio et node.js. Pour plus d'informations sur l'utilisation de Twilio avec node.js, consultez les ressources suivantes :

-   [Documentations officielles sur le module](http://twilio.github.io/twilio-node/)
-   [Didacticiel sur VoIP avec des applications node.js](http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html)
-   [Votr - application de vote par SMS en temps réel avec node.js et CouchDB (trois parties)](http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html)
-   [Programmation par paire dans le navigateur avec node.js](http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html)

Nous espérons que vous aimerez travailler sur node.js et Twilio sur Azure !

[purchase_phone]: https://www.twilio.com/user/account/phone-numbers/available/local
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: http://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/create-a-website-(mac)/
[twilio_dashboard]: https://www.twilio.com/user/account
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png

