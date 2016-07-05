<properties
    pageTitle="Présentation des signatures d’accès partagé | Microsoft Azure"
    description="Que sont les signatures d’accès partagé, comment fonctionnent-elles et comment les utiliser à partir du nœud, de PHP et C#."
    services="service-bus,event-hubs"
    documentationCenter="na"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/22/2016"
    ms.author="darosa;sethm"/>

# Les signatures d’accès partagé

Les *signatures d’accès partagé* (SAS) constituent le principal mécanisme de sécurité pour Service Bus, y compris les concentrateurs d’événements, la messagerie répartie (files d’attente et rubriques) et la messagerie relayée. Cet article traite des signatures d’accès partagé, leur fonctionnement et comment les utiliser de manière indépendante de la plateforme.

## Présentation des signatures d’accès partagé (SAS)

Les signatures d’accès partagé sont un mécanisme d’authentification basé sur des hachages sécurisés SHA-256 ou des URI. Les signatures d’accès partagé sont un mécanisme extrêmement puissant qui est utilisé par tous les services Service Bus. En fait, les signatures d’accès partagé comptent deux composants : une *stratégie d’accès partagé* et une *signature d’accès partagé* (souvent appelée *jeton*).

Vous trouverez des informations plus détaillées sur les signatures d’accès partagé avec Service Bus à la page [Authentification par signature d’accès partagé avec Service Bus](service-bus-shared-access-signature-authentication.md).

## Stratégie d’accès partagé

Concernant les signatures d’accès partagé, il est important de comprendre que tout commence par une stratégie. Pour chaque stratégie, vous choisissez trois éléments d’information : le **nom**, l’**étendue** et les **autorisations**. Le **nom** est un nom unique au sein de cette étendue. L’étendue est l’URI de la ressource en question. Pour un espace de noms Service Bus, l’étendue est le nom de domaine complet (FQDN), tel que **`https://<yournamespace>.servicebus.windows.net/`**.

Les autorisations disponibles pour une stratégie sont relativement explicites :

  + Envoyer
  + Écouter
  + Gérer

Une fois la stratégie créée, une *clé primaire* et une *clé secondaire* lui sont attribuées. Il s’agit de clés de chiffrement fortes. Ne les perdez pas et ne les divulguez pas ; elles seront toujours disponibles sur le [portail Azure Classic][]. Vous pouvez utiliser n’importe laquelle des clés générées et vous pouvez les régénérer à tout moment. Toutefois, si vous régénérez ou modifiez la clé primaire dans la stratégie, les signatures d’accès partagé créées à partir de celle-ci ne seront plus valides.

Lorsque vous créez un espace de noms Service Bus, une stratégie nommée **RootManageSharedAccessKey** est automatiquement créée pour l’espace de noms entier, qui dispose de toutes les autorisations. Étant donné que vous ne vous connectez pas en tant que **racine**, n’utilisez cette stratégie que si vous avez une très bonne raison de le faire. Vous pouvez créer des stratégies supplémentaires sous l’onglet **Configurer** pour l’espace de noms dans le portail. Il est important de noter qu’un seul niveau d’arborescence dans Service Bus (espace de noms, file d’attente, concentrateur d’événements, etc.) peut avoir jusqu’à 12 stratégies.

## Signature d’accès partagé (jeton)

La stratégie elle-même n’est pas le jeton d’accès pour Service Bus. C’est l’objet à partir duquel le jeton d’accès est généré, à l’aide de la clé primaire ou de la clé secondaire. Le jeton est généré suite à l’élaboration soigneuse d’une chaîne au format suivant :

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Où `signature-string` correspond au hachage SHA-256 de l’étendue du jeton (**étendue** telle que décrite dans la section précédente) avec un CRLF ajouté et un délai d’expiration (en secondes depuis le début de l’époque : `00:00:00 UTC` le 1er janvier 1970).

Le hachage est similaire au pseudo-code suivant et retourne 32 octets.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Les valeurs non hachées se trouvent dans la chaîne **SharedAccessSignature** afin que le destinataire puisse calculer le hachage avec les mêmes paramètres, pour s’assurer qu’il renvoie le même résultat. L’URI spécifie l’étendue et le nom de la clé identifie la stratégie à utiliser pour calculer le hachage. Ceci est important du point de vue de la sécurité. Si la signature ne correspond pas à celle calculée par le destinataire (Service Bus), l’accès est refusé. À ce stade, vous pouvez être sûr que l’expéditeur avait accès à la clé et doit bénéficier des droits spécifiés dans la stratégie.

## Génération d’une signature à partir d’une stratégie

Comment réellement procéder dans le code ? Examinons quelques exemples.

### NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time(); 	
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256', 			
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires . 		"&skn=" . $sasKeyName; 
return $token; 
}
```
 
### C&#35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## Utilisation de la signature d’accès partagé (au niveau HTTP)
 
Maintenant que vous savez comment créer des signatures d’accès partagé pour les entités dans Service Bus, vous êtes prêt à effectuer une requête HTTP POST :

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
	
N’oubliez pas que cela fonctionne pour tout. Vous pouvez créer des signatures d’accès partagé pour une file d’attente, une rubrique, un abonnement, un concentrateur d’événements ou un relais. Si vous utilisez une identité par serveur de publication pour Event Hubs, ajoutez simplement `/publishers/< publisherid>`.

Si vous donnez un jeton SAS à un expéditeur ou un client, celui-ci ne dispose pas directement de la clé et il ne peut pas inverser le hachage pour l’obtenir. Ainsi, vous contrôlez le contenu auquel il peut accéder et pour quelle durée. Il est important de ne pas oublier que si vous modifiez la clé primaire dans la stratégie, les signatures d’accès partagé créées à partir de celle-ci ne seront plus valides.

## Utilisation de la signature d’accès partagé (au niveau AMQP)

Dans la section précédente, vous avez vu comment utiliser le jeton SAS avec une requête HTTP POST pour envoyer des données au Service Bus. Comme vous le savez, vous pouvez accéder au Service Bus à l’aide du protocole AMQP (Advanced Message Queuing Protocol) qui est le protocole privilégié pour des raisons de performances dans de nombreux scénarios. L’utilisation de jetons SAP avec AMQP est décrite dans le document [AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc). Bien qu’au stade d’ébauche depuis 2013, elle est bien prise en charge par Azure aujourd’hui.

Avant de commencer à envoyer des données vers Service Bus, le serveur de publication doit envoyer le jeton SAP dans un message AMQP à un nœud AMQP bien défini nommé **« $cbs »** (il s’agit d’une sorte de file d’attente « spéciale » que le service utilise pour acquérir et valider tous les jetons SAP). Le serveur de publication doit spécifier le champ **« ReplyTo »** dans le message AMQP. Il s’agit du nœud sur lequel le service répond au serveur de publication avec le résultat de la validation du jeton (système de demande/réponse simple entre le serveur de publication et le service). Ce nœud de réponse est créé « à la volée » en ce qui concerne la « création dynamique du nœud à distance », comme le décrit la spécification AMQP 1.0. Après avoir vérifié que le jeton SAS est valide, le serveur de publication peut continuer et commencer à envoyer des données au service.

Les étapes suivantes montrent comment envoyer le jeton SAP avec le protocole AMQP à l'aide de la bibliothèque [AMQP.Net Lite](https://github.com/Azure/amqpnetlite). C’est utile si vous ne pouvez pas utiliser le Kit de développement logiciel (SDK) Service Bus officiel (par exemple sur WinRT, .NET Compact Framework, .NET Micro Framework et Mono) pour le développement en C#. Bien évidemment, cette bibliothèque est utile pour comprendre comment la sécurité basée sur les revendications fonctionne au niveau AMQP, tout comme vous avez pu voir comment cela fonctionne au niveau HTTP (avec une demande HTTP POST et le jeton SAP envoyé dans l’en-tête « Autorisation »). Si vous n’avez pas besoin de ces connaissances aussi approfondies concernant AMQP, vous pouvez utiliser le Kit de développement logiciel (SDK) Service Bus officiel avec des applications .NET Framework qui s’en occuperont pour vous.

### C&#35;

```
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

La méthode `PutCbsToken()` reçoit la *connexion* (instance de classe de connexion AMQP telle que fournie par la [bibliothèque AMQP .NET Lite](https://github.com/Azure/amqpnetlite)) qui représente la connexion TCP au service et le paramètre *sasToken* correspondant au jeton SAP à envoyer.

> [AZURE.NOTE] Il est important que la connexion soit créée avec le **mécanisme d’authentification SASL défini sur EXTERNAL** (et non sur le paramètre par défaut PLAIN avec nom d’utilisateur et mot de passe utilisé lorsque vous n’avez pas besoin d’envoyer le jeton SAP).

Le serveur de publication crée ensuite deux liens AMQP pour envoyer le jeton SAP et recevoir la réponse (résultat de validation du jeton) depuis le service.

Le message AMQP inclut un ensemble de propriétés et plus d’informations qu’un simple message. Le jeton SAP est le corps du message (à l’aide de son constructeur). La propriété **« ReplyTo »** est définie sur le nom du nœud permettant de recevoir le résultat de validation sur le lien du récepteur (vous pouvez modifier son nom si vous le souhaitez, il sera créé dynamiquement par le service). Les trois dernières propriétés personnalisées / application sont utilisées par le service pour indiquer le type d’opération à exécuter. Comme indiqué dans le projet de spécification CBS, il doit s’agir du **nom de l’opération** (« put-token »), du **type de jeton** (dans ce cas, « servicebus.windows.net:sastoken ») et du **« nom » de l’audience** à laquelle le jeton s’applique (entité entière).

Après avoir envoyé le jeton SAP sur le lien de l’expéditeur, le serveur de publication doit lire la réponse sur le lien du récepteur. La réponse est un simple message AMQP avec une propriété d’application nommée **« status-code »** qui peut contenir les mêmes valeurs qu’un code d’état HTTP.

## Étapes suivantes

Consultez la [référence API REST Service Bus](https://msdn.microsoft.com/library/azure/hh780717.aspx) pour plus d’informations sur ce que vous pouvez faire avec ces jetons SAS.

Pour plus d’informations sur l’authentification avec Service Bus, consultez [Configuration de l’authentification et de l’autorisation Service Bus](service-bus-authentication-and-authorization.md).

Pour obtenir d’autres exemples de signature d’accès partagé en C# et JavaScript, consultez [ce billet de blog](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx).

[portail Azure Classic]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0622_2016-->