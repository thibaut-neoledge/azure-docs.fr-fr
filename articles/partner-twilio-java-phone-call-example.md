<properties linkid="develop-java-how-to-twilio-phone-call" urlDisplayName="How to Make a Phone Call from Twilio in Java" pageTitle="How to Make a phone call from Twilio (Java) - Azure" metaKeywords="Azure Twilio call, Twilio call website, Azure Twilio Java" description="Learn how to make a phone call from a web page using Twilio in a Java application on Azure." metaCanonical="" services="" documentationCenter="Java" title="How to Make a Phone Call Using Twilio in a Java Application on Azure" authors="MicrosoftHelp@twilio.com; robmcm" videoId="" scriptId="" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Exécution d'un appel téléphonique à l'aide de Twilio dans une application Java sur Azure

L'exemple qui suit montre comment utiliser Twilio pour passer un appel depuis une page Web hébergée sur Azure. L'application finale demande à l'utilisateur les valeurs de l'appel téléphonique, comme illustré dans la capture d'écran qui suit.

![Formulaire d'appel Azure avec Twilio et Java][Formulaire d'appel Azure avec Twilio et Java]

Pour utiliser le code de cette rubrique, vous devrez effectuer les opérations suivantes :

1.  Obtenir un compte Twilio et un jeton d'authentification. Pour démarrer avec Twilio, évaluez les tarifs sur la page [][]<http://www.twilio.com/pricing></a>. Vous pouvez vous inscrire sur la page [][1]<https://www.twilio.com/try-twilio></a>. Pour plus d'informations sur l'API fournie par Twilio, consultez la page [][2]<http://www.twilio.com/api></a>.
2.  Vérifier votre numéro de téléphone en tant qu'ID d'appelant sortant avec Twilio. Pour plus d'informations sur la vérification d'un numéro de téléphone, consultez la page [][3]<https://www.twilio.com/user/account/phone-numbers/verified>\#</a>. Au lieu d'utiliser un numéro existant, vous pouvez acheter un numéro de téléphone Twilio.
    Dans cet exemple, utilisez le numéro de téléphone vérifié comme valeur **From** de callform.jsp (décrit plus loin).
3.  Obtenir le JAR Twilio. Dans la page [][4]<https://github.com/twilio/twilio-java></a>, vous pouvez télécharger les sources GitHub et créer votre propre JAR, ou télécharger un JAR préconstruit (avec ou sans dépendances).
    Le code de cette rubrique a été écrit à l'aide du JAR préconstruit TwilioJava-3.3.8-with-dependencies.
4.  Ajouter le JAR à votre chemin de build Java.
5.  Si vous utilisez Eclipse pour créer cette application Java, ajoutez le JAR Twilio à votre fichier WAR de déploiement d'application à l'aide de la fonction d'assembly de déploiement d'Eclipse. Si vous n'utilisez pas Eclipse pour créer cette application Java, assurez-vous que le JAR Twilio est inclus dans le même rôle Azure que votre application Java et qu'il est ajouté au chemin de classe de votre application.
6.  Vérifiez que le keystore cacerts de votre JDK contient le certificat ESCA (Equifax Secure Certificate Authority) avec l'empreinte MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (le numéro de série est 35:DE:F4:CF et que l'empreinte de SHA1 est D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Il s'agit du certificat d'autorité de certification pour le service [][5]<https://api.twilio.com></a>, qui est appelé lorsque vous utilisez les API Twilio. Pour plus d'informations sur l'ajout de ce certificat d'autorité de certification au magasin cacert de votre JDK, consultez la page [Ajout d'un certificat au magasin de certificats d'autorité de certification Java][Ajout d'un certificat au magasin de certificats d'autorité de certification Java].

En outre, il est recommandé de bien connaître les informations de la page [Création d'une application simple avec le plug-in Azure pour Eclipse avec Java (par Microsoft Open Technologies)][Création d'une application simple avec le plug-in Azure pour Eclipse avec Java (par Microsoft Open Technologies)] ou bien d'autres techniques d'hébergement pour les applications Java dans Azure si vous n'utilisez pas Eclipse.

## Création d'un formulaire Web pour passer un appel

Le code qui suit présente la conception d'un formulaire Web qui extrait les données des utilisateurs pour passer un appel. Pour cet exemple, un projet Web dynamique **TwilioCloud** a été créé et le fichier JSP **callform.jsp** a été ajouté.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## Création du code pour passer l'appel

Le code qui suit, qui est appelé une fois que l'utilisateur remplit le formulaire affiché par callform.jsp, crée le message d'appel et lance l'appel. Dans cet exemple, le fichier JSP s'appelle **makecall.jsp** et il a été ajouté au projet **TwilioCloud**. Utilisez votre compte Twilio et votre jeton d'authentification plutôt que les valeurs par défaut utilisées dans **accountSID** et **authToken** dans le code qui suit.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

En plus de passer l'appel, makecall.jsp affiche le point de terminaison Twilio, la version de l'API et l'état de l'appel. La capture d'écran suivante présente un exemple :

![Réponse d'appel Azure avec Twilio et Java][Réponse d'appel Azure avec Twilio et Java]

## Exécution de l'application

Voici les étapes générales pour exécuter votre application. Ces étapes sont détaillées sur la page [Création d'une application simple avec le plug-in Azure pour Eclipse avec Java (par Microsoft Open Technologies)][Création d'une application simple avec le plug-in Azure pour Eclipse avec Java (par Microsoft Open Technologies)].

1.  Exportez votre WAR TwilioCloud dans le dossier Azure **approot**.
2.  Modifiez **startup.cmd** pour décompresser votre WAR TwilioCloud.
3.  Compilez votre application pour l'émulateur de calcul.
4.  Démarrez votre déploiement dans l'émulateur de calcul.
5.  Ouvrez un navigateur et accédez à l'adresse **http://localhost:8080/TwilioCloud/callform.jsp**.
6.  Entrez les valeurs dans le formulaire, cliquez sur **Make this call**, puis consultez les résultats dans makecall.jsp.

Dès que vous êtes prêt pour le déploiement sur Azure, effectuez une recompilation pour déploiement dans le cloud, déployez sur Azure et accédez à l'adresse http://*votre_nom_hébergé*.cloudapp.net/TwilioCloud/callform.jsp dans le navigateur (remplacez *votre_nom_hébergé* par votre valeur).

## Étapes suivantes

Ce code vous est fourni afin de vous présenter les fonctions de base de l'utilisation de Twilio dans Java sur Azure. Avant d'effectuer le déploiement de production sur Azure, vous pouvez ajouter d'autres fonctionnalités telles que la gestion des erreurs. Par exemple :

-   Au lieu d'utiliser un formulaire Web, vous pouvez utiliser des objets blob de stockage Azure ou une base de données SQL pour stocker les numéros de téléphone et le texte des appels. Pour plus d'informations sur l'utilisation d'objets blob de stockage Azure dans Java, consultez la page [Utilisation du service de stockage d'objets blob de Java][Utilisation du service de stockage d'objets blob de Java]. Pour plus d'informations sur l'utilisation de la base de données SQL dans Java, consultez la page [Utilisation de bases de données SQL dans Java][Utilisation de bases de données SQL dans Java].
-   Vous pouvez utiliser **RoleEnvironment.getConfigurationSettings** pour récupérer l'ID de compte et le jeton d'authentification Twilio à partir des paramètres de votre déploiement, plutôt que de coder en dur ces valeurs dans makecall.jsp. Pour plus d'informations sur la classe **RoleEnvironment**, consultez la page [Utilisation de la bibliothèque runtime Azure Service dans JSP][Utilisation de la bibliothèque runtime Azure Service dans JSP], ainsi que la documentation du package Azure Service Runtime sur [][6]<http://dl.windowsazure.com/javadoc></a>.
-   Le code makecall.jsp attribue une URL Twilio, [][7]<http://twimlets.com/message></a>, à la variable **Url**. Cette URL fournit une réponse TwiML (Twilio Markup Language) qui précise à Twilio comment traiter l'appel. Par exemple, le code TwiML renvoyé peut contenir un verbe **\<Say\>** qui fait que le texte est lu au destinataire de l'appel. Au lieu d'utiliser l'URL fournie par Twilio, vous pouvez concevoir votre propre service pour répondre à la demande de Twilio. Pour plus d'informations, consultez la page [Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans Java][Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans Java]. Pour plus d'informations sur TwiML, consultez la page [][8]<http://www.twilio.com/docs/api/twiml></a> et pour plus d'informations sur **\<Say\>** et sur les autres verbes Twilio, consultez la page [][9]<http://www.twilio.com/docs/api/twiml/say></a>.
-   Consultez les instructions de sécurité Twilio sur la page [][10]<https://www.twilio.com/docs/security></a>.

Pour plus d'informations sur Twilio, consultez la page [][11][https://www.twilio.com/docs/][https://www.twilio.com/docs/]</a>.

## Voir aussi

-   [Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans Java][Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans Java]
-   [Ajout d'un certificat au magasin de certificats d'autorité de certification Java][Ajout d'un certificat au magasin de certificats d'autorité de certification Java]

  [Formulaire d'appel Azure avec Twilio et Java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
  []: http://www.twilio.com/pricing
  [1]: http://www.twilio.com/try-twilio
  [2]: http://www.twilio.com/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [4]: http://github.com/twilio/twilio-java
  [5]: http://api.twilio.com
  [Ajout d'un certificat au magasin de certificats d'autorité de certification Java]: ../java-add-certificate-ca-store
  [Réponse d'appel Azure avec Twilio et Java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
  [Utilisation du service de stockage d'objets blob de Java]: http://www.windowsazure.com/fr-fr/develop/java/how-to-guides/blob-storage/
  [Utilisation de bases de données SQL dans Java]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh749029.aspx
  [Utilisation de la bibliothèque runtime Azure Service dans JSP]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh690948.aspx
  [6]: http://dl.windowsazure.com/javadoc
  [7]: http://twimlets.com/message
  [Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans Java]: ../partner-twilio-java-how-to-use-voice-sms
  [8]: http://www.twilio.com/docs/api/twiml
  [9]: http://www.twilio.com/docs/api/twiml/say
  [10]: http://www.twilio.com/docs/security
  [11]: http://www.twilio.com/docs
  [https://www.twilio.com/docs/]: https://www.twilio.com/docs
