<properties 
	pageTitle="Utilisation du service de messagerie électronique SendGrid (Java) | Microsoft Azure" 
	description="Découvrez comment envoyer un courrier électronique avec le service de messagerie SendGrid dans Azure. Exemples de code écrits en Java." 
	services="" 
	documentationCenter="java" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"/>
# Envoi de courriers électroniques à l'aide de SendGrid depuis Java

Ce guide présente l'exécution de tâches de programmation courantes avec le service de messagerie SendGrid dans Azure. Les exemples sont écrits en Java. Les scénarios traités incluent la **construction** et l'**envoi de courriers électroniques**, l'**ajout de pièces jointes**, l'**utilisation de filtres**, et la **mise à jour de propriétés**. Pour plus d'informations sur SendGrid et l'envoi de courriers électroniques, consultez la section [Étapes suivantes](#next-steps).

## Définition du service de messagerie SendGrid

SendGrid est un [service de messagerie dans le cloud] qui fournit des fonctionnalités fiables en matière de [remise de courrier électronique transactionnelle], d'extensibilité et d'analyse en temps réel, ainsi que des API flexibles qui facilitent l'intégration personnalisée. Voici quelques scénarios courants en termes d'utilisation de SendGrid :

-   Envoi automatique d'accusés de réception aux clients
-   Administration de listes de distribution pour un envoi mensuel de prospectus électroniques et d'offres spéciales aux clients
-   Collecte de mesures en temps réel concernant des éléments tels que les messages électroniques bloqués et la réactivité vis-à-vis des clients
-   Génération de rapports pour identifier les tendances
-   Transfert des demandes des clients
- Notifications par courriers électroniques depuis votre application

Pour plus d'informations, consultez <http://sendgrid.com>.

## Création d'un compte SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## Utilisation des bibliothèques javax.mail

Obtenez les bibliothèques javax.mail, par exemple depuis <http://www.oracle.com/technetwork/java/javamail>, et importez-les dans votre code. Généralement, le processus d'utilisation des bibliothèques javax.mail pour envoyer des messages électroniques via SMTP se déroule comme suit :

1.  Spécifiez les valeurs SMTP, notamment le serveur SMTP qui, dans le cas de SendGrid, est smtp.sendgrid.net.
    
```
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
           	  // …
```

2.  Développez la classe *javax.mail.Authenticator*, puis, dans votre implémentation de la méthode *getPasswordAuthentication*, renvoyez votre nom d'utilisateur et votre mot de passe SendGrid.  

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Créez une session de messagerie électronique authentifiée via un objet *javax.mail.Session*.

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Créez votre message et entrez des valeurs **À**, **De**, **Objet** et du contenu. Ceci est indiqué dans la section [Création d'un message électronique](#bkmk_HowToCreateEmail).
5.  Envoyez le message via un objet *javax.mail.Transport*. Ceci est indiqué dans la section [Envoi d'un message électronique][Envoi d’un message électronique].

## Création d'un message électronique

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

## Envoi d'un message électronique

Le code suivant permet d'envoyer un message électronique.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## Ajout d'une pièce jointe

Le code suivant permet d'ajouter une pièce jointe.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## Utilisation des filtres pour activer les pieds de page, le suivi et les analyses

SendGrid offre des fonctionnalités de messagerie électronique supplémentaires grâce à l'utilisation des *filtres*. Il s'agit de paramètres que vous pouvez ajouter à un message électronique pour activer des fonctionnalités spécifiques telles que le suivi des clics, Google Analytics, le suivi d'abonnement, etc. Pour obtenir une liste exhaustive des filtres, consultez la page [Paramètres de filtre][].

-   Le code suivant permet d'insérer un filtre de pied de page entraînant l'affichage de texte HTML en bas du message électronique à envoyer.

        message.addHeader("X-SMTPAPI", 
			"{"filters": 
			{"footer": 
			{"settings": 
        	{"enable":1,"text/html": 
			"<html><b>Thank you</b> for your business.</html>"}}}}");

-   Parmi les exemples de filtres, on peut également citer le suivi des clics. Imaginons que le texte de votre courrier électronique contient un lien hypertexte comme celui qui suit, et que vous voulez suivre le taux de clics :

        messagePart.setContent(
			"Hello,
			<p>This is the body of the message. Visit 
			<a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
			Thank you.", 
        	"text/html");

-   Pour activer le suivi des clics, utilisez le code suivant :

        message.addHeader("X-SMTPAPI", 
			"{"filters": 
			{"clicktrack": 
			{"settings": 
        	{"enable":1}}}}");

## Mise à jour des propriétés d'un message électronique

Certaines propriétés de courrier électronique peuvent être remplacées à l'aide de **set*Property*** ou ajoutées à l'aide de **add*Property***.

Par exemple, pour indiquer des adresses **ReplyTo**, utilisez le code suivant :

    InternetAddress addresses[] = 
		{ new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };
    
	message.setReplyTo(addresses);

Pour ajouter un destinataire **Cc**, utilisez le code suivant :

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## Utilisation de services SendGrid supplémentaires

SendGrid propose des API web qui peuvent vous aider à tirer parti de fonctionnalités SendGrid supplémentaires à partir de votre application Azure. Pour plus d'informations, consultez la [documentation de l'API SendGrid][].

## Étapes suivantes

Maintenant que vous avez appris les bases du service de messagerie SendGrid, consultez ces liens pour en savoir plus.

* Exemple montrant comment utiliser SendGrid dans un déploiement Azure : [Envoi de courriers électroniques à l'aide de SendGrid à partir de Java dans un déploiement Azure](store-sendgrid-java-how-to-send-email-example.md)
* Kit de développement logiciel (SDK) SendGrid Java : <https://sendgrid.com/docs/Code_Examples/java.html>
* Documentation de l'API SendGrid : <https://sendgrid.com/docs/API_Reference/index.html>
* Offres spéciales SendGrid pour les clients Azure : <https://sendgrid.com/windowsazure.html>

  [http://sendgrid.com]: https://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
  [http://sendgrid.com/features]: https://sendgrid.com/features
  [http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
  [Paramètres de filtre]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
  [documentation de l'API SendGrid]: https://sendgrid.com/docs/API_Reference/index.html
  [http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
  [service de messagerie dans le cloud]: https://sendgrid.com/email-solutions
  [remise de courrier électronique transactionnelle]: https://sendgrid.com/transactional-email

<!---HONumber=Oct15_HO3-->