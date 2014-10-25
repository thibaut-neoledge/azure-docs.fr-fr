<properties linkid="develop-java-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Java) - Azure" metaKeywords="Twilio, Twilio API, phone calls, SMS message, TwiML responses, Azure Twilio Java" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Java." metaCanonical="" services="" videoId="" scriptId="" documentationCenter="Java" title="How to Use Twilio for Voice and SMS Capabilities in Java" authors="MicrosoftHelp@twilio.com; robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm"></tags>

# Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans Java

Ce guide présente l'exécution de tâches de programmation courantes avec le service API Twilio sur Azure. Les scénarios abordés comprennent notamment les appels téléphoniques et l'envoi de SMS. Pour plus d'informations sur Twilio et sur l'utilisation des fonctionnalités vocales et de SMS de vos applications, consultez la section [Étapes suivantes][].

## Sommaire

-   [Présentation de Twilio][]
-   [Tarification de Twilio][]
-   [Concepts][]
-   [Création d'un compte Twilio][]
-   [Vérification des numéros de téléphone][]
-   [Création d'une application Java][]
-   [Configuration de l'application pour utiliser les bibliothèques Twilio][]
-   [Appel téléphonique][]
-   [Envoi d'un SMS][]
-   [Envoi de réponses TwiML à partir de votre propre site Web][]
-   [Utilisation des services Twilio supplémentaires][]
-   [Étapes suivantes][]

## <span id="WhatIs"></span></a>Présentation de Twilio

Twilio est une API de service Web de téléphonie vous permettant d'utiliser vos aptitudes et langages Web existants pour générer des applications vocales et de SMS. Twilio est un service tiers, et non une fonctionnalité Azure ni un produit Microsoft.

**Twilio Voice** permet à vos applications de passer et de recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications de créer et de recevoir des SMS. **Twilio Client** permet à vos applications d'activer les communications vocales au moyen de connexions Internet existantes, y compris des connexions mobiles.

## <span id="Pricing"></span></a>Tarification de Twilio et offres spéciales

Des informations sur la tarification de Twilio sont disponibles dans la page [Tarification de Twilio][1]. Les clients Azure reçoivent une [offre spéciale][] 1 000 SMS ou 1 000 minutes d'appel en entrée. Pour bénéficier de cette offre ou pour obtenir des informations supplémentaires, visitez la page [][offre spéciale]<http://ahoy.twilio.com/azure></a>.

## <span id="Concepts"></span></a>Concepts

L'API Twilio est une API RESTful qui offre des fonctionnalités vocales et de SMS aux applications. Les bibliothèques clientes sont disponibles dans plusieurs langues : pour en obtenir la liste, consultez la page [Bibliothèques de l'API Twilio][].

Les éléments les plus importants de l'API Twilio sont les verbes Twilio et TwiML (Twilio Markup Language).

### <span id="Verbs"></span></a>Verbes Twilio

L'API utilise les verbes Twilio ; par exemple, le verbe **\<Say\>** (Dire) indique à Twilio de transmettre un message de manière audible lors d'un appel.

La liste suivante présente les verbes Twilio.

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

Plusieurs numéros de téléphone doivent être vérifiés avec Twilio pour votre compte. Par exemple, si vous souhaitez passer des appels téléphoniques, le numéro de téléphone doit être vérifié en tant qu'ID d'appelant sortant avec Twilio. De la même façon, si vous voulez un numéro de téléphone pour recevoir des SMS, le numéro de téléphone recevant l'appel doit être vérifié avec Twilio. Pour plus d'informations sur la vérification d'un numéro de téléphone, consultez la page [Gestion des numéros][]. Une partie du code ci-dessous repose sur les numéros de téléphone que vous devez vérifier avec Twilio.

Si vous souhaitez utiliser un autre numéro de téléphone pour vos applications, vous pouvez acheter un numéro de téléphone Twilio. Pour plus d'informations sur l'achat d'un numéro de téléphone Twilio, consultez la page [Aide sur les numéros de téléphone Twilio][].

## <span id="create_app"></span></a>Création d'une application Java

1.  Obtenez le JAR Twilio et ajoutez-le à votre chemin d'accès de build Java et à votre assembly de déploiement WAR. Sur [][]<https://github.com/twilio/twilio-java></a>, vous pouvez télécharger les sources GitHub et créer votre propre JAR, ou télécharger un JAR préconstruit (avec ou sans dépendances).
2.  Vérifiez que le keystore **cacerts** de votre JDK contient le certificat ESCA (Equifax Secure Certificate Authority) avec l'empreinte MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (le numéro de série est 35:DE:F4:CF et l'empreinte de SHA1 est D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Il s'agit du certificat d'autorité de certification pour le service [][2]<https://api.twilio.com></a>, qui est appelé lorsque vous utilisez les API Twilio. Pour plus d'informations sur la façon de s'assurer que le keystore **cacerts** de votre JDK contient le certificat d'autorité de certification correct, consultez la rubrique [Ajout d'un certificat au magasin de certificats d'autorité de certification Java][].

Des instructions détaillées sur l'utilisation de la bibliothèque cliente Twilio pour Java sont disponibles dans la rubrique [Exécution d'un appel téléphonique au moyen de Twilio dans une application Java sur Azure][].

## <span id="configure_app"></span></a>Configuration de l'application pour utiliser les bibliothèques Twilio

Dans votre code, vous pouvez ajouter des instructions **import** en haut de vos fichiers sources pour les classes ou packages Twilio que vous voulez utiliser dans votre application.

Pour les fichiers sources Java :

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Pour les fichiers sources JSP (Java Server Page) :

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"

Selon les packages ou classes Twilio que vous voulez utiliser, vos instructions **import** peuvent être différentes.

## <span id="howto_make_call"></span></a> Appel téléphonique

Le code suivant montre comment passer un appel téléphonique à l'aide de la classe **CallFactory**. Il utilise également un site Twilio afin de renvoyer la réponse TwiML (Twilio Markup Language). Remplacez vos valeurs pour les numéros de téléphone **From** (De) et **To** (À), puis assurez-vous de vérifier le numéro de téléphone **From** de votre compte Twilio avant d'exécuter le code.

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

Pour plus d'informations sur les paramètres transmis dans la méthode **CallFactory.create**, consultez la page [][3]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Comme indiqué, ce code utilise un site Twilio afin de renvoyer la réponse TwiML, À la place, vous pouvez utiliser votre propre site pour fournir la réponse TwiML ; pour plus d'informations, consultez la rubrique [Envoi de réponses TwiML dans une application Java sur Azure][Envoi de réponses TwiML à partir de votre propre site Web].

## <span id="howto_send_sms"></span></a> Envoi d'un SMS

La procédure suivante montre comment envoyer un SMS à l'aide de la classe **SmsFactory**. Le numéro **From**, **4155992671**, est fourni par Twilio pour permettre aux comptes d'évaluation d'envoyer des messages SMS. Le numéro **To** doit être vérifié pour votre compte Twilio avant d'exécuter le code.

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
    // Place the call From, To and Body values into a hash map. 
    HashMap<String, String> smsParams = new HashMap<String, String>();
    smsParams.put("From", "4155992671"); // The second parameter is a phone number provided
                                         // by Twilio for trial accounts.
    smsParams.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    smsParams.put("Body", "This is my SMS message.");

    // Create an instance of the SmsFactory class.
    SmsFactory smsFactory = account.getSmsFactory();

    // Send the message.
    Sms sms = smsFactory.create(smsParams);

Pour plus d'informations sur les paramètres transmis dans la méthode **SmsFactory.create**, consultez la page [][4]<http://www.twilio.com/docs/api/rest/sending-sms></a>.

## <span id="howto_provide_twiml_responses"></span></a> Envoi de réponses TwiML à partir de votre propre site web

Lorsque votre application démarre un appel vers l'API Twilio, par exemple via la méthode **CallFactory.create**, Twilio envoie votre demande à une URL qui est censée renvoyer une réponse TwiML. L'exemple ci-dessus utilise l'URL fournie par Twilio [][5]<http://twimlets.com/message></a>. TwiML est conçu pour être utilisé par des services Web, vous pouvez donc afficher TwiML dans votre navigateur. Par exemple, cliquez sur [][5]<http://twimlets.com/message></a> pour afficher un élément **\<Response\>** vide ; autre exemple, cliquez sur [][6]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a> pour afficher un élément **\<Response\>** contenant un élément **\<Say\>**.

Au lieu de compter sur l'URL Twilio, vous pouvez créer votre propre site d'URL qui renvoie des réponses HTTP. Vous pouvez créer le site dans n'importe quelle langage renvoyant des réponses HTTP ; cette rubrique suppose que vous hébergerez l'URL sur une page JSP.

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

Le paramètre **ApiVersion** est disponible dans les demandes vocales Twilio (pas pour les demandes par SMS). Pour afficher les paramètres de demande disponibles pour les demandes vocales et SMS de Twilio, consultez respectivement les pages <https://www.twilio.com/docs/api/twiml/twilio_request> et <https://www.twilio.com/docs/api/twiml/sms/twilio_request>. La variable d'environnement **RoleName** est disponible au sein du déploiement Azure. Si vous voulez ajouter des variables d'environnement personnalisées de façon à ce qu'elles puissent être sélectionnées à partir de **System.getenv**, consultez la section des variables d'environnement sur la page [Paramètres de configuration de rôle divers][].

Une fois que votre page JSP est configurée et prête à envoyer des réponses TwiML, utilisez son URL en tant qu'URL transmise à la méthode **CallFactory.create**. Par exemple, si vous avez une application Web intitulée MyTwiML et déployée sur un service hébergé Azure, et que le nom de la page JSP est mytwiml.jsp, l'URL peut être transmise à **CallFactory.create**, comme indiqué ci-après :

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Une autre possibilité de réponse avec TwiML consiste à utiliser la classe **TwiMLResponse**, qui est disponible dans le package **com.twilio.sdk.verbs**.

Pour plus d'informations sur l'utilisation de Twilio dans Azure avec Java, consultez la page [Exécution d'un appel téléphonique à l'aide de Twilio dans une application Java sur Azure][Exécution d'un appel téléphonique au moyen de Twilio dans une application Java sur Azure].

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
  [Création d'une application Java]: #create_app
  [Configuration de l'application pour utiliser les bibliothèques Twilio]: #configure_app
  [Appel téléphonique]: #howto_make_call
  [Envoi d'un SMS]: #howto_send_sms
  [Envoi de réponses TwiML à partir de votre propre site Web]: #howto_provide_twiml_responses
  [Utilisation des services Twilio supplémentaires]: #AdditionalServices
  [1]: http://www.twilio.com/pricing
  [offre spéciale]: http://ahoy.twilio.com/azure
  [Bibliothèques de l'API Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [Essayer Twilio]: https://www.twilio.com/try-twilio
  [page du compte Twilio]: https://www.twilio.com/user/account
  [Gestion des numéros]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Aide sur les numéros de téléphone Twilio]: https://www.twilio.com/help/faq/phone-numbers
  []: https://github.com/twilio/twilio-java
  [2]: https://api.twilio.com
  [Ajout d'un certificat au magasin de certificats d'autorité de certification Java]: ../java-add-certificate-ca-store
  [Exécution d'un appel téléphonique au moyen de Twilio dans une application Java sur Azure]: ../partner-twilio-java-phone-call-example
  [3]: http://www.twilio.com/docs/api/rest/making-calls
  [4]: http://www.twilio.com/docs/api/rest/sending-sms
  [5]: http://twimlets.com/message
  [6]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [Paramètres de configuration de rôle divers]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh690945.aspx
  [Conseils de sécurité Twilio]: http://www.twilio.com/docs/security
  [Procédures et exemples de code Twilio]: http://www.twilio.com/docs/howto
  [Didacticiels de démarrage rapide Twilio]: http://www.twilio.com/docs/quickstart
  [Twilio sur GitHub]: https://github.com/twilio
  [Support Twilio]: http://www.twilio.com/help/contact
