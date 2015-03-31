<properties 
	pageTitle="Utilisation de Twilio pour les fonctionnalités vocales et de SMS (Java) - Azure" 
	description="Découvrez comment passer un appel téléphonique et envoyer un message texte avec le service d'API Twilio sur Azure. Exemples de code écrits en Java." 
	services="" 
	documentationCenter="java" 
	authors="devinrader" 
	manager="twilio" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>

# Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans Java

Ce guide présente l'exécution de tâches de programmation courantes avec le service API Twilio sur Azure. Les scénarios abordés comprennent notamment les appels téléphoniques et l'envoi de SMS. Pour plus d'informations sur Twilio et sur l'utilisation des fonctionnalités vocales et de SMS dans vos applications, consultez la section [Étapes suivantes](#NextSteps) .

<h2><a id="WhatIs"></a>Présentation de Twilio</h2>
Twilio est une API de service Web de téléphonie vous permettant d'utiliser vos aptitudes et langages Web existants pour générer des applications vocales et de SMS. Twilio est un service tiers, et non une fonctionnalité Azure ni un produit Microsoft.

**Twilio Voice** permet à vos applications de passer et de recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications de créer et de recevoir des SMS. **Twilio Client** permet à vos applications d'activer les communications vocales au moyen de connexions Internet existantes, y compris des connexions mobiles.

<h2><a id="Pricing"></a>Tarification de Twilio et offres spéciales</h2>
Des informations sur la tarification de Twilio sont disponibles dans la page [Tarification de Twilio] [twilio_pricing]. Les clients Azure reçoivent une [offre spéciale][special_offer] : un crédit gratuit de 1000 textes ou 1000 minutes entrantes. Pour vous inscrire à cette offre ou obtenir plus d'informations, visitez le site [http://ahoy.twilio.com/azure][special_offer].  

<h2><a id="Concepts"></a>Concepts</h2>
L'API Twilio est une API RESTful qui offre des fonctionnalités vocales et de SMS aux applications. Les bibliothèques clientes sont disponibles dans plusieurs langues : pour en obtenir la liste, consultez la page [Bibliothèques de l'API Twilio] [twilio_libraries].

Les éléments les plus importants de l'API Twilio sont les verbes Twilio et TwiML (Twilio Markup Language).

<h3><a id="Verbs"></a>Verbes Twilio</h3>
L'API utilise les verbes Twilio ; par exemple, le verbe **&lt;Say&gt;** ordonne à Twilio de remettre un message audible lors d'un appel. 

La liste suivante présente les verbes Twilio.

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

<h3><a id="TwiML"></a>TwiML</h3>
TwiML est un jeu d'instructions XML reposant sur les verbes Twilio qui informent Twilio sur la façon de traiter un appel ou un SMS.

À titre d'exemple, le code TwiML suivant convertit le texte **Hello World** en parole.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Lorsque votre application appelle l'API Twilio, l'un des paramètres de l'API est l'URL qui renvoie la réponse TwiML. À des fins de développement, vous pouvez utiliser les URL Twilio pour fournir les réponses TwiML utilisées par vos applications. Vous pouvez également héberger vos propres URL afin de produire les réponses TwiML, ou encore utiliser l'objet **TwiMLResponse**.

Pour plus d'informations sur les verbes Twilio, leurs attributs et le langage TwiML, consultez la page [TwiML] [twiml]. Pour plus d'informations sur l'API Twilio, consultez la page [API Twilio] [twilio_api].

<h2><a id="CreateAccount"></a>Création d'un compte Twilio</h2>
Lorsque vous êtes prêt à créer votre compte Twilio, inscrivez-vous dans la page [Essayer Twilio] [try_twilio]. Vous pouvez commencer avec un compte gratuit, avant de le mettre à niveau ultérieurement.

Lorsque vous créez un compte Twilio, vous recevez un ID de compte et un jeton d'authentification. Les deux sont nécessaires pour passer des appels d'API Twilio. Pour éviter qu'une personne non autorisée n'accède à votre compte, conservez votre jeton d'authentification en lieu sûr. Vous pouvez consulter vos ID de compte et jeton d'authentification dans la [page du compte Twilio] [twilio_account], dans les champs intitulés **ACCOUNT SID** et **AUTH TOKEN**, respectivement.

<h2><a id="create_app"></a>Création d'une application Java</h2>
1. Obtenez le JAR Twilio et ajoutez-le à votre chemin d'accès de build Java et à votre assembly de déploiement WAR. À l'adresse [https://github.com/twilio/twilio-java][twilio_java], vous pouvez télécharger les sources GitHub et créer votre propre JAR ou télécharger un JAR préconstruit (avec ou sans dépendances).
2. Vérifiez que le keystore **cacerts** de votre JDK contient le certificat ESCA (Equifax Secure Certificate Authority) avec l'empreinte MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (le numéro de série est 35:DE:F4:CF et l'empreinte de SHA1 est D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Il s'agit du certificat d'autorité de certification pour le service [https://api.twilio.com][twilio_api_service] qui est appelé lorsque vous utilisez les API Twilio. Pour plus d'informations sur la marche à suivre pour vous assurer que le keystore **cacerts** de votre JDK contient le certificat d'autorité de certification correct, consultez la page [Ajout d'un certificat au magasin de certificats d'autorité de certification Java][add_ca_cert].

Les instructions détaillées pour l'utilisation de la bibliothèque cliente Twilio pour Java sont disponibles sur la page [Exécution d'un appel téléphonique à l'aide de Twilio dans une application Java sur Azure][howto_phonecall_java].

<h2><a id="configure_app"></a>Configuration de l'application pour utiliser les bibliothèques Twilio</h2>
Dans votre code, vous pouvez ajouter des instructions **import** en haut de vos fichiers sources pour les classes ou packages Twilio que vous voulez utiliser dans votre application. 

Pour les fichiers sources Java :

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Pour les fichiers sources JSP (Java Server Page) :

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
Selon les packages ou classes Twilio que vous voulez utiliser, vos instructions **import** peuvent être différentes.

<h2><a id="howto_make_call"></a>Procédure : appel téléphonique</h2>
Le code suivant montre comment passer un appel téléphonique à l'aide de la classe **CallFactory**. Il utilise également un site Twilio afin de renvoyer la réponse TwiML (Twilio Markup Language). Indiquez vos valeurs pour les numéros de téléphone **From** (De) et **To** (À), puis assurez-vous de vérifier le numéro de téléphone **From** de votre compte Twilio avant d'exécuter le code.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

Pour plus d'informations sur les paramètres transmis à la méthode **CallFactory.create**, consultez la page [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Comme indiqué, ce code utilise un site Twilio pour renvoyer la réponse TwiML, À la place, vous pouvez utiliser votre propre site pour fournir la réponse TwiML ; pour plus d'informations, consultez la rubrique [Envoi de réponses TwiML dans une application Java sur Azure](#howto_provide_twiml_responses)(ce

<h2><a id="howto_send_sms"></a>Procédure : Envoi d'un SMS</h2>
La procédure suivante montre comment envoyer un SMS à l'aide de la classe **SmsFactory**. Le numéro **From** (De), **4155992671**, est fourni par Twilio pour permettre aux comptes d'évaluation d'envoyer des SMS. Le numéro **To** (À) doit être vérifié pour votre compte Twilio avant d'exécuter le code.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
Pour plus d'informations sur les paramètres transmis à la méthode **SmsFactory.create**, consultez la page [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

<h2><a id="howto_provide_twiml_responses"></a>Procédure : Envoi de réponses TwiML à partir de votre propre site web</h2>
Lorsque votre application démarre un appel vers l'API Twilio, par exemple via la méthode **CallFactory.create**, Twilio envoie votre demande à une URL qui est censée renvoyer une réponse TwiML. L'exemple ci-dessus utilise l'URL fournie par Twilio [http://twimlets.com/message][twimlet_message_url]. (TwiML est conçu pour être utilisé par des services web, vous pouvez donc afficher TwiML dans votre navigateur. Par exemple, cliquez sur [http://twimlets.com/message][twimlet_message_url] pour voir un élément **&lt;<Response>&gt;** vide. Autre exemple : cliquez sur [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] pour voir un élément **&lt;<Response>&gt;** contenant un élément **&lt;<Say>&gt;**.)

Au lieu de compter sur l'URL Twilio, vous pouvez créer votre propre site d'URL qui renvoie des réponses HTTP. Vous pouvez créer le site dans n'importe quelle langage renvoyant des réponses HTTP ; cette rubrique suppose que vous hébergerez l'URL sur une page JSP.

La page JSP suivante génère une réponse TwiML prononçant **Hello World** lors de l'appel.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

La page JSP suivante génère une réponse TwiML qui prononce un texte spécifique, contient plusieurs pauses et fournit des informations sur la version d'API Twilio et le nom de rôle Azure.


    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

Le paramètre **ApiVersion** est disponible dans les demandes vocales Twilio (pas pour les demandes par SMS). Pour afficher les paramètres de demande disponibles pour les demandes vocales et SMS de Twilio, consultez respectivement les pages <https://www.twilio.com/docs/api/twiml/twilio_request> and <https://www.twilio.com/docs/api/twiml/sms/twilio_request>. La variable d'environnement **RoleName** est disponible au sein d'un déploiement Azure. (Si vous souhaitez ajouter des variables d'environnement personnalisées afin qu'elles puissent être sélectionnées à partir de **System.getenv**, consultez la section sur les variables d'environnement dans [Paramètres de configuration de rôle divers][misc_role_config_settings].)

Une fois que votre page JSP est configurée et prête à envoyer des réponses TwiML, utilisez son URL en tant qu'URL transmise à la méthode **CallFactory.create**. Par exemple, si vous avez une application Web intitulée MyTwiML et déployée sur un service hébergé Azure, et que le nom de la page JSP est mytwiml.jsp, l'URL peut être transmise à **CallFactory.create**, comme indiqué ci-après :

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Une autre possibilité de réponse avec TwiML consiste à utiliser la classe **TwiMLResponse**, qui est disponible dans le package **com.twilio.sdk.verbs**.

Pour plus d'informations sur l'utilisation de Twilio dans Azure avec Java, consultez la page [Exécution d'un appel téléphonique à l'aide de Twilio dans une application Java sur Azure][howto_phonecall_java].

<h2><a id="AdditionalServices"></a>Procédure : Utilisation des services Twilio supplémentaires</h2>
En plus des exemples présentés ici, Twilio offre des API Web que vous pouvez utiliser pour tirer profit d'autres fonctionnalités de Twilio à partir de votre application Azure. Pour plus d'informations, consultez la [documentation de l'API Twilio] [twilio_api_documentation].

<h2><a id="NextSteps"></a>Étapes suivantes</h2>
Maintenant que vous avez appris les bases du service Twilio, consultez ces liens pour en savoir plus :

* [Conseils de sécurité Twilio] [twilio_security_guidelines]
* [Procédures et exemples de code Twilio] [twilio_howtos]
* [Didacticiels de démarrage rapide Twilio][twilio_quickstarts] 
* [Twilio sur GitHub] [twilio_on_github]
* [Support Twilio] [twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: ../java-add-certificate-ca-store
[howto_phonecall_java]: ../partner-twilio-java-phone-call-example
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart

<!--HONumber=47-->
