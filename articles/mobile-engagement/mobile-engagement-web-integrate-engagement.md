<properties
	pageTitle="Intégration du SDK web Azure Mobile Engagement | Microsoft Azure"
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
	ms.date="02/29/2016"
	ms.author="piyushjo" />

#Intégration d’Engagement dans une application web

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Cette procédure décrit la méthode la plus simple pour activer les fonctions d’analyse et de surveillance d’Engagement dans votre application web.

Les étapes suivantes suffisent à activer la création de journaux nécessaires au calcul de l'ensemble des statistiques concernant les utilisateurs, les sessions, les activités, les incidents et les éléments techniques. Le rapport des journaux nécessaire pour calculer d’autres statistiques telles que les événements, les erreurs et les tâches, doit être généré manuellement à l’aide de l’API Engagement (consultez la rubrique [Utilisation de l’API de balisage Mobile Engagement avancé dans une application web](mobile-engagement-web-use-engagement-api.md)) étant donné que ces statistiques dépendent de l’application.

## Introduction

Téléchargez le kit de développement logiciel (SDK) web [ici](http://aka.ms/P7b453). Le Kit de développement logiciel (SDK) est fourni sous la forme d’un seul fichier JavaScript nommé **azure-engagement.js** à inclure dans chaque page de votre site ou de votre application web.

Ce script **DOIT** être chargé **APRÈS** un script ou un extrait de code écrit par vous pour configurer Engagement pour votre application.

## Compatibilité des navigateurs

Le Kit de développement logiciel (SDK) web d’Engagement utilise un codage/décodage JSON natif et des requêtes AJAX interdomaines (reposant sur la spécification W3C CORS).

* Edge 12+
* IE 10+
* Firefox 3.5+
* Chrome 4+
* Safari 6+
* Opera 12+

## Configurer Engagement

Écrivez un script qui crée un objet JavaScript **azureEngagement** global comme suit.
 
Dans la mesure où votre site peut contenir plusieurs pages, cet exemple suppose que ce script est également inclus dans chaque page. Nous le nommerons `azure-engagement-conf.js` dans cette procédure.

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	};

L’élément `connectionString` de votre application s’affiche sur le Portail Azure.

> [AZURE.NOTE] `appVersionName` et `appVersionCode` sont facultatifs. Il est recommandé de les configurer de manière à ce que l’analyse traite les informations de version.

## Inclure des scripts d’Engagement dans vos pages

	<head>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</head>

Ou

	<body>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</body>

## Alias

Une fois chargé, le script du Kit de développement logiciel crée l’alias **engagement** pour accéder aux API du SDK (il ne peut pas être utilisé pour définir la configuration du SDK). Cet alias est utilisé comme référence dans cette documentation.

Notez que si l’alias par défaut est en conflit avec une autre variable globale de votre page, vous pouvez le redéfinir dans la configuration avant de charger le Kit de développement logiciel comme suit :

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	  alias:'anotherAlias'
	};

## Génération de rapports de base

### Suivi de session

Une session d’Engagement est divisée en une séquence d’activités identifiées par un nom.

Dans un site web classique, nous vous recommandons de déclarer une autre activité sur chaque page de votre site. Avec un site ou une application web qui ne change jamais la page en cours, vous pouvez suivre les activités de façon plus fine.

Dans les deux cas, pour démarrer ou modifier l’activité utilisateur en cours, appelez la fonction `engagement.agent.startActivity` comme dans cet exemple :

	<body onload="yourOnload()">

	<!-- -->

	yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
	};

Une session ouverte sera automatiquement terminée par le serveur d’Engagement dans un délai de 3 minutes après la fermeture de la page de l’application.

Sinon, vous pouvez également terminer une session manuellement en appelant `engagement.agent.endActivity`. Ceci définira l’activité utilisateur en cours comme étant « inactive » et mettra fin à la session 10 secondes après, sauf si un nouvel appel à `engagement.agent.startActivity` reprend la session dans l’intervalle.
 
Ce délai de 10 secondes peut être configuré dans l’objet **engagement** global :

	engagement.sessionTimeout = 2000; // 2 seconds
	// or
	engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] `engagement.agent.endActivity` ne peut pas être utilisé dans le rappel `onunload` puisque les appels Ajax ne sont pas possibles à ce stade.

## Génération de rapports avancés

Éventuellement, si vous voulez signaler des éléments `events`, `errors` et `jobs` de l’application spécifiques, vous devez utiliser l’API Engagement par le biais de l’objet `engagement.agent`.

L’API Engagement permet d’utiliser toutes les fonctionnalités avancées d’Engagement. Elle est détaillée dans la section [Utilisation de l’API de balisage Mobile Engagement avancé dans une application web](mobile-engagement-web-use-engagement-api.md).

## Personnaliser les URL utilisées pour les appels AJAX

Vous pouvez personnaliser les URL utilisées par le Kit de développement logiciel (SDK). Par exemple, pour redéfinir l’URL de connexion (point de terminaison du kit de développement logiciel pour l’enregistrement), vous pouvez remplacer la configuration comme suit :

	window.azureEngagement = {
	  ...
	  urls: {
	    ...        
	    getLoggerUrl: function() {
	    return 'someProxy/log';
	    }
	  }
	};

Si vos fonctions URL renvoient une chaîne commençant par `/`, `//`, `http://` ou `https://`, le schéma par défaut n’est pas utilisé.

Par défaut, le schéma `https://` est utilisé pour ces URL. Si vous voulez personnaliser le schéma par défaut, remplacez la configuration par ceci :

	window.azureEngagement = {
	  ...
	  urls: {
        ...	     
	    scheme: '//'
	  }
	};

<!---HONumber=AcomDC_0615_2016-->