<properties
	pageTitle="Utilisation du Kit de développement logiciel (SDK) JavaScript pour Azure Mobile Apps"
	description="Utilisation de v pour Azure Mobile Apps"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="adrianha;ricksal"/>

# Utilisation de la bibliothèque cliente JavaScript pour Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide indique le déroulement de scénarios courants dans le cadre de l’utilisation du dernier [Kit de développement logiciel (SDK) JavaScript pour Azure Mobile Apps]. Si vous ne connaissez pas Azure Mobile Apps, consultez d’abord la section [Démarrage rapide d’Azure Mobile Apps] pour créer un backend et créer une table. Dans ce guide, nous nous concentrons sur l’utilisation du backend mobile dans les applications web HTML/JavaScript.

##<a name="Setup"></a>Configuration et conditions préalables

Ce guide part du principe que vous avez créé un serveur principal avec une table. Ce guide suppose que la table a le même schéma que les tables dans ces didacticiels.

Installez le Kit de développement logiciel (SDK) JavaScript Azure Mobile Apps à partir de la commande `npm` :

```
npm install azure-mobile-apps-client --save
```

Une fois le Kit installé, la bibliothèque se trouve dans `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`. Copiez ce fichier vers votre espace web.

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

La bibliothèque peut également être utilisée en tant que module ES2015, au sein d'environnements CommonJS tels que Browserify et Webpack, et en tant que bibliothèque AMD. Par exemple :

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Procédure : authentification des utilisateurs

Azure App Service prend en charge l’authentification et l’autorisation des utilisateurs d’applications par le biais de divers fournisseurs d’identité externes : Facebook, Google, compte Microsoft et Twitter. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l'identité des utilisateurs authentifiés pour implémenter des règles d'autorisation dans les scripts serveur. Pour plus d'informations, consultez la page [Prise en main de l'authentification].

Deux flux d’authentification sont pris en charge : un flux serveur et un flux client. Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface d'authentification Web du fournisseur. Le flux client permet une intégration approfondie avec les fonctionnalités propres aux appareils, telles que l'authentification unique, car il repose sur des Kits de développement logiciel (SDK) propres aux fournisseurs.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

##<a name="register-for-push"></a>Procédure : inscription aux notifications Push

Installez le plug-in [plug-in phonegap-push] pour gérer les notifications Push. Vous pouvez l’ajouter facilement à l’aide de la commande `cordova plugin add` sur la ligne de commande, ou par le biais du programme d’installation de plug-in Git dans Visual Studio. Le code suivant dans votre application Apache Cordova inscrit votre appareil aux notifications Push :

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Utilisez le Kit de développement logiciel (SDK) Notification Hubs pour envoyer des notifications Push à partir du serveur. Vous ne devez jamais envoyer de notifications Push directement à partir de clients, car cela peut favoriser le déclenchement d’une attaque par déni de service contre Notification Hubs ou le PNS.

<!-- URLs. -->
[Démarrage rapide d’Azure Mobile Apps]: app-service-mobile-cordova-get-started.md
[Prise en main de l'authentification]: app-service-mobile-cordova-get-started-users.md
[Ajout de l’authentification à votre application]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova Plugin for Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[your first Apache Cordova app]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[plug-in phonegap-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[documentation de l’objet Query]: https://msdn.microsoft.com/fr-FR/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0504_2016-->