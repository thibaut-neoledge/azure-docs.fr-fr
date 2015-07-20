<properties 
	pageTitle="Intégration du SDK Android d'Azure Mobile Engagement" 
	description="Dernières mises à jour et procédures du SDK Android pour Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#comment intégrer GCM à Mobile Engagement

> [AZURE.IMPORTANT]Vous devez suivre la procédure d'intégration décrite dans le document « Comment intégrer Engagement sur Android » avant de suivre ce guide.
>
> Ce document ne vous sera utile que si vous avez intégré le module de couverture et configuré une remise de campagnes à tout moment. Pour intégrer les couvertures campagnes à votre application, lisez d'abord « Comment intégrer le module de couverture Engagement sur Android ».

##Introduction

L'intégration de GCM permet à votre application de faire l'objet d'un Push, même lorsqu'elle n'est pas en cours d'exécution.

En réalité, les données de campagne ne sont pas envoyées via GCM. Il s'agit simplement d'un signal d'arrière-plan indiquant à l'application de récupérer le Push d'Engagement. Si l'application n'est pas en cours d'exécution quand elle reçoit un Push GCM, une connexion est établie avec les serveurs Engagement pour récupérer le Push. La connexion d'Engagement reste active pendant environ une minute, au cas où l'utilisateur lancerait l'application en réponse au Push.

À titre d'information, Engagement utilise uniquement les messages de type [Envoi pour synchronisation] avec la clé de réduction `engagement.tickle`.

> [AZURE.IMPORTANT]Seuls les appareils disposant d'Android 2.2 ou version ultérieure, de Google Play et d'une connexion d'arrière-plan à Google peuvent être réveillés par GCM. Toutefois, vous pouvez intégrer ce code en toute sécurité dans les anciennes versions du SDK Android et sur les appareils qui ne prennent pas en charge GCM (car il utilise uniquement des intentions). Si l'application ne peut pas être réveillée par GCM, la notification Engagement sera reçue lors du prochain lancement de l'application.


> [AZURE.WARNING]Si votre code client gère les identificateurs d'inscription C2DM alors que le Kit de développement logiciel (SDK) Engagement est configuré pour utiliser GCM, cela entraînera un conflit au niveau des identificateurs d'inscription. Utilisez GCM dans Engagement uniquement si votre code n'utilise pas C2DM.

##S'inscrire à GCM et activer le service GCM

Si vous ne l'avez pas déjà fait, activez le service GCM sur votre compte Google.

Au moment de la rédaction de ce document (5 février 2014), vous pouvez suivre la procédure disponible ici : [<http://developer.android.com/guide/google/gcm/gs.html>].

Suivez cette procédure pour activer GCM dans votre compte. Ne lisez pas la section **Obtention d’une clé API** et revenez à cette page au lieu de suivre la procédure de Google.

Cette procédure explique que le **Numéro de projet** est utilisé comme l'**ID d'expéditeur GCM**. Vous en aurez besoin plus loin dans cette procédure.

> [AZURE.IMPORTANT]Le **Numéro de projet** ne doit pas être confondu avec l'**ID du projet**. L'ID du projet peut désormais être différencié (c'est un nom dans les nouveaux projets). L’élément que vous devez intégrer dans le SDK Engagement est le **Numéro de projet**. Celui-ci se trouve dans le menu **Aperçu** de la [Console développeurs de Google].

##Intégration du SDK

### Gestion des inscriptions des appareils

Chaque appareil doit envoyer une commande d'inscription aux serveurs Google, sinon il ne pourra pas recevoir de campagnes.

Un appareil peut également se désinscrire des notifications GCM (l'appareil est automatiquement désinscrit quand l'application est désinstallée).

Si vous utilisez la [bibliothèque cliente GCM], vous pouvez lire directement android-sdk-gcm-receive.

Si vous n'avez pas encore envoyé l'intention d'inscription, vous pouvez demander à Engagement d'inscrire automatiquement l'appareil.

Pour cela, ajoutez le code suivant au fichier `AndroidManifest.xml`, à l'intérieur de la balise `<application/>` :

			<!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
			<meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### Communiquer l'ID d'inscription au service Push d'Engagement et recevoir des notifications

Pour communiquer l'ID d'inscription de l'appareil au service Push d'Engagement et recevoir ses notifications, ajoutez le code suivant au fichier `AndroidManifest.xml`, à l'intérieur de la balise `<application/>` (même si vous gérez vous-même les inscriptions d'appareil) :

			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			  <intent-filter>
			    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
			    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
			    <category android:name="<your_package_name>" />
			  </intent-filter>
			</receiver>

Assurez-vous de disposer des autorisations suivantes dans votre `AndroidManifest.xml` (après la balise `</application>`).

			<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
			<uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
			<permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##Accorder à Engagement l'accès à la clé d'API du serveur

Si ce n'est déjà fait, créez une **Clé d'API serveur** sur la [Console développeur Google].

La clé serveur **ne doit pas avoir de restrictions d'adresse IP**.

Au moment de la rédaction de ce document (5 février 2014), la procédure à suivre est la suivante :

-   Pour cela, ouvrez la [Console développeur Google].
-   Sélectionnez le même projet que précédemment dans la procédure (celui dont vous avez intégré le **numéro de projet** dans `AndroidManifest.xml`).
-   Accédez à API et authentification -> Informations d’identification. Cliquez sur « CRÉER UNE NOUVELLE CLÉ » dans la section « Accès public API ».
-   Sélectionnez Server key.
-   Sur l'écran suivant, laissez le champ vide **(aucune restriction IP)**, puis cliquez sur Créer.
-   Copiez la **clé d'API** générée.
-   Accédez à $/#application/VOTRE_ID D'APPLICATION_ENGAGEMENT/native-push.
-   Dans la section GCM, remplacez la clé d'API par celle que vous venez de générer et de copier.

Vous pouvez maintenant sélectionner l'option À tout moment lors de la création d'annonces et de sondages dans le module de couverture.

> [AZURE.IMPORTANT]Engagement a besoin d'une **clé serveur**. Une clé Android ne peut pas être utilisée par les serveurs d'Engagement.

##Test

Maintenant, vérifiez votre intégration en lisant « Comment tester l'intégration d'Engagement sur Android ».


[Envoi pour synchronisation]: http://developer.android.com/google/gcm/adv.html#collapsible
[<http://developer.android.com/guide/google/gcm/gs.html>]: http://developer.android.com/guide/google/gcm/gs.html
[Google Developers Console]: https://cloud.google.com/console
[bibliothèque cliente GCM]: http://developer.android.com/guide/google/gcm/gs.html#libs
[Console développeur Google]: https://cloud.google.com/console
[Console développeurs de Google]: https://cloud.google.com/console

 

<!---HONumber=July15_HO2-->