<properties title="How to use the SendGrid email service (PHP) - Azure" pageTitle="Utilisation du service de messagerie électronique SendGrid (PHP) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid PHP, Azure email PHP" description="Découvrez comment envoyer un courrier électronique avec le service de messagerie SendGrid dans Azure. Exemples de code écrits en PHP." documentationCenter="PHP" services="" manager="wpickett" editor="mollybos" authors="robmcm" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" />

# Utilisation du service de messagerie électronique SendGrid à partir de PHP

Ce guide présente l'exécution de tâches de programmation courantes avec le service de messagerie SendGrid dans Azure. Les exemples sont écrits en PHP.
Les scénarios abordés sont les suivants **construction de courriers électroniques**, **envoi de courriers électroniques**, et **ajout de pièces jointes**. Pour plus d'informations sur SendGrid et l'envoi de courriers électroniques, consultez la section [Étapes suivantes][].

## Sommaire

-   [Définition du service de messagerie électronique SendGrid][]
-   [Création d'un compte SendGrid][]
-   [Utilisation de SendGrid à partir de votre application PHP][]
-   [ Envoi d'un courrier électronique][]
-   [ Ajout d'une pièce jointe][]
-   [ Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse][]
-   [Étapes suivantes][]

## <a name="bkmk_WhatIsSendGrid"> </a>Définition du service de messagerie SendGrid

SendGrid est un [service de messagerie électronique basé sur le cloud] qui permet l'envoi fiable
[de courriers électroniques transactionnels], l'évolutivité et l'analyse en temps réel, ainsi que des API flexibles
qui facilitent l'intégration personnalisée. Voici quelques scénarios courants en termes d'utilisation
de SendGrid :

-   Envoi automatique d'accusés de réception aux clients
-   Gestion des listes de distribution pour l'envoi mensuel d'offres
    spéciales et de brochures numériques aux clients
-   Collecte de mesures en temps réel concernant des éléments tels que les messages électroniques bloqués et
    la réactivité vis-à-vis des clients
-   Génération de rapports pour identifier les tendances
-   Transfert des demandes des clients
- Notifications par courriers électroniques depuis votre application

Pour plus d'informations, consultez la page [https://sendgrid.com][].

## <a name="bkmk_CreateSendGrid"> </a>Création d'un compte SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_UsingSendGridfromPHP"> </a>Utilisation de SendGrid à partir de votre application PHP

L'utilisation de SendGrid dans une application Azure PHP ne nécessite
ni configuration spéciale, ni codage particulier. SendGrid étant un service, l'accès s'effectue exactement
de la même manière qu'il s'agisse d'une application cloud ou
d'une application locale.

## <a name="bkmk_HowToSendEmail"> </a> Envoi d'un courrier électronique

Vous pouvez envoyer un courrier électronique à l'aide de l'API SMTP ou de l'API web fournie par
SendGrid.

### API SMTP

Pour envoyer un courrier électronique à l'aide de l'API SMTP SendGrid, utilisez *Swift Mailer*, une
bibliothèque à base de composants destinée à l'envoi de courriers à partir d'applications PHP. Vous
pouvez télécharger la bibliothèque Swift Mailer à
l'adresse suivante : [http://swiftmailer.org/download][]. L'envoi d'un courrier électronique avec la bibliothèque
suppose la création d'instances des
classes <span class="auto-style2">Swift\_SmtpTransport</span>,
<span class="auto-style2">Swift\_Mailer</span>, et
<span class="auto-style2">Swift\_Message</span>, la définition des
propriétés appropriées, et l'appel de
la méthode <span class="auto-style2">Swift\_Mailer::send</span>.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = <<<EOM
           <html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>
           EOM;
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

Utilisez la [fonction curl][] de PHP pour envoyer un courrier électronique à l'aide de l'API web de SendGrid.

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

L'API web de SendGrid présente de nombreuses similitudes avec l'API REST.
 Cependant, il ne s'agit pas, à proprement parler, d'une API RESTful, dans la mesure où les verbes GET et POST
peuvent être utilisés de manière interchangeable.

## <a name="bkmk_HowToAddAttachment"> </a> Ajout d'une pièce jointe

### API SMTP

L'envoi d'une pièce jointe à l'aide de l'API SMTP comprend une seule ligne de
code supplémentaire par rapport à l'exemple de script destiné à l'envoi d'un courrier électronique avec Swift Mailer.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = <<<EOM
          <html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>
     EOM;

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
          echo "Something went wrong - "
          print_r($failures);
     }

Cette ligne de code supplémentaire est la suivante :

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Cette ligne de code appelle la méthode d'attachement sur
l'objet <span class="auto-style2">Swift\_Message</span> et utilise la méthode statique
<span class="auto-style2">fromPath</span> sur la
classe <span class="auto-style2">Swift\_Attachment</span> pour obtenir et
joindre un fichier à un message.

### API Web

La procédure d'envoi d'une pièce jointe à l'aide de l'API web est pratiquement identique à
celle utilisée pour envoyer un courrier à l'aide de l'API web. Notez toutefois que dans l'exemple suivant,
le tableau de paramètres doit contenir cet élément :

     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

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

## <a name="bkmk_HowToUseFilters"> </a> Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse

SendGrid offre d'autres fonctionnalités de messagerie grâce à l'utilisation de
" filtres ". Il s'agit de paramètres que vous pouvez ajouter à un message électronique pour
activer des fonctionnalités spécifiques telles que le suivi des clics, Google
Analytics, le suivi d'abonnement, etc.

Il est possible d'appliquer des filtres à un message par l'intermédiaire de la propriété filters. Chaque
filtre est spécifié par un hachage contenant des paramètres propres au filtre. 
L'exemple suivant active le filtre " footer " et indique un message texte
qui sera ajouté au bas du message électronique :

    <?php
     /*
      * This example is used for Swift Mailer V4
      */
     include "./lib/swift_required.php";
     include 'SmtpApiHeader.php';
     
     $hdr = new SmtpApiHeader();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to 
     SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');
     
     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');
     
     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');
     
     // Set all of the above variables
     $hdr->addTo($toList);
     $hdr->addSubVal('-name-', $nameList);
     $hdr->addSubVal('-time-', $timeList);
     
     // Specify that this is an initial contact message
     $hdr->setCategory("initial");
     
     // You can optionally setup individual filters here, in this example, we have 
     enabled the footer filter
     $hdr->addFilterSetting('footer', 'enable', 1);
     $hdr->addFilterSetting('footer', "text/plain", "Thank you for your business");
     
     // The subject of your email
     $subject = 'Example SendGrid Email';
     
     // Where is this message coming from. For example, this message can be from support@yourcompany.com, info@yourcompany.com
     $from = array('someone@example.com' => 'Name Of Your Company');
     
     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = array('john@contoso.com'=>'Personal Name Of Recipient');
     
     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed
     
     /*
     * Note the variable substitution here =)
     */
     $text = <<<EOM 
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred
     EOM;
     
     $html = <<<EOM
     < html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.
     
     Regards,
     
     Fred, How are you?<br>
     </p>
     </body>
     < /html>
     EOM;
     
     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';
     
     // Create new swift connection and authenticate
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 25);
     $transport ->setUsername($username);
     $transport ->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // add SMTPAPI header to the message
     // *****IMPORTANT NOTE*****
     // SendGrid's asJSON function escapes characters. If you are using Swift Mailer's
     // PHP Mailer functions, the getTextHeader function will also escape characters.
     // This can cause the filter to be dropped.
     $headers = $message->getHeaders();
     $headers->addTextHeader('X-SMTPAPI', $hdr->asJSON());
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message
     if ($recipients = $swift->send($message, $failures))
     {
     // This will let us know how many users received this message
     // If we specify the names in the X-SMTPAPI header, then this will always be 1.
     echo 'Message sent out to '.$recipients.' users';
     }

     // something went wrong =(
     else
     {
     echo "Something went wrong - ";
     print_r($failures);
     }

## <a name="bkmk_NextSteps"> </a>Étapes suivantes

Maintenant que vous avez appris les bases du service de messagerie électronique SendGrid, consultez
ces liens pour en savoir plus.

-   Documentation de SendGrid : <https://sendgrid.com/docs>
-   Offres spéciales SendGrid pour les clients Azure : <https://sendgrid.com/windowsazure.html>

  [Étapes suivantes]: #bkmk_NextSteps
  [Définition du service de messagerie électronique SendGrid]: #bkmk_WhatIsSendGrid
  [Création d'un compte SendGrid]: #bkmk_CreateSendGrid
  [Utilisation de SendGrid à partir de votre application PHP]: #bkmk_UsingSendGridfromPHP
  [ Envoi d'un courrier électronique]: #bkmk_HowToSendEmail
  [ Ajout d'une pièce jointe]: #bkmk_HowToAddAttachment
  [ Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse]: #bkmk_HowToUseFilters
  [ Utilisation de services SendGrid supplémentaires]: #bkmk_HowToUseAdditionalSvcs
  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [Offre spéciale]: https://www.sendgrid.com/windowsazure.html
  [Déploiement et empaquetage des applications PHP pour Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [fonction curl]: http://php.net/curl
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email
