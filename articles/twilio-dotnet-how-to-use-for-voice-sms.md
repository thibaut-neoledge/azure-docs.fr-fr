<properties linkid="develop-net-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (.NET) - Azure" metaKeywords="Azure Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to use Twilio for voice and SMS capabilities from Azure" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com"></tags>

# Utilisation de Twilio pour les fonctionnalités vocales et de SMS depuis Azure

Ce guide présente l'exécution de tâches de programmation courantes avec le service API Twilio sur Azure. Les scénarios abordés comprennent notamment les appels téléphoniques et l'envoi de SMS. Pour plus d'informations sur Twilio et sur l'utilisation des fonctionnalités vocales et de SMS de vos applications, consultez la section [Étapes suivantes][Étapes suivantes].

## Sommaire

-   [Présentation de Twilio][Présentation de Twilio]
-   [Tarification de Twilio][Tarification de Twilio]
-   [Concepts][Concepts]
-   [Création d'un compte Twilio][Création d'un compte Twilio]
-   [Vérification des numéros de téléphone][Vérification des numéros de téléphone]
-   [Création d'une application Azure][Création d'une application Azure]
-   [Configuration de l'application pour utiliser les bibliothèques Twilio][Configuration de l'application pour utiliser les bibliothèques Twilio]
-   [Appel téléphonique][Appel téléphonique]
-   [Envoi d'un SMS][Envoi d'un SMS]
-   [Envoi de réponses TwiML à partir de votre propre site Web][Envoi de réponses TwiML à partir de votre propre site Web]
-   [Utilisation des services Twilio supplémentaires][Utilisation des services Twilio supplémentaires]
-   [Étapes suivantes][Étapes suivantes]

## <span id="WhatIs"></span></a>Présentation de Twilio

Twilio alimente le futur des communications professionnelles, en permettant aux développeurs d'incorporer les fonctionnalités vocales, VoIP et de messagerie dans les applications. Ils virtualisent toute l'infrastructure nécessaire dans un environnement global dans le cloud, en l'exposant au moyen de la plateforme de l'API de communication Twilio. Les applications sont faciles à générer et sont évolutives. Tirez parti de la souplesse du paiement à l'utilisation et de la fiabilité du cloud.

**Twilio Voice** permet à vos applications de passer et de recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications d'envoyer et de recevoir des SMS. **Twilio Client** permet de passer des appels VoIP à partir d'un téléphone, d'une tablette ou d'un navigateur et prend en charge WebRTC.

## <span id="Pricing"></span></a>Tarification de Twilio et offres spéciales

Les clients Azure reçoivent une [offre spéciale][offre spéciale] sous la forme d'un crédit Twilio d'un montant de 10 $ offert lorsqu'ils mettent à niveau leur compte Twilio. Ce crédit peut servir à l'achat d'une prestation Twilio (un crédit de 10 $ correspond à l'envoi de 1 000 SMS ou à la réception de 1 000 minutes vocales maximum, en fonction de l'emplacement de votre numéro de téléphone, du message ou de la destination de l'appel). Profitez de ce crédit Twilio et démarrez en consultant la page [ahoy.twilio.com/azure][ahoy.twilio.com/azure].

Twilio est un service de paiement à l'utilisation. Il n'existe pas de frais d'entrée et vous pouvez fermer votre compte quand vous le souhaitez. Pour plus d'informations, consultez la page [Tarification de Twilio][1].

## <span id="Concepts"></span></a>Concepts

L'API Twilio est une API RESTful qui offre des fonctionnalités vocales et de SMS aux applications. Les bibliothèques clientes sont disponibles dans plusieurs langues : pour en obtenir la liste, consultez la page [Bibliothèques de l'API Twilio][Bibliothèques de l'API Twilio].

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

## <span id="create_app"></span></a>Création d'une application Azure

Une application Azure qui héberge une application Twilio n'est pas différente d'une autre application Azure. Vous ajoutez simplement la bibliothèque .NET Twilio et configurez le rôle permettant de l'utiliser.
Pour plus d'informations sur la création d'un projet Azure initial, consultez la page [Création d'un projet Azure avec Visual Studio][Création d'un projet Azure avec Visual Studio].

## <span id="configure_app"></span></a>Configuration de l'application pour utiliser les bibliothèques Twilio

Twilio offre un ensemble de bibliothèques d'aide .NET qui couvrent différents aspects de Twilio afin de fournir des moyens simples et faciles d'interaction avec l'API REST Twilio et le client Twilio en vue de générer des réponses TwiML.

Twilio offre cinq bibliothèques destinées aux développeurs .NET :

<table border="1">

<tr>

<th>
Bibliothèque

</th>

<th>
Description

</th>

</tr>

<tr>

<td>
Twilio.API

</td>

<td>
Bibliothèque Twilio principale qui englobe l'API REST Twilio dans une bibliothèque .NET conviviale. Cette bibliothèque est disponible pour .NET, Silverlight et Windows Phone 7.

</td>

</tr>

<tr>

<td>
Twilio.TwiML

</td>

<td>
Offre un moyen .NET convivial pour générer le balisage TwiML.

</td>

</tr>

<tr>

<td>
Twilio.MVC

</td>

<td>
Pour les développeurs utilisant ASP.NET MVC, cette bibliothèque inclut TwilioController, TwiML ActionResult et un attribut de validation de demande.

</td>

</tr>

<tr>

<td>
Twilio.WebMatrix

</td>

<td>
Pour les développeurs utilisant l'outil de développement WebMatrix gratuit de Microsoft, cette bibliothèque contient les aides à la syntaxe Razor pour différentes actions Twilio.

</td>

</tr>

<tr>

<td>
Twilio.Client.Capability

</td>

<td>
Contient le générateur de jetons de capacité à utiliser avec le Kit de développement logiciel (SDK) JavaScript du client Twilio.

</td>

</tr>

</table>
</p>
Notez que toutes les bibliothèques nécessitent .NET 3.5, Silverlight 4 ou Windows Phone 7 ou version ultérieure.

Les exemples fournis dans ce guide utilisent la bibliothèque Twilio.API.

Les bibliothèques peuvent être [installées à l'aide de l'extension du gestionnaire de packages NuGet][installées à l'aide de l'extension du gestionnaire de packages NuGet] disponible pour Visual Studio 2010 et 2012. Le code source est hébergé sur [GitHub][GitHub], qui comprend un Wiki doté d'une documentation complète sur l'utilisation des bibliothèques.

Par défaut, Microsoft Visual Studio 2010 installe la version 1.2 de NuGet. L'installation des bibliothèques Twilio requiert la version 1.6 de NuGet ou ultérieure. Pour plus d'informations sur l'installation ou la mise à jour de NuGet, consultez le site [][]<http://nuget.org/></a>.

<div class="dev-callout">
<b>Remarque</b>
<p>Pour installer la derni&egrave;re version de NuGet, vous devez au pr&eacute;alable d&eacute;sinstaller la version charg&eacute;e &agrave; l'aide du gestionnaire d'extensions de Visual Studio. Pour ce faire, vous devez ex&eacute;cuter Visual Studio en tant qu'administrateur. Sinon, le bouton D&eacute;sinstaller est d&eacute;sactiv&eacute;.</p>
</div>

### <span id="use_nuget"></span></a>Pour ajouter les bibliothèques Twilio à votre projet Visual Studio :

1.  Ouvrez votre solution dans Visual Studio.
2.  Cliquez avec le bouton droit sur **Références**.
3.  Cliquez sur **Gérer les packages NuGet...**
4.  Cliquez sur **En ligne**.
5.  Dans la zone de recherche en ligne, tapez *twilio*.
6.  Cliquez sur le bouton **Installer** du package Twilio.

## <span id="howto_make_call"></span></a> Appel téléphonique

Le code suivant montre comment passer un appel téléphonique à l'aide de la classe **TwilioRestClient**. Il utilise également un site Twilio afin de renvoyer la réponse TwiML (Twilio Markup Language). Remplacez vos valeurs pour les numéros de téléphone **From** (De) et **To** (À), puis assurez-vous de vérifier le numéro de téléphone **From** de votre compte Twilio avant d'exécuter le code.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    string accountSID = "your_twilio_account";
    string authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Instantiate the call options that are passed
    // to the outbound call
    CallOptions options = new CallOptions();

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    options.From = "+NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = Url;

    // Make the call.
    var call = client.InitiateOutboundCall(options);

Pour plus d'informations sur les paramètres transmis dans la méthode **client.InitiateOutboundCall**, consultez la page [][2]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Comme indiqué, ce code utilise un site Twilio afin de renvoyer la réponse TwiML, mais vous pouvez utiliser votre propre site. Pour plus d'informations, consultez la page [Envoi de réponses TwiML à partir de votre propre site Web][Envoi de réponses TwiML à partir de votre propre site Web].

## <span id="howto_send_sms"></span></a> Envoi d'un SMS

La capture d'écran suivante montre comment envoyer un SMS à l'aide de la classe **TwilioRestClient**. Le numéro **From** est fourni par Twilio pour permettre aux comptes d'évaluation d'envoyer des SMS. Le numéro **To** doit être vérifié pour votre compte Twilio avant d'exécuter le code.

        // Use your account SID and authentication token instead
        // of the placeholders shown here.
        string accountSID = "your_twilio_account";
        string authToken = "your_twilio_authentication_token";

        // Create an instance of the Twilio client.
        TwilioRestClient client;
        client = new TwilioRestClient(accountSID, authToken);

        // Send an SMS message.
        SMSMessage result = client.SendSmsMessage(
            "+14155992671", "+12069419717", "This is my SMS message.");

        if (result.RestException != null)
        {
            //an exception occurred making the REST call
            string message = result.RestException.Message;
        }

## <span id="howto_provide_twiml_responses"></span></a> Envoi de réponses TwiML à partir de votre propre site web

Lorsque votre application démarre un appel à l'API Twilio, par exemple via la méthode **client.InitiateOutboundCall**, Twilio envoie votre demande à une URL qui est censée renvoyer une réponse TwiML. L'exemple figurant dans la section [Appel téléphonique][Appel téléphonique] utilise l'URL Twilio [][3]<http://twimlets.com/message></a> pour renvoyer la réponse.

<div class="dev-callout">
<b>Remarque</b>
<p>TwiML est con&ccedil;u pour &ecirc;tre utilis&eacute; par des services Web, vous pouvez donc afficher TwiML dans votre navigateur. Par exemple, cliquez sur [http://twimlets.com/message](twimlet_message_url) pour afficher un &eacute;l&eacute;ment &lt;Response&gt;&nbsp;; autre exemple, cliquez sur [http://twimlets.com/message?Message%5B0%5D=Hello%20World](twimlet_message_url_hello_world) pour afficher un &eacute;l&eacute;ment &lt;Response&gt; contenant un &eacute;l&eacute;ment &lt;Say&gt;.</p>
</div>

Au lieu de compter sur l'URL Twilio, vous pouvez créer votre propre site d'URL qui renvoie des réponses HTTP. Vous pouvez créer le site dans tout langage qui renvoie des réponses HTTP. Cette rubrique part du principe que vous hébergerez l'URL à partir d'un gestionnaire générique ASP.NET.

Le gestionnaire ASP.NET suivant élabore une réponse TwiML qui dit **Hello World** lors de l'appel.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.ContentEncoding = System.Text.Encoding.UTF8;
                string twiMLResponse = "<Response><Say>Hello World.</Say></Response>";
                context.Response.Write(twiMLResponse);
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Comme vous pouvez le constater dans l'exemple ci-dessus, la réponse TwiML est simplement un document XML. La bibliothèque Twilio.TwiML contient des classes qui généreront TwiML pour vous. L'exemple ci-dessous produit la réponse équivalente comme illustré ci-dessus, mais utilise la classe TwilioResponse.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                var twiml = new Twilio.TwiML.TwilioResponse();
                twiml.Say("Hello World.");

                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.Write(twiml.ToString());
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Pour plus d'informations sur TwiML, consultez la page [][4]<https://www.twilio.com/docs/api/twiml></a>.

Une fois que vous avez défini une façon de fournir des réponses TwiML, vous pouvez transmettre cette URL à la méthode **client.InitiateOutboundCall**. Par exemple, si une application Web intitulée MyTwiML est déployée sur un service cloud Azure, et que le nom de votre gestionnaire ASP.NET est mytwiml.ashx, l'URL peut être transmise à **client.InitiateOutboundCall** comme illustré dans l'exemple de code suivant :

    // Place the call From, To, and URL values into a hash map.
    // This sample uses the sandbox number provided by Twilio to make the call.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Place the call.
    var call = client.InitiateOutboundCall(options);

Pour plus d'informations sur l'utilisation de Twilio sur Azure avec ASP.NET, consultez la page [Appel téléphonique à l'aide de Twilio dans un rôle Web sur Azure][Appel téléphonique à l'aide de Twilio dans un rôle Web sur Azure].

[WACOM.INCLUDE [twilio\_additional\_services\_and\_next\_steps][twilio\_additional\_services\_and\_next\_steps]]

  [Étapes suivantes]: #NextSteps
  [Présentation de Twilio]: #WhatIs
  [Tarification de Twilio]: #Pricing
  [Concepts]: #Concepts
  [Création d'un compte Twilio]: #CreateAccount
  [Vérification des numéros de téléphone]: #VerifyPhoneNumbers
  [Création d'une application Azure]: #create_app
  [Configuration de l'application pour utiliser les bibliothèques Twilio]: #configure_app
  [Appel téléphonique]: #howto_make_call
  [Envoi d'un SMS]: #howto_send_sms
  [Envoi de réponses TwiML à partir de votre propre site Web]: #howto_provide_twiml_responses
  [Utilisation des services Twilio supplémentaires]: #AdditionalServices
  [offre spéciale]: http://www.twilio.com/azure
  [ahoy.twilio.com/azure]: http://ahoy.twilio.com/azure
  [1]: http://www.twilio.com/voice/pricing
  [Bibliothèques de l'API Twilio]: https://www.twilio.com/docs/libraries
  [Documentation de Twilio Markup Language]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [Essayer Twilio]: https://www.twilio.com/try-twilio
  [page du compte Twilio]: https://www.twilio.com/user/account
  [Gestion des numéros]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Aide sur les numéros de téléphone Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [Création d'un projet Azure avec Visual Studio]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405487.aspx
  [installées à l'aide de l'extension du gestionnaire de packages NuGet]: http://www.twilio.com/docs/csharp/install
  [GitHub]: https://github.com/twilio/twilio-csharp
  []: http://nuget.org/
  [2]: http://www.twilio.com/docs/api/rest/making-calls
  [3]: http://twimlets.com/message
  [4]: https://www.twilio.com/docs/api/twiml
  [Appel téléphonique à l'aide de Twilio dans un rôle Web sur Azure]: ../partner-twilio-cloud-services-dotnet-phone-call-web-role/
  [twilio\_additional\_services\_and\_next\_steps]: ../includes/twilio_additional_services_and_next_steps.md
