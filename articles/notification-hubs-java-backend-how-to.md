<properties linkid="notification-hubs-java-back-end-how-to" urlDisplayName="How to use Notification Hubs with Java" pageTitle="How to use Notification Hubs with Java" metaKeywords="" description="Learn how to use Azure Notification Hubs from a Java back-end." metaCanonical="" services="mobile-services,notification-hubs,push,java" documentationCenter="" title="How to use Notification Hubs with Java" authors="elioda" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="elioda"></tags>

# Utilisation de Notification Hubs à partir de Java/PHP

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/fr-fr/documentation/articles/notification-hubs-java-backend-how-to/" title="Java" class="current">Java</a><a href="/fr-fr/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a>
</div>

Vous pouvez accéder à toutes les fonctionnalités de Notification Hubs à partir d'un serveur principal Java/PHP/Ruby en utilisant l'interface REST des concentrateurs de notifications, comme décrit dans la rubrique MSDN [API REST de Notification Hubs][API REST de Notification Hubs].

Dans cette rubrique, nous vous montrons comment :

-   créer un client REST pour les fonctionnalités Notification Hubs en Java ;
-   suivre le [didacticiel de prise en main][didacticiel de prise en main] pour la plateforme mobile de votre choix, en implémentant la partie concernant le serveur principal en Java.

## <a name="client-interface"></a>Interface client

L'interface client principale peut fournir les mêmes méthodes que celles disponibles dans le [Kit de développement logiciel (SDK) .NET Notification Hubs][Kit de développement logiciel (SDK) .NET Notification Hubs], ce qui vous permet de traduire directement l'ensemble des didacticiels et des exemples actuellement disponibles sur ce site, enrichis par les contributions de la communauté Internet.

Tout le code est disponible dans l'[exemple de wrapper REST Java][exemple de wrapper REST Java].

Par exemple, pour créer un client :

    new NotificationHub("connection string", "hubname");    

Pour créer une inscription iOS (analogue pour Windows, Android, Windows Phone et Kindle Fire) :

    String id = hub.createRegistrationId();
    AppleRegistration reg = new AppleRegistration(id, DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.upsertRegistration(reg);

Pour envoyer une notification iOS native :

    Notification n = Notification.createAppleNotifiation("APNS body");
    hub.sendNotification(n);

## <a name="implementation"></a>Implémentation

Si vous ne l'avez pas déjà fait, suivez le [Didacticiel de prise en main][didacticiel de prise en main] jusqu'à la dernière section, où vous devez implémenter le serveur principal.
En outre, vous pouvez, si vous le souhaitez, utiliser le code de l'[exemple de wrapper REST Java][exemple de wrapper REST Java] et passer directement à la section [Suivi du didacticiel][Suivi du didacticiel].

Tous les détails de l'implémentation d'un wrapper REST complet se trouvent sur [MSDN][MSDN]. Dans cette section, nous allons décrire l'implémentation Java des principales étapes requises pour accéder aux points de terminaison REST de Notification Hubs :

1.  Analyse de la chaîne de connexion
2.  Génération du jeton d'autorisation
3.  Réalisation de l'appel HTTP

Dans les extraits de code suivants, nous utilisons les composants suivants :

-   [Apache HttpComponents][Apache HttpComponents]
-   [Apache Commons-Codec][Apache Commons-Codec]
-   [Apache Commons-Io][Apache Commons-Io]

### Analyse de la chaîne de connexion

Voici la classe principale implémentant le client, dont le constructeur analyse la chaîne de connexion :

    public class NotificationHub {

        private static final String APIVERSION = "?api-version=2013-10";
        private static final String CONTENT_LOCATION_HEADER = "Location";
        private String endpoint;
        private String hubPath;
        private String SasKeyName;
        private String SasKeyValue;

        private HttpClient httpClient;

        public NotificationHub(String connectionString, String hubPath) {
            this.httpClient = HttpClients.createDefault();
            this.hubPath = hubPath;

            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);

            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.endpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.SasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.SasKeyValue = parts[i].substring(16);
                }
            }
        }
    }

### Création du jeton de sécurité

Les détails de la création du jeton de sécurité sont disponibles [ici][ici].
La méthode suivante doit être ajoutée à la classe **NotificationHub** pour créer le jeton à partir de l'URI de la demande actuelle et des informations d'identification extraites de la chaîne de connexion.

    private String generateSasToken(URI uri) {
        String targetUri;
        try {
            targetUri = URLEncoder
                    .encode(uri.toString().toLowerCase(), "UTF-8")
                    .toLowerCase();

            long expiresOnDate = System.currentTimeMillis();
            int expiresInMins = 60; // 1 hour
            expiresOnDate += expiresInMins * 60 * 1000;
            long expires = expiresOnDate / 1000;
            String toSign = targetUri + "\n" + expires;

            // Get an hmac_sha1 key from the raw key bytes
            byte[] keyBytes = SasKeyValue.getBytes("UTF-8");
            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

            // Get an hmac_sha1 Mac instance and initialize with the signing key
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);

            // Compute the hmac on input data bytes
            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            // Convert raw bytes to Hex
            String signature = URLEncoder.encode(
                    Base64.encodeBase64String(rawHmac), "UTF-8");

            // construct authorization string
            String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                    + signature + "&se=" + expires + "&skn=" + SasKeyName;
            return token;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

### Envoi d'une notification

Commençons par définir une classe représentant une notification.

    import java.util.HashMap;
    import java.util.Iterator;
    import java.util.Map;
    import org.apache.http.entity.ContentType;

    public class Notification {
        private Map<String, String> headers = new HashMap<String, String>();
        private String body;
        private ContentType contentType;

        public static Notification createWindowsNotification(String body) {
            Notification n = new Notification();
            n.body = body;
            n.headers.put("ServiceBusNotification-Format", "windows");

            if (body.contains("<toast>"))
                n.headers.put("X-WNS-Type", "wns/toast");
            if (body.contains("<tile>"))
                n.headers.put("X-WNS-Type", "wns/tile");
            if (body.contains("<badge>"))
                n.headers.put("X-WNS-Type", "wns/badge");
            if (body.startsWith("<")) {
                n.contentType = ContentType.APPLICATION_XML;
            }
            return n;
        }

        public static Notification createAppleNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "apple");
            return n;
        }

        public static Notification createGcmNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "gcm");
            return n;
        }

        public static Notification createAdmNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "adm");
            return n;
        }

        public static Notification createMpnsNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.headers.put("ServiceBusNotification-Format", "windowsphone");

            if (body.contains("<wp:Toast>")) {
                n.headers.put("X-WindowsPhone-Target", "toast");
                n.headers.put("X-NotificationClass", "2");
            }
            if (body.contains("<wp:Tile>")) {
                n.headers.put("X-WindowsPhone-Target", "tile");
                n.headers.put("X-NotificationClass", "1");
            }
            if (body.startsWith("<")) {
                n.contentType = ContentType.APPLICATION_XML;
            }
            return n;
        }

        public static Notification createTemplateNotification(
                Map<String, String> properties) {
            Notification n = new Notification();
            StringBuffer buf = new StringBuffer();
            buf.append("{");
            for (Iterator<String> iterator = properties.keySet().iterator(); iterator
                    .hasNext();) {
                String key = iterator.next();
                buf.append("\"" + key + "\":\"" + properties.get(key) + "\"");
                if (iterator.hasNext())
                    buf.append(",");
            }
            buf.append("}");
            n.body = buf.toString();
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "template");
            return n;
        }

        public Map<String, String> getHeaders() { return headers; }

        public void setHeaders(Map<String, String> headers) { this.headers = headers; }

        public String getBody() { return body; }

        public void setBody(String body) { this.body = body; }

        public ContentType getContentType() { return contentType; }

        public void setContentType(ContentType contentType) { this.contentType = contentType; }
    }

Cette classe est un conteneur pour un corps de notification natif ou un ensemble de propriétés dans le cas d'un modèle de notification, et un ensemble d'en-têtes contenant le format (plateforme native ou modèle) et des propriétés spécifiques de la plateforme (telles que la propriété expiration d'Apple et les en-têtes WNS). Nous définissons également des constructeurs pratiques pour générer les types de notification couramment utilisés.

Pour connaître toutes les options disponibles, reportez-vous à la [documentation sur les API REST de Notification Hubs][documentation sur les API REST de Notification Hubs] et aux formats spécifiques des plateformes de notification.

Munis de cette classe, nous pouvons à présent écrire les méthodes d'envoi des notifications à l'intérieur de la classe **NotificationHub**.

    public void sendNotification(Notification notification) {
        sendNotification(notification, "");
    }

    public void sendNotification(Notification notification, Set<String> tags) {
        if (tags.isEmpty())
            throw new IllegalArgumentException(
                    "tags has to contain at least an element");

        StringBuffer exp = new StringBuffer();
        for (Iterator<String> iterator = tags.iterator(); iterator.hasNext();) {
            exp.append(iterator.next());
            if (iterator.hasNext())
                exp.append(" || ");
        }

        sendNotification(notification, exp.toString());
    }

    public void sendNotification(Notification notification, String tagExpression) {
        HttpPost post = null;
        try {
            URI uri = new URI(endpoint + hubPath + "/messages" + APIVERSION);
            post = new HttpPost(uri);
            post.setHeader("Authorization", generateSasToken(uri));

            if (tagExpression != null && !"".equals(tagExpression)) {
                post.setHeader("ServiceBusNotification-Tags", tagExpression);
            }

            for (String header : notification.getHeaders().keySet()) {
                post.setHeader(header, notification.getHeaders().get(header));
            }

            post.setEntity(new StringEntity(notification.getBody()));
            HttpResponse response = httpClient.execute(post);

            if (response.getStatusLine().getStatusCode() != 201) {
                String msg = "";
                if (response.getEntity() != null
                        && response.getEntity().getContent() != null) {
                    msg = IOUtils.toString(response.getEntity().getContent());
                }
                throw new RuntimeException("Error: " + response.getStatusLine()
                        + " body: " + msg);
            }

        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            if (post != null)
                post.releaseConnection();
        }
    }

Les méthodes ci-dessus envoient une demande POST HTTP au point de terminaison /messages de votre concentrateur de notification, avec le corps et les en-têtes corrects pour envoyer la notification.

## <a name="complete-tutorial"></a>Suivi du didacticiel

Vous pouvez à présent suivre le didacticiel de prise en main en envoyant la notification à partir d'un serveur principal Java.

Initialisez votre client Notification Hubs (remplacez la chaîne de connexion et le nom du concentrateur comme indiqué dans le [didacticiel de prise en main][didacticiel de prise en main]) :
 NotificationHub hub = new NotificationHub("{connection string}", "{hubname}");

Ajoutez ensuite le code d'envoi en fonction de la plateforme mobile cible.

### Windows Store et Windows Phone 8.1 (non-Silverlight)

    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);

### iOS

    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);

### Android

    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createGcmNotification(message);
    hub.sendNotification(n);

### Windows Phone 8.0 et 8.1 Silverlight

    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);

### Kindle Fire

    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);

L'exécution de votre code Java produit normalement une notification qui apparaît sur votre appareil cible.

## <a name="next-steps"></a>Étapes suivantes

Dans cette rubrique, nous vous avons montré comment créer un client REST Java simple pour Notification Hubs. À ce stade, vous pouvez :

-   télécharger l'intégralité de l'[exemple de wrapper REST Java][exemple de wrapper REST Java], qui contient tout le code ci-dessus plus la gestion de l'inscription ;
-   poursuivre l'apprentissage de la fonction de balisage dans le [didacticiel Nouvelles de dernière minute] ;
-   vous familiariser avec l'envoi de notifications Push à des utilisateurs individuels dans le [didacticiel Envoi de notifications à des utilisateurs]

  [Java]: /fr-fr/documentation/articles/notification-hubs-java-backend-how-to/ "Java"
  [PHP]: /fr-fr/documentation/articles/notification-hubs-php-backend-how-to/ "PHP"
  [API REST de Notification Hubs]: http://msdn.microsoft.com/fr-fr/library/dn223264.aspx
  [didacticiel de prise en main]: http://azure.microsoft.com/fr-fr/documentation/articles/notification-hubs-ios-get-started/
  [Kit de développement logiciel (SDK) .NET Notification Hubs]: http://msdn.microsoft.com/fr-fr/library/jj933431.aspx
  [exemple de wrapper REST Java]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-java
  [Suivi du didacticiel]: #complete-tutorial
  [MSDN]: http://msdn.microsoft.com/fr-fr/library/dn530746.aspx
  [Apache HttpComponents]: http://hc.apache.org/httpcomponents-client-ga/
  [Apache Commons-Codec]: http://commons.apache.org/proper/commons-codec/
  [Apache Commons-Io]: http://commons.apache.org/proper/commons-io/
  [ici]: http://msdn.microsoft.com/fr-fr/library/dn495627.aspx
  [documentation sur les API REST de Notification Hubs]: http://msdn.microsoft.com/fr-fr/library/dn495827.aspx
