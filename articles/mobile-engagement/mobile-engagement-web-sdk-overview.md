<properties
	pageTitle="Vue d’ensemble du SDK web Azure Mobile Engagement | Microsoft Azure"
	description="Dernières mises à jour et procédures du Kit de développement logiciel (SDK) web pour Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="web"
	ms.devlang="js"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="piyushjo" />


# Kit de développement logiciel (SDK) web pour Azure Mobile Engagement

Démarrez ici pour obtenir tous les détails de l’intégration d’Azure Mobile Engagement dans une application web. Si vous souhaitez faire un essai avant de créer votre propre application web, consultez notre [didacticiel de 15 minutes](mobile-engagement-web-app-get-started.md).

## Procédures d'intégration
1. Découvrez [comment intégrer Mobile Engagement dans votre application web](mobile-engagement-web-integrate-engagement.md).

2. Pour l’implémentation du plan de balise, découvrez [comment utiliser l’API avancée de balisage de Mobile Engagement dans votre application web](mobile-engagement-web-use-engagement-api.md)

## Notes de publication

### 2\.0.1 (10/06/2016)

-   Désactivation du kit de développement logiciel (SDK) web Mobile Engagement dans Internet Explorer 8 et Internet Explorer 9.
-   Réparation de la détection du navigateur web Opera.

Pour toutes les versions, consultez les [notes de publication complètes](mobile-engagement-web-release-notes.md).

## Procédures de mise à niveau

### Mise à niveau de 1.2.1 vers 2.0.0

Les sections suivantes décrivent comment migrer une intégration du kit de développement logiciel (SDK) web Mobile Engagement à partir du service Capptain, offert par Capptain SAS, vers une application Azure Mobile Engagement. Si vous migrez à partir d’une version antérieure à 1.2.1, consultez le site web de Capptain pour migrer tout d’abord vers 1.2.1, puis appliquez les procédures suivantes.

Cette version du Kit de développement logiciel (SDK) web Mobile Engagement ne prend pas en charge Samsung Smart TV, Opera TV, webOS ou la fonctionnalité Reach.

>[AZURE.IMPORTANT] Capptain et Azure Mobile Engagement ne sont pas les mêmes services et les procédures ci-dessous expliquent uniquement comment migrer l’application cliente. La migration du Kit de développement logiciel (SDK) web Mobile Engagement dans l'application ne migre pas vos données d’un serveur Capptain vers un serveur Mobile Engagement.

#### Fichiers JavaScript

Remplacez le fichier capptain-sdk.js par le fichier azure-engagement.js, puis mettez à jour en conséquence les importations de votre script.

#### Supprimer Capptain Reach

Cette version du Kit de développement logiciel (SDK) web Mobile Engagement ne prend pas en charge la fonctionnalité Reach. Si vous avez intégré Capptain Reach à votre application, vous devez la supprimer.

Supprimez l’import CSS Reach de votre page et supprimez le fichier .css associé (capptain-reach.css par défaut).

Supprimez les ressources Reach suivantes : l’image de fermeture (capptain-close.png par défaut) et l’icône de marque (capptain-notification-icon par défaut).

Supprimez l’interface utilisateur Reach pour les notifications dans l’application. La disposition par défaut ressemble à ceci :

	<!-- capptain notification -->
	<div id="capptain_notification_area" class="capptain_category_default">
	  <div class="icon">
	    <img src="capptain-notification-icon.png" alt="icon" />
	  </div>
	  <div class="content">
	    <div class="title" id="capptain_notification_title"></div>
	    <div class="message" id="capptain_notification_message"></div>
	  </div>
	  <div id="capptain_notification_image"></div>
	  <div>
	    <button id="capptain_notification_close">Close</button>
	  </div>
	</div>

Supprimez l’interface utilisateur Reach pour les annonces texte et web ainsi que pour les sondages. La disposition par défaut ressemble à ceci :

	<div id="capptain_overlay" class="capptain_category_default">
	  <button id="capptain_overlay_close">x</button>
	  <div id="capptain_overlay_title"></div>
	  <div id="capptain_overlay_body"></div>
	  <div id="capptain_overlay_poll"></div>
	  <div id="capptain_overlay_buttons">
	    <button id="capptain_overlay_exit"></button>
	    <button id="capptain_overlay_action"></button>
	  </div>
	</div>

Supprimez l’objet `reach` de la configuration, s’il existe. Voici à quoi cela ressemble :

	window.capptain = {
	  [...]
	  reach: {
	    [...]
	  }
	}

Supprimez toute autre personnalisation Reach, notamment les catégories.

#### Supprimer les API déconseillées

Certaines API de Capptain sont déconseillées dans le Kit de développement logiciel (SDK) web Mobile Engagement.

Supprimez tous les appels vers les API suivantes : `agent.connect`, `agent.disconnect`, `agent.pause` et `agent.sendMessageToDevice`.

Supprimez tous les rappels suivants de votre configuration Capptain : `onConnected`, `onDisconnected`, `onDeviceMessageReceived` et `onPushMessageReceived`.

#### Configuration

Mobile Engagement utilise une chaîne de connexion pour configurer les identificateurs du SDK, par exemple l'identificateur d'application.

Remplacez l’ID d’application par votre chaîne de connexion. Notez que l’objet global pour la configuration du kit de développement logiciel (SDK) passe de `capptain` à `azureEngagement`.

Avant la migration :

	window.capptain = {
	  appId: ...,
	  [...]
	};

Après la migration :

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  [...]
	};

La chaîne de connexion de votre application est affichée sur le Portail Azure.

#### API JavaScript

L’objet JavaScript global `window.capptain` a été renommé `window.azureEngagement`, mais vous pouvez utiliser l’alias `window.engagement` pour les appels d’API. Vous ne pouvez pas utiliser l’alias pour définir la configuration du kit de développement logiciel (SDK).

Par exemple : `capptain.deviceId` devient `engagement.deviceId`, `capptain.agent.startActivity` devient `engagement.agent.startActivity`, etc.

Si vous avez déjà intégré une version antérieure du kit de développement logiciel (SDK) web Azure Mobile Engagement à votre application, consultez les [procédures de mise à niveau](mobile-engagement-web-upgrade-procedure.md).

<!---HONumber=AcomDC_0713_2016-->