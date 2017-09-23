---
title: "Utilisation de Twilio pour les fonctionnalités vocales et de SMS (.NET) | Microsoft Docs"
description: "Découvrez comment passer un appel téléphonique et envoyer un message texte avec le service d'API Twilio sur Azure. Exemples de code écrits en .NET."
services: 
documentationcenter: .net
author: devinrader
manager: twilio
editor: 
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: MicrosoftHelp@twilio.com
ms.translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 1442e3af26ae87e645cf207228ed1197b2afdd4d
ms.contentlocale: fr-fr
ms.lasthandoff: 04/07/2017

---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Utilisation de Twilio pour les fonctionnalités vocales et de SMS depuis Azure
Ce guide présente l'exécution de tâches de programmation courantes avec le service API Twilio sur Azure. Les scénarios abordés comprennent notamment les appels téléphoniques et l'envoi de SMS. Pour plus d'informations sur Twilio et sur l'utilisation des fonctionnalités vocales et de SMS de vos applications, consultez la section [Étapes suivantes](#NextSteps) .

## <a id="WhatIs"></a>Présentation de Twilio
Twilio alimente le futur des communications professionnelles, en permettant aux développeurs d'incorporer les fonctionnalités vocales, VoIP et de messagerie dans les applications. Ils virtualisent toute l'infrastructure nécessaire dans un environnement global dans le cloud, en l'exposant au moyen de la plateforme de l'API de communication Twilio. Les applications sont faciles à générer et sont évolutives. Tirez parti de la souplesse du paiement à l’utilisation et de la fiabilité du cloud.

**Twilio Voice** permet à vos applications de passer et de recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications d'envoyer et de recevoir des SMS. **Twilio Client** permet de passer des appels VoIP à partir d'un téléphone, d'une tablette ou d'un navigateur et prend en charge WebRTC.

## <a id="Pricing"></a>Tarification de Twilio et offres spéciales
Les clients Azure reçoivent une [offre spéciale](http://www.twilio.com/azure)de 10 $ en crédit Twilio lorsqu'ils mettent à niveau leur compte Twilio. Ce crédit peut servir à l'achat d'une prestation Twilio (un crédit de 10 $ correspond à l'envoi de 1 000 SMS ou à la réception de 1 000 minutes vocales maximum, en fonction de l'emplacement de votre numéro de téléphone, du message ou de la destination de l'appel). Profitez de ce crédit Twilio et démarrez en consultant la page [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio est un service de paiement à l'utilisation. Il n’y a pas de frais d’entrée et vous pouvez fermer votre compte quand vous le souhaitez. Pour plus d'informations, consultez la page [Tarification de Twilio](http://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Concepts
L'API Twilio est une API RESTful qui offre des fonctionnalités vocales et de SMS aux applications. Les bibliothèques clientes sont disponibles dans plusieurs langues : pour en obtenir la liste, consultez la page [Bibliothèques de l’API Twilio][twilio_libraries].

Les éléments les plus importants de l'API Twilio sont les verbes Twilio et TwiML (Twilio Markup Language).

### <a id="Verbs"></a>Verbes Twilio
L'API utilise les verbes Twilio ; par exemple, le verbe **&lt;Say&gt;** (Dire) indique à Twilio de transmettre un message de manière audible lors d'un appel.

La liste suivante présente les verbes Twilio.  Découvrez les autres verbes et fonctionnalités dans la page [Documentation de Twilio Markup Language](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;** : connecte l'appelant à un autre téléphone.
* **&lt;Gather&gt;** : rassemble les chiffres numériques entrés sur le clavier du téléphone.
* **&lt;Hangup&gt;** : met fin à un appel.
* **&lt;Play&gt;** : lit un fichier audio.
* **&lt;Pause&gt;** : patiente silencieusement pendant un nombre déterminé de secondes.
* **&lt;Record&gt;** : enregistre la voix de l’appelant et renvoie l’URL d’un fichier contenant l’enregistrement.
* **&lt;Redirect&gt;** : transfère le contrôle d'un appel ou d'un SMS au TwiML à une autre URL.
* **&lt;Reject&gt;** : refuse un appel entrant sur votre numéro Twilio sans vous facturer
* **&lt;Say&gt;** : convertit le texte d'un appel en parole.
* **&lt;Sms&gt;** : envoie un SMS.

### <a id="TwiML"></a>TwiML
TwiML est un jeu d'instructions XML reposant sur les verbes Twilio qui informent Twilio sur la façon de traiter un appel ou un SMS.

À titre d'exemple, le code TwiML suivant convertit le texte **Hello World** en parole.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Lorsque votre application appelle l'API Twilio, l'un des paramètres de l'API est l'URL qui renvoie la réponse TwiML. À des fins de développement, vous pouvez utiliser les URL Twilio pour fournir les réponses TwiML utilisées par vos applications. Vous pouvez également héberger vos propres URL afin de produire les réponses TwiML, ou encore utiliser l'objet **TwiMLResponse** .

Pour plus d’informations sur les verbes Twilio, leurs attributs et le langage TwiML, consultez la page [TwiML][twiml]. Pour plus d’informations sur l’API Twilio, consultez la page [API Twilio][twilio_api].

## <a id="CreateAccount"></a>Créer un compte Twilio
Lorsque vous êtes prêt à créer votre compte Twilio, inscrivez-vous sur la page [Essayer Twilio][try_twilio]. Vous pouvez commencer avec un compte gratuit, avant de le mettre à niveau ultérieurement.

Lorsque vous créez un compte Twilio, vous recevez un ID de compte et un jeton d'authentification. Les deux sont nécessaires pour passer des appels d'API Twilio. Pour éviter qu'une personne non autorisée n'accède à votre compte, conservez votre jeton d'authentification en lieu sûr. Vous pouvez consulter vos ID de compte et jeton d’authentification sur la [page du compte Twilio][twilio_account], dans les champs intitulés **ACCOUNT SID** et **AUTH TOKEN**, respectivement.

## <a id="create_app"></a>Créer une application Microsoft Azure
Une application Azure qui héberge une application Twilio n'est pas différente d'une autre application Azure. Vous ajoutez la bibliothèque .NET Twilio et configurez le rôle permettant de l’utiliser.
Pour plus d’informations sur la création d’un projet Azure initial, consultez la page [Création d’un projet Azure avec Visual Studio][vs_project].

## <a id="configure_app"></a>Configurer l’application pour utiliser les bibliothèques Twilio
Twilio offre un ensemble de bibliothèques d'aide .NET qui couvrent différents aspects de Twilio afin de fournir des moyens simples et faciles d'interaction avec l'API REST Twilio et le client Twilio en vue de générer des réponses TwiML.

Twilio offre cinq bibliothèques destinées aux développeurs .NET :
Bibliothèque|Description
---|---
Twilio.API|Bibliothèque Twilio principale qui englobe l'API REST Twilio dans une bibliothèque .NET conviviale. Cette bibliothèque est disponible pour .NET, Silverlight et Windows Phone 7.
Twilio.TwiML|Offre un moyen .NET convivial pour générer le balisage TwiML.
Twilio.MVC|Pour les développeurs utilisant ASP.NET MVC, cette bibliothèque inclut TwilioController, TwiML ActionResult et un attribut de validation de demande.
Twilio.WebMatrix|Pour les développeurs utilisant l'outil de développement WebMatrix gratuit de Microsoft, cette bibliothèque contient les aides à la syntaxe Razor pour différentes actions Twilio.
Twilio.Client.Capability|Contient le générateur de jetons de capacité à utiliser avec le Kit de développement logiciel (SDK) JavaScript du client Twilio.

Notez que toutes les bibliothèques nécessitent .NET 3.5, Silverlight 4 ou Windows Phone 7 ou version ultérieure.

Les exemples fournis dans ce guide utilisent la bibliothèque Twilio.API.

Les bibliothèques peuvent être [installées à l’aide de NuGet Package Manager Extension](http://www.twilio.com/docs/csharp/install) disponible pour Visual Studio 2010 à 2015.  Le code source est hébergé sur [GitHub][twilio_github_repo], qui comprend un Wiki doté d’une documentation complète sur l’utilisation des bibliothèques.

Par défaut, Microsoft Visual Studio 2010 installe la version 1.2 de NuGet. L'installation des bibliothèques Twilio requiert la version 1.6 de NuGet ou ultérieure. Pour plus d’informations sur l’installation ou la mise à jour de NuGet, consultez le site [http://nuget.org/][nuget].

> [!NOTE]
> Pour installer la dernière version de NuGet, vous devez au préalable désinstaller la version chargée à l’aide du gestionnaire d’extensions de Visual Studio. Pour ce faire, vous devez exécuter Visual Studio en tant qu'administrateur. Sinon, le bouton Désinstaller est désactivé.
>
>

### <a id="use_nuget"></a>Pour ajouter les bibliothèques Twilio à vos projets Visual Studio :
1. Ouvrez votre solution dans Visual Studio.
2. Cliquez avec le bouton droit sur **Références**.
3. Cliquez sur **Gérer les packages NuGet...**
4. Cliquez sur **En ligne**.
5. Dans la zone de recherche en ligne, tapez *twilio*.
6. Cliquez sur le bouton **Installer** du package Twilio.

## <a id="howto_make_call"></a>Procédure : passer un appel sortant
Le code suivant montre comment passer un appel téléphonique à l’aide de la classe **CallResource**. Il utilise également un site Twilio afin de renvoyer la réponse TwiML (Twilio Markup Language). Remplacez les numéros de téléphone **de destination** et **d’origine** par vos valeurs, puis vérifiez le numéro de téléphone **d’origine** de votre compte Twilio avant d’exécuter le code.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    const string accountSID = "your_twilio_account";
    const string authToken = "your_twilio_authentication_token";

    // Initialize the TwilioClient.
    TwilioClient.Init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    var url = "http://twimlets.com/message";
    url = $"{url}?Message%5B0%5D=Hello%20World";

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri(url));
        }

Pour plus d’informations sur les paramètres transmis dans la méthode **CallResource.Create**, consultez la page [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Comme indiqué, ce code utilise un site Twilio afin de renvoyer la réponse TwiML, À la place, vous pouvez utiliser votre propre site pour fournir la réponse TwiML. mais vous pouvez utiliser votre propre site. Pour plus d’informations, consultez [Envoi de réponses TwiML à partir de votre propre site web](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Procédure : envoi d'un message SMS
La capture d’écran suivante montre comment envoyer un SMS à l’aide de la classe **MessageResource**. Le numéro **From** est fourni par Twilio pour permettre aux comptes d’évaluation d’envoyer des SMS. Le numéro **To** doit être vérifié pour votre compte Twilio avant d’exécuter le code.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    const string accountSID = "your_twilio_account";
    const string authToken = "your_twilio_authentication_token";

    // Initialize the TwilioClient.
    TwilioClient.Init(accountSID, authToken);

    try
    {
        // Send an SMS message.
        var message = MessageResource.Create(
            to: new PhoneNumber("+12069419717"),
            from: new PhoneNumber("+14155992671"),
            body: "This is my SMS message.");
    }
    catch (TwilioException ex)
    {
        // An exception occurred making the REST call
        Console.WriteLine(ex.Message);
    }

## <a id="howto_provide_twiml_responses"></a>Procédure : envoi de réponses TwiML depuis votre propre site Web
Quand votre application démarre un appel vers l’API Twilio, par exemple via la méthode **CallResource.create**, Twilio envoie votre demande à une URL qui est censée renvoyer une réponse TwiML. L’exemple dans [Exécution d’un appel téléphonique](#howto_make_call) utilise l’URL fournie par Twilio [http://twimlets.com/message][twimlet_message_url] pour renvoyer la réponse.

> [!NOTE]
> TwiML est conçu pour être utilisé par des services Web, vous pouvez donc afficher TwiML dans votre navigateur. Par exemple, cliquez sur [http://twimlets.com/message][twimlet_message_url] pour afficher un élément &lt;Response&gt; vide ; autre exemple, cliquez sur [http://twimlets.com/message?Message%5B0%5D=Hello%20World](http://twimlets.com/message?Message%5B0%5D=Hello%20World) pour afficher un élément &lt;Response&gt; contenant un élément &lt;Say&gt;.
>
>

Au lieu de compter sur l'URL Twilio, vous pouvez créer votre propre site d'URL qui renvoie des réponses HTTP. Vous pouvez créer le site dans tout langage qui renvoie des réponses HTTP. Cette rubrique part du principe que vous hébergerez l'URL à partir d'un gestionnaire générique ASP.NET.

Le gestionnaire ASP.NET suivant élabore une réponse TwiML qui dit **Hello World** lors de l'appel.

    using System.Text;
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
                const string twiMLResponse =
                    "<Response><Say>Hello World.</Say></Response>";
                
                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.ContentEncoding = Encoding.UTF8;
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
    
Comme vous pouvez le constater dans l'exemple ci-dessus, la réponse TwiML est simplement un document XML. La bibliothèque Twilio.TwiML contient des classes qui généreront TwiML pour vous. L’exemple ci-dessous produit la réponse équivalente comme illustré ci-dessus, mais utilise la classe **VoiceResponse**.

    using System.Web;
    using Twilio.TwiML;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                var twiml = new VoiceResponse();
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

Pour plus d'informations sur TwiML, consultez la page [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Une fois que vous avez défini une façon de fournir des réponses TwiML, vous pouvez transmettre cette URL à la méthode **CallResource.Create**. Par exemple, si une application web intitulée MyTwiML est déployée sur un service cloud Azure et que le nom de votre gestionnaire ASP.NET est mytwiml.ashx, l’URL peut être transmise à **CallResource.Create** comme illustré dans l’exemple de code suivant :

    // This sample uses the sandbox number provided by Twilio to make the call.
    // Place the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
        }

Pour plus d’informations sur l’utilisation de Twilio sur Azure avec ASP.NET, consultez la page [Appel téléphonique à l’aide de Twilio dans un rôle Web sur Azure][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]:http://msdn.microsoft.com/library/windowsazure/ee405487.aspx
[nuget]:http://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

