<properties urlDisplayName="SendGrid Email Service" pageTitle="Utilisation du service de messagerie SendGrid (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Découvrez comment envoyer un courrier électronique avec le service de messagerie SendGrid dans Azure. Les exemples de code sont écrits en C# et utilisent l'API .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="erikre" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/24/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />





# Envoi de courriers électroniques à l'aide de SendGrid avec Azure

Dernière mise à jour : 24 octobre 2014

Ce guide présente l'exécution de tâches de programmation courantes avec le service de messagerie SendGrid dans Azure. Les exemples sont écrits en C\# et utilisent l'API .NET. Les scénarios traités incluent le **développement d'une messagerie électronique**, l'**envoi de courriers électroniques**, l'**ajout de pièces jointes** et l'**utilisation de filtres**. Pour plus d'informations sur SendGrid et l'envoi de courriers électroniques, consultez la section [Étapes suivantes][].

<h2><a name="toc"></a>Sommaire</h2>

[Définition du service de messagerie SendGrid][]   
[Création d'un compte SendGrid][]   
[Référencement de la bibliothèque de classes .NET de SendGrid][]   
[ Création d'un courrier électronique][]   
[ Envoi d'un courrier électronique][]   
[ Ajout d'une pièce jointe][]   
[ Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse][]   
[ Utilisation de services SendGrid supplémentaires][]   
[Étapes suivantes][]

<h2><a name="whatis"></a>Définition du service de messagerie SendGrid</h2>

SendGrid est un [service de messagerie électronique basé sur le cloud] qui permet l'envoi fiable
[de courriers électroniques transactionnels], l'évolutivité et l'analyse en temps réel, ainsi que des API flexibles
qui facilitent l'intégration personnalisée. Voici quelques scénarios courants en termes d'utilisation
de SendGrid :

-   envoi automatique d'accusés de réception aux clients ;
-   Gestion des listes de distribution pour l'envoi mensuel d'offres
    spéciales et de brochures numériques aux clients.
-   Collecte de mesures en temps réel concernant des éléments tels que les messages électroniques bloqués et
    la réactivité vis-à-vis des clients.
-   génération de rapports d'identification des tendances ;
-   transfert des demandes de renseignements des clients.

Pour plus d'informations, consultez la page [https://sendgrid.com](https://sendgrid.com).

<h2><a name="createaccount"></a>Création d'un compte SendGrid</h2>

[WACOM.INCLUDE [sendgrid-inscription](../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a>Référencement de la bibliothèque de classes .NET de SendGrid</h2>

Le [package NuGet SendGrid] (https://www.nuget.org/packages/Sendgrid) permet d'obtenir facilement l'API SendGrid et de configurer votre application avec toutes les dépendances associées. NuGet est une extension Visual Studio incluse dans Microsoft Visual Studio 2012 et qui facilite l'installation et la mise à jour des bibliothèques et outils. 

<div class="dev-callout">
<b>Remarque</b>
<p>Pour
installer NuGet si vous exécutez une version de Visual Studio antérieure à Visual Studio 2012, consultez la page <a href="http://www.nuget.org">http://www.nuget.org</a> et cliquez sur le bouton <b>Install
NuGet</b>.</p>
</div>

Pour installer le package NuGet SendGrid dans votre application, procédez comme suit :

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **Références**, puis cliquez sur
    **Gérer les packages NuGet**.

2.  Dans le volet gauche de la boîte de dialogue **Gérer les packages NuGet**, cliquez sur **En ligne**.

3.  Recherchez **SendGrid** et sélectionnez l'élément **SendGrid** dans la
    liste des résultats.

    ![SendGrid NuGet package][SendGrid-NuGet-package]

4.  Cliquez sur **Installer** pour terminer l'installation, puis fermez cette
    boîte de dialogue.

La bibliothèque de classes .NET de SendGrid est appelée **SendGridMail**. Elle contient
les espaces de noms suivants :

-   **SendGridMail** pour la création et l'utilisation d'éléments de messagerie.
-   **SendGridMail.Transport** pour l'envoi de courriers électroniques à l'aide du
    **protocole SMTP** ou du protocole HTTP 1.1 avec **Web/REST**.

Ajoutez les déclarations d'espace de noms de code suivantes en haut de chaque fichier C\# dans lequel vous souhaitez accéder au service de messagerie SendGrid par programme. **System.Net** et **System.Net.Mail** sont des espaces de noms .NET Framework qui sont inclus, car ils comprennent les types que vous utilisez généralement avec les API SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

<h2><a name="createemail"></a> Création d'un courrier électronique</h2>

Utilisez la méthode statique **SendGrid.GetInstance** pour créer un message électronique de type **SendGrid**. Une fois le message créé, utilisez les propriétés et méthodes **SendGrid** pour définir des valeurs qui incluent l'expéditeur, le destinataire, l'objet et le corps du message.

L'exemple suivant montre comment créer un objet de message entièrement renseigné :

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

<h2><a name="sendemail"></a> Envoi d'un courrier électronique</h2>

Après avoir créé un message, vous pouvez l'envoyer en utilisant l'API web fournie par SendGrid. Vous pouvez également [utiliser la bibliothèque intégrée de .NET](https://sendgrid.com/docs/Code_Examples/csharp.html).

Pour envoyer des messages électroniques, vous devez fournir les informations d'identification de votre compte SendGrid (nom d'utilisateur et mot de passe). Le code suivant montre comment encapsuler vos informations d'identification dans un objet **NetworkCredential** :
    
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

The following examples show how to send a message using the Web API.

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

<h2><a name="addattachment"></a> Ajout d'une pièce jointe</h2>

Vous pouvez ajouter des pièces jointes à un message en appelant la méthode **AddAttachment** et en spécifiant le nom et le chemin d'accès du fichier que vous souhaitez joindre. Vous pouvez inclure plusieurs pièces jointes en appelant cette méthode une fois pour chaque fichier que vous souhaitez joindre. L'exemple suivant montre comment ajouter une pièce jointe à un message :

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Vous pouvez également ajouter des pièces jointes depuis le **Flux** de données. Pour ce faire, appelez la méthode décrite ci-dessus, **AddAttachment**, mais en transmettant le Flux de données et le nom de fichier que vous souhaitez afficher dans le message.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }

<h2><a name="usefilters"></a> Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse</h2>

SendGrid offre d'autres fonctionnalités de messagerie grâce à l'utilisation de filtres. Il s'agit de paramètres que vous pouvez ajouter à un message électronique pour activer des fonctionnalités spécifiques, telles que le suivi des clics, Google Analytics, le suivi des abonnements, et ainsi de suite. Pour obtenir une liste exhaustive des filtres, consultez la page [Paramètres de filtre][].

Vous pouvez appliquer des filtres aux messages électroniques **SendGrid** à l'aide des méthodes implémentées au sein de la classe **SendGrid**.

Les exemples suivants montrent les filtres de pied de page et de suivi des clics :

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
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

<h2><a name="useservices"></a> Utilisation de services SendGrid supplémentaires</h2>

SendGrid propose des API web qui peuvent vous aider à tirer parti de fonctionnalités SendGrid supplémentaires à partir de votre application Azure. Pour plus d'informations, consultez la [documentation de l'API SendGrid][].

<h2><a name="nextsteps"></a>Étapes suivantes</h2>

Maintenant que vous avez appris les bases du service de messagerie SendGrid, consultez ces liens pour en savoir plus.

* Référentiel de la bibliothèque C\# de SendGrid : [sendgrid-csharp][]
*   Documentation de l'API SendGrid : <https://sendgrid.com/docs>
*   Offres spéciales SendGrid pour les clients Azure : [https://sendgrid.com](https://sendgrid.com)

  [Étapes suivantes]: #nextsteps
  [Définition du service de messagerie SendGrid]: #whatis
  [Création d'un compte SendGrid]: #createaccount
  [Référencement de la bibliothèque de classes .NET de SendGrid]: #reference
  [ Création d'un courrier électronique]: #createemail
  [ Envoi d'un courrier électronique]: #sendemail
  [ Ajout d'une pièce jointe]: #addattachment
  [ Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse]: #usefilters
  [ Utilisation de services SendGrid supplémentaires]: #useservices
  
  
  [Offre spéciale]: https://www.sendgrid.com/windowsazure.html
  
  
  
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid API documentation]: https://sendgrid.com/docs
  
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email
