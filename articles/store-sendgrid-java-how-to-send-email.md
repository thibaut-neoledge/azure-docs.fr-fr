<properties linkid="dev-java-how-to-access-control" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Java) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Java, Azure email Java" description="Learn how send email with the SendGrid email service on Azure. Code samples written in Java." metaCanonical="" services="" documentationCenter="Java" title="How to Send Email Using SendGrid from Java" authors="waltpo" solutions="" manager="" editor="mollybos" />

Envoi de courriers électroniques à l'aide de SendGrid depuis Java
=================================================================

Ce guide présente l'exécution de tâches de programmation courantes avec le service de messagerie SendGrid dans Azure. Les exemples sont écrits en Java. Les scénarios traités incluent la **construction** et l'**envoi de courriers électroniques**, l'**ajout de pièces jointes**, l'**utilisation de filtres**, et la **mise à jour de propriétés**. Pour plus d'informations sur SendGrid et l'envoi de courriers électroniques, consultez la section [Étapes suivantes](#bkmk_NextSteps).

Sommaire
--------

-   [Définition du service de messagerie SendGrid](#bkmk_WhatIsSendGrid)
-   [Création d'un compte SendGrid](#bkmk_CreateSendGridAcct)
-   [Utilisation des bibliothèques javax.mail](#bkmk_HowToUseJavax)
-   [Création d'un courrier électronique](#bkmk_HowToCreateEmail)
-   [Envoi d'un courrier électronique](#bkmk_HowToSendEmail)
-   [Ajout d'une pièce jointe](#bkmk_HowToAddAttachment)
-   [Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse](#bkmk_HowToUseFilters)
-   [Mise à jour des propriétés de courrier électronique](#bkmk_HowToUpdateEmail)
-   [Utilisation de services SendGrid supplémentaires](#bkmk_HowToUseAdditionalSvcs)
-   [Étapes suivantes](#bkmk_NextSteps)

Présentation du service de messagerie électronique SendGrid
-----------------------------------------------------------

SendGrid est un [service de messagerie dans le cloud](http://sendgrid.com/solutions) qui fournit des fonctionnalités fiables en matière de [remise de courrier électronique transactionnelle](http://sendgrid.com/transactional-email), d'extensibilité et d'analyse en temps réel, ainsi que des API flexibles qui facilitent l'intégration personnalisée. Voici quelques scénarios courants en termes d'utilisation de SendGrid :

-   Envoi automatique d'accusés de réception aux clients
-   Administration de listes de distribution pour un envoi mensuel de prospectus électroniques et d'offres spéciales aux clients
-   Collecte de mesures en temps réel concernant des éléments tels que les messages électroniques bloqués et la réactivité vis-à-vis des clients
-   Génération de rapports pour identifier les tendances
-   Transfert des demandes des clients
-   Notifications par courriers électroniques depuis votre application

Pour plus d'informations, consultez la page &lt;http://sendgrid.com&gt;.

Création d'un compte SendGrid
-----------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

Utilisation des bibliothèques javax.mail
----------------------------------------

Obtenez des bibliothèques javax.mail, par exemple depuis &lt;http://www.oracle.com/technetwork/java/javamail&gt; et importez-les dans votre code. Généralement, le processus d'utilisation des bibliothèques javax.mail pour envoyer des messages électroniques via SMTP se déroule comme suit :

1.  Spécifiez les valeurs SMTP, notamment le serveur SMTP qui, dans le cas de SendGrid, est smtp.sendgrid.net.

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
              // ”¦

2.  Développez la classe javax.mail.Authenticator, puis, dans votre implémentation de la méthode getPasswordAuthentication, renvoyez votre nom d'utilisateur et votre mot de passe SendGrid.

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Créez une session de messagerie électronique authentifiée via un objet javax.mail.Session.

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Créez votre message et entrez des valeurs **À**, **De**, **Objet** et du contenu. Cette étape est présentée dans la section [Création d'un courrier électronique](#bkmk_HowToCreateEmail).
5.  Envoyez le message via un objet javax.mail.Transport. Cette étape est présentée dans la section [Envoi d'un courrier électronique](#bkmk_HowToSendEmail).

Création d'un courrier électronique
-----------------------------------

Le code suivant permet d'entrer des valeurs pour un message électronique.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Bonjour, votre commande Contoso a été livrée. Merci, Thomas");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Bonjour,</p>
        <p>Votre commande Contoso a <b>été livrée</b>.</p>
        <p>Merci,<br>Thomas</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("thomas@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("xyz@example.com"));
    message.setSubject("Votre commande récente");
    message.setContent(multipart);

Envoi d'un courrier électronique
--------------------------------

Le code suivant permet d'envoyer un message électronique.

    Transport transport = mailSession.getTransport();
    // Connectez l'objet de transport.
    transport.connect();
    // Envoyez le message.
    transport.sendMessage(message, message.getRecipients(Message.RecipientType.TO));
    // Arrêtez la connexion.
    transport.close();

Ajout d'une pièce jointe
------------------------

Le code suivant permet d'ajouter une pièce jointe.

    // Nom et chemin d'accès du fichier local.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\myfiles\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Indiquez le fichier local à joindre.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // Cet exemple utilise le nom de fichier local en tant que nom de pièce jointe.
    // Si vous le voulez, ils peuvent être différents.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

Utilisation de filtres pour activer les pieds de page, le suivi et l'analyse
----------------------------------------------------------------------------

SendGrid offre des fonctionnalités de messagerie électronique supplémentaires grâce à l'utilisation des *filtres*. Il s'agit de paramètres que vous pouvez ajouter à un message électronique pour activer des fonctionnalités spécifiques telles que le suivi des clics, Google Analytics, le suivi d'abonnement, etc. Pour obtenir une liste exhaustive des filtres, consultez la page [Paramètres de filtre](http://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html).

-   Le code suivant permet d'insérer un filtre de pied de page entraînant l'affichage de texte HTML en bas du message électronique à envoyer.

        message.addHeader("X-SMTPAPI", 
            "{message.addHeader("X-SMTPAPI", 
            "{\"filters\": 
            {\"footer\": 
            {\"settings\": 
            {\"enable\":1,\"text/html\": 
            \"<html><b>Merci</b> pour votre collaboration.</html>\"}}}}");


-   Parmi les exemples de filtres, on peut également citer le suivi des clics. Imaginons que le texte de votre courrier électronique contient un lien hypertexte comme celui qui suit, et que vous voulez suivre le taux de clics :

        messagePart.setContent(
            "Bonjour,
            <p>Ceci est le corps du message. Visitez 
            <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
            Merci.", 
            "text/html");

-   Pour activer le suivi des clics, utilisez le code suivant :

        message.addHeader("X-SMTPAPI", 
            "{message.addHeader("X-SMTPAPI", 
            "{\"filters\": 
            {\"clicktrack\": 
            {\"settings\": 
            {\"enable\":1}}}}");


Mise à jour des propriétés de courrier électronique
---------------------------------------------------

Vous pouvez remplacer certaines propriétés de message électronique en utilisant **set*Property*** ou en ajouter en utilisant **add*Property***.

Par exemple, pour indiquer des adresses **ReplyTo**, utilisez le code suivant :

    InternetAddress addresses[] = 
        { new InternetAddress("thomas@contoso.com"),
          new InternetAddress("denise@contoso.com") };

    message.setReplyTo(addresses);

Pour ajouter un destinataire **Cc**, utilisez le code suivant :

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("thomas@contoso.com"));

Utilisation de services SendGrid supplémentaires
------------------------------------------------

SendGrid propose des API Web qui peuvent vous aider à tirer parti de fonctionnalités SendGrid supplémentaires à partir de votre application Azure. Pour plus d'informations, consultez la [documentation de l'API SendGrid](http://sendgrid.com/docs/API_Reference/index.html).

Étapes suivantes
----------------

Maintenant que vous avez appris les bases du service de messagerie électronique SendGrid, consultez ces liens pour en savoir plus :

-   Exemple montrant comment utiliser SendGrid dans un déploiement Azure : [Envoi de courriers électroniques à l'aide de SendGrid à partir de Java dans un déploiement Azure](/en-us/develop/java/how-to-guides/sendgrid-sample/)
-   Informations sur Java avec SendGrid : &lt;http://sendgrid.com/docs/Code\_Examples/java.html&gt;
-   Documentation de l'API SendGrid : &lt;http://sendgrid.com/docs/API\_Reference/index.html&gt;
-   Offres spéciales SendGrid pour les clients Azure : &lt;http://sendgrid.com/azure.html&gt;


