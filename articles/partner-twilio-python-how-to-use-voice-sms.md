---
title: "Guide pratique pour utiliser Twilio avec les fonctionnalités vocales et SMS (Python) | Microsoft Docs"
description: "Découvrez comment passer un appel téléphonique et envoyer un SMS avec le service d'API Twilio sur Azure. Exemples de code écrits en Python."
services: 
documentationcenter: python
author: devinrader
manager: twilio
editor: 
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: MicrosoftHelp@twilio.com
ms.translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: f4a02bb7a7c46e7a0e3c75b870c522eae8294339
ms.contentlocale: fr-fr
ms.lasthandoff: 03/07/2017

---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Guide pratique pour utiliser Twilio avec les fonctionnalités vocales et SMS dans Python
Ce guide présente l'exécution de tâches de programmation courantes avec le service API Twilio sur Azure. Les scénarios abordés comprennent notamment les appels téléphoniques et l'envoi de SMS. Pour plus d'informations sur Twilio et sur l'utilisation des fonctionnalités vocales et de SMS de vos applications, consultez la section [Étapes suivantes](#NextSteps) .

## <a id="WhatIs"></a>Présentation de Twilio
Twilio alimente le futur des communications professionnelles, en permettant aux développeurs d'incorporer les fonctionnalités vocales, VoIP et de messagerie dans les applications. Ils virtualisent toute l'infrastructure nécessaire dans un environnement global dans le cloud, en l'exposant au moyen de la plateforme de l'API de communication Twilio. Les applications sont faciles à générer et sont évolutives. Tirez parti de la souplesse du paiement à l'utilisation et de la fiabilité du cloud.

**Twilio Voice** permet à vos applications de passer et de recevoir des appels téléphoniques.
**Twilio SMS** permet à vos applications d'envoyer et de recevoir des SMS.
**Twilio Client** permet de passer des appels VoIP à partir d'un téléphone, d'une tablette ou d'un navigateur et prend en charge WebRTC.

## <a id="Pricing"></a>Tarification de Twilio et offres spéciales
Les clients Azure reçoivent une [offre spéciale][special_offer] de 10 $ en crédit Twilio quand ils mettent à niveau leur compte Twilio. Ce crédit peut servir à l'achat d'une prestation Twilio (un crédit de 10 $ correspond à l'envoi de 1 000 SMS ou à la réception de 1 000 minutes vocales maximum, en fonction de l'emplacement de votre numéro de téléphone, du message ou de la destination de l'appel). Profitez de ce [crédit Twilio][special_offer] et lancez-vous.

Twilio est un service de paiement à l'utilisation. Il n'existe pas de frais d'entrée et vous pouvez fermer votre compte quand vous le souhaitez. Pour plus d’informations, consultez la page [Tarification de Twilio][twilio_pricing].

## <a id="Concepts"></a>Concepts
L'API Twilio est une API RESTful qui offre des fonctionnalités vocales et de SMS aux applications. Les bibliothèques clientes sont disponibles dans plusieurs langues : pour en obtenir la liste, consultez la page [Bibliothèques de l’API Twilio][twilio_libraries].

Les éléments les plus importants de l'API Twilio sont les verbes Twilio et TwiML (Twilio Markup Language).

### <a id="Verbs"></a>Verbes Twilio
L'API utilise les verbes Twilio ; par exemple, le verbe **&lt;Say&gt;** (Dire) indique à Twilio de transmettre un message de manière audible lors d'un appel.

La liste suivante présente les verbes Twilio. Découvrez les autres verbes et fonctionnalités dans la page [Documentation de Twilio Markup Language][twiml].

* **&lt;Dial&gt;** : connecte l'appelant à un autre téléphone.
* **&lt;Gather&gt;** : rassemble les chiffres numériques entrés sur le clavier du téléphone.
* **&lt;Hangup&gt;** : met fin à un appel.
* **&lt;Pause&gt;** : patiente silencieusement pendant un nombre déterminé de secondes.
* **&lt;Play&gt;** : lit un fichier audio.
* **&lt;Queue&gt;** : ajoute l’appelant à une file d’attente d’appelants.
* **&lt;Record&gt;** : enregistre la voix de l'appelant et renvoie une URL permettant d'accéder à un fichier contenant l'enregistrement.
* **&lt;Redirect&gt;** : transfère le contrôle d'un appel ou d'un SMS au TwiML à une autre URL.
* **&lt;Reject&gt;** : refuse un appel entrant sur votre numéro Twilio sans vous facturer.
* **&lt;Say&gt;** : convertit le texte d'un appel en parole.
* **&lt;Sms&gt;** : envoie un SMS.

### <a id="TwiML"></a>TwiML
TwiML est un jeu d'instructions XML reposant sur les verbes Twilio qui informent Twilio sur la façon de traiter un appel ou un SMS.

À titre d'exemple, le code TwiML suivant convertit le texte **Hello World** en parole.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Lorsque votre application appelle l'API Twilio, l'un des paramètres de l'API est l'URL qui renvoie la réponse TwiML. À des fins de développement, vous pouvez utiliser les URL Twilio pour fournir les réponses TwiML utilisées par vos applications. Vous pouvez également héberger vos propres URL afin de produire les réponses TwiML, ou encore utiliser l’objet `TwiMLResponse`.

Pour plus d’informations sur les verbes Twilio, leurs attributs et le langage TwiML, consultez la page [TwiML][twiml]. Pour plus d’informations sur l’API Twilio, consultez la page [API Twilio][twilio_api].

## <a id="CreateAccount"></a>Créer un compte Twilio
Quand vous êtes prêt à obtenir un compte Twilio, inscrivez-vous dans la page [Essayer Twilio][try_twilio]. Vous pouvez commencer avec un compte gratuit, avant de le mettre à niveau ultérieurement.

Quand vous vous inscrivez pour obtenir un compte Twilio, vous recevez un SID de compte et un jeton d’authentification. Les deux sont nécessaires pour passer des appels d'API Twilio. Pour éviter qu'une personne non autorisée n'accède à votre compte, conservez votre jeton d'authentification en lieu sûr. Vous pouvez consulter vos SID de compte et jeton d’authentification dans la [console Twilio][twilio_console], dans les champs intitulés **ACCOUNT SID** et **AUTH TOKEN**, respectivement.

## <a id="create_app"></a>Créer une application Python
Une application Python qui utilise le service Twilio et qui s’exécute dans Azure est identique aux autres applications Python qui utilisent le service Twilio. Bien que les services Twilio soient basés sur REST et puissent être appelés de différentes manières à partir de Python, cet article met l’accent sur l’utilisation des services Twilio avec la [bibliothèque Twilio pour Python de Github][twilio_python]. Pour plus d’informations sur l’utilisation de la bibliothèque Twilio pour Python, consultez la page [http://readthedocs.org/docs/twilio-python/en/latest/index.html][twilio_lib_docs].

Tout d’abord, [configurez une nouvelle machine virtuelle Linux Azure][azure_vm_setup] pour servir d’hôte à votre nouvelle application web Python. Une fois que la machine virtuelle est en cours d’exécution, vous devez exposer votre application sur un port public comme décrit ci-dessous.

### <a name="add-an-incoming-rule"></a>Ajouter une règle entrante
  1. Accédez à la page [Groupe de sécurité réseau][azure_nsg].
  2. Sélectionnez le groupe de sécurité réseau qui correspond à votre machine virtuelle.
  3. Ajoutez une **règle sortante** pour le **port 80**. Veillez à autoriser le trafic entrant à partir de n’importe quelle adresse.

### <a name="set-the-dns-name-label"></a>Définir l’étiquette du nom DNS
  1. Accédez à la page [Adresses IP publiques] [azure_ips].
  2. Sélectionnez l’adresse IP publique qui correspond à votre machine virtuelle.
  3. Définissez la valeur **Étiquette du nom DNS** dans la section **Configuration**. Dans le cadre de cet exemple, elle ressemble à *votre-étiquette-domaine*.centralus.cloudapp.azure.com

Une fois que vous êtes en mesure de vous connecter via SSH à la machine virtuelle, vous pouvez installer la structure web de votre choix (les deux plus connues dans Python étant [Flask](http://flask.pocoo.org/) et [Django](https://www.djangoproject.com)). Vous pouvez installer l’une des deux en exécutant simplement la commande `pip install`.

Gardez à l’esprit que nous avons configuré la machine virtuelle pour autoriser le trafic uniquement sur le port 80. Veillez à configurer l’application pour utiliser ce port.

## <a id="configure_app"></a>Configurer l’application pour utiliser les bibliothèques Twilio
Vous pouvez configurer votre application pour utiliser la bibliothèque Twilio pour Python de deux manières :

* Installez la bibliothèque Twilio pour Python sous forme de package Pip. Elle peut être installée via les commandes suivantes :
   
        $ pip install twilio

    OU

* Téléchargez la bibliothèque Twilio pour Python à partir de GitHub ([https://github.com/twilio/twilio-python][twilio_python]) et installez-la comme suit :

        $ python setup.py install

Une fois que vous avez installé la bibliothèque Twilio pour Python, vous pouvez ensuite `import` cette bibliothèque dans vos fichiers Python :

        import twilio

Pour plus d’informations, consultez [https://github.com/twilio/twilio-python/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Procédure : passer un appel sortant
Le code suivant permet d'exécuter un appel sortant. Il utilise également un site Twilio afin de renvoyer la réponse TwiML (Twilio Markup Language). Remplacez les numéros de téléphone **numéro_origine** et **numéro_destination** par vos valeurs, puis vérifiez le numéro de téléphone **numéro_origine** de votre compte Twilio avant d’exécuter le code.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "http://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Comme indiqué, ce code utilise un site Twilio afin de renvoyer la réponse TwiML, À la place, vous pouvez utiliser votre propre site pour fournir la réponse TwiML. Pour plus d'informations, consultez la rubrique [Envoi de réponses TwiML à partir de votre propre site Web](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Procédure : envoi d'un message SMS
La procédure suivante montre comment envoyer un SMS à l’aide de la classe `TwilioRestClient`. Le numéro **numéro_origine** est fourni par Twilio pour permettre aux comptes d’évaluation d’envoyer des SMS. Le numéro **numéro_destination** doit être vérifié pour votre compte Twilio avant d’exécuter le code.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a id="howto_provide_twiml_responses"></a>Procédure : envoi de réponses TwiML depuis votre propre site web
Lorsque votre application démarre un appel à l'API Twilio, Twilio envoie votre demande à une URL qui est censée renvoyer une réponse TwiML. L’exemple ci-dessus utilise l’URL fournie par Twilio [http://twimlets.com/message][twimlet_message_url]. Comme TwiML est conçu pour être utilisé par Twilio, vous pouvez l’afficher dans votre navigateur. Par exemple, cliquez sur [http://twimlets.com/message][twimlet_message_url] pour visualiser un élément `<Response>` vide. Autre exemple : cliquez sur [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] pour afficher un élément `<Response>` contenant un élément `<Say>`.

Au lieu de compter sur l'URL Twilio, vous pouvez créer votre propre site qui retourne des réponses HTTP. Vous pouvez créer le site dans n’importe quel langage retournant des réponses XML ; cette rubrique suppose que vous utilisez Python pour créer le TwiML.

Les exemples suivants génèrent en sortie une réponse TwiML prononçant **Hello World** lors de l’appel.

Avec Flask :

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Avec Django :

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Comme vous pouvez le constater dans l'exemple ci-dessus, la réponse TwiML est simplement un document XML. La bibliothèque Twilio pour Python contient des classes qui généreront TwiML pour vous. L’exemple ci-dessous produit la réponse équivalente à celle illustrée ci-dessus, mais utilise le module `twiml` dans la bibliothèque Twilio pour Python :

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Pour plus d’informations sur TwiML, consultez la page [https://www.twilio.com/docs/api/twiml][twiml_reference].

Une fois que votre application Python est configurée pour envoyer des réponses TwiML, utilisez son URL en tant qu’URL transmise à la méthode `client.calls.create`. Par exemple, si une application web intitulée **MyTwiML** est déployée sur un service hébergé Azure, vous pouvez utiliser son URL en tant que webhook, comme illustré dans l’exemple suivant :

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a id="AdditionalServices"></a>Utilisation de services Twilio supplémentaires
En plus des exemples présentés ici, Twilio offre des API web que vous pouvez utiliser pour tirer profit d'autres fonctionnalités de Twilio à partir de votre application Azure. Pour plus d’informations, consultez la [documentation de l’API Twilio][twilio_api].

## <a id="NextSteps"></a>Étapes suivantes
Maintenant que vous avez appris les bases du service Twilio, consultez ces liens pour en savoir plus :

* [Conseils de sécurité Twilio][twilio_security_guidelines]
* [Procédures et exemples de code Twilio][twilio_howtos]
* [Didacticiels de démarrage rapide Twilio][twilio_quickstarts]
* [Twilio sur GitHub][twilio_on_github]
* [Support Twilio][twilio_support]

[special_offer]: http://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-python/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart

