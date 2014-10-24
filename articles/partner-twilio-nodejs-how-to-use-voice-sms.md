<properties linkid="dev-nodejs-how-to-twilio-voice-sms-service" urlDisplayName="Twilio Voice and SMS Service" pageTitle="Using Twilio for Voice, VoIP, and SMS Messaging in Azure" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="Node.js" title=" VoIP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com"></tags>

# Utilisation de Twilio pour les fonctionnalités vocales, VoIP et de messagerie SMS dans Azure

Ce guide montre comment générer des applications qui communiquent avec Twilio et node.js sur Azure.

## Sommaire

-   [Présentation de Twilio][Présentation de Twilio]
-   [Inscription à Twilio (remise Microsoft)][Inscription à Twilio (remise Microsoft)]
-   [Création et déploiement d'un site Web Azure node.js][Création et déploiement d'un site Web Azure node.js]
-   [Configuration du module Twilio][Configuration du module Twilio]
-   [Exécution d'un appel téléphonique][Exécution d'un appel téléphonique]
-   [Envoi d'un SMS][Envoi d'un SMS]
-   [Étapes suivantes][Étapes suivantes]

<span id="whatis"></span></a>

## Présentation de Twilio

Twilio est une plateforme API qui facilite pour les développeurs le passage et la réception d'appels téléphoniques, l'envoi et la réception de SMS. Elle incorpore l'appel VoIP dans des applications basées sur un navigateur et mobiles natives. Survolons rapidement le fonctionnement de Twilio avant de l'étudier plus en détail.

### Réception d'appels et de SMS

Twilio permet aux développeurs d'[acheter des numéros de téléphone programmables][acheter des numéros de téléphone programmables] qui peuvent être utilisés à la fois pour envoyer et recevoir des appels et des SMS. Lorsqu'un numéro Twilio reçoit un appel ou un SMS, il envoie à votre application Web une requête HTTP POST ou GET, vous demandant des instructions sur la gestion de l'appel ou du SMS. Votre serveur répond à la requête HTTP de Twilio avec [TwiML][TwiML], un jeu simple de balises XML qui contient des instructions sur la gestion d'un appel ou d'un SMS. Nous vous présenterons des exemples de TwiML un peu plus loin.

### Passage d'appels et envoi de SMS

En émettant des requêtes HTTP vers l'API de service Web Twilio, les développeurs peuvent envoyer des SMS et passer des appels téléphoniques. Pour les appels, le développeur doit également spécifier une URL qui renvoie des instructions TwiML relatives à la gestion de l'appel une fois qu'il est connecté.

### Incorporation des fonctionnalités VoIP dans un code de l'interface utilisateur (JavaScript, iOS ou Android)

Twilio offre un Kit de développement logiciel (SDK) côté client qui peut transformer n'importe quel navigateur Web, application iOS ou Android en un téléphone VoIP. Dans cet article, nous nous concentrerons sur l'utilisation de l'appel VoIP dans le navigateur. En plus du Kit de développement logiciel (SDK) JavaScript Twilio s'exécutant dans le navigateur, une application côté serveur (notre application node.js) doit être utilisée pour émettre un « jeton de fonctionnalité » au client JavaScript. Vous trouverez plus d'informations sur l'utilisation de VoIP avec node.js [sur le blog des développeurs Twilio][sur le blog des développeurs Twilio].

<span id="signup"></span></a>

## Inscription à Twilio (remise Microsoft)

Avant d'utiliser les services Twilio, vous devez [créer un compte][créer un compte]. Les clients Microsoft Azure reçoivent une remise spéciale. Pour y avoir droit, [veillez à vous inscrire ici][créer un compte] !

<span id="azuresite"></span></a>

## Création et déploiement d'un site Web Azure node.js

Ensuite, vous allez devoir créer un site Web node.js s'exécutant sur Azure. [La documentation officielle relative à cette création est située ici][La documentation officielle relative à cette création est située ici]. À un niveau supérieur, vous réaliserez les tâches suivantes :

-   Création d'un compte Azure si vous n'en possédez pas déjà un
-   Utilisation de la console d'administration pour créer un site Web
-   Ajout d'une prise en charge du contrôle de code source (nous présumons que vous avez utilisé git)
-   Création d'un fichier `server.js` avec une application web node.js simple
-   Déploiement de cette application simple sur Azure

<span id="twiliomodule"></span></a>

## Configuration du module Twilio

Ensuite, nous commencerons à rédiger une simple application node.js qui utilise l'API Twilio. Avant de commencer, nous devons configurer les informations d'identification de notre compte Twilio.

### Configuration des informations d'identification Twilio dans des variables d'environnement système

Afin d'émettre des requêtes authentifiées au serveur principal Twilio, nous avons besoin de notre SID de compte et de notre jeton d'authentification, qui fonctionnent en tant que nom d'utilisateur et mot de passe pour notre compte Twilio. Le moyen le plus sécurisé de configurer ces deux éléments pour une utilisation avec le module Node dans Azure est d'utiliser les variables d'environnement système, que vous pouvez définir directement dans la console d'administration d'Azure.

Sélectionnez votre site Web node.js et cliquez sur le lien « CONFIGURER ». Si vous faites un peu défiler vers le bas, vous verrez une zone dans laquelle vous pouvez définir les propriétés de configuration de votre application. Entrez les informations d'identification de votre compte Twilio ([disponibles dans le tableau de bord Twilio][disponibles dans le tableau de bord Twilio]) comme illustré : assurez-vous de les nommer « TWILIO\_ACCOUNT\_SID » et « TWILIO\_AUTH\_TOKEN », respectivement :

![Console d'administration Azure][Console d'administration Azure]

Une fois que vous avez configuré ces variables, redémarrez votre application dans la console Azure.

### Déclaration du module Twilio dans package.json

Ensuite, nous devons créer un fichier package.json pour gérer nos dépendances de module Node via [npm][npm]. Au même niveau que le fichier « server.js » que vous avez créé dans le didacticiel Azure/node.js, créez un fichier nommé « package.json ». Placez-y les éléments suivants :

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

Cette instruction déclare le module Twilio en tant que dépendance ainsi que la [structure Web Express][structure Web Express] courante et le moteur de modèles EJS. Tout est maintenant prêt et nous pouvons passer à l'écriture du code.

<span id="makecall"></span></a>

## Exécution d'un appel téléphonique

Créons un simple formulaire qui passera un appel vers un numéro que nous choisissons. Ouvrez server.js et saisissez le code suivant. Lorsque le code indique "CHANGE\_ME", placez le nom de votre site Web Azure à cet endroit :

	// Module dependencies
	 var express = require('express'),
	 path = require('path'),
	 http = require('http'),
	 twilio = require('twilio');
	
	// Create Express web application
	 var app = express();
	
	// Express configuration
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
	
	// Render an HTML user interface for the application's home page
	 app.get('/', function(request, response) {
	 response.render('index');
	 });
	
	// Handle the form POST to place a call
	 app.post('/call', function(request, response) {
	 var client = twilio();
	 client.makeCall({
	 // make a call to this number
	 to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });

	});

	// Generate TwiML to handle an outbound call
	 app.post('/outbound\_call', function(request, response) {
	 var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());

	});
	
	// Start server
	 http.createServer(app).listen(app.get('port'), function(){
	 console.log("Express server listening on port " + app.get('port'));
	 });

Ensuite, créez un répertoire appelé « views » et créez-y un fichier nommé « index.ejs » avec le contenu suivant :

	 \<html\>
	 \<head\>
	 \<title\>Twilio Test\</title\>
	 \<style\>
	 input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
	 \</style\>
	 \</head\>
	 \<body\>
	 \<h1\>Twilio Test\</h1\>
	 \<form action="/call" method="POST"\>
	 \<input placeholder="Enter a phone number" name="number"/\>
	 \<br/\>
	 \<input type="submit" value="Call the number above"/\>
	 \</form\>
	 \</body\>
	 \</html\>

Maintenant, déployez votre site Web sur Azure et ouvrez votre page d'accueil. Vous devez être en mesure de saisir votre numéro de téléphone dans le champ de texte et de recevoir un appel depuis votre numéro Twilio !

<span id="sendmessage"></span></a>

## Envoi d'un SMS

À présent, configurons une interface utilisateur et une logique de gestion pour envoyer un SMS. Ouvrez « server.js » et ajoutez le code suivant après le dernier appel à « app.post » :

	app.post('/sms', function(request, response) {
	 var client = twilio();
	 client.sendSms({
	 // send a text to this number
	 to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });

	

Dans « views/index.ejs », ajoutez un autre formulaire sous le premier afin de soumettre un numéro et un SMS :

	\<form action="/sms" method="POST"\>
	 \<input placeholder="Enter a phone number" name="number"/\>
	 \<br/\>
	 \<input placeholder="Enter a message to send" name="message"/\>
	 \<br/\>
	 \<input type="submit" value="Send text to the number above"/\>
	 \</form\>

Redéployez votre application sur Azure. Vous devriez désormais être en mesure d'envoyer ce formulaire et d'envoyer à vous-même (ou à un ami proche) un SMS !

<span id="nextsteps"></span></a>

## Étapes suivantes

Vous avez appris les bases d'utilisation de node.js et de Twilio pour générer des applications qui communiquent. Mais ces exemples ne font qu'effleurer les possibilités offertes par Twilio et node.js. Pour plus d'informations sur l'utilisation de Twilio avec node.js, consultez les ressources suivantes :

-   [Documentations officielles sur le module][Documentations officielles sur le module]
-   [Didacticiel sur VoIP avec des applications node.js][sur le blog des développeurs Twilio]
-   [Votr - application de vote par SMS en temps réel avec node.js et CouchDB (trois parties)][Votr - application de vote par SMS en temps réel avec node.js et CouchDB (trois parties)]
-   [Programmation par paire dans le navigateur avec node.js][Programmation par paire dans le navigateur avec node.js]

Nous espérons que vous aimerez travailler sur node.js et Twilio sur Azure !

  [Présentation de Twilio]: #whatis
  [Inscription à Twilio (remise Microsoft)]: #signup
  [Création et déploiement d'un site Web Azure node.js]: #azuresite
  [Configuration du module Twilio]: #twiliomodule
  [Exécution d'un appel téléphonique]: #makecall
  [Envoi d'un SMS]: #sendmessage
  [Étapes suivantes]: #nextsteps
  [acheter des numéros de téléphone programmables]: https://www.twilio.com/user/account/phone-numbers/available/local
  [TwiML]: https://www.twilio.com/docs/api/twiml
  [sur le blog des développeurs Twilio]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
  [créer un compte]: http://ahoy.twilio.com/azure
  [La documentation officielle relative à cette création est située ici]: http://www.windowsazure.com/en-us/develop/nodejs/tutorials/create-a-website-(mac)/
  [disponibles dans le tableau de bord Twilio]: https://www.twilio.com/user/account
  [Console d'administration Azure]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
  [npm]: http://npmjs.org
  [structure Web Express]: http://expressjs.com
  [Documentations officielles sur le module]: http://twilio.github.io/twilio-node/
  [Votr - application de vote par SMS en temps réel avec node.js et CouchDB (trois parties)]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
  [Programmation par paire dans le navigateur avec node.js]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
