<properties
	pageTitle="Procédures de mise à niveau du SDK web Azure Mobile Engagement | Microsoft Azure"
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


# Procédures de mise à niveau du SDK web Azure Mobile Engagement

Si vous avez déjà intégré une ancienne version de notre SDK à votre application, tenez compte des points suivants avant de procéder à la mise à niveau du SDK.

Vous devrez peut-être suivre quelques procédures si vous avez manqué plusieurs versions du kit SDK. Par exemple, si vous migrez de la version 1.4.0 vers 1.6.0, vous devez tout d'abord suivre la procédure « Migration de 1.4.0 vers 1.5.0 », puis la procédure « Migration de 1.5.0 vers 1.6.0 ».

Quelle que soit la version que vous mettez à niveau, vous devez remplacer `azure-engagement.js`.

## Migration de 1.2.1 vers 2.0.0

La section qui suit décrit comment migrer une intégration du SDK à partir du service Capptain offert par Capptain SAS dans une application reposant sur Azure Mobile Engagement. Si vous migrez à partir d’une version antérieure, consultez le site web de Capptain pour migrer tout d’abord vers 1.2.1, puis appliquez la procédure suivante.

Cette version du SDK web Engagement ne prend pas en charge samsung-tv, OperaTV, webOS et la fonctionnalité Reach.

>[AZURE.IMPORTANT] Capptain et Mobile Engagement ne sont pas les mêmes services et la procédure décrite ci-dessous explique uniquement comment migrer l'application cliente. La migration du SDK dans l'application ne migre PAS vos données des serveurs Capptain vers les serveurs Mobile Engagement .

### Fichiers JavaScript

Remplacez le fichier `capptain-sdk.js` par le fichier `azure-engagement.js` et mettez à jour vos imports de script en conséquence.

### Supprimer Capptain Reach

Cette version du Kit de développement logiciel (SDK) web Engagement ne prend pas en charge la fonctionnalité Reach. Si vous avez intégré Capptain Reach dans votre application, il doit être supprimé.

Supprimez l’import CSS Reach de votre page et supprimez le fichier CSS associé (capptain-reach.css par défaut).

Supprimez les ressources Reach : l’image de fermeture (capptain-close.png par défaut) et l’icône de marque (capptain-notification-icon par défaut).

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

Supprimez l’interface utilisateur Reach pour les annonces et sondages texte/web. La disposition par défaut ressemble à ceci :

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

Supprimez l’objet `reach` de la configuration le cas échéant. Cela se présente comme suit :

	window.capptain = {
	  [...]
	  reach: {
	    [...]
	  }
	}

Supprimez toute autre personnalisation Reach, notamment les catégories.

### Supprimer les API déconseillées

Certaines des API de Capptain sont déconseillées dans la version Engagement du SDK.

Supprimez tous les appels vers les API suivantes : `agent.connect`, `agent.disconnect`, `agent.pause` et `agent.sendMessageToDevice`.

Supprimez les rappels suivants, le cas échéant, de votre configuration Capptain : `onConnected`, `onDisconnected`, `onDeviceMessageReceived` et `onPushMessageReceived`.

### Configuration

Engagement utilise désormais une chaîne de connexion pour configurer les identificateurs du SDK tels que l'identificateur d'application.

Remplacez l’ID d’application par votre chaîne de connexion. Notez également que l’objet global de la configuration du SDK est déplacé de `capptain` vers `azureEngagement`.

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

### API JavaScript

L’objet JavaScript global `window.capptain` a été renommé `window.azureEngagement` mais vous pouvez utiliser l’alias `window.engagement` pour les appels d’API (vous ne pouvez pas utiliser l’alias pour définir la configuration du SDK).

Par exemple : `capptain.deviceId` devient `engagement.deviceId`, `capptain.agent.startActivity` devient `engagement.agent.startActivity`, etc.

<!---HONumber=AcomDC_0615_2016-->