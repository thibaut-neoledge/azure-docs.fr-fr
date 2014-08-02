<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="" solutions="" manager="" editor="" />

Envoi de courriers électroniques à l'aide de SendGrid avec Azure
================================================================

Ce guide présente l'exécution de tâches de programmation courantes avec le service de messagerie SendGrid dans Azure. Les exemples sont écrits en C\# et utilisent l'API .NET. Les scénarios traités incluent le **développement d'une messagerie électronique**, l'**envoi de courriers électroniques**, l'**ajout de pièces jointes** et l'**utilisation de filtres**. Pour plus d'informations sur SendGrid et l'envoi de courriers électroniques, consultez la section [Étapes suivantes](#nextsteps).

Sommaire
--------

[Définition du service de messagerie SendGrid](#whatis)
 [Création d'un compte SendGrid](#createaccount)
 [Référencement de la bibliothèque de classes .NET SendGrid](#reference)
 [Création d'un message électronique](#createemail)
 [Envoi de courrier électronique](#sendemail)
 [Ajout d'une pièce jointe](#addattachment)
 [Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse](#usefilters)
 [Utilisation de services SendGrid supplémentaires](#useservices)
 [Étapes suivantes](#nextsteps)

Définition du service de messagerie SendGridDéfinition du service de messagerie SendGrid
----------------------------------------------------------------------------------------

SendGrid est un [service de messagerie dans le cloud](http://sendgrid.com/solutions) qui fournit des fonctionnalités fiables en matière de [remise de courrier électronique transactionnelle](http://sendgrid.com/transactional-email), d'extensibilité et d'analyse en temps réel, ainsi que des API flexibles qui facilitent l'intégration personnalisée. Voici quelques scénarios courants en termes d'utilisation de SendGrid :

-   envoi automatique d'accusés de réception aux clients ;
-   gestion des listes de distribution pour l'envoi mensuel de brochures et d'offres spéciales aux clients ;
-   collecte de mesures en temps réel en ce qui concerne les messages bloqués et la réactivité des clients, par exemple ;
-   génération de rapports d'identification des tendances ;
-   transfert des demandes de renseignements des clients.

Pour plus d'informations, consultez la page <http://sendgrid.com>.

Création d'un compte SendGridCréation d'un compte SendGrid
----------------------------------------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

Référencement de la bibliothèque de classes .NET SendGridRéférencement de la bibliothèque de classes .NET SendGrid
------------------------------------------------------------------------------------------------------------------

Le package NuGet SendGrid permet facilement d'obtenir l'API SendGrid et de configurer votre application avec toutes les dépendances associées. NuGet est une extension Visual Studio incluse dans Microsoft Visual Studio 2012 et qui facilite l'installation et la mise à jour des bibliothèques et outils.

**Remarque**

Pour installer NuGet si vous exécutez une version de Visual Studio antérieure à Visual Studio 2012, consultez la page <http://www.nuget.org>, puis cliquez sur le bouton **Install NuGet**.

Pour installer le package NuGet SendGrid dans votre application, procédez comme suit :

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **References**, puis cliquez sur **Manage NuGet Packages**.

2.  Dans le volet gauche de la boîte de dialogue **Manage NuGet Packages**, cliquez sur **En ligne**.

3.  Recherchez **SendGrid**, puis sélectionnez l'élément **SendGrid** dans la liste de résultats.

    ![Package NuGet SendGrid](./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png)

4.  Cliquez sur **Install** pour terminer l'installation, puis fermez cette boîte de dialogue.

La bibliothèque de classes .NET de SendGrid est appelée **SendGridMail**. Elle contient les espaces de noms suivants :

-   **SendGridMail** pour la création et l'utilisation d'éléments de messagerie.
-   **SendGridMail.Transport** pour l'envoi de courriers électroniques à l'aide du protocole **SMTP** ou du protocole HTTP 1.1 avec **Web/REST**.

Ajoutez les déclarations d'espace de noms de code suivantes en haut de chaque fichier C\# dans lequel vous souhaitez accéder au service de messagerie SendGrid par programme. **System.Net** et **System.Net.Mail** sont des espaces de noms .NET Framework qui sont inclus, car ils comprennent les types que vous utilisez généralement avec les API SendGrid.

    using System.Net;
    using System.Net.Mail;
    using SendGridMail;
    using SendGridMail.Transport;

Création d'un message électronique Création d'un message électronique
---------------------------------------------------------------------

La méthode **SendGrid.GetInstance** statique permet de créer un message électronique de type **SendGrid**. Une fois le message créé, utilisez les propriétés et méthodes **SendGrid** pour définir des valeurs qui incluent l'expéditeur, le destinataire, l'objet et le corps du message.

L'exemple suivant montre comment créer un objet de message entièrement renseigné :

    // Définissez les propriétés du message.
    var from = new MailAddress("john@contoso.com");
    var to   = new MailAddress[] { new MailAddress("jeff@contoso.com") };
    var cc   = new MailAddress[] { new MailAddress("anna@contoso.com") };
    var bcc  = new MailAddress[] { new MailAddress("peter@contoso.com") };
    var subject = "Testing the SendGrid Library";
    var html    = "Hello World!";
    var text = "Hello World plain text!";
    var transport = SendGridMail.TransportType.SMTP;

    // Créez un message en passant les huit propriétés comme arguments.
    SendGrid myMessage = SendGrid.GetInstance(from, to, cc, bcc, subject, html, text, transport);

L'exemple suivant montre comment créer un objet de message vide :

    // Commencez par créer l'objet du message, puis ajoutez des propriétés.
    SendGrid myMessage = SendGrid.GetInstance();
     
    // Ajoutez les propriétés du message.
    MailAddress sender = new MailAddress(@"John Smith ");
    myMessage.From = sender;
     
    // Ajoutez plusieurs adresses au champ À.
    List recipients = new List
    {
        @"Jeff Smith ",
        @"Anna Lidman ",
        @"Peter Saddow "
    };
     
    foreach (string recipient in recipients)
    {
        myMessage.AddTo(recipient);
    }
     
    // Ajoutez le corps du message au format HTML.
    myMessage.Html = "Hello World!";

    // Ajoutez l'objet.
    myMessage.Subject = "Testing the SendGrid Library";

Pour plus d'informations sur les propriétés et méthodes prises en charge par le type **SendGrid**, consultez la page [sendgrid-csharp](https://github.com/sendgrid/sendgrid-csharp) sur GitHub.

Envoi de courrier électronique Envoi de courrier électronique
-------------------------------------------------------------

Une fois le message électronique créé, vous pouvez l'envoyer à l'aide de SMTP ou de l'API Web fournie par SendGrid. Pour plus d'informations sur les avantages et les inconvénients de chaque API, consultez la page [SMTP et API Web](http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/) dans la documentation SendGrid.

L'envoi de messages électroniques avec l'un de ces protocoles requiert que vous fournissiez les informations d'identification de votre compte SendGrid (nom d'utilisateur et mot de passe). Le code suivant montre comment encapsuler vos informations d'identification dans un objet **NetworkCredential** :

    // Créez les informations d'identification réseau pour accéder à votre compte SendGrid.
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);

Pour envoyer un message électronique, utilisez la méthode **Deliver** dans la classe **SMTP**, qui utilise le protocole SMTP, ou la classe de transport **REST**, qui appelle l'API Web SendGrid. Les exemples suivants montrent comment envoyer un message à l'aide de SMTP et de l'API Web.

### SMTP

    // Commencez par créer l'objet du message, puis ajoutez des propriétés.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Créez les informations d'identification en spécifiant votre nom d'utilisateur et votre mot de passe.
    var credentials = new NetworkCredential("username", "password");

    // Créez un transport SMTP pour l'envoi de messages.
    var transportSMTP = SMTP.GetInstance(credentials);

    // Envoyez le message.
    transportSMTP.Deliver(myMessage);

### API Web

    // Commencez par créer l'objet du message, puis ajoutez des propriétés.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Créez les informations d'identification en spécifiant votre nom d'utilisateur et votre mot de passe.
    var credentials = new NetworkCredential("username", "password");

    // Créez un transport REST pour l'envoi de messages.
    var transportREST = Web.GetInstance(credentials);

    // Envoyez le message.
    transportREST.Deliver(myMessage);

Ajout d'une pièce jointe Ajout d'une pièce jointe
-------------------------------------------------

Vous pouvez ajouter des pièces jointes à un message en appelant la méthode **AddAttachment** et en spécifiant le nom et le chemin d'accès du fichier que vous souhaitez joindre. Vous pouvez inclure plusieurs pièces jointes en appelant cette méthode une fois pour chaque fichier que vous souhaitez joindre. L'exemple suivant montre comment ajouter une pièce jointe à un message :

    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse
---------------------------------------------------------------------------------------------------------------------------------------------------------

SendGrid offre d'autres fonctionnalités de messagerie grâce à l'utilisation de filtres. Il s'agit de paramètres que vous pouvez ajouter à un message électronique pour activer des fonctionnalités spécifiques, telles que le suivi des clics, Google Analytics, le suivi des abonnements, et ainsi de suite. Pour obtenir une liste exhaustive des filtres, consultez la page [Paramètres de filtre](http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/).

Vous pouvez appliquer des filtres aux messages électroniques **SendGrid** à l'aide des méthodes implémentées au sein de la classe **SendGrid**. Avant de pouvoir activer des filtres dans un message électronique, vous devez initialiser la liste des filtres disponibles en appelant la méthode **InitalizeFilters**.

Les exemples suivants montrent les filtres de pied de page et de suivi des clics :

### Pied de page

    // Commencez par créer l'objet du message, puis ajoutez des propriétés.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // Ajoutez un pied de page au message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "HTML FOOTER");

### Suivi des clics

    // Commencez par créer l'objet du message, puis ajoutez des propriétés.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "Hello World Link!";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // true indique que les liens en texte brut du message 
    // doivent également être écrasés à des fins de suivi des liens. 
    myMessage.EnableClickTracking(true);

Utilisation de services Twilio supplémentaires Utilisation de services Twilio supplémentaires
---------------------------------------------------------------------------------------------

SendGrid propose des API Web qui peuvent vous aider à tirer parti de fonctionnalités SendGrid supplémentaires à partir de votre application Azure. Pour plus d'informations, consultez la [documentation de l'API SendGrid](http://docs.sendgrid.com/documentation/api/).

Étapes suivantesÉtapes suivantes
--------------------------------

Maintenant que vous avez appris les bases du service de messagerie SendGrid, consultez ces liens pour en savoir plus.

-   Référentiel de la bibliothèque C\# de SendGrid : [sendgrid-csharp](https://github.com/sendgrid/sendgrid-csharp)
-   Documentation de l'API SendGrid : http://docs.sendgrid.com/documentation/api/
-   Offres spéciales SendGrid pour les clients Azure : <http://sendgrid.com>

