<properties 
	pageTitle="Utilisation de Twilio pour les fonctionnalités vocales et de SMS (PHP) - Azure" 
	description="Découvrez comment passer un appel téléphonique et envoyer un message texte avec le service d'API Twilio sur Azure. Exemples de code écrits en PHP." 
	documentationCenter="php" 
	services="" 
	authors="devinrader" 
	manager="twilio" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>

# Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans PHP
Ce guide présente l'exécution de tâches de programmation courantes avec le service API Twilio sur Azure. Les scénarios abordés comprennent notamment les appels téléphoniques et l'envoi de SMS. Pour plus d'informations sur Twilio et sur l'utilisation des fonctionnalités vocales et de SMS de vos applications, consultez la section [Étapes suivantes](#NextSteps).

## <a id="WhatIs"></a>Présentation de Twilio
Twilio alimente le futur des communications professionnelles, en permettant aux développeurs d'incorporer les fonctionnalités vocales, VoIP et de messagerie dans les applications. Ils virtualisent toute l'infrastructure nécessaire dans un environnement global dans le cloud, en l'exposant au moyen de la plateforme de l'API de communication Twilio. Les applications sont faciles à générer et sont évolutives. Tirez parti de la souplesse du paiement à l'utilisation et de la fiabilité du cloud.

**Twilio Voice** permet à vos applications de passer et de recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications d'envoyer et de recevoir des messages texte. **Twilio Client** permet de passer des appels VoIP à partir d'un téléphone, d'une tablette ou d'un navigateur et prend en charge WebRTC.

## <a id="Pricing"></a>Tarification de Twilio et offres spéciales

Les clients Azure reçoivent une [offre spéciale ](http://www.twilio.com/azure)de 10 $ en crédit Twilio lorsqu'ils mettent à niveau leur compte Twilio. Ce crédit peut servir à l'achat d'une prestation Twilio (un crédit de 10 $ correspond à l'envoi de 1 000 SMS ou à la réception de 1 000 minutes vocales maximum, en fonction de l'emplacement de votre numéro de téléphone, du message ou de la destination de l'appel). Profitez de ce crédit Twilio et démarrez en consultant la page : [http://ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio est un service de paiement à l'utilisation. Il n'existe pas de frais d'entrée et vous pouvez fermer votre compte quand vous le souhaitez. Pour plus d'informations, consultez la page [Tarification de Twilio][twilio_pricing].

## <a id="Concepts"></a>Concepts
L'API Twilio est une API RESTful qui offre des fonctionnalités vocales et de SMS aux applications. Les bibliothèques clientes sont disponibles dans plusieurs langues : pour en obtenir la liste, consultez la page [Bibliothèques de l'API Twilio][twilio_libraries].

Les éléments les plus importants de l'API Twilio sont les verbes Twilio et TwiML (Twilio Markup Language).

### <a id="Verbs"></a>Verbes Twilio
L'API utilise les verbes Twilio ; par exemple, le verbe **&lt;Say&gt;** (Dire) indique à Twilio de transmettre un message de manière audible lors d'un appel.

La liste suivante présente les verbes Twilio. Découvrez les autres verbes et fonctionnalités dans la page [Documentation de Twilio Markup Language](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;** : connecte l'appelant à un autre téléphone.
* **&lt;Gather&gt;** : rassemble les chiffres numériques entrés sur le clavier du téléphone.
* **&lt;Hangup&gt;** : met fin à un appel.
* **&lt;Play&gt;** : lit un fichier audio.
* **&lt;Pause&gt;** : patiente silencieusement pendant un nombre déterminé de secondes.
* **&lt;Record&gt;** : enregistre la voix de l'appelant et renvoie une URL permettant d'accéder à un fichier contenant l'enregistrement.
* **&lt;Redirect&gt;** : transfère le contrôle d'un appel ou d'un SMS au TwiML à une autre URL.
* **&lt;Reject&gt;** : refuse un appel entrant sur votre numéro Twilio sans vous facturer.
* **&lt;Say&gt;** : convertit le texte d'un appel en parole.
* **&lt;Sms&gt;** : envoie un SMS.

### <a id="TwiML"></a>TwiML
TwiML est un jeu d'instructions XML reposant sur les verbes Twilio qui informent Twilio sur la façon de traiter un appel ou un SMS.

À titre d'exemple, le code TwiML suivant convertit le texte **Hello World** en parole.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Lorsque votre application appelle l'API Twilio, l'un des paramètres de l'API est l'URL qui renvoie la réponse TwiML. À des fins de développement, vous pouvez utiliser les URL Twilio pour fournir les réponses TwiML utilisées par vos applications. Vous pouvez également héberger vos propres URL afin de produire les réponses TwiML, ou encore utiliser l'objet **TwiMLResponse**.

Pour plus d'informations sur les verbes Twilio, leurs attributs et le langage TwiML, consultez la page [TwiML][twiml]. Pour plus d'informations sur l'API Twilio, consultez la page [API Twilio][twilio_api].

## <a id="CreateAccount"></a>Créer un compte Twilio
Lorsque vous êtes prêt à créer votre compte Twilio, inscrivez-vous sur la page [Essayer Twilio][try_twilio]. Vous pouvez commencer avec un compte gratuit, avant de le mettre à niveau ultérieurement.

Lorsque vous créez un compte Twilio, vous recevez un ID de compte et un jeton d'authentification. Les deux sont nécessaires pour passer des appels d'API Twilio. Pour éviter qu'une personne non autorisée n'accède à votre compte, conservez votre jeton d'authentification en lieu sûr. Vous pouvez consulter vos ID de compte et jeton d'authentification sur la [page du compte Twilio][twilio_account], dans les champs intitulés **ACCOUNT SID** et **AUTH TOKEN**, respectivement.


## <a id="create_app"></a>Création d’une application PHP
Une application PHP qui utilise le service Twilio et qui s'exécute dans Azure est identique aux autres applications PHP qui utilisent le service Twilio. Bien que les services Twilio soient basés sur REST et puissent être appelés de différentes manières à partir de PHP, cet article met l’accent sur l’utilisation des services Twilio avec la [bibliothèque Twilio pour PHP de Github][twilio_php]. Pour plus d'informations sur l'utilisation de la bibliothèque Twilio pour PHP, consultez la page [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Des instructions détaillées sur la conception et le déploiement d'une application Twilio/PHP sur Azure sont disponibles sur la page [Exécution d'un appel téléphonique à l'aide de Twilio dans une application PHP sur Azure][howto_phonecall_php].

## <a id="configure_app"></a>Configurer l’application pour utiliser les bibliothèques Twilio
Vous pouvez configurer votre application pour la bibliothèque Twilio pour PHP de deux manières :

1. Téléchargez la bibliothèque Twilio pour PHP sur GitHub ([https://github.com/twilio/twilio-php][twilio_php]) et ajoutez le répertoire **Services** à votre application.

	OU

2. Installez la bibliothèque Twilio pour PHP sous forme de package PEAR. Elle peut être installée via les commandes suivantes :

		$ pear channel-discover twilio.github.com/pear
		$ pear install twilio/Services_Twilio

Une fois que vous avez installé la bibliothèque Twilio pour PHP, vous pouvez ajouter une instruction **require_once** au début de vos fichiers PHP pour faire référence à cette bibliothèque :

    	require_once 'Services/Twilio.php';

Pour plus d'informations, consultez la page [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Procédure : passer un appel sortant
Le code suivant montre comment passer un appel téléphonique à l'aide de la classe **Services_Twilio**. Il utilise également un site Twilio afin de renvoyer la réponse TwiML (Twilio Markup Language). Remplacez vos valeurs pour les numéros de téléphone **From** (De) et **To** (À), puis assurez-vous de vérifier le numéro de téléphone **From** de votre compte Twilio avant d'exécuter le code.

	// Include the Twilio PHP library.
	require_once 'Services/Twilio.php';

	// Library version.
	$version = "2010-04-01";

	// Set your account ID and authentication token.
	$sid = "your_twilio_account_sid";
	$token = "your_twilio_authentication_token";

	// The number of the phone initiating the the call.
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

Comme indiqué, ce code utilise un site Twilio afin de renvoyer la réponse TwiML, À la place, vous pouvez utiliser votre propre site pour fournir la réponse TwiML. Pour plus d'informations, consultez la rubrique [Envoi de réponses TwiML à partir de votre propre site Web](#howto_provide_twiml_responses).


- **Remarque** : pour corriger les erreurs de validation des certificats SSL, consultez la page [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 


## <a id="howto_send_sms"></a>Procédure : envoi d'un message SMS
La séquence suivante montre comment envoyer un SMS à l'aide de la classe **Services_Twilio**. Le numéro **From** est fourni par Twilio pour permettre aux comptes d'évaluation d'envoyer des SMS. Le numéro **To** doit être vérifié pour votre compte Twilio avant d'exécuter le code.

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
		$client->$client->account->messages->sendMessage($from_number, $to_number, $message);
	}
	catch (Exception $e) 
	{
		echo 'Error: ' . $e->getMessage();
	}

## <a id="howto_provide_twiml_responses"></a>Procédure : envoi de réponses TwiML depuis votre propre site web
Lorsque votre application démarre un appel à l'API Twilio, Twilio envoie votre demande à une URL qui est censée renvoyer une réponse TwiML. L'exemple ci-dessus utilise l'URL fournie par Twilio [http://twimlets.com/message][twimlet_message_url]. TwiML est conçu pour être utilisé par Twilio, vous pouvez donc l'afficher dans votre navigateur. Par exemple, cliquez sur [http://twimlets.com/message][twimlet_message_url] pour visualiser un élément `<Response>` vide. Autre exemple : cliquez sur [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] pour afficher un élément `<Response>` contenant un élément `<Say>`.

Au lieu de compter sur l'URL Twilio, vous pouvez créer votre propre site qui renvoie des réponses HTTP. Vous pouvez créer ce site dans n'importe quel langage qui renvoie des réponses XML. Dans cette rubrique, il est admis que vous utilisez le langage PHP pour créer le TwiML.

La page PHP qui suit génère une réponse TwiML prononçant **Hello World** lors de l'appel.

    <?php    
		header("content-type: text/xml");    
		echo "<?xml version="1.0" encoding="UTF-8"?>\n";
	?>
	<Response>    
		<Say>Hello world.</Say>
	</Response>

Comme vous pouvez le constater dans l'exemple ci-dessus, la réponse TwiML est simplement un document XML. La bibliothèque Twilio pour PHP contient des classes qui généreront TwiML pour vous. L'exemple ci-dessous produit la réponse équivalente comme illustré ci-dessus, mais utilise la classe **Services_Twilio_Twiml** dans la bibliothèque Twilio pour PHP.

	require_once('Services/Twilio.php');
	
	$response = new Services_Twilio_Twiml();
	$response->say("Hello world.");
	print $response;

Pour plus d'informations sur TwiML, consultez la page [https://www.twilio.com/docs/api/twiml][twiml_reference].

Une fois que votre page PHP est configurée pour envoyer des réponses TwiML, utilisez son URL en tant qu’URL transmise à la méthode `Services_Twilio->account->calls->create`. Par exemple, si une application Web intitulée **MyTwiML** est déployée sur un service hébergé Azure, et que le nom de la page PHP est **mytwiml.php**, l'URL peut être transmise à **Services_Twilio->account->calls->create** comme illustré dans l'exemple de code suivant :

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

Pour plus d'informations sur l'utilisation de Twilio dans Azure avec PHP, consultez la page [Exécution d'un appel téléphonique à l'aide de Twilio dans une application PHP sur Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Utilisation de services Twilio supplémentaires
En plus des exemples présentés ici, Twilio offre des API Web que vous pouvez utiliser pour tirer profit d'autres fonctionnalités de Twilio à partir de votre application Azure. Pour plus d'informations, consultez la [documentation de l'API Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Étapes suivantes
Maintenant que vous avez appris les bases du service Twilio, consultez ces liens pour en savoir plus :

* [Conseils de sécurité Twilio][twilio_security_guidelines]
* [Procédures et exemples de code Twilio][twilio_howtos]
* [Didacticiels de démarrage rapide Twilio][twilio_quickstarts] 
* [Twilio sur GitHub][twilio_on_github]
* [Support Twilio][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]: https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart

<!---HONumber=July15_HO3-->