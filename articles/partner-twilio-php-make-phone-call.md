<properties title="How to make a phone call from Twilio (PHP) - Azure" pageTitle="How to make a phone call from Twilio (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, PHP twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Samples are for PHP application." documentationCenter="PHP" services="" solutions="" videoId="" scriptId="" authors="MicrosoftHelp@twilio.com; robmcm" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm"></tags>

# Exécution d'un appel téléphonique à l'aide de Twilio dans une application PHP sur Azure

L'exemple qui suit montre comment utiliser Twilio pour passer un appel depuis une page Web PHP hébergée sur Azure. L'application finale demande à l'utilisateur les valeurs de l'appel téléphonique, comme illustré dans la capture d'écran qui suit.

![Formulaire d'appel Azure avec Twilio et PHP][]

Pour utiliser le code de cette rubrique, vous devrez effectuer les opérations suivantes :

1.  Obtenir un compte Twilio et un jeton d'authentification. Pour démarrer avec Twilio, évaluez les tarifs sur la page [][]<http://www.twilio.com/pricing></a>. Vous pouvez demander un compte d'évaluation sur la page [][1]<https://www.twilio.com/try-twilio></a>. Pour plus d'informations sur l'API fournie par Twilio, consultez la page [][2]<http://www.twilio.com/api></a>.
2.  Vérifier votre numéro de téléphone en tant qu'ID d'appelant sortant avec Twilio. Pour plus d'informations sur la vérification d'un numéro de téléphone, consultez la page [][3]<https://www.twilio.com/user/account/phone-numbers/verified#></a>. Au lieu d'utiliser un numéro existant, vous pouvez acheter un numéro de téléphone Twilio.
    Dans cet exemple, utilisez le numéro de téléphone vérifié comme valeur **From** de **callform.php** (décrit plus loin).
3.  Récupérer la bibliothèque Twilio pour PHP. Vous pouvez la télécharger depuis Github ([][4]<https://github.com/twilio/twilio-php></a>) ou l'installer sous forme de package PEAR. Pour plus d'informations, consultez la page [][5]<https://github.com/twilio/twilio-php/blob/master/README.md></a>.
4.  Installer le Kit de développement logiciel (SDK) Azure pour PHP. Pour une présentation du Kit de développement logiciel et des instructions sur son installation, consultez la page [Installation du SDK Azure pour PHP][].

## Création d'un formulaire Web pour passer un appel

Le code HTML qui suit présente la conception d'une page Web (**callform.html**) qui extrait les données des utilisateurs pour passer un appel :

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
    <p>Fill in all fields and click <b>Make this call</b>.</p>
    <form action="makecall.php" method="post">
    <table>
        <tr>
            <td>To:</td>
            <td><input type="text" size=50 name="callTo" value=""></td>
        </tr>
        <tr>
            <td>From:</td>
            <td><input type="text" size=50 name="callFrom" value=""></td>
        </tr>
        <tr>
            <td>Call message:</td>
            <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
        </tr>
        <tr>
            <td colspan=2><input type="submit" value="Make this call"></td>
        </tr>
    </table>
    </form>
    <br/>
    </body>
    </html>

## Création du code pour passer l'appel

Le code qui suit montre comment créer une page Web (**makecall.php**) qui est appelée lorsque l'utilisateur envoie le formulaire affiché par **callform.html**. Le code présenté ici crée le message d'appel et passe l'appel. Utilisez votre compte Twilio et votre jeton d'authentification plutôt que les valeurs par défaut utilisées dans **$sid** et **$token** dans le code qui suit.

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

En plus de passer l'appel, **makecall.php** affiche des métadonnées sur l'appel (comme dans l'exemple de capture d'écran ci-dessous). Pour plus d'informations sur les métadonnées de l'appel, consultez la page [][6]<https://www.twilio.com/docs/api/rest/call#instance-properties></a>.

![Réponse d'appel Azure avec Twilio et PHP][]

## Exécution de l'application

L'étape suivante consiste à déployer votre application sur Sites Web Azure. Les articles suivants contiennent les informations relatives à la création du site web et au déploiement de votre code avec Git, FTP ou WebMatrix (même si toutes les informations de ces articles ne sont pas pertinentes) :

-   [Création et déploiement d'un site Web Azure PHP-MySQL avec Git][]
-   [Création et déploiement d'un site Web Azure PHP-MySQL avec FTP][]
-   [Création et déploiement d'un site Web Azure PHP-MySQL avec WebMatrix][]

## Étapes suivantes

Ce code vous est fourni afin de vous présenter les fonctions de base de l'utilisation de Twilio dans PHP sur Azure. Avant d'effectuer le déploiement de production sur Azure, vous pouvez ajouter d'autres fonctionnalités telles que la gestion des erreurs. Par exemple :

-   Au lieu d'utiliser un formulaire Web, vous pouvez utiliser des objets blob de stockage Azure ou une base de données SQL pour stocker les numéros de téléphone et le texte des appels. Pour plus d'informations sur l'utilisation d'objets blob de stockage Azure dans PHP, consultez la page [Utilisation du stockage Azure dans les applications PHP][]. Pour plus d'informations sur l'utilisation de la base de données SQL dans PHP, consultez la page [Utilisation de la base de données SQL avec les applications PHP][].
-   Le code **makecall.php** utilise une URL Twilio ([][7]<http://twimlets.com/message></a>) pour fournir une réponse TwiML (Twilio Markup Language) qui précise à Twilio comment traiter l'appel. Par exemple, le code TwiML renvoyé peut contenir un verbe `<Say>` entraînant la lecture du texte au destinataire de l'appel. Au lieu d'utiliser l'URL fournie par Twilio, vous pouvez concevoir votre propre service pour répondre à la demande de Twilio. Pour plus d'informations, consultez la page [Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans PHP][]. Pour plus d'informations sur TwiML, consultez la page [][8]<http://www.twilio.com/docs/api/twiml></a> et pour plus d'informations sur `<Say>` et sur les autres verbes Twilio, consultez la page [][9]<http://www.twilio.com/docs/api/twiml/say></a>.
-   Consultez les instructions de sécurité Twilio sur la page [][10]<https://www.twilio.com/docs/security></a>.

Pour plus d'informations sur Twilio, consultez la page [][11][https://www.twilio.com/docs/][]</a>.

## Voir aussi

-   [Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans PHP][]

  [Formulaire d'appel Azure avec Twilio et PHP]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
  []: http://www.twilio.com/pricing
  [1]: http://www.twilio.com/try-twilio
  [2]: http://www.twilio.com/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [4]: https://github.com/twilio/twilio-php
  [5]: https://github.com/twilio/twilio-php/blob/master/README.md
  [Installation du SDK Azure pour PHP]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
  [6]: https://www.twilio.com/docs/api/rest/call#instance-properties
  [Réponse d'appel Azure avec Twilio et PHP]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
  [Création et déploiement d'un site Web Azure PHP-MySQL avec Git]: https://www.windowsazure.com/fr-fr/develop/php/tutorials/website-w-mysql-and-git/
  [Création et déploiement d'un site Web Azure PHP-MySQL avec FTP]: https://www.windowsazure.com/fr-fr/develop/php/tutorials/website-w-mysql-and-ftp/
  [Création et déploiement d'un site Web Azure PHP-MySQL avec WebMatrix]: https://www.windowsazure.com/fr-fr/develop/php/tutorials/website-w-mysql-and-webmatrix/
  [Utilisation du stockage Azure dans les applications PHP]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh674502(v=vs.103).aspx
  [Utilisation de la base de données SQL avec les applications PHP]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh674500(v=vs.103).aspx
  [7]: http://twimlets.com/message
  [Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans PHP]: ../partner-twilio-php-how-to-use-voice-sms
  [8]: http://www.twilio.com/docs/api/twiml
  [9]: http://www.twilio.com/docs/api/twiml/say
  [10]: http://www.twilio.com/docs/security
  [11]: http://www.twilio.com/docs
  [https://www.twilio.com/docs/]: https://www.twilio.com/docs
