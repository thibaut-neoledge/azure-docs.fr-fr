<properties
 pageTitle="Générer des jetons de sécurité IoT Hub | Microsoft Azure"
 description="Description des différents types de jeton de sécurité utilisés par IoT Hub et de leur création."
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="03/02/2016"
 ms.author="elioda"/>

# Utilisation de jetons de sécurité IoT Hub

IoT Hub utilise des jetons de sécurité pour authentifier les appareils et les services afin d’éviter d’envoyer des clés sur le réseau. En outre, la validité et la portée des jetons sont limitées dans le temps. Les [Kits de développement logiciel (SDK) Azure IoT Hub][lnk-apis-sdks] génèrent automatiquement les jetons sans configuration spéciale. Certains scénarios requièrent toutefois que l’utilisateur génère et utilise directement des jetons de sécurité. Par exemple, il peut s’agir de l’utilisation directe des surfaces HTTP, MQTT ou AMQP, ou de l’implémentation du modèle de service de jeton, comme expliqué dans les [Conseils IoT Hub][lnk-guidance-security].

Cet article explique :

* Le format des jetons de sécurité et comment les créer.
* Le principal cas d’utilisation de jetons de sécurité pour authentifier les appareils et les services principaux.

## Structure du jeton de sécurité
Vous utilisez des jetons de sécurité pour accorder un accès limité dans le temps aux appareils et aux services à des fonctionnalités spécifiques dans IoT Hub. Pour vérifier que seuls les appareils et les services autorisés peuvent se connecter, les jetons de sécurité doivent être signés avec une clé de stratégie d’accès partagé ou une clé symétrique stockée avec l’identité de l’appareil dans le registre de l’identité.

Un jeton signé avec une clé de stratégie d’accès partagé accorde un accès à toutes les fonctionnalités associées aux autorisations de stratégie d’accès partagé. Consultez la [section Sécurité du guide du développeur IoT Hub][lnk-devguide-security]. En revanche, un jeton signé uniquement avec la clé symétrique de l’identité de l’appareil accorde l’autorisation **DeviceConnect** à l’identité de l’appareil associé.

Le jeton de sécurité présente le format suivant :

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Voici les valeurs attendues :

| Valeur | Description |
| ----- | ----------- |
| {signature} | Une chaîne de signature HMAC-SHA256 sous la forme : `{URL-encoded-resourceURI} + "\n" + expiry`. **Important** : la clé est décodée à partir de base64 et utilisée comme clé pour effectuer le calcul HMAC-SHA256. |
| {resourceURI} | Préfixe URI (par segment) des points de terminaison qui sont accessibles avec ce jeton, en commençant par le nom d’hôte IoT Hub (sans protocole). Par exemple, `myHub.azure-devices.net/devices/device1` |
| {expiry} | Chaînes UTF8 pour le nombre de secondes depuis l’époque 00:00:00 UTC 1er janvier 1970. |
| {URL-encoded-resourceURI} | Encodage en URL minuscules de l’URI de ressource en minuscules |
| {policyName} | Le nom de la stratégie d’accès partagé à laquelle ce jeton fait référence. Absent dans le cas de jetons faisant référence aux informations d’identification de registre des appareils. |

**Remarque sur le préfixe** : le préfixe URI est calculé par segment et non par caractère. Par exemple `/a/b` est un préfixe de `/a/b/c`, mais pas de `/a/bc`.

Il s’agit d’une fonction Node qui calcule le jeton à partir des entrées `resourceUri, signingKey, policyName, expiresInMins`. Les sections suivantes décrivent en détail comment initialiser les différentes entrées pour les différents cas d’utilisation des jetons.

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

> [AZURE.NOTE] Étant donné que la validité du jeton est validée sur les ordinateurs IoT Hub, il est important que la dérive de l’horloge de l’ordinateur qui génère le jeton soit minime.

## Utilisation de jetons SAP comme appareil

Il existe deux façons d’obtenir des autorisations **DeviceConnect** avec IoT Hub avec jetons de sécurité : avec une clé d’identité d’appareil ou une clé de stratégie d’accès partagé.

En outre, il est important de noter que toutes les fonctionnalités accessibles à partir des appareils sont exposées dès le départ sur les points de terminaison avec le préfixe `/devices/{deviceId}`.

> [AZURE.IMPORTANT] La seule façon dont IoT Hub authentifie un appareil est à l’aide de la clé symétrique d’identité de l’appareil. Dans le cas où une stratégie d’accès partagé est utilisée pour accéder aux fonctionnalités de l’appareil, la solution doit prendre en compte le composant émettant le jeton de sécurité en tant que sous-composant approuvé.

Les points de terminaison côté appareil sont (quel que soit le protocole) :

| Point de terminaison | Fonctionnalités |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | Envoyer des messages Appareil vers cloud. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | Recevoir des messages Cloud vers appareil. |

### Utilisation d’une clé symétrique dans le registre de l’identité

Lors de l’utilisation d’une clé symétrique d’identité de l’appareil pour générer un jeton, l’élément PolicyName (`skn`) du jeton est omis.

Par exemple, un jeton créé pour accéder à toutes les fonctionnalités de l’appareil doit avoir les paramètres suivants :

* URI de ressource : `{IoT hub name}.azure-devices.net/devices/{device id}`,
* clé de signature : n’importe quelle clé symétrique pour l’identité `{device id}`,
* pas de nom de stratégie,
* n’importe quelle heure d’expiration.

Voici un exemple d’utilisation de la fonction ci-dessus :

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

Le résultat, qui accorde l’accès à toutes les fonctionnalités de device1, est alors :

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] Il est possible de générer un jeton de sécurité à l’aide de l’outil .NET [Explorateur d’appareils][lnk-device-explorer].

### Utilisation d’une stratégie d’accès partagé

Lorsque vous créez un jeton à partir d’une stratégie d’accès partagé, le champ `skn` du nom de la stratégie doit être défini sur le nom de la stratégie utilisée. Il est également nécessaire que la stratégie accorde l’autorisation **DeviceConnect**.

Les deux principaux scénarios pour l’utilisation de stratégies d’accès partagé pour accéder aux fonctionnalités de l’appareil sont :

* [passerelles de protocole cloud][lnk-azure-protocol-gateway],
* [services de jeton][lnk-devguide-security] utilisés pour implémenter des schémas d’authentification personnalisés.

Étant donné que la stratégie d’accès partagé peut potentiellement accorder un accès pour se connecter comme n’importe quel appareil, il est important d’utiliser l’URI de ressource correct lors de la création de jetons de sécurité. Cela est particulièrement important pour les services de jeton dont il faut définir l’étendue pour un appareil spécifique à l’aide de l’URI de ressource. Ce point est moins important pour les passerelles de protocole, car elles interceptent déjà le trafic pour tous les appareils.

Par exemple, un service de jeton utilisant la stratégie d’accès partagé précréée appelée **device** crée un jeton avec les paramètres suivants :

* URI de ressource : `{IoT hub name}.azure-devices.net/devices/{device id}`,
* clé de signature : une des clés de la stratégie `device`,
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

## Utilisation de jetons de sécurité de composants du service

Les composants de service peuvent uniquement créer des jetons de sécurité utilisant des stratégies d’accès partagé pour accorder les autorisations adaptées, comme expliqué dans la [section Sécurité du guide du développeur IoT Hub][lnk-devguide-security].

Voici les fonctions de service exposées sur les points de terminaison :

| Point de terminaison | Fonctionnalités |
| ----- | ----------- |
| `{iot hub host name}/devices` | Créer, mettre à jour, récupérer et supprimer les identités des appareils. |
| `{iot hub host name}/messages/events` | Recevoir des messages Appareil vers cloud. |
| `{iot hub host name}/servicebound/feedback` | Recevoir des commentaires pour les messages Cloud vers appareil. |
| `{iot hub host name}/devicebound` | Envoyer des messages Cloud vers appareil. |

Par exemple, un service qui génère à l’aide de la stratégie d’accès partagé précréée appelée **registryRead** crée un jeton avec les paramètres suivants :

* URI de ressource : `{IoT hub name}.azure-devices.net/devices`,
* clé de signature : une des clés de la stratégie `registryRead`,
* nom de la stratégie : `registryRead`,
* n’importe quelle heure d’expiration.

    var endpoint ="myhub.azure-devices.net/devices"; var policyName = 'device'; var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

Le résultat, qui revient à accorder l’accès en lecture à toutes les identités des appareils, est alors :

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead


[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

<!---HONumber=AcomDC_0413_2016-->