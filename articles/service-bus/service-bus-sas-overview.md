<properties
   pageTitle="Présentation des signatures d'accès partagé"
   description="Que sont les signatures d'accès partagé, comment fonctionnent-elles et comment les utiliser à partir du nœud, de PHP et C#."
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
   ms.workload="tbd"
   ms.date="07/24/2015"
   ms.author="darosa"/>

# Les signatures d'accès partagé

Les *signatures d'accès partagé* (SAS) constituent le principal mécanisme de sécurité pour Service Bus, y compris les concentrateurs d'événements, la messagerie répartie (files d'attente et rubriques) et la messagerie relayée. Cet article traite des signatures d'accès partagé, leur fonctionnement et comment les utiliser de manière indépendante de la plateforme.

## Présentation des signatures d'accès partagé (SAS)

Les signatures d'accès partagé sont un mécanisme d'authentification basé sur des hachages sécurisés SHA-256 ou des URI. Les signatures d'accès partagé sont un mécanisme extrêmement puissant qui est utilisé par tous les services Service Bus. En fait, les signatures d'accès partagé comptent deux composants : une *stratégie d'accès partagé* et une *signature d'accès partagé* (souvent appelée *jeton*).

Vous trouverez des informations plus détaillées sur les signatures d'accès partagé avec Service Bus à la page [Authentification par signature d'accès partagé avec Service Bus](https://msdn.microsoft.com/library/azure/dn170477.aspx).

## Stratégie d'accès partagé

Concernant les signatures d'accès partagé, il est important de comprendre que tout commence par une stratégie. Pour chaque stratégie, vous choisissez trois éléments d'information : le **nom**, l'**étendue** et les **autorisations**. Le **nom** est un nom unique au sein de cette étendue. L'étendue est l'URI de la ressource en question. Pour un espace de noms Service Bus, l'étendue est le nom de domaine complet (FQDN), tel que **`https://<yournamespace>.servicebus.windows.net/`**.

Les autorisations disponibles pour une stratégie sont relativement explicites :

  + Envoyer
  + Écouter
  + Gérer

Une fois la stratégie créée, une *clé primaire* et une *clé secondaire* lui sont attribuées. Il s'agit de clés de chiffrement fortes. Ne les perdez pas et ne les égarez pas ; elles seront toujours disponibles sur le portail. Vous pouvez utiliser n'importe laquelle des clés générées et vous pouvez les régénérer à tout moment. Toutefois, si vous régénérez ou modifiez la clé primaire dans la stratégie, les signatures d'accès partagé créées à partir de celle-ci ne seront plus valides.

Lorsque vous créez un espace de noms Service Bus, une stratégie est automatiquement créée pour l'espace de noms complet, appelée **RootManageSharedAccessKey** et cette stratégie dispose de toutes les autorisations. Vous ne vous connectez pas en tant que **racine**, donc n'utilisez pas cette stratégie sauf s'il existe une très bonne raison. Vous pouvez créer des stratégies supplémentaires dans l'onglet **Configurer** pour l'espace de noms dans le portail de gestion Azure. Il est important de noter qu'un seul niveau d'arborescence dans Service Bus (espace de noms, file d'attente, concentrateur d'événements, etc.) peut avoir jusqu'à 12 stratégies.

## Signature d'accès partagé (jeton)

La stratégie elle-même n'est pas le jeton d'accès pour Service Bus. C'est l'objet à partir duquel le jeton d'accès est généré, à l'aide de la clé primaire ou de la clé secondaire. Le jeton est généré suite à l'élaboration soigneuse d'une chaîne au format suivant :

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Où `signature-string` correspond au hachage SHA-256 de l'étendue du jeton (\*\*étendue\*\* telle que décrite dans la section précédente) avec un CRLF ajouté et un délai d'expiration (en secondes depuis le début de l'époque : `00:00:00 UTC` le 1er janvier 1970).

Le hachage est similaire au pseudo-code suivant et retourne 32 octets.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Les valeurs non hachées se trouvent dans la chaîne **SharedAccessSignature**, afin que le destinataire puisse calculer le hachage avec les mêmes paramètres, pour s'assurer qu'il retourne le même résultat. L'URI spécifie l'étendue et le nom de la clé identifie la stratégie à utiliser pour calculer le hachage. Ceci est important du point de vue de la sécurité. Si la signature ne correspond pas à celle calculée par le destinataire (Service Bus), l'accès est refusé. À ce stade, nous pouvons être sûrs que l'expéditeur avait accès à la clé et doit bénéficier des droits spécifiés dans la stratégie.

## Génération d'une signature à partir d'une stratégie

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

## Utilisation de la signature d'accès partagé
 
Maintenant que vous savez comment créer des signatures d'accès partagé pour les entités dans Service Bus, vous êtes prêt à effectuer une requête HTTP POST :

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
	
N'oubliez pas que cela fonctionne pour tout. Vous pouvez créer des signatures d'accès partagé pour une file d'attente, une rubrique, un abonnement, un concentrateur d'événements ou un relais. Si vous utilisez l'identité par serveur de publication pour les concentrateurs d'événements, ajoutez simplement `/publishers/< publisherid>`.

Si vous donnez un jeton SAS à un expéditeur ou un client, celui-ci ne dispose pas directement de la clé et il ne peut pas inverser le hachage pour l'obtenir. Ainsi, vous contrôlez le contenu auquel il peut accéder et pour quelle durée. Il est important de ne pas oublier que si vous modifiez la clé primaire dans la stratégie, les signatures d'accès partagé créées à partir de celle-ci ne seront plus valides.

## Étapes suivantes

Consultez la [référence API REST Service Bus](https://msdn.microsoft.com/library/azure/hh780717.aspx) pour plus d'informations sur ce que vous pouvez faire avec ces jetons SAS.

Pour plus d'informations sur les signatures d'accès partagé, consultez le nœud [Authentification Service Bus](https://msdn.microsoft.com/library/azure/dn155925.aspx) sur MSDN.

<!---HONumber=July15_HO5-->