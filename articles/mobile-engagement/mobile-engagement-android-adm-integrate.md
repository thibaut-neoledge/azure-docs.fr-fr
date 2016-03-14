<properties
	pageTitle="Intégration du SDK Android d'Azure Mobile Engagement"
	description="Dernières mises à jour et procédures du SDK Android pour Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="piyushjo" />


#Comment intégrer ADM à Engagement

> [AZURE.IMPORTANT] Vous devez suivre la procédure d'intégration décrite dans le document « Comment intégrer Engagement sur Android » avant de suivre ce guide.
>
> Ce document ne vous sera utile que si vous avez intégré le module de couverture et configuré une remise de campagnes à tout moment. Pour intégrer les couvertures campagnes à votre application, lisez d'abord « Comment intégrer le module de couverture Engagement sur Android ».

##Introduction

L'intégration d'ADM permet à votre application d’être envoyée lorsque vous ciblez des appareils Android Amazon.

Les charges utiles ADM envoyées vers le Kit de développement logiciel (SDK) contiennent toujours la clé `azme` dans l'objet de données. Donc si vous utilisez ADM à d’autres fins dans votre application, vous pouvez filtrer les notifications push en fonction de cette clé.

> [AZURE.IMPORTANT] Seuls les appareils Amazon Kindle exécutant Android 4.0.3 ou version ultérieure sont pris en charge par Amazon Device Messaging. Vous pouvez toutefois intégrer ce code en toute sécurité sur d'autres appareils.

##S'inscrire à ADM

Si vous ne l'avez pas déjà fait, vous devez activer ADM sur votre compte Amazon.

La procédure détaillée est disponible ici : [<https://developer.amazon.com/sdk/adm/credentials.html>].

À la fin de la procédure, vous obtenez :

-   Des informations d'identification OAuth (un ID client et une clé secrète client) pour qu'Engagement puisse effectuer un Push sur vos appareils.
-   Une clé d'API qui doit être intégrée à votre application.

##Intégration du SDK

### Gestion des inscriptions des appareils

Chaque appareil doit envoyer une commande d'inscription aux serveurs ADM, sinon il ne pourra pas recevoir de campagnes.

Si vous utilisez déjà la [bibliothèque cliente ADM] et que vous avez déjà [intégré ADM], vous pouvez accéder directement au fichier android-sdk-adm-receive.

Si vous n'avez pas encore intégré ADM, Engagement vous permet de l'activer dans votre application encore plus facilement :

Modifiez le fichier `AndroidManifest.xml` :

-   Ajoutez l'espace de noms Amazon. Le fichier doit commencer ainsi :

		<?xml version="1.0" encoding="utf-8"?>
		<manifest xmlns:android="http://schemas.android.com/apk/res/android"
		          xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   Dans la balise `<application/>`, ajoutez la section suivante :

		<amazon:enable-feature
		   android:name="com.amazon.device.messaging"
		   android:required="false"/>

		<meta-data android:name="engagement:adm:register" android:value="true" />

-   Après l'ajout de la balise Amazon, vous pouvez rencontrer une erreur de build si la cible de la build du projet est antérieure à Android 2.1. Vous devez utiliser une cible de build **Android 2.1 +** (vous pourrez toujours avoir un `minSdkVersion` défini sur 4).
-   Intégrez la clé d'API ADM en tant que ressource en suivant [cette procédure].

Ensuite, suivez les instructions des sections ci-dessous.

### Communiquer l'ID d'inscription au service Push d'Engagement et recevoir des notifications

Pour communiquer l'ID d'inscription de l'appareil au service Push d'Engagement et recevoir ses notifications, ajoutez le code suivant au fichier `AndroidManifest.xml`, dans la balise `<application/>` (même si vous utilisez ADM sans Engagement) :

		<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
		  android:exported="false">
		  <intent-filter>
		    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
		  </intent-filter>
		</receiver>

		 <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
		   android:permission="com.amazon.device.messaging.permission.SEND">
		  <intent-filter>
		    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
		    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
		    <category android:name="<your_package_name>"/>
		  </intent-filter>
		</receiver>   

Assurez-vous de disposer des autorisations suivantes dans votre `AndroidManifest.xml` (avant la balise `</application>`).

		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
		<uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
		<permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##Accorder à Engagement des informations d'identification OAuth

Envoyez vos informations d'identification OAuth (ID client et question secrète du client) ici : $/#application/YOUR\_APPID/native-push.

Vous pouvez maintenant sélectionner l'option À tout moment lors de la création d'annonces et de sondages dans le module de couverture.


[<https://developer.amazon.com/sdk/adm/credentials.html>]: https://developer.amazon.com/sdk/adm/credentials.html
[bibliothèque cliente ADM]: https://developer.amazon.com/sdk/adm/setup.html
[intégré ADM]: https://developer.amazon.com/sdk/adm/integrating-app.html
[cette procédure]: https://developer.amazon.com/sdk/adm/integrating-app.html#Asset

<!---HONumber=AcomDC_0302_2016-->