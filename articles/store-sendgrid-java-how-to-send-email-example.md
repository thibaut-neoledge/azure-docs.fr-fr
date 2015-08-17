<properties 
	pageTitle="store-sendgrid-java-how-to-send-email-example" 
	description="Envoi de courriers électroniques à l'aide de SendGrid à partir de Java dans un déploiement Azure" 
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
	ms.author="vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com"/>

# Envoi de courriers électroniques à l'aide de SendGrid à partir de Java dans un déploiement Azure

L'exemple qui suit montre comment vous pouvez utiliser SendGrid pour envoyer des courriers électroniques depuis une page Web hébergée sur Azure. L'application finale demande à l'utilisateur les valeurs du courrier électronique, comme illustré dans la capture d'écran qui suit.

![Formulaire de courrier électronique][emailform]

Le courrier électronique obtenu est semblable à la capture d'écran suivante.

![Message électronique][emailsent]

Pour utiliser le code de cette rubrique, vous devrez effectuer les opérations suivantes :

1. Obtenez les JAR javax.mail, par exemple depuis <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Ajoutez les JAR à votre chemin de build Java.
3. Si vous utilisez Eclipse pour créer cette application Java, vous pouvez ajouter les bibliothèques SendGrid à votre fichier WAR de déploiement d'application à l'aide de la fonction d'assembly de déploiement d'Eclipse. Si vous n'utilisez pas Eclipse pour créer cette application Java, assurez-vous que les bibliothèques sont incluses dans le même rôle Azure que votre application Java et qu'elles sont ajoutées au chemin de classe de votre application.


Vous devez également avoir vos propres nom d'utilisateur et mot de passe SendGrid pour pouvoir envoyer le courrier électronique. Pour vos premiers pas avec SendGrid, consultez la page [Envoi de courriers électroniques à l'aide de SendGrid depuis Java](store-sendgrid-java-how-to-send-email.md).

En outre, il est recommandé de bien connaître les informations de la page [Création d'une application Hello World pour Azure dans Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944) ou bien d'autres techniques d'hébergement pour les applications Java dans Azure si vous n'utilisez pas Eclipse.

## Création d'un formulaire Web pour l'envoi de courriers électroniques

Le code qui suit présente la conception d'un formulaire Web qui extrait les données des utilisateurs pour envoyer des courriers électroniques. Dans le cadre du présent contenu, le fichier JSP est nommé **emailform.jsp**.

	<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
	    pageEncoding="ISO-8859-1" %>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
	<title>Email form</title>
	</head>
	<body>
	 <p>Fill in all fields and click <b>Send this email</b>.</p>
	 <br/>
	  <form action="sendemail.jsp" method="post">
	   <table>
	     <tr>
	       <td>To:</td>
	       <td><input type="text" size=50 name="emailTo">
	       </td>
	     </tr>
	     <tr>
	       <td>From:</td>
	       <td><input type="text" size=50 name="emailFrom">
	       </td>
	     </tr>
	     <tr>
	       <td>Subject:</td>
	       <td><input type="text" size=100 name="emailSubject" value="My email subject">
	       </td>
	     </tr>
	     <tr>
	       <td>Text:</td>
	       <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
	       </td>
	     </tr>
	     <tr>
	       <td>SendGrid user name:</td>
	       <td><input type="text" name="sendGridUser">
	       </td>
	     </tr>
	     <tr>
	       <td>SendGrid password:</td>
	       <td><input type="password" name="sendGridPassword">
	       </td>
	     </tr>
	     <tr>
	       <td colspan=2><input type="submit" value="Send this email">
	       </td>
	     </tr>
	   </table>
	 </form>
	 <br/>
	</body>
	</html>

## Création du code pour envoyer le courrier électronique

Le code suivant, qui est appelé lorsque vous remplissez le formulaire dans emailform.jsp, crée le courrier électronique et l'envoie. Dans le cadre du présent contenu, le fichier JSP est nommé **sendemail.jsp**.

	<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
	    pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
	<title>Email processing happens here</title>
	</head>
	<body>
	    <b>This is my send mail page.</b><p/>
	 <%
	 
	 final String sendGridUser = request.getParameter("sendGridUser");
	 final String sendGridPassword = request.getParameter("sendGridPassword");
	 
	 class SMTPAuthenticator extends Authenticator
	 {
	   public PasswordAuthentication getPasswordAuthentication()
	   {
	        String username = sendGridUser;
	        String password = sendGridPassword;
	      
	        return new PasswordAuthentication(username, password);   
	   }
	 }
	 try
	 {
	     
	     // The SendGrid SMTP server.
	     String SMTP_HOST_NAME = "smtp.sendgrid.net";
	
	     Properties properties;
	    
	     properties = new Properties();
	     
	     // Specify SMTP values.
	     properties.put("mail.transport.protocol", "smtp");
	     properties.put("mail.smtp.host", SMTP_HOST_NAME);
	     properties.put("mail.smtp.port", 587);
	     properties.put("mail.smtp.auth", "true");
	     
	     // Display the email fields entered by the user. 
	     out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
	     out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
	     out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
	     out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");
	
	     // Create the authenticator object.
	     Authenticator authenticator = new SMTPAuthenticator();
	     
	     // Create the mail session object.
	     Session mailSession;
	     mailSession = Session.getDefaultInstance(properties, authenticator);
	     
	     // Display debug information to stdout, useful when using the
	     // compute emulator during development.
	     mailSession.setDebug(true);
	
	     // Create the message and message part objects.
	     MimeMessage message;
	     Multipart multipart;
	     MimeBodyPart messagePart; 
	     
	     message = new MimeMessage(mailSession);
	     
	     multipart = new MimeMultipart("alternative");
	     messagePart = new MimeBodyPart();
	     messagePart.setContent(request.getParameter("emailText"), "text/html");
	     multipart.addBodyPart(messagePart);            
	
	     // Specify the email To, From, Subject and Content. 
	     message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
	     message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
	     message.setSubject(request.getParameter("emailSubject")); 
	     message.setContent(multipart);
	     
	     // Uncomment the following if you want to add a footer.
	     // message.addHeader("X-SMTPAPI", "{"filters": {"footer": {"settings": {"enable":1,"text/html": "<html>This is my <b>email footer</b>.</html>"}}}}");
	
	     // Uncomment the following if you want to enable click tracking.
	     // message.addHeader("X-SMTPAPI", "{"filters": {"clicktrack": {"settings": {"enable":1}}}}");
	     
	     Transport transport;
	     transport = mailSession.getTransport();
	     // Connect the transport object.
	     transport.connect();
	     // Send the message.
	     transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
	     // Close the connection.
	     transport.close();
	 
	    out.println("<p>Email processing completed.</p>");
	     
	 }
	 catch (Exception e)
	 {
	     out.println("<p>Exception encountered: " + 
	                        e.getMessage()     +
	                        "</p>");   
	 }
	%>
	
	</body>
	</html>

En plus d'envoyer le courrier électronique, emailform.jsp fournit un résultat à l'utilisateur. La capture d'écran suivante présente un exemple :

![Résultat de l'envoi d'un courrier électronique][emailresult]

## Étapes suivantes

Déployez votre application sur l'émulateur de calcul, puis dans un navigateur, exécutez emailform.jsp, entrez les valeurs dans le formulaire, cliquez sur **Send this email**, puis consultez les résultats dans sendemail.jsp.

Ce code vous est fourni afin de vous montrer comment utiliser SendGrid dans Java sur Azure. Avant d'effectuer le déploiement de production sur Azure, vous pouvez ajouter d'autres fonctionnalités telles que la gestion des erreurs. Par exemple :

* Vous pouvez utiliser des objets blob de stockage Azure ou une base de données SQL pour stocker les adresses de messagerie et les courriers électroniques, plutôt que d'utiliser un formulaire Web. Pour plus d'informations sur l'utilisation d'objets blob de stockage Azure dans Java, consultez la page [Utilisation du service de stockage d'objets blob de Java](http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/). Pour plus d'informations sur l'utilisation de la base de données SQL dans Java, consultez la page [Utilisation de bases de données SQL dans Java](http://www.windowsazure.com/develop/java/how-to-guides/using-sql-azure-in-java/).
* Vous pouvez utiliser `RoleEnvironment.getConfigurationSettings` pour extraire le nom d'utilisateur et le mot de passe SendGrid à partir des paramètres de configuration de votre déploiement, au lieu d'utiliser un formulaire Web. Pour plus d'informations sur la classe `RoleEnvironment`, consultez la page [Utilisation de la bibliothèque runtime Azure Service dans JSP](http://msdn.microsoft.com/library/windowsazure/hh690948), ainsi que la documentation du package Azure Service Runtime sur <http://dl.windowsazure.com/javadoc>.
* Pour plus d'informations sur l'utilisation de SendGrid dans Java, consultez la page [Envoi de courriers électroniques à l'aide de SendGrid depuis Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg

<!---HONumber=August15_HO6-->