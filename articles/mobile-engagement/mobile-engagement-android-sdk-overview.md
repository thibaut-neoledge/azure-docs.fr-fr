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

##4\.1.2 (25/11/2015)

- Améliorations de la stabilité.

##4\.1.1 (11/04/2015)

- Améliorations de la stabilité.

##4\.1.0 (25/08/2015)

- Gestion du nouveau modèle d’autorisation pour Android M.
- Vous pouvez désormais configurer les fonctionnalités de localisation lors de l’exécution au lieu d’utiliser `AndroidManifest.xml`.
- Résolution d’un bogue d’autorisation : si vous utilisez `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` n’est plus nécessaire.
- Améliorations de la stabilité.

Pour les versions antérieures, consultez les [notes de publication complètes](mobile-engagement-android-release-notes.md).

##Procédures de mise à niveau

Si vous avez déjà intégré une version plus ancienne de notre kit de développement logiciel (SDK) dans votre application, consultez [Procédures de mise à niveau](mobile-engagement-android-upgrade-procedure.md).

<!---HONumber=AcomDC_1203_2015-->