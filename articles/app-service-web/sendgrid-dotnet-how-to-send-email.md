<properties 
	pageTitle="Utilisation du service de messagerie électronique SendGrid (.NET) | Microsoft Azure" 
	description="Découvrez comment envoyer un courrier électronique avec le service de messagerie SendGrid dans Azure. Code samples written in C# and use the .NET API." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="thinkingserious" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="01/14/2016" 
	ms.author="team-pi@sendgrid.com"/>





# Envoi de courriers électroniques à l'aide de SendGrid avec Azure


## Vue d'ensemble

Ce guide présente l'exécution de tâches de programmation courantes avec le service de messagerie SendGrid dans Azure. Les exemples sont écrits en C# et utilisent l'API .NET. Les scénarios traités incluent le **développement d'une messagerie électronique**, l'**envoi de courriers électroniques**, l'**ajout de pièces jointes** et l'**utilisation de filtres**. Pour plus d'informations sur SendGrid et l'envoi de courriers électroniques, consultez la section [Étapes suivantes][].

## Présentation du service de messagerie SendGrid

SendGrid est un [service de messagerie dans le cloud] qui fournit des fonctionnalités fiables en matière de [remise de courrier électronique transactionnelle], d'extensibilité et d'analyse en temps réel, ainsi que des API flexibles qui facilitent l'intégration personnalisée. Voici quelques scénarios courants en termes d'utilisation de SendGrid :

-   envoi automatique d'accusés de réception aux clients ;
-   gestion des listes de distribution pour l'envoi mensuel de brochures et d'offres spéciales aux clients ;
-   collecte de mesures en temps réel en ce qui concerne les messages bloqués et la réactivité des clients, par exemple ;
-   génération de rapports d'identification des tendances ;
-   transfert des demandes de renseignements des clients.
-   traitement des messages électroniques entrants.

Pour plus d'informations, consultez la page [https://sendgrid.com](https://sendgrid.com) ou notre [bibliothèque C#][sendgrid-csharp]

## Création d'un compte SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## Référencement de la bibliothèque de classes .NET de SendGrid

Le [package NuGet SendGrid](https://www.nuget.org/packages/Sendgrid) permet facilement d'obtenir l'API SendGrid et de configurer votre application avec toutes les dépendances associées. NuGet est une extension Visual Studio incluse dans Microsoft Visual Studio 2015 qui facilite l'installation et la mise à jour des bibliothèques et outils.

> [AZURE.NOTE] Pour installer NuGet si vous exécutez une version de Visual Studio antérieure à Visual Studio 2015, consultez la page [http://www.nuget.org](http://www.nuget.org), puis cliquez sur le bouton **Install NuGet**.

Pour installer le package NuGet SendGrid dans votre application, procédez comme suit :

1.  Créez un projet.

    ![Création d'un projet][create-new-project]

2.  Sélectionnez un modèle.

    ![Sélectionner un modèle][select-a-template]

3.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **References**, puis cliquez sur **Manage NuGet Packages**.

4.  Recherchez **SendGrid**, puis sélectionnez l'élément **SendGrid** dans la liste de résultats.

    ![Package NuGet SendGrid][SendGrid-NuGet-package]

5.  Cliquez sur **Install** pour terminer l'installation, puis fermez cette boîte de dialogue.

La bibliothèque de classes .NET de SendGrid est appelée **SendGridMail**. Elle contient les espaces de noms suivants :

-   **SendGridMail** pour la création et l'utilisation d'éléments de messagerie.
-   **SendGridMail.Transport** pour l'envoi de courriers électroniques à l'aide du protocole **SMTP** ou du protocole HTTP 1.1 avec **Web/REST**.

Ajoutez les déclarations d'espace de noms de code suivantes en haut de chaque fichier C# dans lequel vous souhaitez accéder au service de messagerie SendGrid par programme. **System.Net** et **System.Net.Mail** sont des espaces de noms .NET Framework qui sont inclus, car ils comprennent les types que vous utilisez généralement avec les API SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## Création d'un message électronique

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

## Envoi d'un message électronique

Une fois le message électronique créé, vous pouvez l'envoyer à l'aide de l'API Web fournie par SendGrid. Vous pouvez également [utiliser la bibliothèque intégrée de .NET](https://sendgrid.com/docs/Code_Examples/csharp.html).

Pour envoyer des messages électroniques, vous devez fournir les informations d'identification de votre compte SendGrid (nom d'utilisateur et mot de passe) ou votre clé d’API SendGrid. La clé d’API est la méthode privilégiée. Pour plus de détails sur la configuration des clés d’API, consultez notre [documentation](https://sendgrid.com/docs/Classroom/Send/api_keys.html).

Vous pouvez stocker ces informations d’identification via votre portail Azure en cliquant sur CONFIGURER et en ajoutant les paires clé / valeur dans Paramètres de l’application.

 ![Paramètres de l’application Azure][azure_app_settings]

 Vous pouvez ensuite y accéder comme suit :
    
    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USER"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASS");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

Utilisation des informations d’identification
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

Utilisation de la clé d’API :

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


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

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## Ajout d'une pièce jointe

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


## Procédure : Utilisation d'applications pour activer les pieds de page, le suivi et l'analyse

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

## Utilisation de services SendGrid supplémentaires

SendGrid propose des API web et des « webhooks » que vous pouvez utiliser pour tirer parti d'autres fonctionnalités SendGrid à partir de votre application Azure. Pour plus d'informations, consultez la [documentation de l'API SendGrid][].

## Étapes suivantes

Maintenant que vous avez appris les bases du service de messagerie SendGrid, consultez ces liens pour en savoir plus.

*   Référentiel de la bibliothèque C# de SendGrid : [sendgrid-csharp][]
*   Documentation de l'API SendGrid : <https://sendgrid.com/docs>
*   Offre spéciale SendGrid pour les clients Azure : [https://sendgrid.com](https://sendgrid.com)

  [Étapes suivantes]: #next-steps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  [create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
  [select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
  [azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Paramètres de l'application]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [documentation de l'API SendGrid]: https://sendgrid.com/docs
  
  [service de messagerie dans le cloud]: https://sendgrid.com/email-solutions
  [remise de courrier électronique transactionnelle]: https://sendgrid.com/transactional-email
 

<!---HONumber=AcomDC_0128_2016-->