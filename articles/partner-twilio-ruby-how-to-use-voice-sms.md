<properties linkid="develop-ruby-how-to-twilio-sms-voice-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Ruby) - Azure" metaKeywords="Azure Ruby Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Ruby." metaCanonical="" services="" documentationCenter="Ruby" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans Ruby

Ce guide présente l'exécution de tâches de programmation courantes avec le service API Twilio sur Azure. Les scénarios abordés comprennent notamment les appels téléphoniques et l'envoi de SMS. Pour plus d'informations sur Twilio et sur l'utilisation des fonctionnalités vocales et de SMS de vos applications, consultez la section [Étapes suivantes][Étapes suivantes].

## Sommaire

-   [Présentation de Twilio][Présentation de Twilio]
-   [Tarification de Twilio][Tarification de Twilio]
-   [Concepts][Concepts]
-   [Création d'un compte Twilio][Création d'un compte Twilio]
-   [Création d'une application Ruby Sinatra][Création d'une application Ruby Sinatra]
-   [Configuration de l'application pour utiliser les bibliothèques Twilio][Configuration de l'application pour utiliser les bibliothèques Twilio]
-   [Appel téléphonique][Appel téléphonique]
-   [Réception d'un SMS][Réception d'un SMS]
-   [Services Twilio supplémentaires][Services Twilio supplémentaires]
-   [Étapes suivantes][Étapes suivantes]

## <span id="WhatIs"></span></a>Présentation de Twilio

Twilio est une API de service Web de téléphonie vous permettant d'utiliser vos aptitudes et langages Web existants pour générer des applications vocales et de SMS. Twilio est un service tiers, et non une fonctionnalité Azure ni un produit Microsoft.

**Twilio Voice** permet à vos applications de passer et de recevoir des appels téléphoniques. **Twilio SMS** permet à vos applications de créer et de recevoir des SMS. **Twilio Client** permet à vos applications d'activer les communications vocales au moyen de connexions Internet existantes, y compris des connexions mobiles.

## <span id="Pricing"></span></a>Tarification de Twilio et offres spéciales

Des informations sur la tarification de Twilio sont disponibles dans la page [Tarification de Twilio][1]. Les clients Azure reçoivent une [offre spéciale][offre spéciale] 1 000 SMS ou 1 000 minutes d'appel en entrée. Pour bénéficier de cette offre ou pour obtenir des informations supplémentaires, visitez la page [][offre spéciale]<http://ahoy.twilio.com/azure></a>.

## <span id="Concepts"></span></a>Concepts

L'API Twilio est une API RESTful qui offre des fonctionnalités vocales et de SMS aux applications. Les bibliothèques clientes sont disponibles dans plusieurs langues : pour en obtenir la liste, consultez la page [Bibliothèques de l'API Twilio][Bibliothèques de l'API Twilio].

### <span id="TwiML"></span></a>TwiML

TwiML est un jeu d'instructions XML qui informent Twilio sur la façon de traiter un appel ou un SMS.

À titre d'exemple, le code TwiML suivant convertit le texte **Hello World** en parole.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Tous les documents TwiML ont un élément racine `<Response>`. Vous pouvez donc utiliser des verbes Twilio pour définir le comportement de votre application.

### <span id="Verbs"></span></a>Verbes TwiML

Les verbes Twilio sont des balises XML qui indiquent à Twilio les **actions** à effectuer. Par exemple, le verbe **\<Say\>** indique à Twilio de délivrer un message audible lors d'un appel.

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

Pour plus d'informations sur les verbes Twilio, leurs attributs et le langage TwiML, consultez la page [TwiML][TwiML]. Pour plus d'informations sur l'API Twilio, consultez la page [API Twilio][API Twilio].

## <span id="CreateAccount"></span></a>Création d'un compte Twilio

Lorsque vous êtes prêt à créer votre compte Twilio, inscrivez-vous sur la page [Essayer Twilio][Essayer Twilio]. Vous pouvez commencer avec un compte gratuit, avant de le mettre à niveau ultérieurement.

Lors de la création d'un compte Twilio, vous obtenez un numéro de téléphone gratuit pour votre application. Vous recevez également un SID de compte et un jeton d'authentification. Les deux sont nécessaires pour passer des appels d'API Twilio. Pour éviter qu'une personne non autorisée n'accède à votre compte, conservez votre jeton d'authentification en lieu sûr. Votre SID de compte et votre jeton d'authentification sont consultables sur la [page du compte Twilio][page du compte Twilio], dans les champs **ACCOUNT SID** et **AUTH TOKEN**.

### <span id="VerifyPhoneNumbers"></span></a>Vérification des numéros de téléphone

Outre le numéro donné par Twilio, vous pouvez également vérifier les numéros que vous contrôlez (votre numéro de portable ou de téléphone fixe) pour les utiliser dans vos applications.

Pour plus d'informations sur la vérification d'un numéro de téléphone, consultez la page [Gestion des numéros][Gestion des numéros].

## <span id="create_app"></span></a>Création d'une application Ruby

Une application Ruby qui utilise le service Twilio et qui s'exécute dans Azure est identique aux autres applications Ruby qui utilisent le service Twilio. Bien que les services Twilio soient basés sur RESTful et puissent être appelés de différentes manières depuis Ruby, cet article met l'accent sur l'utilisation des services Twilio avec la [bibliothèque d'aide Twilio pour Ruby][bibliothèque d'aide Twilio pour Ruby].

Tout d'abord, [configurez une nouvelle machine virtuelle Linux Azure][configurez une nouvelle machine virtuelle Linux Azure] pour servir d'hôte à votre nouvelle application Web Ruby. Ignorez les étapes concernant la création de l'application Rails. Il vous suffit de configurer la machine virtuelle. Veillez à créer un point de terminaison avec un port externe défini sur 80 et un port interne défini sur 5000.

Dans les exemples ci-dessous, nous utilisons [Sinatra][Sinatra], une infrastructure Web très simple pour Ruby. Vous pouvez certainement utiliser la bibliothèque d'aide Twilio pour Ruby avec une autre infrastructure Web, dont Ruby on Rails.

Utilisez SSH dans votre nouvelle machine virtuelle et créez un répertoire pour votre nouvelle application. Dans ce répertoire, créez un fichier nommé Gemfile et copiez-y le code suivant :

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Exécutez `bundle install` dans la ligne de commande. Ceci permet d'installer les dépendances ci-dessus. Créez ensuite un fichier nommé `web.rb`. C'est là que va résider le code de votre application Web. Collez-y le code suivant :

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

À ce stade, vous devez pouvoir exécuter la commande `ruby web.rb -p 5000`. Ceci met en place un petit serveur Web sur le port 5000. Vous devez pouvoir accéder à cette application dans votre navigateur en vous rendant à l'URL définie pour la création de votre machine virtuelle Azure. Lorsque vous accédez à votre application Web dans le navigateur, vous êtes prêt à créer une application Twilio.

## <span id="configure_app"></span></a>Configuration de l'application pour utiliser Twilio

Vous pouvez configurer votre application web afin qu'elle utilise la bibliothèque Twilio en mettant à jour votre fichier `Gemfile` pour y inclure cette ligne :

    gem 'twilio-ruby'

Exécutez `bundle install` dans la ligne de commande. Ouvrez ensuite `web.rb` et ajoutez cette ligne au début du code :

    require 'twilio-ruby'

Vous pouvez maintenant utiliser la bibliothèque d'aide Twilio dans votre application Web.

## <span id="howto_make_call"></span></a> Appel téléphonique

Le code suivant permet d'effectuer un appel sortant. Les concepts principaux incluent l'utilisation de la bibliothèque d'aide Twilio pour Ruby afin de passer des appels d'API REST et d'assurer le rendu TwiML. Remplacez vos valeurs pour les numéros de téléphone **From** (De) et **To** (À), puis assurez-vous de vérifier le numéro de téléphone **From** de votre compte Twilio avant d'exécuter le code.

Ajoutez la fonction suivante à `web.md` :

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

Si vous ouvrez l'adresse `http://yourdomain.cloudapp.net/make_call` dans un navigateur, cela déclenche l'appel vers l'API Twilio qui, en retour, passera l'appel. Les deux premiers paramètres de `client.account.calls.create` sont clairs : le numéro d'où provient l'appel est `from` et le numéro de destination est `to`.

Le troisième paramètre (`url`) est l'URL demandée par Twilio pour obtenir les instructions à suivre une fois l'appel connecté. Dans notre exemple, nous avons défini une URL (`http://yourdomain.cloudapp.net`) qui renvoie un document TwiML simple utilisant le verbe `<Say>` pour convertir du texte en parole et dire « Hello Monkey » à la personne qui reçoit l'appel.

## <span id="howto_recieve_sms"></span></a> Réception d'un SMS

Dans l'exemple précédent, nous avons émis un appel **sortant**. Cette fois, nous allons utiliser le numéro de téléphone fourni par Twilio lors de l'inscription pour traiter un SMS **entrant**.

Commencez par vous connecter à votre [tableau de bord Twilio][page du compte Twilio]. Cliquez sur « Numbers » dans la zone de navigation supérieure, puis sur le numéro fourni par Twilio. Deux URL sont disponibles pour configuration : une URL de requête vocale et une URL de requête SMS. Il s'agit des URL que Twilio appelle lors de toute émission d'appel ou toute réception de SMS. On parle de « raccordements Web » pour désigner ces URL.

Pour traiter les SMS entrants, il faut mettre à jour l'URL avec l'adresse `http://yourdomain.cloudapp.net/sms_url`. Cliquez sur Save Changes en bas de la page pour poursuivre. Revenons maintenant à `web.rb` pour programmer notre application afin qu'elle effectue cette action :

    post '/sms_url' do
      "<Response>
         <Sms>Hey, thanks for the ping! Twilio and Azure rock!</Sms>
       </Response>"
    end

Veillez à redémarrer l'application Web après avoir effectué cette modification. Maintenant, à l'aide de votre téléphone, envoyez un SMS à votre numéro Twilio. Vous devriez rapidement recevoir une réponse par SMS avec le message « Hey, merci pour le ping ! Twilio et Azure sont les meilleurs ! »

## <span id="additional_services"></span></a> Utilisation des services Twilio supplémentaires

En plus des exemples présentés ici, Twilio offre des API Web que vous pouvez utiliser pour tirer profit d’autres fonctionnalités de Twilio à partir de votre application Azure. Pour plus d’informations, consultez la [documentation de l’API Twilio][API Twilio].

### <span id="NextSteps"></span></a>Étapes suivantes

Maintenant que vous avez appris les bases du service Twilio, consultez ces liens pour en savoir plus :

-   [Conseils de sécurité Twilio][Conseils de sécurité Twilio]
-   [Procédures et exemples de code Twilio][Procédures et exemples de code Twilio]
-   [Didacticiels de démarrage rapide Twilio][Didacticiels de démarrage rapide Twilio]
-   [Twilio sur GitHub][Twilio sur GitHub]
-   [Support Twilio][Support Twilio]

  [Étapes suivantes]: #NextSteps
  [Présentation de Twilio]: #WhatIs
  [Tarification de Twilio]: #Pricing
  [Concepts]: #Concepts
  [Création d'un compte Twilio]: #CreateAccount
  [Création d'une application Ruby Sinatra]: #create_app
  [Configuration de l'application pour utiliser les bibliothèques Twilio]: #configure_app
  [Appel téléphonique]: #howto_make_call
  [Réception d'un SMS]: #howto_recieve_sms
  [Services Twilio supplémentaires]: #additional_services
  [1]: http://www.twilio.com/pricing
  [offre spéciale]: http://ahoy.twilio.com/azure
  [Bibliothèques de l'API Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [Essayer Twilio]: https://www.twilio.com/try-twilio
  [page du compte Twilio]: https://www.twilio.com/user/account
  [Gestion des numéros]: https://www.twilio.com/user/account/phone-numbers/verified#
  [bibliothèque d'aide Twilio pour Ruby]: https://www.twilio.com/docs/ruby/install
  [configurez une nouvelle machine virtuelle Linux Azure]: http://www.windowsazure.com/fr-fr/develop/ruby/tutorials/web-app-with-linux-vm/
  [Sinatra]: http://www.sinatrarb.com/
  [Conseils de sécurité Twilio]: http://www.twilio.com/docs/security
  [Procédures et exemples de code Twilio]: http://www.twilio.com/docs/howto
  [Didacticiels de démarrage rapide Twilio]: http://www.twilio.com/docs/quickstart
  [Twilio sur GitHub]: https://github.com/twilio
  [Support Twilio]: http://www.twilio.com/help/contact
