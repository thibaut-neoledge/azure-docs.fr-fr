<properties linkid="develop-net-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (.NET) - Azure" metaKeywords="Azure Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to use Twilio for voice and SMS capabilities from Azure" authors="" solutions="" manager="" editor="" />

Utilisation de Twilio pour les fonctionnalités vocales et de SMS depuis Azure
=============================================================================

Ce guide présente l'exécution de tâches de programmation courantes avec le service API Twilio sur Azure. Les scénarios abordés comprennent notamment les appels téléphoniques et l'envoi de SMS. Pour plus d'informations sur Twilio et sur l'utilisation des fonctionnalités vocales et de SMS de vos applications, consultez la section [Étapes suivantes](#NextSteps).

Sommaire
--------

-   [Présentation de Twilio](#WhatIs)
-   [Tarification de Twilio](#Pricing)
-   [Concepts](#Concepts)
-   [Création d'un compte Twilio](#CreateAccount)
-   [Vérification des numéros de téléphone](#VerifyPhoneNumbers)
-   [Création d'une application Azure](#create_app)
-   [Configuration de l'application pour utiliser les bibliothèques Twilio](#configure_app)
-   [Appel téléphonique](#howto_make_call)
-   [Envoi d'un SMS](#howto_send_sms)
-   [Envoi de réponses TwiML à partir de votre propre site Web](#howto_provide_twiml_responses)
-   [Utilisation des services Twilio supplémentaires](#AdditionalServices)
-   [Étapes suivantes](#NextSteps)

Présentation de Twilio
----------------------

Twilio alimente le futur des communications professionnelles, en permettant aux développeurs d'incorporer les fonctionnalités vocales, VoIP et de messagerie dans les applications. Ils virtualisent toute l'infrastructure nécessaire dans un environnement global dans le cloud, en l'exposant au moyen de la plateforme de l'API de communication Twilio. Les applications sont faciles à générer et sont évolutives. Tirez parti de la souplesse du paiement à l'utilisation et de la fiabilité du cloud.

**Twilio Voice** permet à vos applications de passer et de recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications d'envoyer et de recevoir des SMS. **Twilio Client** permet de passer des appels VoIP à partir d'un téléphone, d'une tablette ou d'un navigateur et prend en charge WebRTC.

Tarification de Twilio et offres spéciales
------------------------------------------

Les clients Azure reçoivent une [offre spéciale](http://www.twilio.com/azure) sous la forme d'un crédit Twilio d'un montant de 10 \$ offert lorsqu'ils mettent à niveau leur compte Twilio. Ce crédit peut servir à l'achat d'une prestation Twilio (un crédit de 10 \$ correspond à l'envoi de 1 000 SMS ou à la réception de 1 000 minutes vocales maximum, en fonction de l'emplacement de votre numéro de téléphone, du message ou de la destination de l'appel). Profitez de ce crédit Twilio et démarrez en consultant la page [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio est un service de paiement à l'utilisation. Il n'existe pas de frais d'entrée et vous pouvez fermer votre compte quand vous le souhaitez. Pour plus d'informations, consultez la page [Tarification de Twilio](http://www.twilio.com/voice/pricing).

Concepts
--------

L'API Twilio est une API RESTful qui offre des fonctionnalités vocales et de SMS aux applications. Les bibliothèques clientes sont disponibles dans plusieurs langues : pour en obtenir la liste, consultez la page [Bibliothèques de l'API Twilio](https://www.twilio.com/docs/libraries).

Les éléments les plus importants de l'API Twilio sont les verbes Twilio et TwiML (Twilio Markup Language).

### Verbes Twilio

L'API utilise les verbes Twilio ; par exemple, le verbe **<Say>** (Dire) indique à Twilio de transmettre un message de manière audible lors d'un appel.

La liste suivante présente les verbes Twilio. Découvrez les autres verbes et fonctionnalités dans la page [Documentation de Twilio Markup Language](http://www.twilio.com/docs/api/twiml).

-   **<Dial>** (Composer) : connecte l'appelant à un autre téléphone.
-   **<Gather>** (Rassembler) : rassemble les chiffres numériques entrés sur le clavier du téléphone.
-   **<Hangup>** (Raccrocher) : met fin à un appel.
-   **<Play>** (Lire) : lit un fichier audio.
-   **<Pause>** : patiente silencieusement pendant un nombre déterminé de secondes.
-   **<Record>** (Enregistrer) : enregistre la voix de l'appelant et renvoie une URL permettant d'accéder à un fichier contenant l'enregistrement.
-   **<Redirect>** (Rediriger) : transfère le contrôle d'un appel ou d'un SMS au TwiML à une autre URL.
-   **<Reject>** (Rejeter) : refuse un appel entrant sur votre numéro Twilio sans vous facturer.
-   **<Say>** (Dire) : convertit le texte d'un appel en parole.
-   **<Sms>** : envoie un SMS.

### TwiML

TwiML est un jeu d'instructions XML reposant sur les verbes Twilio qui informent Twilio sur la façon de traiter un appel ou un SMS.

À titre d'exemple, le code TwiML suivant convertit le texte **Hello World** en parole.

    <
       xml version="1.0" encoding="UTF-8" 
       >
    <Response>
       <Say>Hello World</Say>
    </Response>

Lorsque votre application appelle l'API Twilio, l'un des paramètres de l'API est l'URL qui renvoie la réponse TwiML. À des fins de développement, vous pouvez utiliser les URL Twilio pour fournir les réponses TwiML utilisées par vos applications. Vous pouvez également héberger vos propres URL afin de produire les réponses TwiML, ou encore utiliser l'objet **TwiMLResponse**.

Pour plus d'informations sur les verbes Twilio, leurs attributs et le langage TwiML, consultez la page [TwiML](http://www.twilio.com/docs/api/twiml). Pour plus d'informations sur l'API Twilio, consultez la page [API Twilio](http://www.twilio.com/api).

Création d'un compte Twilio
---------------------------

Lorsque vous êtes prêt à créer votre compte Twilio, inscrivez-vous sur la page [Essayer Twilio](https://www.twilio.com/try-twilio). Vous pouvez commencer avec un compte gratuit, avant de le mettre à niveau ultérieurement.

Lorsque vous créez un compte Twilio, vous recevez un ID de compte et un jeton d'authentification. Les deux sont nécessaires pour passer des appels d'API Twilio. Pour éviter qu'une personne non autorisée n'accède à votre compte, conservez votre jeton d'authentification en lieu sûr. Vous pouvez consulter vos ID de compte et jeton d'authentification sur la [page du compte Twilio](https://www.twilio.com/user/account), dans les champs intitulés **ACCOUNT SID** et **AUTH TOKEN**, respectivement.

Vérification des numéros de téléphone
-------------------------------------

Plusieurs numéros de téléphone doivent être vérifiés avec Twilio pour votre compte. Par exemple, si vous souhaitez passer des appels téléphoniques, le numéro de téléphone doit être vérifié en tant qu'ID d'appelant sortant avec Twilio. De la même façon, si vous voulez un numéro de téléphone pour recevoir des SMS, le numéro de téléphone recevant l'appel doit être vérifié avec Twilio. Pour plus d'informations sur la vérification d'un numéro de téléphone, consultez la page [Gestion des numéros](https://www.twilio.com/user/account/phone-numbers/verified#). Une partie du code ci-dessous repose sur les numéros de téléphone que vous devez vérifier avec Twilio.

Si vous souhaitez utiliser un autre numéro de téléphone pour vos applications, vous pouvez acheter un numéro de téléphone Twilio. Pour plus d'informations sur l'achat d'un numéro de téléphone Twilio, consultez la page [Aide sur les numéros de téléphone Twilio](https://www.twilio.com/help/faq/phone-numbers).

Création d'une application Azure
--------------------------------

Une application Azure qui héberge une application Twilio n'est pas différente d'une autre application Azure. Vous ajoutez simplement la bibliothèque .NET Twilio et configurez le rôle permettant de l'utiliser. Pour plus d'informations sur la création d'un projet Azure initial, consultez la page [Création d'un projet Azure avec Visual Studio](http://msdn.microsoft.com/en-us/library/windowsazure/ee405487.aspx).

Configuration de l'application pour utiliser les bibliothèques Twilio
---------------------------------------------------------------------

Twilio offre un ensemble de bibliothèques d'aide .NET qui couvrent différents aspects de Twilio afin de fournir des moyens simples et faciles d'interaction avec l'API REST Twilio et le client Twilio en vue de générer des réponses TwiML.

Twilio offre cinq bibliothèques destinées aux développeurs .NET :

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Bibliothèque
Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Twilio.API
Bibliothèque Twilio principale qui englobe l'API REST Twilio dans une bibliothèque .NET conviviale. Cette bibliothèque est disponible pour .NET, Silverlight et Windows Phone 7.</td>
<td align="left">Twilio.TwiML
Offre un moyen .NET convivial pour générer le balisage TwiML.</td>
</tr>
</tbody>
</table>

Notez que toutes les bibliothèques nécessitent .NET 3.5, Silverlight 4 ou Windows Phone 7 ou version ultérieure.

Les exemples fournis dans ce guide utilisent la bibliothèque Twilio.API.

Les bibliothèques peuvent être [installées à l'aide de l'extension du gestionnaire de packages NuGet](http://www.twilio.com/docs/csharp/install) disponible pour Visual Studio 2010 et 2012. Le code source est hébergé sur [GitHub](https://github.com/twilio/twilio-csharp), qui comprend un Wiki doté d'une documentation complète sur l'utilisation des bibliothèques.

Par défaut, Microsoft Visual Studio 2010 installe la version 1.2 de NuGet. L'installation des bibliothèques Twilio requiert la version 1.6 de NuGet ou ultérieure. Pour plus d'informations sur l'installation ou la mise à jour de NuGet, consultez le site <http://nuget.org/>.

**Remarque**

Pour installer la dernière version de NuGet, vous devez au préalable désinstaller la version chargée à l'aide du gestionnaire d'extensions de Visual Studio. Pour ce faire, vous devez exécuter Visual Studio en tant qu'administrateur. Sinon, le bouton Désinstaller est désactivé.

### Pour ajouter les bibliothèques Twilio à votre projet Visual Studio :

1.  Ouvrez votre solution dans Visual Studio.
2.  Cliquez avec le bouton droit sur **Références**.
3.  Cliquez sur **Gérer les packages NuGet...**
4.  Cliquez sur **En ligne**.
5.  Dans la zone de recherche en ligne, tapez *twilio*.
6.  Cliquez sur le bouton **Installer** du package Twilio.

Appel téléphonique
------------------

Le code suivant montre comment passer un appel téléphonique à l'aide de la classe **TwilioRestClient**. Il utilise également un site Twilio afin de renvoyer la réponse TwiML (Twilio Markup Language). Remplacez vos valeurs pour les numéros de téléphone **From** (De) et **To** (À), puis assurez-vous de vérifier le numéro de téléphone **From** de votre compte Twilio avant d'exécuter le code.

    // Utilisez vos SID de compte et jeton d'authentification à la place
    // des espaces réservés qui sont présentés ici.
    string accountSID = "your_twilio_account";
    string authToken = "your_twilio_authentication_token";

    // Créez une instance du client Twilio.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Utilisez le site Twilio pour la réponse TwiML.
    String Url="http://twimlets.com/message";
    Url = Url + "
    Message%5B0%5D=Hello%20World";

    // Instanciez les options de l'appel qui sont transmises
    // à l'appel sortant
    CallOptions options = new CallOptions();

    // Définissez l'appel From, To et les valeurs d'URL à utiliser pour l'appel.
    // Cet exemple utilise le numéro de bac à sable (sandbox) fourni par
    // Twilio pour passer l'appel.
    options.From = "+NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = Url;

    // Passez l'appel.
    var call = client.InitiateOutboundCall(options);

Pour plus d'informations sur les paramètres transmis dans la méthode **client.InitiateOutboundCall**, consultez la page <http://www.twilio.com/docs/api/rest/making-calls>.

Comme indiqué, ce code utilise un site Twilio afin de renvoyer la réponse TwiML, mais vous pouvez utiliser votre propre site. Pour plus d'informations, consultez la section [Envoi de réponses TwiML à partir de votre propre site Web](#howto_provide_twiml_responses).

Envoi d'un SMS
--------------

La capture d'écran suivante montre comment envoyer un SMS à l'aide de la classe **TwilioRestClient**. Le numéro **From** est fourni par Twilio pour permettre aux comptes d'évaluation d'envoyer des SMS. Le numéro **To** doit être vérifié pour votre compte Twilio avant d'exécuter le code.

        // Utilisez vos SID de compte et jeton d'authentification à la place
        // des espaces réservés qui sont présentés ici.
        string accountSID = "your_twilio_account";
        string authToken = "your_twilio_authentication_token";

        // Créez une instance du client Twilio.
        TwilioRestClient client;
        client = new TwilioRestClient(accountSID, authToken);

        // Envoyez un SMS.
        SMSMessage result = client.SendSmsMessage(
            "+14155992671", "+12069419717", "This is my SMS message.");

        if (result.RestException != null)
        {
            //Une exception s'est produite lors de l'appel REST
            string message = result.RestException.Message;
        }

Envoi de réponses TwiML à partir de votre propre site Web
---------------------------------------------------------

Lorsque votre application démarre un appel à l'API Twilio, par exemple via la méthode **client.InitiateOutboundCall**, Twilio envoie votre demande à une URL qui est censée renvoyer une réponse TwiML. L'exemple figurant dans la section [Appel téléphonique](#howto_make_call) utilise l'URL Twilio <http://twimlets.com/message> pour renvoyer la réponse.

**Remarque**

TwiML est conçu pour être utilisé par des services Web, vous pouvez donc afficher TwiML dans votre navigateur. Par exemple, cliquez sur [http://twimlets.com/message](twimlet\_message\_url) pour afficher un élément <Response> ; autre exemple, cliquez sur [http://twimlets.com/message?Message%5B0%5D=Hello%20World](twimlet\_message\_url\_hello\_world) pour afficher un élément <Response> contenant un élément <Say>.

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

Pour plus d'informations sur TwiML, consultez la page <https://www.twilio.com/docs/api/twiml>.

Une fois que vous avez défini une façon de fournir des réponses TwiML, vous pouvez transmettre cette URL à la méthode **client.InitiateOutboundCall**. Par exemple, si une application Web intitulée MyTwiML est déployée sur un service cloud Azure, et que le nom de votre gestionnaire ASP.NET est mytwiml.ashx, l'URL peut être transmise à **client.InitiateOutboundCall** comme illustré dans l'exemple de code suivant :

    // Placez l'appel From, To et les valeurs d'URL dans une configuration de hachage.
    // Cet exemple utilise le numéro de bac à sable (sandbox) fourni par Twilio pour passer l'appel.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Passez l'appel.
    var call = client.InitiateOutboundCall(options);

Pour plus d'informations sur l'utilisation de Twilio sur Azure avec ASP.NET, consultez la page [Appel téléphonique à l'aide de Twilio dans un rôle Web sur Azure](../partner-twilio-cloud-services-dotnet-phone-call-web-role/).

[WACOM.INCLUDE [twilio\_additional\_services\_and\_next\_steps](../includes/twilio_additional_services_and_next_steps.md)]

