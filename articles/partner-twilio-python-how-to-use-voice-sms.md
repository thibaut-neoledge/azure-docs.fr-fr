<properties linkid="develop-php-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in PHP." metaCanonical="" services="" documentationCenter="" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com"></tags>

# Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans PHP

Ce guide présente l'exécution de tâches de programmation courantes avec le service API Twilio sur Azure. Les scénarios abordés comprennent notamment les appels téléphoniques et l'envoi de SMS. Pour plus d'informations sur Twilio et sur l'utilisation des fonctionnalités vocales et de SMS de vos applications, consultez la section [Étapes suivantes][].

## Sommaire

-   [Présentation de Twilio][]
-   [Tarification de Twilio][]
-   [Concepts][]
-   [Création d'un compte Twilio][]
-   [Vérification des numéros de téléphone][]
-   [Création d'une application PHP][]
-   [Configuration de l'application pour utiliser les bibliothèques Twilio][]
-   [Appel téléphonique][]
-   [Envoi d'un SMS][]
-   [Envoi de réponses TwiML à partir de votre propre site Web][]

## <span id="WhatIs"></span></a>Présentation de Twilio

Twilio alimente le futur des communications professionnelles, en permettant aux développeurs d'incorporer les fonctionnalités vocales, VoIP et de messagerie dans les applications. Ils virtualisent toute l'infrastructure nécessaire dans un environnement global dans le cloud, en l'exposant au moyen de la plateforme de l'API de communication Twilio. Les applications sont faciles à générer et sont évolutives. Tirez parti de la souplesse du paiement à l'utilisation et de la fiabilité du cloud.

**Twilio Voice** permet à vos applications de passer et de recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications d'envoyer et de recevoir des messages texte. **Twilio Client** permet de passer des appels VoIP à partir d'un téléphone, d'une tablette ou d'un navigateur et prend en charge WebRTC.

## <span id="Pricing"></span></a>Tarification de Twilio et offres spéciales

Les clients Azure reçoivent une [offre spéciale] de 10 $ en crédit Twilio lorsqu'ils mettent à niveau leur compte Twilio. Ce crédit peut servir à l'achat d'une prestation Twilio (un crédit de 10 $ correspond à l'envoi de 1 000 SMS ou à la réception de 1 000 minutes vocales maximum, en fonction de l'emplacement de votre numéro de téléphone, du message ou de la destination de l'appel). Profitez de ce crédit Twilio et démarrez en consultant la page : [ahoy.twilio.com/azure][].

Twilio est un service de paiement à l'utilisation. Il n'existe pas de frais d'entrée et vous pouvez fermer votre compte quand vous le souhaitez. Pour plus d'informations, consultez la page [Tarification de Twilio][1].

## <span id="Concepts"></span></a>Concepts

L'API Twilio est une API RESTful qui offre des fonctionnalités vocales et de SMS aux applications. Les bibliothèques clientes sont disponibles dans plusieurs langues : pour en obtenir la liste, consultez la page [Bibliothèques de l'API Twilio][].

Les éléments les plus importants de l'API Twilio sont les verbes Twilio et TwiML (Twilio Markup Language).

### <span id="Verbs"></span></a>Verbes Twilio

L'API utilise les verbes Twilio ; par exemple, le verbe **\<Say\>** (Dire) indique à Twilio de transmettre un message de manière audible lors d'un appel.

La liste suivante présente les verbes Twilio. Découvrez les autres verbes et fonctionnalités dans la page [Documentation de Twilio Markup Language][].

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

Pour plus d'informations sur les verbes Twilio, leurs attributs et le langage TwiML, consultez la page [TwiML][]. Pour plus d'informations sur l'API Twilio, consultez la page [API Twilio][].

## <span id="CreateAccount"></span></a>Création d'un compte Twilio

Lorsque vous êtes prêt à créer votre compte Twilio, inscrivez-vous sur la page [Essayer Twilio][]. Vous pouvez commencer avec un compte gratuit, avant de le mettre à niveau ultérieurement.

Lorsque vous créez un compte Twilio, vous recevez un ID de compte et un jeton d'authentification. Les deux sont nécessaires pour passer des appels d'API Twilio. Pour éviter qu'une personne non autorisée n'accède à votre compte, conservez votre jeton d'authentification en lieu sûr. Vous pouvez consulter vos ID de compte et jeton d'authentification sur la [page du compte Twilio][], dans les champs intitulés **ACCOUNT SID** et **AUTH TOKEN**, respectivement.

## <span id="VerifyPhoneNumbers"></span></a>Vérification des numéros de téléphone

Plusieurs numéros de téléphone doivent être vérifiés avec Twilio pour votre compte. Par exemple, si vous voulez passer des appels sortants avec votre numéro de téléphone en tant qu'ID d'appelant, le numéro de téléphone doit être vérifié avec Twilio. De la même façon, jusqu'à la mise à niveau, si vous voulez envoyer des SMS à un numéro de téléphone, celui-ci doit être vérifié avec Twilio. Après la mise à niveau, vous pouvez envoyer des SMS à n'importe quel numéro, sans vérification. Pour plus d'informations sur la vérification d'un numéro de téléphone, consultez la page [Gestion des numéros][]. Une partie du code ci-dessous repose sur les numéros de téléphone que vous devez vérifier avec Twilio.

Si vous souhaitez utiliser un autre numéro de téléphone pour vos applications, vous pouvez acheter un numéro de téléphone Twilio. Pour plus d'informations sur l'achat d'un numéro de téléphone Twilio, consultez la page [Aide sur les numéros de téléphone Twilio][].

## <span id="create_app"></span></a>Création d'une application PHP

Une application PHP qui utilise le service Twilio et qui s'exécute dans Azure est identique aux autres applications PHP qui utilisent le service Twilio. Bien que les services Twilio soient basés sur REST et puissent être appelés de différentes manières depuis PHP, cet article met l'accent sur l'utilisation des services Twilio avec la [bibliothèque Twilio pour PHP depuis Github][]. Pour plus d'informations sur l'utilisation de la bibliothèque Twilio pour PHP, consultez la page [][]<http://readthedocs.org/docs/twilio-php/en/latest/index.html></a>.

Des instructions détaillées sur la conception et le déploiement d'une application Twilio/PHP sur Azure sont disponibles sur la page [Exécution d'un appel téléphonique à l'aide de Twilio dans une application PHP sur Azure][].

## <span id="configure_app"></span></a>Configuration de l'application pour utiliser les bibliothèques Twilio

Vous pouvez configurer votre application pour la bibliothèque Twilio pour PHP de deux manières :

1.  Téléchargez la bibliothèque Twilio pour PHP sur Github ([][bibliothèque Twilio pour PHP depuis Github]<https://github.com/twilio/twilio-php></a>) et ajoutez le répertoire **Services** à votre application.

    OU

2.  Installez la bibliothèque Twilio pour PHP sous forme de package PEAR. Elle peut être installée via les commandes suivantes :

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Une fois que vous avez installé la bibliothèque Twilio pour PHP, vous pouvez ajouter une instruction **require\_once** au début de vos fichiers PHP pour faire référence à cette bibliothèque :

        require_once 'Services/Twilio.php';

Pour plus d'informations, consultez la page [][2]<https://github.com/twilio/twilio-php/blob/master/README.md></a>.

## <span id="howto_make_call"></span></a> Appel téléphonique

Le code suivant montre comment passer un appel téléphonique à l'aide de la classe **Services\_Twilio**. Il utilise également un site Twilio afin de renvoyer la réponse TwiML (Twilio Markup Language). Remplacez vos valeurs pour les numéros de téléphone **From** (De) et **To** (À), puis assurez-vous de vérifier le numéro de téléphone **From** de votre compte Twilio avant d'exécuter le code.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Comme indiqué, ce code utilise un site Twilio afin de renvoyer la réponse TwiML, À la place, vous pouvez utiliser votre propre site pour fournir la réponse TwiML. Pour plus d'informations, consultez la rubrique [Envoi de réponses TwiML à partir de votre propre site Web][].

-   **Remarque** : Pour résoudre les erreurs de validation des certificats SSL, consultez la page [][3]<http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html></a>

## <span id="howto_send_sms"></span></a> Envoi d'un SMS

La séquence suivante montre comment envoyer un SMS à l'aide de la classe **Services\_Twilio**. Le numéro **From** est fourni par Twilio pour permettre aux comptes d'évaluation d'envoyer des SMS. Le numéro **To** doit être vérifié pour votre compte Twilio avant d'exécuter le code.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <span id="howto_provide_twiml_responses"></span></a> Envoi de réponses TwiML à partir de votre propre site web

Lorsque votre application démarre un appel à l'API Twilio, Twilio envoie votre demande à une URL qui est censée renvoyer une réponse TwiML. L'exemple ci-dessus utilise l'URL fournie par Twilio [][4]<http://twimlets.com/message></a>. TwiML est conçu pour être utilisé par Twilio, vous pouvez donc l'afficher dans votre navigateur. Par exemple, cliquez sur [][4]<http://twimlets.com/message></a> pour afficher un élément `<Response>` vide. Autre exemple, cliquez sur [][5]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a> pour afficher un élément `<Response>` contenant un élément `<Say>`.)

Au lieu de compter sur l'URL Twilio, vous pouvez créer votre propre site qui renvoie des réponses HTTP. Vous pouvez créer ce site dans n'importe quel langage qui renvoie des réponses XML. Dans cette rubrique, il est admis que vous utilisez le langage PHP pour créer le TwiML.

La page PHP qui suit génère une réponse TwiML prononçant **Hello World** lors de l'appel.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Comme vous pouvez le constater dans l'exemple ci-dessus, la réponse TwiML est simplement un document XML. La bibliothèque Twilio pour PHP contient des classes qui généreront TwiML pour vous. L'exemple ci-dessous produit la réponse équivalente comme illustré ci-dessus, mais utilise la classe **Services\_Twilio\_Twiml** dans la bibliothèque Twilio pour PHP.

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Pour plus d'informations sur TwiML, consultez la page [][6]<https://www.twilio.com/docs/api/twiml></a>.

Une fois que votre page PHP est configurée pour envoyer des réponses TwiML, utilisez son URL en tant qu'URL transmise à la méthode `Services_Twilio->account->calls->create`. Par exemple, si une application Web intitulée **MyTwiML** est déployée sur un service hébergé Azure, et que le nom de la page PHP est **mytwiml.php**, l'URL peut être transmise à **Services\_Twilio-\>account-\>calls-\>create** comme illustré dans l'exemple de code suivant :

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Pour plus d'informations sur l'utilisation de Twilio dans Azure avec PHP, consultez la page [Exécution d'un appel téléphonique à l'aide de Twilio dans une application PHP sur Azure][].

## <span id="AdditionalServices"></span></a> Utilisation des services Twilio supplémentaires

En plus des exemples présentés ici, Twilio offre des API Web que vous pouvez utiliser pour tirer profit d’autres fonctionnalités de Twilio à partir de votre application Azure. Pour plus d’informations, consultez la [documentation de l’API Twilio][API Twilio].

## <span id="NextSteps"></span></a>Étapes suivantes

Maintenant que vous avez appris les bases du service Twilio, consultez ces liens pour en savoir plus :

-   [Conseils de sécurité Twilio][]
-   [Procédures et exemples de code Twilio][]
-   [Didacticiels de démarrage rapide Twilio][]
-   [Twilio sur GitHub][]
-   [Support Twilio][]

  [Étapes suivantes]: #NextSteps
  [Présentation de Twilio]: #WhatIs
  [Tarification de Twilio]: #Pricing
  [Concepts]: #Concepts
  [Création d'un compte Twilio]: #CreateAccount
  [Vérification des numéros de téléphone]: #VerifyPhoneNumbers
  [Création d'une application PHP]: #create_app
  [Configuration de l'application pour utiliser les bibliothèques Twilio]: #configure_app
  [Appel téléphonique]: #howto_make_call
  [Envoi d'un SMS]: #howto_send_sms
  [Envoi de réponses TwiML à partir de votre propre site Web]: #howto_provide_twiml_responses
  [1]: http://www.twilio.com/pricing
  [Bibliothèques de l'API Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [Essayer Twilio]: https://www.twilio.com/try-twilio
  [page du compte Twilio]: https://www.twilio.com/user/account
  [Gestion des numéros]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Aide sur les numéros de téléphone Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [bibliothèque Twilio pour PHP depuis Github]: https://github.com/twilio/twilio-php
  []: http://readthedocs.org/docs/twilio-php/en/latest/index.html
  [Exécution d'un appel téléphonique à l'aide de Twilio dans une application PHP sur Azure]: http://windowsazure.com/fr-fr/documentation/articles/partner-twilio-php-make-phone-call
  [2]: https://github.com/twilio/twilio-php/blob/master/README.md
  [3]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
  [4]: http://twimlets.com/message
  [5]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [6]: https://www.twilio.com/docs/api/twiml
  [Conseils de sécurité Twilio]: http://www.twilio.com/docs/security
  [Procédures et exemples de code Twilio]: http://www.twilio.com/docs/howto
  [Didacticiels de démarrage rapide Twilio]: http://www.twilio.com/docs/quickstart
  [Twilio sur GitHub]: https://github.com/twilio
  [Support Twilio]: http://www.twilio.com/help/contact
  [ahoy.twilio.com/azure]: http://ahoy.twilio.com/azure
  [Documentation de Twilio Markup Language]: http://www.twilio.com/docs/api/twiml
