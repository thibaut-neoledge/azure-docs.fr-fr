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
	ms.date="08/10/2015" 
	ms.author="piyushjo" />


#SDK Android pour Azure Mobile Engagement

Commencez par là pour obtenir tous les détails de l'intégration d'Azure Mobile Engagement dans une application Android. Si vous souhaitez tout d'abord l'essayer, pensez à effectuer notre [didacticiel de 15 minutes](mobile-engagement-android-get-started.md).

Cliquez pour voir le [contenu du Kit de développement logiciel](mobile-engagement-android-sdk-content.md).

##Procédures d'intégration
1. Démarrez ici : [comment intégrer Mobile Engagement dans votre application Android](mobile-engagement-android-integrate-engagement.md)

2. Pour les notifications : [comment intégrer le module Couverture (notifications) à votre application Android](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM) : [comment intégrer GCM à Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM) : [comment intégrer ADM à Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. Implémentation du plan de balise : [utilisation de l'API avancée de balisage de Mobile Engagement dans votre application Android](mobile-engagement-android-use-engagement-api.md)


##Notes de publication

###4\.0.0 (06/07/2015)

-   Modifications de protocole interne pour rendre les analyses et la diffusion plus fiables.
-   Native Push (GCM/ADM) est désormais également utilisé pour les notifications dans l’application. Vous devez donc configurer les informations d’identification Native Push pour tout type de campagne push.
-   Correction de la notification de la vue d’ensemble : elle ne s’affichait que 10 s après avoir été transmise.
-   Correction du clic sur un lien dans une annonce web qui comprend une URL d’action par défaut.
-   Résolution d’un blocage rare lié à la gestion du stockage local.
-   Correction de la gestion des chaînes de configuration dynamique.
-   Mise à jour du CLUF.

Pour les versions antérieures, consultez les [notes de publication complètes](mobile-engagement-android-release-notes.md).

##Procédures de mise à niveau

Si vous avez déjà intégré une ancienne version de notre SDK à votre application, tenez compte des points suivants avant de procéder à la mise à niveau du SDK.

Si vous avez manqué plusieurs versions du Kit de développement logiciel (SDK), vous devrez peut-être effectuer plusieurs procédures. Consultez l’ensemble des [procédures de mise à niveau](mobile-engagement-android-upgrade-procedure.md) pour les découvrir. Par exemple, si vous migrez de la version 1.4.0 vers 1.6.0, vous devez tout d'abord suivre la procédure « Migration de 1.4.0 vers 1.5.0 », puis la procédure « Migration de 1.5.0 vers 1.6.0 ».

Quelle que soit la version que vous mettez à niveau, vous devez remplacer `mobile-engagement-VERSION.jar`.

###Migration de 3.0.0 vers 4.0.0

#### Native Push

Native Push (GCM/ADM) est désormais également utilisé pour les notifications dans l’application. Vous devez donc configurer les informations d’identification Native Push pour tout type de campagne push.

Si ce n'est déjà fait, suivez [cette procédure](mobile-engagement-android-integrate-engagement-reach.md#native-push).

#### AndroidManifest.xml

L’intégration de Reach a été modifiée dans ``AndroidManifest.xml``.

Remplacez ceci :

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

par

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

Il se peut désormais qu’un écran de chargement s’affiche lorsque vous cliquez sur une annonce (avec texte/contenu web) ou sur une interrogation. Vous devez ajouter ceci pour que les campagnes fonctionnent dans la version 4.0.0 :

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

#### Ressources

Incorporez le nouveau fichier `res/layout/engagement_loading.xml` dans votre projet.

<!---HONumber=August15_HO7-->