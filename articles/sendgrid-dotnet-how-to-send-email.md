<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="carolz" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Envoi de courriers électroniques à l'aide de SendGrid avec Azure

Dernière mise à jour : 21.08.14

Ce guide présente l'exécution de tâches de programmation courantes avec le
 service de messagerie SendGrid dans Azure. Les exemples sont écrits en C#
 et utilisent l'API .NET. Les scénarios abordés sont les suivants : **construction
de courriers électroniques**, **envoi de courriers électroniques**, **ajout de pièces jointes** et **utilisation
de filtres**. Pour plus d'informations sur SendGrid et l'envoi de courriers électroniques, consultez la section
[Étapes suivantes][Étapes suivantes].

## <a name="toc"></a>Sommaire

[Définition du service de messagerie SendGrid][Définition du service de messagerie SendGrid]
[Création d'un compte SendGrid][Création d'un compte SendGrid]
[Référencement de la bibliothèque de classes .NET SendGrid][Référencement de la bibliothèque de classes .NET SendGrid]
[Création d'un message électronique][Création d'un message électronique]
[Envoi de courrier électronique][Envoi de courrier électronique]
[Ajout d'une pièce jointe][Ajout d'une pièce jointe]
[Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse][Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse]
[Utilisation de services SendGrid supplémentaires][Utilisation de services SendGrid supplémentaires]
[Étapes suivantes][Étapes suivantes]

## <a name="whatis"></a><span class="short-header">Définition du service de messagerie SendGrid</span>

SendGrid est un [service de messagerie électronique dans le cloud][service de messagerie électronique dans le cloud] qui fournit des fonctionnalités fiables en matière de
[remise de courrier électronique transactionnelle][remise de courrier électronique transactionnelle], d'extensibilité et d'analyse en temps réel, ainsi que des API
flexibles qui facilitent l'intégration personnalisée. Voici quelques scénarios courants en
termes d'utilisation de SendGrid :

-   envoi automatique d'accusés de réception aux clients ;
-   Administration de listes de distribution pour un envoi mensuel
    de prospectus électroniques et d'offres spéciales aux clients.
-   Collecte de mesures en temps réel en ce qui concerne, par exemple, les
    messages bloqués et la réactivité des clients.
-   génération de rapports d'identification des tendances ;
-   transfert des demandes de renseignements des clients.

Pour plus d'informations, consultez la page [][]<http://sendgrid.com></a>.

## <a name="createaccount"></a><span class="short-header">Création d'un compte SendGrid</span>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"></a><span class="short-header">Référencement de la bibliothèque de classes .NET SendGrid</span>Référencement de la bibliothèque de classes .NET SendGrid

Le package [SendGrid NuGet][SendGrid NuGet] permet facilement d'obtenir l'API SendGrid
et de configurer votre application avec toutes les dépendances associées. NuGet est une
extension Visual Studio incluse dans Microsoft Visual Studio 2012 et qui facilite l'installation et la mise à jour
des bibliothèques et outils.

<div class="dev-callout">
<b>Remarque</b>
<p>Pour
installer NuGet si vous ex&eacute;cutez une version de Visual Studio ant&eacute;rieure &agrave; Visual Studio&nbsp;2012, consultez la page <a href="http://www.nuget.org">http://www.nuget.org</a>, puis cliquez sur le bouton <b>Install
NuGet</b>.</p>
</div>

Pour installer le package NuGet SendGrid dans votre application, procédez comme suit :

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **References**, puis cliquez sur
    **Manage NuGet Packages**.

2.  Dans le volet gauche de la boîte de dialogue **Manage NuGet Packages**, cliquez sur **En ligne**.

3.  Recherchez **SendGrid**, puis sélectionnez l'élément **SendGrid** dans la
    liste de résultats.

    ![Package NuGet SendGrid][Package NuGet SendGrid]

4.  Cliquez sur **Install** pour terminer l'installation, puis fermez cette
    boîte de dialogue.

La bibliothèque de classes .NET de SendGrid est appelée **SendGridMail**. Elle contient
les espaces de noms suivants :

-   **SendGridMail** pour la création et l'utilisation d'éléments de messagerie.
-   **SendGridMail.Transport** pour l'envoi de courriers électroniques à l'aide du protocole
    **SMTP** ou du protocole HTTP 1.1 avec
    **Web/REST**.

Ajoutez les déclarations d'espace de noms suivantes au début de chaque fichier C#
dans lequel vous souhaitez accéder au service de messagerie SendGrid par programme.
**System.Net** et **System.Net.Mail** sont des espaces de noms .NET Framework
qui sont inclus, car ils comprennent les types que vous utilisez généralement avec
les API SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="createemail"></a><span class="short-header"> Création d'un message électronique</span> Création d'un courrier électronique

La méthode **SendGrid.GetInstance** statique permet de créer un message
électronique de type **SendGrid**. Une fois le message créé,
utilisez les propriétés et méthodes **SendGrid** pour définir des valeurs qui incluent l'
expéditeur, le destinataire, l'objet et le corps du
message.

L'exemple suivant montre comment créer un objet de
message entièrement renseigné :

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

Pour plus d'informations sur les propriétés et méthodes prises en charge par le type
**SendGrid**, consultez la page [sendgrid-csharp][sendgrid-csharp] sur GitHub.

## <a name="sendemail"></a><span class="short-header"> Envoi d'un courrier électronique</span>

Après avoir créé un message, vous pouvez l'envoyer en utilisant
l'API Web fournie par SendGrid. Vous pouvez également [utiliser la bibliothèque intégrée de .NET][utiliser la bibliothèque intégrée de .NET].

L'envoi de messages électroniques requiert que vous fournissiez
les informations d'identification de votre compte SendGrid (nom d'utilisateur et mot de passe). Le code suivant
montre comment encapsuler vos informations d'identification dans un objet
**NetworkCredential** :

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

## <a name="addattachment"></a><span class="short-header"> Ajout d'une pièce jointe</span> Ajout d'une pièce jointe

Il est possible d'ajouter des pièces jointes à un message en appelant la méthode**AddAttachment**
 et en indiquant le chemin et le nom du fichier que vous souhaitez joindre.
Vous pouvez inclure plusieurs pièces jointes en appelant cette méthode pour
chaque fichier que vous souhaitez joindre. L'exemple suivant montre comment ajouter
une pièce jointe à un message :

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

Vous pouvez également ajouter des pièces jointes depuis le **Flux** de données. Pour ce faire, appelez la méthode décrite ci-dessus, **AddAttachment**, mais en transmettant le flux de données, et le nom de fichier que vous souhaitez afficher dans le message.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }

## <a name="usefilters"></a><span class="short-header"> Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse</span>

SendGrid offre d'autres fonctionnalités de messagerie grâce à l'utilisation
de filtres. Il s'agit de paramètres que vous pouvez ajouter à un message électronique
pour activer des fonctionnalités spécifiques, telles que le suivi des clics, Google Analytics,
le suivi des abonnements, et ainsi de suite. Pour obtenir une liste exhaustive des filtres, consultez la page
[Paramètres de filtre][Paramètres de filtre].

Vous pouvez appliquer des filtres aux messages électroniques **SendGrid** à l'aide des méthodes
implémentées au sein de la classe **SendGrid**.

Les exemples suivants montrent les filtres
de pied de page et de suivi des clics :

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

## <a name="useservices"></a><span class="short-header"> Utilisation de services SendGrid supplémentaires</span>

SendGrid propose des API Web qui peuvent vous aider à tirer parti de
fonctionnalités SendGrid supplémentaires à partir de votre application Azure. Pour plus
d'informations, consultez la [documentation de l'API SendGrid][documentation de l'API SendGrid].

## <a name="nextsteps"></a><span class="short-header">Étapes suivantes</span>Étapes suivantes

Maintenant que vous avez appris les bases du service de messagerie électronique SendGrid, consultez
ces liens pour en savoir plus.

-   Référentiel de la bibliothèque C# de SendGrid : [sendgrid-csharp][sendgrid-csharp]
-   Documentation de l'API SendGrid : <http://docs.sendgrid.com/documentation/api/>
-   Offres spéciales SendGrid pour les clients Azure : [][]<http://sendgrid.com></a>

  [Étapes suivantes]: #nextsteps
  [Définition du service de messagerie SendGrid]: #whatis
  [Création d'un compte SendGrid]: #createaccount
  [Référencement de la bibliothèque de classes .NET SendGrid]: #reference
  [Création d'un message électronique]: #createemail
  [Envoi de courrier électronique]: #sendemail
  [Ajout d'une pièce jointe]: #addattachment
  [Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse]: #usefilters
  [Utilisation de services SendGrid supplémentaires]: #useservices
  [service de messagerie électronique dans le cloud]: http://sendgrid.com/solutions
  [remise de courrier électronique transactionnelle]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [SendGrid NuGet]: https://www.nuget.org/packages/Sendgrid
  [Package NuGet SendGrid]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [utiliser la bibliothèque intégrée de .NET]: https://sendgrid.com/docs/Code_Examples/csharp.html
  [Paramètres de filtre]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [documentation de l'API SendGrid]: http://docs.sendgrid.com/documentation/api/
