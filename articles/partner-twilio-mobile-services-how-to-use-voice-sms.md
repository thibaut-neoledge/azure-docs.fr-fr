<properties pageTitle="Utilisation de Twilio pour les fonctionnalités vocales et SMS | Centre de développement mobile " metakeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="10/03/2014" ms.author="MicrosoftHelp@twilio.com" />


<h1>Utilisation de Twilio pour les fonctionnalités vocales et de SMS depuis Mobile Services</h1>

Cette rubrique vous présente le déroulement de tâches courantes dans le cadre de l'utilisation de l'API Twilio avec Azure Mobile Services. Dans ce didacticiel, vous allez apprendre à créer des scripts d'API personnalisées qui utilisent l'API Twilio pour passer des appels et envoyer des SMS. 

<h2><a id="WhatIs"></a>Présentation de Twilio</h2>
Twilio alimente le futur des communications professionnelles, en permettant aux développeurs d'incorporer les fonctionnalités vocales, VoIP et de messagerie dans les applications. Ils virtualisent toute l'infrastructure nécessaire dans un environnement global dans le cloud, en l'exposant au moyen de la plateforme de l'API de communication Twilio. Les applications sont faciles à générer et sont évolutives. Tirez parti de la souplesse du paiement à l'utilisation et de la fiabilité du cloud.

**Twilio Voice** permet à vos applications de passer et de recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications d'envoyer et de recevoir des SMS. **Twilio Client** permet de passer des appels VoIP à partir d'un téléphone, d'une tablette ou d'un navigateur et prend en charge WebRTC.

<h2><a id="Pricing"></a>Tarification de Twilio et offres spéciales</h2>
Les clients Azure reçoivent une [offre spéciale][special_offer] : sous la forme d'un crédit Twilio d'un montant de 10 $ offert lorsqu'ils mettent à niveau leur compte Twilio. Ce crédit peut servir à l'achat d'une prestation Twilio (un crédit de 10 $ correspond à l'envoi de 1 000 SMS ou à la réception de 1 000 minutes vocales maximum, en fonction de l'emplacement de votre numéro de téléphone, du message ou de la destination de l'appel). Profitez de ce crédit Twilio et démarrez en consultant la page [ahoy.twilio.com/azure][special_offer].

Twilio est un service de paiement à l'utilisation. Il n'existe pas de frais d'entrée et vous pouvez fermer votre compte quand vous le souhaitez. Pour plus d'informations, consultez la page [Tarification de Twilio][twilio_pricing].  

<h2><a id="Concepts"></a>Concepts</h2>
L'API Twilio est une API RESTful qui offre des fonctionnalités vocales et de SMS aux applications. Les bibliothèques clientes sont disponibles dans plusieurs langues : pour en obtenir la liste, consultez la page [Bibliothèques de l'API Twilio] [twilio_libraries].  D'autres didacticiels sont disponibles pour l'utilisation de Twilio dans des applications Azure écrites en [.NET][azure_twilio_howto_dotnet], [node.js][azure_twilio_howto_node], [Java][azure_twilio_howto_java], [PHP][azure_twilio_howto_php], [Python][azure_twilio_howto_python] ou [Ruby][azure_twilio_howto_ruby].

Les éléments les plus importants de l'API Twilio sont les verbes Twilio et TwiML (Twilio Markup Language).

<h3><a id="Verbs"></a>Verbes Twilio</h3>
L'API utilise les verbes Twilio ; par exemple, le verbe **&lt;Say&gt;** (Dire) indique à Twilio de transmettre un message de manière audible lors d'un appel. 

La liste suivante présente les verbes Twilio.  Découvrez les autres verbes et fonctionnalités dans la page [Documentation de Twilio Markup Language](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;** : connecte l'appelant à un autre téléphone.
* **&lt;Gather&gt;** : rassemble les chiffres numériques entrés sur le clavier du téléphone.
* **&lt;Hangup&gt;** : met fin à un appel.
* **&lt;Play&gt;** : lit un fichier audio.
* **&lt;Pause&gt;** : patiente silencieusement pendant un nombre déterminé de secondes.
* **&lt;Record&gt;** : enregistre la voix de l'appelant et renvoie une URL permettant d'accéder à un fichier contenant l'enregistrement.
* **&lt;Redirect&gt;** : transfère le contrôle d'un appel ou d'un SMS au TwiML à une autre URL.
* **&lt;Reject&gt;** : refuse un appel entrant sur votre numéro Twilio sans vous facturer.
* **&lt;Say&gt;** : convertit le texte d'un appel en parole.
* **&lt;Sms&gt;** : envoie un SMS.

<h3> <a id="TwiML"></a>TwiML</h3>
TwiML est un jeu d'instructions XML reposant sur les verbes Twilio qui informent Twilio sur la façon de traiter un appel ou un SMS.

À titre d'exemple, le code TwiML suivant convertit le texte **Hello World** en parole.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Lorsque votre application appelle l'API Twilio, l'un des paramètres de l'API est l'URL qui renvoie la réponse TwiML. À des fins de développement, vous pouvez utiliser les URL Twilio pour fournir les réponses TwiML utilisées par vos applications. Vous pouvez également héberger vos propres URL afin de produire les réponses TwiML, ou encore utiliser l'objet **TwiMLResponse**.

Pour plus d'informations sur les verbes Twilio, leurs attributs et le langage TwiML, consultez la page [TwiML][twiml]. Pour plus d'informations sur l'API Twilio, consultez la page [API Twilio][twilio_api].

<h2><a id="CreateAccount"></a>Création d'un compte Twilio</h2>
Lorsque vous êtes prêt à créer votre compte Twilio, inscrivez-vous sur la page [Essayer Twilio][try_twilio]. Vous pouvez commencer avec un compte gratuit, avant de le mettre à niveau ultérieurement.

Lorsque vous créez un compte Twilio, vous recevez un ID de compte et un jeton d'authentification. Les deux sont nécessaires pour passer des appels d'API Twilio. Pour éviter qu'une personne non autorisée n'accède à votre compte, conservez votre jeton d'authentification en lieu sûr. L'ID et le jeton d'authentification de votre compte sont consultables sur la [page de votre compte Twilio][twilio_account], dans les champs **ACCOUNT SID** et **AUTH TOKEN**.

<h2><a id="VerifyPhoneNumbers"></a>Vérification des numéros de téléphone</h2>
Plusieurs numéros de téléphone doivent être vérifiés avec Twilio pour votre compte. Par exemple, si vous souhaitez passer des appels téléphoniques, le numéro de téléphone doit être vérifié en tant qu'ID d'appelant sortant avec Twilio. De la même façon, si vous voulez un numéro de téléphone pour recevoir des SMS, le numéro de téléphone recevant l'appel doit être vérifié avec Twilio. Pour plus d'informations sur la vérification d'un numéro de téléphone, consultez la page [Gestion des numéros] [verify_phone]. Une partie du code ci-dessous repose sur les numéros de téléphone que vous devez vérifier avec Twilio.

Si vous souhaitez utiliser un autre numéro de téléphone pour vos applications, vous pouvez acheter un numéro de téléphone Twilio. Pour plus d'informations sur l'achat d'un numéro de téléphone Twilio, consultez la page [Aide sur les numéros de téléphone Twilio](https://www.twilio.com/help/faq/phone-numbers).

<h2><a id="create_app"></a>Création d'un service mobile</h2>
Un service mobile qui héberge une application Twilio n'est pas différent d'un autre service mobile. Vous ajoutez simplement la bibliothèque node.js Twilio afin de pouvoir y faire référence depuis vos scripts d'API Mobile Service personnalisée. Pour plus d'informations sur la création d'un service mobile initial, consultez la page [Prise en main de Mobile Services](/fr-fr/documentation/articles/mobile-services-ios-get-started/).

<h2><a id="ConfigureMobileService"></a>Configuration de votre service mobile pour utiliser la bibliothèque Twilio Node.js</h2>
Twilio comporte une bibliothèque Node.js qui couvre différents aspects de Twilio afin de fournir des moyens simples et faciles d'interaction avec l'API REST Twilio et le client Twilio en vue de générer des réponses TwiML.

Pour utiliser la bibliothèque Twilio node.js dans votre service mobile, vous devez réutiliser la prise en charge du module npm de Mobile Services, ce que vous pouvez faire en enregistrant vos scripts dans le contrôle de code source. 

1. Terminez le didacticiel [Stockage de scripts dans un contrôle de code source](/fr-fr/documentation/articles/mobile-services-store-scripts-source-control/). Cet article vous présente la configuration du contrôle de code source pour vos services mobiles et le stockage de vos scripts serveur dans un référentiel Git.

2. Une fois le contrôle de code source configuré pour votre service mobile, ouvrez le référentiel sur votre ordinateur local, accédez au sous-dossier `\services`, ouvrez le fichier package.json dans un éditeur de texte et ajoutez le champ suivant dans l'objet **dependencies** :

		"twilio": "~1.7.0"
 
3. Après avoir ajouté la référence de package Twilio à l'objet **dependencies**, le fichier package.json doit se présenter comme suit :

		{
		  "name": "todolist",
		  "version": "1.0.0",
		  "description": "todolist - hosted on Windows Azure Mobile Services",
		  "main": "server.js",
		  "engines": {
		    "node": ">= 0.8.19"
		  },
		  "dependencies": {
			"twilio": "~1.7.0" 
		  },
		  "devDependencies": {},
		  "scripts": {},
		  "author": "unknown",
		  "licenses": [],
		  "keywords":[]
		}

	>[WACOM.NOTE]La dépendance de Twilio doit être ajoutée en tant que `" twilio " : " ~1.7.0 "`, avec un caractère (~). Les références avec accent circonflexe (^) ne sont pas prises en charge. 

4. Validez cette mise à jour du fichier, puis remettez à jour le service mobile.

	Cette mise à jour du fichier package.json va redémarrer votre service mobile.
	
Le service mobile installe maintenant le package Twilio et le charge afin de pouvoir référencer et utiliser la bibliothèque Twilio dans votre API personnalisée et dans les scripts de table.

<h2><a id="howto_make_call"></a> Appel téléphonique</h2>
Le script qui suit montre comment passer un appel depuis votre service mobile à l'aide de la fonction **makeCall**. Il utilise également un site Twilio afin de renvoyer la réponse TwiML (Twilio Markup Language). Remplacez vos valeurs pour les numéros de téléphone **From** (De) et **To** (À), puis vérifiez le numéro de téléphone **From** de votre compte Twilio avant d'exécuter le code.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://www.example.com/twiml.php' 

        }, function(err, responseData) {
            console.log(responseData.from); 
            response.send(200, '');
        });
    };

Pour plus d'informations sur les paramètres transmis dans la fonction **client.makeCall**, consultez la page [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Comme indiqué, ce code utilise un site Twilio afin de renvoyer la réponse TwiML, mais vous pouvez utiliser votre propre site. Pour plus d'informations, consultez la page [ Envoi de réponses TwiML à partir de votre propre site web](#howto_provide_twiml_responses)

<h2><a id="howto_send_sms"></a> Envoi d'un SMS</h2>
Le code suivant montre comment envoyer un message SMS à l'aide de la fonction **sendSms**. Le numéro **From** est fourni par Twilio pour permettre aux comptes d'évaluation d'envoyer des SMS. Le numéro **To** doit être vérifié pour votre compte Twilio avant d'exécuter le code.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');
 
        client.sendSms({
            to:'[]',
            from:'[]',
            body:'ahoy hoy! Testing Twilio and node.js'
        }, function(error, message) {
    
            // The "error" variable will contain error information, if any.
            // If the request was successful, this value will be "false"
            if (!error) {
                console.log('Success! The SID for this SMS message is: ' + message.sid);
                console.log('Message sent on: ' + message.dateCreated);
            }
            else {
                console.log('Oops! There was an error.');
            }
            response.send(200, { error : error } );
        });
    };


<h2><a id="howto_provide_twiml_responses"></a> Envoi de réponses TwiML à partir de votre propre site web</h2>

Lorsque votre application démarre un appel à l'API Twilio, par exemple via la méthode client.InitiateOutboundCall, Twilio envoie votre demande à une URL qui est censée renvoyer une réponse TwiML. L'exemple présenté dans la section " Exécution d'un appel téléphonique " utilise l'URL Twilio http://twimlets.com/message pour renvoyer la réponse.

<div class="dev-callout">
<b>Remarque</b>
<p>TwiML est conçu pour être utilisé par des services Web, vous pouvez donc afficher TwiML dans votre navigateur. Par exemple, cliquez sur <a href="http://twimlets.com/message">twimlet_message_url</a> pour afficher un élément &lt;Response&gt; ; autre exemple, cliquez sur <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a> pour afficher un élément &lt;Response&gt; contenant un élément &lt;Say&gt;.</p>
</div>

Au lieu de compter sur l'URL Twilio, vous pouvez créer votre propre site d'URL qui renvoie des réponses HTTP. Vous pouvez créer le site dans tout langage qui renvoie des réponses HTTP. Cette rubrique part du principe que vous hébergerez l'URL à partir d'un gestionnaire générique ASP.NET.

Le script qui suit génère une réponse TwiML prononçant Hello World lors de l'appel.

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

Pour plus d'informations sur TwiML, consultez la page [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Une fois que vous avez défini une façon de fournir des réponses TwiML, vous pouvez transmettre cette URL à la méthode **client.makeCall**.
    
    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://<your_mobile_service>.azure-mobile.net/api/makeCall' 

        }, function(err, responseData) {

            console.log(responseData.from);
            response.send(200, '');
        });
    };

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]


[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#


[azure_twilio_howto_dotnet]: /fr-fr/develop/net/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_java]: /fr-fr/develop/java/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_node]: /fr-fr/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_ruby]: /fr-fr/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_python]: /fr-fr/develop/python/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_php]: /fr-fr/develop/php/how-to-guides/twilio-voice-and-sms-service/
