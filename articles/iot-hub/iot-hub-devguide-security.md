<properties
 pageTitle="Guide du développeur - Contrôler l’accès à un IoT Hub | Microsoft Azure"
 description="Guide du développeur IoT Hub Azure - Comment contrôler l’accès à IoT Hub et gérer la sécurité"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>


# <a name="control-access-to-iot-hub"></a>Contrôler l’accès à IoT Hub

## <a name="overview"></a>Vue d’ensemble

Cet article décrit les options de sécurisation de votre IoT Hub. IoT Hub utilise des *autorisations* pour accorder l’accès à chaque point de terminaison IoT Hub. Les autorisations limitent l’accès à un hub IoT selon la fonctionnalité.

Cet article explique :

- les différentes autorisations que vous pouvez accorder à une application d’appareil ou de serveur principal pour accéder à votre IoT Hub ;
- le processus d’authentification et les jetons qu’il utilise pour vérifier les autorisations ;
- comment définir l’étendue des informations d’identification pour limiter l’accès à des ressources spécifiques ;
- la prise en charge par IoT Hub des certificats X.509 ;
- les mécanismes d’authentification d’appareil personnalisés qui utilisent des registres d’identités d’appareil ou des schémas d’authentification existants.

### <a name="when-to-use"></a>Quand utiliser

Pour accéder à tout point de terminaison IoT Hub, vous devez disposer des autorisations appropriées. Par exemple, un appareil doit inclure un jeton contenant des informations d’identification de sécurité dans chaque message envoyé à IoT Hub.

## <a name="access-control-and-permissions"></a>Contrôle d’accès et autorisations

Vous pouvez accorder des [autorisations](#iot-hub-permissions) de différentes manières :

* **Stratégies d’accès partagé au niveau du concentrateur**. Les stratégies d’accès partagé peuvent accorder n’importe quelle combinaison d’[autorisations](#iot-hub-permissions). Vous pouvez définir des stratégies dans le [portail de gestion Azure][lnk-management-portal], ou par programmation à l’aide des [API du fournisseur de ressources Azure IoT Hub][lnk-resource-provider-apis]. Un hub IoT qui vient d’être créé a les stratégies par défaut suivantes :

    - **iothubowner**: stratégie jouissant de toutes les autorisations.
    - **service**: stratégie jouissant de l’autorisation ServiceConnect.
    - **device**: stratégie jouissant de l’autorisation DeviceConnect.
    - **registryRead**: stratégie jouissant de l’autorisation RegistryRead.
    - **registryReadWrite**: stratégie jouissant des autorisations RegistryRead et RegistryWrite.


* **Informations d’identification de sécurité par appareil**. Chaque IoT Hub contient un [registre des identités des appareils][lnk-identity-registry]. Pour chaque appareil figurant dans ce registre, vous pouvez configurer des informations d’identification de sécurité qui accordent des autorisations **DeviceConnect** incluses dans l’étendue des points de terminaison des appareils correspondants.

Par exemple, dans une solution IoT classique :
- Le composant de gestion des appareils utilise la stratégie *registryReadWrite* .
- Le composant de processeur d’événements utilise la stratégie *service* .
- Le composant de logique métier des appareils d’exécution utilise la stratégie *service* .
- Les appareils individuels se connectent à l’aide d’informations d’identification stockées dans le registre des identités du hub IoT.

## <a name="authentication"></a>Authentification

Azure IoT Hub accorde l’accès aux points de terminaison en vérifiant un jeton par rapport aux stratégies d’accès partagé et aux informations d’identification de sécurité du registre des identités des appareils.

Les informations d’identification de sécurité telles que les clés symétriques ne sont jamais envoyées sur le réseau.

> [AZURE.NOTE] Le fournisseur de ressources Azure IoT Hub est sécurisé au moyen de votre abonnement Azure, à l’instar de tous les fournisseurs dans [Azure Resource Manager][lnk-azure-resource-manager].

Pour plus d’informations sur la construction et l’utilisation des jetons de sécurité, voir [Jetons de sécurité IoT Hub][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Spécifications de protocole

Chaque protocole pris en charge (par exemple, MQTT, AMQP et HTTP) transporte des jetons de différentes façons.

Lorsque vous utilisez MQTT, le paquet CONNECT utilise deviceid en tant que ClientId, {iothubhostname}/{deviceId} dans le champ Nom d’utilisateur et un jeton SAP dans le champ Mot de passe. {iothubhostname} doit être le nom canonique (CNAME) complet d’IoT Hub (par exemple, contoso.azure-devices.net).

Lorsque vous utilisez [AMQP][lnk-amqp], IoT Hub prend en charge [SASL PLAIN][lnk-sasl-plain] et la [sécurité basée sur des revendications AMQPy][lnk-cbs].

Si vous utilisez une sécurité basée sur des revendications AMQP, la norme indique comment transmettre les jetons répertoriés ci-dessus.

Pour SASL PLAIN, le **nom d’utilisateur** peut être :

* `{policyName}@sas.root.{iothubName}` si vous utilisez des jetons au niveau du hub.
* `{deviceId}@sas.{iothubname}` si vous utilisez des jetons à l’échelle de l’appareil.

Dans les deux cas, le champ de mot de passe contient le jeton, comme décrit dans [Jetons de sécurité IoT Hub][lnk-sas-tokens].

Le protocole HTTP implémente l’authentification en incluant un jeton valide dans l’en-tête de demande **Authorization** .

#### <a name="example"></a>Exemple

Nom d’utilisateur (DeviceId respecte la casse) : `iothubname.azure-devices.net/DeviceId`

Mot de passe (générer SAP avec l’Explorateur d’appareils) : `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] Les [Kits de développement logiciel (SDK) Azure IoT Hub][lnk-sdks] génèrent automatiquement des jetons lors de la connexion au service. Dans certains cas, les kits de développement logiciel ne prennent pas en charge l’ensemble des protocoles ou méthodes d’authentification.

### <a name="special-considerations-for-sasl-plain"></a>Considérations spécifiques concernant SASL PLAIN

Lorsque vous utilisez SASL PLAIN avec AMQP, un client qui se connecte à un IoT Hub peut utiliser un jeton unique pour chaque connexion TCP. Lorsque le jeton expire, la connexion TCP est déconnectée du service, ce qui déclenche une reconnexion. Bien que non problématique pour un composant de serveur d’applications principal, ce comportement peut créer de graves dommages pour une application côté appareils pour les motifs suivants :

*  Les passerelles se connectent généralement au nom de nombreux appareils. Lorsque vous utilisez SASL PLAIN, elles doivent créer une connexion TCP distincte pour chaque appareil se connectant à un IoT Hub. Ce scénario augmente considérablement la consommation des ressources d’alimentation et de mise en réseau, ainsi que la latence de chaque connexion d’appareil.
* Les appareils avec des contraintes de ressources sont affectés par l’utilisation accrue des ressources pour se reconnecter après chaque expiration du jeton.

## <a name="scope-hublevel-credentials"></a>Étendue des informations d’identification au niveau du hub

Vous pouvez étendre les stratégies de sécurité au niveau du hub en créant des jetons avec un URI de ressource restreint. Par exemple, le point de terminaison pour l’envoi de messages appareil-à-cloud est **/devices/{deviceId}/messages/events**. Vous pouvez également utiliser une stratégie d’accès partagé au niveau du concentrateur avec des autorisations **DeviceConnect** pour signer un jeton dont l’URI de ressource est **/devices/{deviceId}**. Cette approche crée un jeton utilisable uniquement pour envoyer des messages au nom de l’appareil **deviceId**.

Il s’agit d’un mécanisme semblable à la [stratégie de l’éditeur Event Hubs][lnk-event-hubs-publisher-policy], qui vous permet d’implémenter des méthodes d’authentification personnalisées.

## <a name="security-tokens"></a>Jetons de sécurité

IoT Hub utilise des jetons de sécurité pour authentifier les appareils et les services afin d’éviter d’envoyer des clés sur le réseau. En outre, la validité et la portée des jetons sont limitées dans le temps. Les [Kits de développement logiciel (SDK) Azure IoT Hub][lnk-sdks] génèrent automatiquement des jetons sans configuration spéciale. Certains scénarios requièrent toutefois que l’utilisateur génère et utilise directement des jetons de sécurité. Par exemple, il peut s’agir de l’utilisation directe des surfaces HTTP, MQTT ou AMQP, ou de l’implémentation du modèle de service de jeton, comme expliqué dans [Authentification d’appareil personnalisée][lnk-custom-auth].

IoT Hub permet également aux appareils de s’authentifier auprès d’IoT Hub à l’aide de [certificats X.509][lnk-x509]. 

### <a name="security-token-structure"></a>Structure du jeton de sécurité
Vous utilisez des jetons de sécurité pour accorder un accès limité dans le temps aux appareils et aux services à des fonctionnalités spécifiques dans IoT Hub. Pour vérifier que seuls les appareils et les services autorisés peuvent se connecter, les jetons de sécurité doivent être signés avec une clé de stratégie d’accès partagé ou une clé symétrique stockée avec l’identité de l’appareil dans le registre de l’identité.

Un jeton signé avec une clé de stratégie d’accès partagé accorde un accès à toutes les fonctionnalités associées aux autorisations de stratégie d’accès partagé. En revanche, un jeton signé uniquement avec la clé symétrique de l’identité de l’appareil accorde l’autorisation **DeviceConnect** à l’identité de l’appareil associé.

Le jeton de sécurité présente le format suivant :

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Voici les valeurs attendues :

| Valeur | Description |
| ----- | ----------- |
| {signature} | Une chaîne de signature HMAC-SHA256 sous la forme : `{URL-encoded-resourceURI} + "\n" + expiry`. **Important**: la clé est décodée à partir de base64 et utilisée comme clé pour effectuer le calcul HMAC-SHA256. |
| {resourceURI} | Préfixe URI (par segment) des points de terminaison qui sont accessibles avec ce jeton, en commençant par le nom d’hôte IoT Hub (sans protocole). Par exemple, `myHub.azure-devices.net/devices/device1` |
| {expiry} | Chaînes UTF8 pour le nombre de secondes depuis l’époque 00:00:00 UTC 1er janvier 1970. |
| {URL-encoded-resourceURI} | Encodage en URL minuscules de l’URI de ressource en minuscules |
| {policyName} | Le nom de la stratégie d’accès partagé à laquelle ce jeton fait référence. Absent dans le cas de jetons faisant référence aux informations d’identification de registre des appareils. |

**Remarque sur le préfixe**: le préfixe URI est calculé par segment et non par caractère. Par exemple `/a/b` est un préfixe de `/a/b/c`, mais pas de `/a/bc`.

Voici une fonction Node.js qui calcule le jeton à partir des entrées `resourceUri, signingKey, policyName, expiresInMins`. Les sections suivantes décrivent en détail comment initialiser les différentes entrées pour les différents cas d’utilisation des jetons.

    var crypto = require('crypto');

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // using crypto
        var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
        const hmac = crypto.createHmac('sha256', decodedPassword);
        hmac.update(toSign);
        var base64signature = hmac.digest('base64');
        var base64UriEncoded = encodeURIComponent(base64signature);

        // construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        // console.log("signature:" + token);
        return token;
    };
 
 À titre de comparaison, le code Python équivalent est le suivant :
 
    from base64 import b64encode, b64decode
    from hashlib import sha256
    from hmac import HMAC
    from urllib import urlencode
    
    def generate_sas_token(uri, key, policy_name='device', expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % (uri, int(ttl))
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())
     
        return 'SharedAccessSignature ' + urlencode({
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl)),
            'skn': policy_name
        })

> [AZURE.NOTE] Étant donné que la validité du jeton est validée sur les ordinateurs IoT Hub, il est important que la dérive de l’horloge de l’ordinateur qui génère le jeton soit minime.

### <a name="use-sas-tokens-in-a-device-client"></a>Utiliser des jetons SPA dans un client d’appareil

Il existe deux façons d’obtenir des autorisations **DeviceConnect** avec IoT Hub avec des jetons de sécurité : utiliser une [clé d’appareil symétrique extraite du registre des identités des appareils](#use-a-symmetric-key-in-the-identity-registry) une [clé de stratégie d’accès partagé](#use-a-shared-access-policy).

N’oubliez pas que toutes les fonctionnalités accessibles à partir des appareils sont exposées par définition sur les points de terminaison avec le préfixe `/devices/{deviceId}`.

> [AZURE.IMPORTANT] La seule façon dont IoT Hub authentifie un appareil est à l’aide de la clé symétrique d’identité de l’appareil. Dans le cas où une stratégie d’accès partagé est utilisée pour accéder aux fonctionnalités de l’appareil, la solution doit prendre en compte le composant émettant le jeton de sécurité en tant que sous-composant approuvé.

Les points de terminaison côté appareil sont (quel que soit le protocole) :

| Point de terminaison | Fonctionnalités |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | Envoyer des messages Appareil vers cloud. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | Recevoir des messages Cloud vers appareil. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Utilisation d’une clé symétrique dans le registre d’identité

Lorsqu’une clé symétrique d’identité de l’appareil est utilisée pour générer un jeton, l’élément PolicyName (`skn`) du jeton est omis.

Par exemple, un jeton créé pour accéder à toutes les fonctionnalités de l’appareil doit avoir les paramètres suivants :

* URI de ressource : `{IoT hub name}.azure-devices.net/devices/{device id}`,
* clé de signature : n’importe quelle clé symétrique pour l’identité `{device id}` ,
* pas de nom de stratégie,
* n’importe quelle heure d’expiration.

Voici un exemple d’utilisation de la fonction ci-dessus :

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

Le résultat, qui accorde l’accès à toutes les fonctionnalités de device1, est alors :

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] Il est possible de générer un jeton de sécurité à l’aide de l’outil .NET [Explorateur d’appareils][lnk-device-explorer].

### <a name="use-a-shared-access-policy"></a>Utilisation d’une stratégie d’accès partagé

Lorsque vous créez un jeton à partir d’une stratégie d’accès partagé, le champ `skn` du nom de la stratégie doit être défini sur le nom de la stratégie utilisée. Il est également nécessaire que la stratégie accorde l’autorisation **DeviceConnect** .

Les deux principaux scénarios pour l’utilisation de stratégies d’accès partagé pour accéder aux fonctionnalités de l’appareil sont :

* les [passerelles de protocole cloud][lnk-endpoints] ;
* les [services de jeton][lnk-custom-auth] utilisés pour implémenter des schémas d’authentification personnalisés.

Étant donné que la stratégie d’accès partagé peut potentiellement accorder un accès pour se connecter comme n’importe quel appareil, il est important d’utiliser l’URI de ressource correct lors de la création de jetons de sécurité. Cela est particulièrement important pour les services de jeton dont il faut définir l’étendue pour un appareil spécifique à l’aide de l’URI de ressource. Ce point est moins important pour les passerelles de protocole, car elles interceptent déjà le trafic pour tous les appareils.

Par exemple, un service de jeton utilisant la stratégie d’accès partagé précréée appelée **device** crée un jeton avec les paramètres suivants :

* URI de ressource : `{IoT hub name}.azure-devices.net/devices/{device id}`,
* clé de signature : une des clés de la stratégie `device` ,
* nom de la stratégie : `device`,
* n’importe quelle heure d’expiration.

Voici un exemple d’utilisation de la fonction ci-dessus :

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

Le résultat, qui accorde l’accès à toutes les fonctionnalités de device1, est alors :

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

Une passerelle de protocole peut utiliser le même jeton pour tous les appareils en définissant simplement l’URI de ressource sur `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Utilisation de jetons de sécurité de composants du service

Les composants de service peuvent uniquement créer des jetons de sécurité utilisant des stratégies d’accès partagé pour accorder les autorisations adaptées, comme expliqué précédemment.

Voici les fonctions de service exposées sur les points de terminaison :

| Point de terminaison | Fonctionnalités |
| ----- | ----------- |
| `{iot hub host name}/devices` | Créer, mettre à jour, récupérer et supprimer les identités des appareils. |
| `{iot hub host name}/messages/events` | Recevoir des messages Appareil vers cloud. |
| `{iot hub host name}/servicebound/feedback` | Recevoir des commentaires pour les messages Cloud vers appareil. |
| `{iot hub host name}/devicebound` | Envoyer des messages Cloud vers appareil. |

Par exemple, un service qui génère à l’aide de la stratégie d’accès partagé précréée appelée **registryRead** crée un jeton avec les paramètres suivants :

* URI de ressource : `{IoT hub name}.azure-devices.net/devices`,
* clé de signature : une des clés de la stratégie `registryRead` ,
* nom de la stratégie : `registryRead`,
* n’importe quelle heure d’expiration.

    var endpoint ="myhub.azure-devices.net/devices";   var policyName = ’device’;   var policyKey = ’...’;

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

Le résultat, qui revient à accorder l’accès en lecture à toutes les identités des appareils, est alors :

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## <a name="supported-x509-certificates"></a>Certificats X.509 pris en charge

Vous pouvez utiliser n’importe quel certificat X.509 pour authentifier un appareil sur IoT Hub. notamment :

-   **un certificat X.509 existant**. Un appareil peut être déjà associé à un certificat X.509. L’appareil peut utiliser ce certificat pour s’authentifier sur IoT Hub ;

-   **un certificat X-509 généré et signé automatiquement**. Un fabricant d’appareils ou un technicien de déploiement en interne peut générer ces certificats et stocker la clé privée correspondante (ainsi que le certificat) sur l’appareil. Vous pouvez utiliser des outils tels que [OpenSSL][lnk-openssl] et l’utilitaire [Windows SelfSignedCertificate][lnk-selfsigned] à cette fin.

-   **un certificat X.509 signé par une autorité de certification**. Vous pouvez également utiliser un certificat X.509 généré et signé par une autorité de certification (CA) pour identifier un appareil et l’authentifier sur IoT Hub.

Un appareil peut utiliser un certificat X.509 ou un jeton de sécurité pour l’authentification, mais pas les deux.

### <a name="register-an-x509-client-certificate-for-a-device"></a>Inscrire un certificat de client X.509 pour un appareil

Le [Kit de développement logiciel (SDK) de service Azure IoT pour Java][lnk-service-sdk] (version 1.0.8+) prend en charge l’inscription d’un appareil utilisant un certificat client X.509 pour s’authentifier. D’autres API telles que l’importation/exportation d’appareils prennent également en charge les certificats clients X.509.

### <a name="c-support"></a>Prise en charge de C\#

La classe **RegistryManager** offre un moyen d’inscrire un appareil dans le cadre d’un programme. Les méthodes **AddDeviceAsync** et **UpdateDeviceAsync** permettent en particulier à un utilisateur d’inscrire et de mettre à jour un appareil dans le registre des identité des appareils Iot Hub. Ces deux méthodes utilisent une instance **Device** comme entrée. La classe **Device** inclut une propriété **Authentication** qui permet à l’utilisateur de spécifier les empreintes de certificats X.509 primaires et secondaires. L’empreinte numérique représente un hachage SHA-1 du certificat X.509 (stocké à l’aide d’un codage DER binaire). Les utilisateurs ont la possibilité de spécifier une empreinte numérique principale et/ou une empreinte numérique secondaire. Les empreintes numériques principales et secondaires sont prises en charge pour la gestion des scénarios de substitution de certificat.

> [AZURE.NOTE] IoT Hub ne requiert ni ne stocke le certificat client X.509 dans son intégralité, mais uniquement l’empreinte numérique.

Voici un exemple d’extrait de code C\# permettant d’inscrire un appareil à l’aide d’un certificat client X.509 :

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-client-certificate-during-runtime-operations"></a>Utiliser un certificat client X.509 pendant les opérations d’exécution

Le [Kit de développement logiciel (SDK) d’appareil Azure IoT pour .NET][lnk-client-sdk] (version 1.0.11+) prend en charge l’utilisation de certificats clients X.509.

### <a name="c-support"></a>Prise en charge de C\#

La classe **DeviceAuthenticationWithX509Certificate** prend en charge la création d’instances  **DeviceClient** à l’aide d’un certificat client X.509.

Voici un exemple d’extrait de code :

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Authentification d'appareil personnalisée

Vous pouvez utiliser le [registre des identités des appareils][lnk-identity-registry] IoT Hub pour configurer les informations d’identification de sécurité et le contrôle d’accès par appareil à l’aide de [jetons][lnk-sas-tokens]. Cependant, si une solution IoT représente déjà un investissement significatif dans un registre d'identité d'appareil personnalisé et/ou un schéma d'authentification, vous pouvez intégrer cette infrastructure existante à IoT Hub en créant un *service de jeton*. De cette façon, vous pouvez utiliser d'autres fonctionnalités IoT dans votre solution.

Un service de jeton est un service cloud personnalisé. Il utilise une *stratégie d’accès partagé* IoT Hub avec des autorisations **DeviceConnect** pour créer des jetons *device-scoped*. Ces jetons permettent à un appareil de se connecter à votre IoT Hub.

  ![Étapes du modèle de service de jeton][img-tokenservice]

Voici les principales étapes du modèle de service de jeton :

1. Créez une stratégie d’accès partagé IoT Hub avec des autorisations **DeviceConnect** pour votre IoT Hub. Vous pouvez créer cette stratégie via le [portail Azure][lnk-management-portal] ou par programme. Le service de jetons utilise cette stratégie pour signer les jetons qu'elle crée.
2. Lorsqu'un appareil doit accéder à votre hub IoT, il demande à votre service de jetons un jeton signé. L’appareil peut s’authentifier avec votre registre d’identité d’appareil personnalisé/schéma d’authentification pour déterminer l’identité d’appareil que le service de jeton utilise pour créer le jeton.
3. Le service de jeton renvoie un jeton. Le jeton est créé en utilisant `/devices/{deviceId}` en tant qu’`resourceURI`, avec `deviceId` en tant qu’appareil en cours d’authentification. Le service de jeton utilise la stratégie d'accès partagé pour construire le jeton.
4. L’appareil utilise le jeton directement avec le hub IoT.

> [AZURE.NOTE] Vous pouvez utiliser la classe .NET [SharedAccessSignatureBuilder][lnk-dotnet-sas] ou la classe Java [IotHubServiceSasToken][lnk-java-sas] pour créer un jeton dans votre service d’émission de jeton.

Le service de jetons peut définir l’expiration du jeton comme vous le souhaitez. Lorsque le jeton expire, le hub IoT interrompt la connexion. L’appareil doit ensuite demander un nouveau jeton au service de jeton. Si vous utilisez un délai d'expiration court, cela accroît la charge de l'appareil et du service de jeton.

Pour qu’un appareil se connecte à votre hub, vous devez l’ajouter au Registre d’identité d’appareil IoT Hub, même si l’appareil utilise un jeton et non une clé d’appareil pour se connecter. Par conséquent, vous pouvez continuer à utiliser le contrôle d’accès par appareil en activant ou désactivant les identités des appareils dans le [registre des identités IoT Hub][lnk-identity-registry] lorsque l’appareil s’authentifie avec un jeton. Cela réduit les risques liés à l'utilisation de jetons avec des délais d'expiration longs.

### <a name="comparison-with-a-custom-gateway"></a>Comparaison avec une passerelle personnalisée

Le modèle de service de jeton est la méthode recommandée pour implémenter un schéma de registre d’identité/d’authentification avec IoT Hub. Il est recommandé, car il laisse IoT Hub gérer la plus grande partie du trafic de la solution. Cependant, il existe des cas où le schéma d’authentification personnalisé est si étroitement couplé au protocole qu’un service traitant l’ensemble du trafic (*passerelle personnalisée*) est requis. Le [protocole TLS (Transport Layer Security) et les clés prépartagées (PSK)][lnk-tls-psk] en sont un exemple. Pour plus d’informations, voir [passerelle de protocole][lnk-protocols].

## <a name="reference"></a>Référence

### <a name="iot-hub-permissions"></a>Autorisations IoT Hub

Le tableau suivant répertorie les autorisations que vous pouvez utiliser pour contrôler l’accès à votre IoT Hub.

| Autorisation            | Remarques |
| --------------------- | ----- |
| **RegistryRead**.      | Accorde l’accès en lecture au registre des identités des appareils. Pour plus d’informations, voir [Registre des identités des appareils][lnk-identity-registry]. |
| **RegistryReadWrite**. | Accorde l’accès en lecture et en écriture au registre des identités des appareils. Pour plus d’informations, voir [Registre des identités des appareils][lnk-identity-registry]. |
| **ServiceConnect**.    | Accorde l’accès à la communication de services cloud et à la surveillance des points de terminaison. Par exemple, elle permet aux services cloud principaux de recevoir des messages appareil-à-cloud, d’envoyer des messages cloud-à-appareil et de récupérer les accusés de remise correspondants. |
| **DeviceConnect**.     | Accorde l’accès aux points de terminaison de communication d’appareils. Par exemple, elle permet d’envoyer des messages appareil-à-cloud et de recevoir des messages cloud-à-appareil. Cette autorisation est utilisée par les appareils. |

### <a name="additional-reference-material"></a>Matériel de référence supplémentaire

Autres rubriques de référence dans le Guide du développeur :

- La rubrique [Points de terminaison IoT Hub][lnk-endpoints] décrit les différents points de terminaison que chaque IoT Hub expose pour les opérations d’exécution et de gestion.
- La rubrique [Quotas et limitation][lnk-quotas] décrit les quotas appliqués au service IoT Hub, et le comportement de limitation auquel s’attendre lors de l’utilisation du service.
- La rubrique [Kits de développement logiciel (SDK) de services et appareils IoT Hub][lnk-sdks] répertorie les kits de développement logiciel en différents langages que vous pouvez utiliser lors du développement d’applications d’appareil et de service qui interagissent avec IoT Hub.
- La rubrique [Langage de requête pour les représentations, les méthodes et les travaux][lnk-query] décrit le langage de requête permettant de récupérer à partir d’IoT Hub des informations sur des représentations d’appareil, des méthodes et des travaux.
- La rubrique [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt] fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous savez comment contrôler l’accès à IoT Hub, vous serez peut-être intéressé par les rubriques suivantes du Guide du développeur :

- [Utiliser des représentations d’appareil pour synchroniser les données d’état et de configuration][lnk-devguide-device-twins]
- [Appeler une méthode directe sur un appareil][lnk-devguide-directmethods]
- [Planifier des travaux sur plusieurs appareils][lnk-devguide-jobs]

Si vous souhaitez tenter de mettre en pratique certains des concepts décrits dans cet article, vous serez peut-être intéressé par les didacticiels IoT Hub suivants :

- [Mise en route d’Azure IoT Hub][lnk-getstarted-tutorial]
- [Envoi de messages cloud-à-appareil avec IoT Hub][lnk-c2d-tutorial]
- [Traitement des messages appareil-à-cloud IoT Hub][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md



<!---HONumber=Oct16_HO2-->


