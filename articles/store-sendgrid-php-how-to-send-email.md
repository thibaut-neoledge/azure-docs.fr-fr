<properties title="How to use the SendGrid email service (PHP) - Azure" pageTitle="Utilisation du service de messagerie SendGrid (PHP) - Azure" metaKeywords="Azure SendGrid, service de messagerie électronique Azure, Azure SendGrid PHP, Azure messagerie électronique PHP" description="Découvrez comment envoyer un courrier électronique avec le service de messagerie SendGrid dans Azure. Exemples de code écrits en PHP." documentationCenter="PHP" services="" manager="wpickett" editor="mollybos" authors="robmcm" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" />

# Utilisation du service de messagerie électronique SendGrid à partir de PHP

Ce guide présente l'exécution de tâches de programmation courantes avec le service de messagerie SendGrid dans Azure. Les exemples sont écrits en PHP.
Les scénarios traités incluent la **construction**, l'**envoi de courriers électroniques** et l'**ajout de pièces jointes**. Pour plus d'informations sur SendGrid et l'envoi de courriers électroniques, consultez la section [Étapes suivantes][].

## Sommaire

-   [Présentation du service de messagerie électronique SendGrid][]
-   [Création d'un compte SendGrid][]
-   [Utilisation de SendGrid à partir de votre application PHP][]
-   [Procédure : Envoi d'un courrier électronique][]
-   [Procédure : Ajout d'une pièce jointe][]
-   [Procédure : Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse][]
-   [Étapes suivantes][]

## <a name="bkmk_WhatIsSendGrid"> </a>Présentation du service de messagerie électronique SendGrid

SendGrid est un [service de messagerie électronique dans le cloud] qui fournit des fonctionnalités fiables en matière de [remise de courrier électronique transactionnelle], d'extensibilité et d'analyse en temps réel, ainsi que des API flexibles qui facilitent l'intégration personnalisée. Voici quelques scénarios courants en termes d'utilisation de SendGrid :

-   Envoi automatique d'accusés de réception aux clients
-   Administration de listes de distribution pour un envoi mensuel de prospectus électroniques et d'offres spéciales aux clients
-   Collecte de mesures en temps réel concernant des éléments tels que les messages électroniques bloqués et la réactivité vis-à-vis des clients
-   Génération de rapports pour identifier les tendances
-   Transfert des demandes des clients
- Notifications par courriers électroniques depuis votre application

Pour plus d'informations, consultez la page [https://sendgrid.com][].

## <a name="bkmk_CreateSendGrid"> </a>Création d'un compte SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_UsingSendGridfromPHP"> </a>Utilisation de SendGrid à partir de votre application PHP

L'utilisation de SendGrid dans une application Azure PHP ne nécessite ni configuration spéciale,
ni codage particulier. SendGrid étant un service, il est
accessible exactement de la même façon à partir d'une application cloud et à partir d'une
application locale.

## <a name="bkmk_HowToSendEmail"> </a>Procédure : Envoi d'un courrier électronique

Vous pouvez envoyer un courrier électronique à l'aide de l'API SMTP ou de l'API web fournie
SendGrid.

### API SMTP

Pour envoyer un courrier électronique à l'aide de l'API SMTP SendGrid, utilisez Swift Mailer, une bibliothèque à base de composants destinée à l'envoi de courriers à partir d'applications PHP. Vous pouvez télécharger la bibliothèque *Swift Mailer* à partir de [http://swiftmailer.org/download][] v5.3.0 (utilisez [Composer] pour installer Swift Mailer). L'envoi de courriers électroniques avec la bibliothèque implique la création d'instances des classes <span class="auto-style2">Swift_SmtpTransport</span>, <span class="auto-style2">Swift_Mailer</span>et <span class="auto-style2">Swift_Message</span> , la définition des propriétés appropriées et l'appel de la méthode <span class="auto-style2">Swift_Mailer::send</span> .

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### API Web

Utilisez la [fonction curl][] de PHP pour envoyer un courrier électronique à l'aide de l'API web ddeSendGrid.

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

L'API web de SendGrid présente de nombreuses similitudes avec l'API REST. Cependant, il ne s'agit pas à proprement parler d'une API RESTful, dans la mesure où les verbes GET et POST peuvent être utilisés de manière interchangeable dans la plupart des appels.

## <a name="bkmk_HowToAddAttachment"> </a>Procédure : Ajout d'une pièce jointe

### API SMTP

L'envoi d'une pièce jointe à l'aide de l'API SMTP comprend une seule ligne de code supplémentaire par rapport à l'exemple de script destiné à l'envoi d'un courrier électronique avec Swift Mailer.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - ";
          print_r($failures);
     }

Cette ligne de code supplémentaire est la suivante :

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Cette ligne de code appelle la méthode attach sur l'objet <span class="auto-style2">Swift_Message</span> et utilise la méthode statique <span class="auto-style2">fromPath</span> sur la classe <span class="auto-style2">Swift_Attachment</span> pour obtenir et joindre un fichier à un message.

### API Web

La procédure d'envoi d'une pièce jointe à l'aide de l'API web est pratiquement identique à celle utilisée pour envoyer un courrier à l'aide de l'API web. Notez toutefois que dans l'exemple suivant, le tableau de paramètres doit contenir cet élément :

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Exemple :

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@contoso.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="bkmk_HowToUseFilters"> </a>Utilisation Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse

SendGrid offre d'autres fonctionnalités de messagerie grâce à l'utilisation de 'filtres'. Il s'agit de paramètres que vous pouvez ajouter à un message électronique pour activer des fonctionnalités spécifiques telles que le suivi des clics, Google Analytics, le suivi d'abonnement, etc.

Il est possible d'appliquer des filtres à un message par l'intermédiaire de la propriété " filters ". Chaque filtre est spécifié par un hachage contenant des paramètres propres au filtre. L'exemple suivant active le filtre de pied de page et indique un message texte qui sera ajouté au bas du message électronique. Pour cet exemple, nous utiliserons la bibliothèque [sendgrid-php]. Utilisez [Composer] pour installer la bibliothèque :
    
    php composer.phar require sendgrid/sendgrid 2.1.1

Exemple :    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have 
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from 
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Créez le corps du message (une version en texte brut et une version HTML). 
     # text correspond à votre courrier électronique en texte brut 
     # html correspond à votre version HTML du courrier électronique
     # Si le destinataire peut afficher les messages électroniques HTML, eux seuls
     # seront affichés.

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="bkmk_NextSteps"> </a>Étapes suivantes

Maintenant que vous avez appris les notions de base du service de messagerie SendGrid, consultez
ces liens pour en savoir plus.

-   Documentation de SendGrid : <https://sendgrid.com/docs>
-   Bibliothèque PHP SendGrid : <https://github.com/sendgrid/sendgrid-php>
-   Offres spéciales SendGrid pour les clients Azure : <https://sendgrid.com/windowsazure.html>

  [Étapes suivantes]: #bkmk_NextSteps
  [Présentation du service de messagerie électronique SendGrid]: #bkmk_WhatIsSendGrid
  [Création d'un compte SendGrid]: #bkmk_CreateSendGrid
  [Utilisation de SendGrid à partir de votre application PHP]: #bkmk_UsingSendGridfromPHP
  [Procédure : Envoi d'un courrier électronique]: #bkmk_HowToSendEmail
  [Procédure : Ajout d'une pièce jointe]: #bkmk_HowToAddAttachment
  [Procédure : Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse]: #bkmk_HowToUseFilters
  [ Utilisation de services SendGrid supplémentaires]: #bkmk_HowToUseAdditionalSvcs
  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [offre spéciale]: https://www.sendgrid.com/windowsazure.html
  [Empaquetage et déploiement d'applications PHP pour Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [fonction curl]: http://php.net/curl
  [service de messagerie électronique dans le cloud]: https://sendgrid.com/email-solutions
  [Remise de courrier électronique transactionnelle]: https://sendgrid.com/transactional-email
  [Bibliothèque sendgrid-php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
  [Composer]: https://getcomposer.org/download/

<!--HONumber=35.2-->
