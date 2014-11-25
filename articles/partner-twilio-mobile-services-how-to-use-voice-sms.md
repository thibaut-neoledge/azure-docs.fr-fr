<properties linkid="develop-mobile-tutorials-twilio-for-voice-and-sms" pageTitle="Use Twilio for Voice and SMS Capabilities | Mobile Dev Center" metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# Utilisation de Twilio pour les fonctionnalités vocales et de SMS depuis Mobile Services

Cette rubrique décrit le déroulement de tâches courantes dans le cadre de l'utilisation de l'API Twilio avec Azure Mobile Services. Dans ce didacticiel, vous allez apprendre à créer des scripts d'API personnalisées qui utilisent l'API Twilio pour passer des appels et envoyer des SMS.

## <span id="WhatIs"></span></a>Présentation de Twilio

Twilio alimente le futur des communications professionnelles, en permettant aux développeurs d'incorporer les fonctionnalités vocales, VoIP et de messagerie dans les applications. Ils virtualisent toute l'infrastructure nécessaire dans un environnement global dans le cloud, en l'exposant au moyen de la plateforme de l'API de communication Twilio. Les applications sont faciles à générer et sont évolutives. Tirez parti de la souplesse du paiement à l'utilisation et de la fiabilité du cloud.

**Twilio Voice** permet à vos applications de passer et de recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications d'envoyer et de recevoir des SMS. **Twilio Client** permet de passer des appels VoIP à partir d'un téléphone, d'une tablette ou d'un navigateur et prend en charge WebRTC.

## <span id="Pricing"></span></a>Tarification de Twilio et offres spéciales

Les clients Azure reçoivent une [offre spéciale][offre spéciale] sous la forme d'un crédit Twilio d'un montant de 10 $ offert lorsqu'ils mettent à niveau leur compte Twilio. Ce crédit peut servir à l'achat d'une prestation Twilio (un crédit de 10 $ correspond à l'envoi de 1 000 SMS ou à la réception de 1 000 minutes vocales maximum, en fonction de l'emplacement de votre numéro de téléphone, du message ou de la destination de l'appel). Profitez de ce crédit Twilio et démarrez en consultant la page [ahoy.twilio.com/azure][offre spéciale].

Twilio est un service de paiement à l'utilisation. Il n'existe pas de frais d'entrée et vous pouvez fermer votre compte quand vous le souhaitez. Pour plus d'informations, consultez la page [Tarification de Twilio][Tarification de Twilio].

## <span id="Concepts"></span></a>Concepts

L'API Twilio est une API RESTful qui offre des fonctionnalités vocales et de SMS aux applications. Les bibliothèques clientes sont disponibles dans plusieurs langues : pour en obtenir la liste, consultez la page [Bibliothèques de l'API Twilio][Bibliothèques de l'API Twilio]. D'autres didacticiels sont disponibles pour l'utilisation de Twilio dans des applications Azure écrites en [.NET][.NET], [node.js][node.js], [Java][Java], [PHP][PHP], [Python][Python] ou [Ruby][Ruby].

Les éléments les plus importants de l'API Twilio sont les verbes Twilio et TwiML (Twilio Markup Language).

### <span id="Verbs"></span></a>Verbes Twilio

L'API utilise les verbes Twilio ; par exemple, le verbe **\<Say\>** (Dire) indique à Twilio de transmettre un message de manière audible lors d'un appel.

La liste suivante présente les verbes Twilio. Découvrez les autres verbes et fonctionnalités dans la page [Documentation de Twilio Markup Language][Documentation de Twilio Markup Language].

-   **\<Dial\>** (Composer) : connecte l'appelant à un autre téléphone.
-   **\<Gather\>** (Rassembler) : rassemble les chiffres numériques entrés sur le clavier du téléphone.
-   **\<Hangup\>** (Raccrocher) : met fin à un appel.
-   **\<Play\>** (Lire) : lit un fichier audio.
-   **\<Pause\>** : patiente silencieusement pendant un nombre déterminé de secondes.
-   **\<Record\>** (Enregistrer) : enregistre la voix de l'appelant et renvoie une URL permettant d'accéder à un fichier contenant l'enregistrement.
-   **\<Redirect\>** (Rediriger) : transfère le contrôle d'un appel ou d'un SMS au TwiML à une autre URL.
-   **\<Reject\>** (Rejeter) : refuse un appel entrant sur votre numéro Twilio sans vous facturer.
-   **\<Say\>** (Dire) : convertit le texte d'un appel en parole.
-   **\<Sms\>** : envoie un SMS.

### <span id="TwiML"></span></a>TwiML

TwiML est un jeu d'instructions XML reposant sur les verbes Twilio qui informent Twilio sur la façon de traiter un appel ou un SMS.

À titre d'exemple, le code TwiML suivant convertit le texte **Hello World** en parole.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Lorsque votre application appelle l'API Twilio, l'un des paramètres de l'API est l'URL qui renvoie la réponse TwiML. À des fins de développement, vous pouvez utiliser les URL Twilio pour fournir les réponses TwiML utilisées par vos applications. Vous pouvez également héberger vos propres URL afin de produire les réponses TwiML, ou encore utiliser l'objet **TwiMLResponse**.

Pour plus d'informations sur les verbes Twilio, leurs attributs et le langage TwiML, consultez la page [TwiML][Documentation de Twilio Markup Language]. Pour plus d'informations sur l'API Twilio, consultez la page [API Twilio][API Twilio].

## <span id="CreateAccount"></span></a>Création d'un compte Twilio

Lorsque vous êtes prêt à créer votre compte Twilio, inscrivez-vous sur la page [Essayer Twilio][Essayer Twilio]. Vous pouvez commencer avec un compte gratuit, avant de le mettre à niveau ultérieurement.

Lorsque vous créez un compte Twilio, vous recevez un ID de compte et un jeton d'authentification. Les deux sont nécessaires pour passer des appels d'API Twilio. Pour éviter qu'une personne non autorisée n'accède à votre compte, conservez votre jeton d'authentification en lieu sûr. Vous pouvez consulter vos ID de compte et jeton d'authentification sur la [page du compte Twilio][page du compte Twilio], dans les champs intitulés **ACCOUNT SID** et **AUTH TOKEN**, respectivement.

## <span id="VerifyPhoneNumbers"></span></a>Vérification des numéros de téléphone

Plusieurs numéros de téléphone doivent être vérifiés avec Twilio pour votre compte. Par exemple, si vous souhaitez passer des appels téléphoniques, le numéro de téléphone doit être vérifié en tant qu'ID d'appelant sortant avec Twilio. De la même façon, si vous voulez un numéro de téléphone pour recevoir des SMS, le numéro de téléphone recevant l'appel doit être vérifié avec Twilio. Pour plus d'informations sur la vérification d'un numéro de téléphone, consultez la page [Gestion des numéros][Gestion des numéros]. Une partie du code ci-dessous repose sur les numéros de téléphone que vous devez vérifier avec Twilio.

Si vous souhaitez utiliser un autre numéro de téléphone pour vos applications, vous pouvez acheter un numéro de téléphone Twilio. Pour plus d'informations sur l'achat d'un numéro de téléphone Twilio, consultez la page [Aide sur les numéros de téléphone Twilio][Aide sur les numéros de téléphone Twilio].

## <span id="create_app"></span></a>Création d'un service mobile

Un service mobile qui héberge une application Twilio n'est pas différent d'un autre service mobile. Vous ajoutez simplement la bibliothèque node.js Twilio afin de pouvoir y faire référence depuis vos scripts d'API Mobile Service personnalisée. Pour plus d'informations sur la création d'un service mobile initial, consultez la page [Prise en main de Mobile Services][Prise en main de Mobile Services].

## <span id="VerifyPhoneNumbers"></span></a>Configuration de votre service mobile pour utiliser la bibliothèque Twilio Node.js

Twilio comporte une bibliothèque Node.js qui couvre différents aspects de Twilio afin de fournir des moyens simples et faciles d'interaction avec l'API REST Twilio et le client Twilio en vue de générer des réponses TwiML.

Pour utiliser la bibliothèque Twilio node.js dans votre service mobile, vous devez réutiliser la prise en charge du module npm de Mobile Services, ce que vous pouvez faire en enregistrant vos scripts dans le contrôle de code source. Le didacticiel [Stockage de scripts dans un contrôle de code source][Stockage de scripts dans un contrôle de code source] montre comment configurer un contrôle de code source pour la première fois dans Mobile Services pour stocker vos scripts serveur dans un référentiel Git.

Une fois que vous avez configuré le contrôle de code source pour votre service mobile, ouvrez l'onglet Configurer du tableau de bord de votre service mobile, puis recherchez l'URL Git et copiez-la.

Collez cette URL dans le navigateur et remplacez le nom du référentiel par */DebugConsole/index.html*

Par exemple, remplacez :

    https://twilioSample.scm.azure-mobile.net/twilioSample.git

to:

    https://twilioSample.scm.azure-mobile.net/DebugConsole

Lorsque vous y êtes invité, entrez les informations d'identification que vous avez utilisées lors de la configuration du contrôle de code source du service. Une fois connecté, vous pouvez voir la console Azure Mobile Service.

![Console du service mobile][Console du service mobile]

Dans la console, définissez le répertoire sur celui du dossier de scripts :

    cd site\wwwroot\App_Data\config\scripts

Une fois dans le dossier de l'API, vous pouvez installer le module Node Twilio en exécutant la commande suivante :

    npm install twilio

Vous pouvez maintenant utiliser la bibliothèque Twilio et y faire référence dans votre API personnalisée et dans vos scripts de table.

## <span id="howto_make_call"></span></a> Appel téléphonique

Le script qui suit montre comment passer un appel depuis votre service mobile à l'aide de la fonction **makeCall**. Il utilise également un site Twilio afin de renvoyer la réponse TwiML (Twilio Markup Language). Remplacez vos valeurs pour les numéros de téléphone **From** (De) et **To** (À), puis assurez-vous de vérifier le numéro de téléphone **From** de votre compte Twilio avant d'exécuter le code.

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

Pour plus d'informations sur les paramètres transmis dans la fonction **client.makeCall**, consultez la page [][]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Comme indiqué, ce code utilise un site Twilio afin de renvoyer la réponse TwiML, mais vous pouvez utiliser votre propre site. Pour plus d'informations, consultez la page [Envoi de réponses TwiML à partir de votre propre site Web][Envoi de réponses TwiML à partir de votre propre site Web].

## <span id="howto_send_sms"></span></a> Envoi d'un SMS

Le code qui suit montre comment envoyer un SMS à l'aide de la fonction **sendSms**. Le numéro **From** est fourni par Twilio pour permettre aux comptes d'évaluation d'envoyer des SMS. Le numéro **To** doit être vérifié pour votre compte Twilio avant d'exécuter le code.

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

## <span id="howto_provide_twiml_responses"></span></a> Envoi de réponses TwiML à partir de votre propre site web

Lorsque votre application démarre un appel à l'API Twilio, par exemple via la méthode client.InitiateOutboundCall, Twilio envoie votre demande à une URL qui est censée renvoyer une réponse TwiML. L'exemple présenté dans la section Appel téléphonique utilise l'URL Twilio <http://twimlets.com/message> pour renvoyer la réponse.

<div class="dev-callout">
<b>Remarque</b>
<p>TwiML est con&ccedil;u pour &ecirc;tre utilis&eacute; par des services Web, vous pouvez donc afficher TwiML dans votre navigateur. Par exemple, cliquez sur <a href="http://twimlets.com/message">twimlet_message_url</a> pour afficher un &eacute;l&eacute;ment &lt;Response&gt;&nbsp;; autre exemple, cliquez sur <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a> pour afficher un &eacute;l&eacute;ment &lt;Response&gt; contenant un &eacute;l&eacute;ment &lt;Say&gt;.</p>
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

Pour plus d'informations sur TwiML, consultez la page [][1]<https://www.twilio.com/docs/api/twiml></a>.

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

  [offre spéciale]: http://ahoy.twilio.com/azure
  [Tarification de Twilio]: http://www.twilio.com/pricing
  [Bibliothèques de l'API Twilio]: https://www.twilio.com/docs/libraries
  [.NET]: /fr-fr/develop/net/how-to-guides/twilio-voice-and-sms-service/
  [node.js]: /fr-fr/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
  [Java]: /fr-fr/develop/java/how-to-guides/twilio-voice-and-sms-service/
  [PHP]: /fr-fr/develop/php/how-to-guides/twilio-voice-and-sms-service/
  [Python]: /fr-fr/develop/python/how-to-guides/twilio-voice-and-sms-service/
  [Ruby]: /fr-fr/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
  [Documentation de Twilio Markup Language]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [Essayer Twilio]: https://www.twilio.com/try-twilio
  [page du compte Twilio]: https://www.twilio.com/user/account
  [Gestion des numéros]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Aide sur les numéros de téléphone Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [Prise en main de Mobile Services]: http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started/
  [Stockage de scripts dans un contrôle de code source]: http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/store-scripts-in-source-control/
  [Console du service mobile]: ./media/partner-twilio-mobile-services-how-to-use-voice-sms/twilio-kuduconsole.png
  []: http://www.twilio.com/docs/api/rest/making-calls
  [Envoi de réponses TwiML à partir de votre propre site Web]: #howto_provide_twiml_responses
  [1]: https://www.twilio.com/docs/api/twiml
