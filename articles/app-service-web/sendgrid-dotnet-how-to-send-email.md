<properties 
	pageTitle="Utilisation du service de messagerie SendGrid (.NET) - Azure" 
	description="Découvrez comment envoyer un courrier électronique avec le service de messagerie SendGrid dans Azure. Code samples written in C# and use the .NET API." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor="erikre"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"/>





# Envoi de courriers électroniques à l'aide de SendGrid avec Azure

Dernière mise à jour : 24 février 2015

<h2><a name="overview"></a><span  class="short-header">Vue d'ensemble</span></h2>

Ce guide présente l'exécution de tâches de programmation courantes avec le service de messagerie SendGrid dans Azure. Les exemples sont écrits en C# et utilisent l'API .NET. Les scénarios traités incluent le **développement d'une messagerie électronique**, l'**envoi de courriers électroniques**, l'**ajout de pièces jointes** et l'**utilisation de filtres**. Pour plus d'informations sur SendGrid et l'envoi de courriers électroniques, consultez la section [Étapes suivantes][].

<h2><a name="whatis"></a><span  class="short-header">Présentation du service de messagerie électronique SendGrid</span></h2>

SendGrid est un [service de messagerie dans le cloud] qui fournit des fonctionnalités fiables en matière de [remise de courrier électronique transactionnelle], d'extensibilité et d'analyse en temps réel, ainsi que des API flexibles qui facilitent l'intégration personnalisée. Voici quelques scénarios courants en termes d'utilisation de SendGrid :

-   envoi automatique d'accusés de réception aux clients ;
-   gestion des listes de distribution pour l'envoi mensuel de brochures et d'offres spéciales aux clients ;
-   collecte de mesures en temps réel en ce qui concerne les messages bloqués et la réactivité des clients, par exemple ;
-   génération de rapports d'identification des tendances ;
-   transfert des demandes de renseignements des clients.
-   traitement des messages électroniques entrants.

Pour plus d'informations, consultez la page [https://sendgrid.com](https://sendgrid.com).

<h2><a name="createaccount"></a>Création d'un compte SendGrid</h2>

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a>Référencement de la bibliothèque de classes .NET de SendGrid</h2>

Le [package NuGet SendGrid](https://www.nuget.org/packages/Sendgrid) permet facilement d'obtenir l'API SendGrid et de configurer votre application avec toutes les dépendances associées. NuGet est une extension Visual Studio incluse dans Microsoft Visual Studio 2012 et qui facilite l'installation et la mise à jour des bibliothèques et outils.

> [AZURE.NOTE]Pour installer NuGet si vous exécutez une version de Visual Studio antérieure à Visual Studio 2012, consultez la page [http://www.nuget.org](http://www.nuget.org), puis cliquez sur le bouton **Install NuGet**.

Pour installer le package NuGet SendGrid dans votre application, procédez comme suit :

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **References**, puis cliquez sur **Manage NuGet Packages**.

2.  Dans le volet gauche de la boîte de dialogue **Manage NuGet Packages**, cliquez sur **En ligne**.

3.  Recherchez **SendGrid** et sélectionnez l'élément **SendGrid** dans la liste des résultats (la version actuelle est 5.0.0).

    ![Package NuGet SendGrid][SendGrid-NuGet-package]

4.  Cliquez sur **Install** pour terminer l'installation, puis fermez cette boîte de dialogue.

La bibliothèque de classes .NET de SendGrid est appelée **SendGridMail**. Elle contient les espaces de noms suivants :

-   **SendGridMail** pour la création et l'utilisation d'éléments de messagerie.
-   **SendGridMail.Transport** pour l'envoi de courriers électroniques à l'aide du protocole **SMTP** ou du protocole HTTP 1.1 avec **Web/REST**.

Ajoutez les déclarations d'espace de noms de code suivantes en haut de chaque fichier C# dans lequel vous souhaitez accéder au service de messagerie SendGrid par programme. **System.Net** et **System.Net.Mail** sont des espaces de noms .NET Framework qui sont inclus, car ils comprennent les types que vous utilisez généralement avec les API SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

<h2><a name="createemail"></a>Création d'un message électronique</h2>

Pour créer un message électronique, utilisez l'objet **SendGridMessage**. Une fois que l'objet du message est créé, vous pouvez définir les propriétés et les méthodes, notamment l'expéditeur, le destinataire, l'objet et le corps du message électronique.

L'exemple suivant montre comment créer un objet de message entièrement renseigné :

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

Pour plus d'informations sur les propriétés et méthodes prises en charge par le type **SendGrid**, consultez la page [sendgrid-csharp][] sur GitHub.

<h2><a name="sendemail"></a>Envoi d'un message électronique</h2>

Une fois le message électronique créé, vous pouvez l'envoyer à l'aide de l'API Web fournie par SendGrid. Vous pouvez également [utiliser la bibliothèque intégrée de .NET](https://sendgrid.com/docs/Code_Examples/csharp.html).

Pour envoyer des messages électroniques, vous devez fournir les informations d'identification de votre compte SendGrid (nom d'utilisateur et mot de passe). Le code suivant montre comment encapsuler vos informations d'identification dans un objet **NetworkCredential** :
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    /* Alternatively, you may store these credentials via your Azure portal
       by clicking CONFIGURE and adding the key/value pairs under "app settings".
       Then, you may access them as follows: 
       var username = System.Environment.GetEnvironmentVariable("SENDGRID_USER"); 
       var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASS");
       assuming you named your keys SENDGRID_USER and SENDGRID_PASS */

    var credentials = new NetworkCredential(username, pswd);

Les exemples suivants montrent comment envoyer un message à l'aide de l'API Web.

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email.
    // You can also use the **DeliverAsync** method, which returns an awaitable task.
    transportWeb.Deliver(myMessage);

<h2><a name="addattachment"></a>Ajout d'une pièce jointe</h2>

Vous pouvez ajouter des pièces jointes à un message en appelant la méthode **AddAttachment** et en spécifiant le nom et le chemin d'accès du fichier que vous souhaitez joindre. Vous pouvez inclure plusieurs pièces jointes en appelant cette méthode une fois pour chaque fichier que vous souhaitez joindre. L'exemple suivant montre comment ajouter une pièce jointe à un message :

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Vous pouvez également ajouter des pièces jointes depuis le **Flux** de données. Pour ce faire, appelez la méthode décrite ci-dessus, **AddAttachment**, mais en transmettant le flux de données, et le nom de fichier que vous souhaitez afficher dans le message. Dans ce cas, vous devez ajouter la bibliothèque System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


<h2><a name="usefilters"></a><span  class="short-header">Utilisation d'applications pour activer les pieds de page, le suivi et l'analyse</span></h2>

SendGrid offre d'autres fonctionnalités de messagerie grâce à l'utilisation d'applications. Il s'agit de paramètres que vous pouvez ajouter à un message électronique pour activer des fonctionnalités spécifiques, telles que le suivi des clics, Google Analytics, le suivi des abonnements, et ainsi de suite. Pour obtenir la liste complète des applications, consultez [Paramètres de l'application][].

Les applications peuvent être appliquées aux messages électroniques **SendGrid** à l'aide des méthodes implémentées dans le cadre de la classe **SendGrid**.

Les exemples suivants montrent les filtres de pied de page et de suivi des clics :

### Pied de page

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### Suivi des clics

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href="http://www.example.com">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

<h2><a name="useservices"></a>Utilisation de services SendGrid supplémentaires</h2>

SendGrid propose des API web et des « webhooks » que vous pouvez utiliser pour tirer parti d'autres fonctionnalités SendGrid à partir de votre application Azure. Pour plus d'informations, consultez la [documentation de l'API SendGrid][].

<h2><a name="nextsteps"></a>Étapes suivantes</h2>

Maintenant que vous avez appris les bases du service de messagerie SendGrid, consultez ces liens pour en savoir plus.

* Référentiel de la bibliothèque C# de SendGrid : [sendgrid-csharp][]
*   Documentation de l'API SendGrid : <https://sendgrid.com/docs>
*   Offre spéciale SendGrid pour les clients Azure : [https://sendgrid.com](https://sendgrid.com)

  [Étapes suivantes]: #nextsteps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  
  
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Paramètres de l'application]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [documentation de l'API SendGrid]: https://sendgrid.com/docs
  
  [service de messagerie dans le cloud]: https://sendgrid.com/email-solutions
  [remise de courrier électronique transactionnelle]: https://sendgrid.com/transactional-email
 

<!---HONumber=July15_HO4-->