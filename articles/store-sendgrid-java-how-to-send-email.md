<properties urlDisplayName="SendGrid Email Service" pageTitle="Utilisation du service de messagerie SendGrid (Java) - Azure" metaKeywords="Azure SendGrid, service de messagerie électronique Azure, Azure SendGrid Java, Azure messagerie électronique Java" description="Découvrez comment envoyer un courrier électronique avec le service de messagerie SendGrid dans Azure. Exemples de code écrits en Java." metaCanonical="" services="" documentationCenter="Java" title="How to Send Email Using SendGrid from Java" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Envoi de courriers électroniques à l'aide de SendGrid depuis Java

Ce guide présente l'exécution de tâches de programmation courantes avec le service de messagerie SendGrid dans Azure. Les exemples sont écrits en Java. Les scénarios traités incluent la **construction** et l'**envoi de courriers électroniques**, l'**ajout de pièces jointes**, l'**utilisation de filtres**, et la **mise à jour de propriétés**. Pour plus d'informations sur SendGrid et l'envoi de courriers électroniques, consultez la section [Étapes suivantes][].

## Sommaire

-   [Présentation du service de messagerie électronique SendGrid][]
-   [Création d'un compte SendGrid][]
-   [Procédure : Utilisation des bibliothèques javax.mail][]
-   [Procédure : Création d'un courrier électronique][]
-   [Procédure : Envoi d'un courrier électronique][]
-   [Procédure : Ajout d'une pièce jointe][]
-   [Procédure : Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse][]
-   [Procédure : Mise à jour des propriétés de courrier électronique][]
-   [Procédure : Utilisation de services SendGrid supplémentaires][]
-   [Étapes suivantes][]

## <a name="bkmk_WhatIsSendGrid"> </a>Présentation du service de messagerie électronique SendGrid

SendGrid est un [service de messagerie électronique dans le cloud] qui fournit des fonctionnalités fiables en matière de [remise de courrier électronique transactionnelle], d'extensibilité et d'analyse en temps réel, ainsi que des API flexibles qui facilitent l'intégration personnalisée. Voici quelques scénarios courants en termes d'utilisation de SendGrid :

-   Envoi automatique d'accusés de réception aux clients
-   Administration de listes de distribution pour un envoi mensuel de prospectus électroniques et d'offres spéciales aux clients
-   Collecte de mesures en temps réel concernant des éléments tels que les messages électroniques bloqués et la réactivité vis-à-vis des clients
-   Génération de rapports pour identifier les tendances
-   Transfert des demandes des clients
- Notifications par courriers électroniques depuis votre application

Pour plus d'informations, consultez <http://sendgrid.com>.

## <a name="bkmk_CreateSendGridAcct"> </a>Création d'un compte SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_HowToUseJavax"> </a>Utilisation Utilisation des bibliothèques javax.mail

Obtenez les bibliothèques javax.mail, par exemple depuis <http://www.oracle.com/technetwork/java/javamail>, et importez-les dans votre code. Généralement, le processus d'utilisation des bibliothèques javax.mail pour envoyer des messages électroniques via SMTP se déroule comme suit :

1.  Spécifiez les valeurs SMTP, notamment le serveur SMTP qui, dans le cas de
    SendGrid, est smtp.sendgrid.net.
    
        import java.util.Properties;
        import javax.activation.*;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
	       private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
	       private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";
        
		   public static void main(String[] args) throws Exception{
         	  new MyEmailer().SendMail();
           }
        
		   public void SendMail() throws Exception
           {
              Properties properties = new Properties();
           	  properties.put("mail.transport.protocol", "smtp");
           	  properties.put("mail.smtp.host", SMTP_HOST_NAME);
           	  properties.put("mail.smtp.port", 587);
           	  properties.put("mail.smtp.auth", "true");
           	  // ...

2.  Étendez la classe <span class="auto-style1">javax.mail.Authenticator</span>     et, dans votre implémentation de la méthode     <span class="auto-style1">getPasswordAuthentication</span> , retournez votre nom d'utilisateur et mot de passe SendGrid.  

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Créez une session de messagerie électronique authentifiée via un objet
    <span class="auto-style1">javax.mail.Session</span> .  

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Créez votre message et entrez des valeurs **﻿À**, **De**, **Objet** et du contenu. Cette étape est présentée dans la section [ Création d'une section de courrier électronique](#bkmk_HowToCreateEmail) .
5.  Envoyez le message via un objet     <span class="auto-style1">javax.mail.Transport</span> . Cette étape est présentée dans la section [ Envoi d'un courrier électronique][Procédure : Envoi d'un courrier électronique].

## <a name="bkmk_HowToCreateEmail"> </a>Utilisation Création d'un courrier électronique

Le code suivant permet d'entrer des valeurs pour un message électronique.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
		"<p>Hello,</p>
		<p>Your Contoso order has <b>shipped</b>.</p>
		<p>Thank you,<br>John</br></p>",
		"text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="bkmk_HowToSendEmail"> </a>Utilisation Envoi d'un courrier électronique

Le code suivant permet d'envoyer un message électronique.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="bkmk_HowToAddAttachment"> </a>Utilisation Ajout d'une pièce jointe

Le code suivant permet d'ajouter une pièce jointe.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="bkmk_HowToUseFilters"> </a>Utilisation Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse

SendGrid offre d'autres fonctionnalités de messagerie grâce à l'utilisation de *filtres*. Il s'agit de paramètres que vous pouvez ajouter à un message électronique pour activer des fonctionnalités spécifiques telles que le suivi des clics, Google Analytics, le suivi d'abonnement, etc. Pour obtenir une liste exhaustive des filtres, consultez la page [Paramètres de filtre][].

-   Le code suivant montre comment insérer un filtre de pied de page qui a pour conséquence
    l'affichage de texte HTML en bas du message électronique envoyé.

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"footer\": 
			{\"settings\": 
        	{\"enable\":1,\"text/html\": 
			\"<html><b>Thank you</b> for your business.</html>\"}}}}");

-   Parmi les exemples de filtres, on peut également citer le suivi des clics. Imaginons que le texte de votre courrier électronique contient un lien hypertexte comme celui qui suit, et que vous voulez suivre le taux de clics :

        messagePart.setContent(
			"Hello,
			<p>This is the body of the message. Visit 
			<a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
			Thank you.", 
        	"text/html");

-   To enable the click tracking, use the following code:

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"clicktrack\": 
			{\"settings\": 
        	{\"enable\":1}}}}");

## <a name="bkmk_HowToUpdateEmail"> </a>Procédure : Mise à jour des propriétés de courrier électronique

Certaines propriétés de courrier électronique peuvent être remplacées à l'aide de **set*Property*** ou ajoutées à l'aide de **add*Property***.

Par exemple, pour indiquer des adresses **ReplyTo**, utilisez le code suivant :

    InternetAddress addresses[] = 
		{ new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };
    
	message.setReplyTo(addresses);

Pour ajouter un destinataire **Cc**, utilisez le code suivant :

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>Procédure : Utilisation de services SendGrid supplémentaires

SendGrid propose des API web que vous pouvez utiliser pour tirer parti
de fonctionnalités SendGrid supplémentaires à partir de votre application Azure. Pour obtenir des détails complets,
consultez la [Documentation sur l'API SendGrid][].

## <a name="bkmk_NextSteps"> </a>Étapes suivantes

Maintenant que vous avez appris les notions de base du service de messagerie SendGrid, consultez
ces liens pour en savoir plus.

* Exemple montrant comment utiliser SendGrid dans un déploiement Azure : [Envoi de courriers électroniques à l'aide de SendGrid à partir de Java dans un déploiement Azure](store-sendgrid-java-how-to-send-email-example.md)
* Kit SDK SendGrid Java : <https://sendgrid.com/docs/Code_Examples/java.html>
* Documentation de l'API SendGrid : <https://sendgrid.com/docs/API_Reference/index.html>
* Offres spéciales SendGrid pour les clients Azure : <https://sendgrid.com/windowsazure.html>

  [Étapes suivantes]: #bkmk_NextSteps
  [Présentation du service de messagerie électronique SendGrid]: #bkmk_WhatIsSendGrid
  [Création d'un compte SendGrid]: #bkmk_CreateSendGridAcct
  [Procédure : Utilisation des bibliothèques javax.mail]: #bkmk_HowToUseJavax
  [Procédure : Création d'un courrier électronique]: #bkmk_HowToCreateEmail
  [Procédure : Envoi d'un courrier électronique]: #bkmk_HowToSendEmail
  [Procédure : Ajout d'une pièce jointe]: #bkmk_HowToAddAttachment
  [Procédure : Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse]: #bkmk_HowToUseFilters
  [Procédure : Mise à jour des propriétés de courrier électronique]: #bkmk_HowToUpdateEmail
  [Procédure : Utilisation de services SendGrid supplémentaires]: #bkmk_HowToUseAdditionalSvcs
  [http://sendgrid.com]: https://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
  [http://sendgrid.com/features]: https://sendgrid.com/features
  [http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
  [Paramètres de filtre]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
  [Documentation sur l'API SendGrid]: https://sendgrid.com/docs/API_Reference/index.html
  [http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
  [service de messagerie électronique dans le cloud]: https://sendgrid.com/email-solutions
  [remise de courrier électronique transactionnelle]: https://sendgrid.com/transactional-email

<!--HONumber=35.2-->
