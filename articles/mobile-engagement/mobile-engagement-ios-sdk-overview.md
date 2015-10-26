<properties
	pageTitle="Vue d'ensemble du Kit de développement logiciel (SDK) Azure Engagement Mobile pour iOS"
	description="Dernières mises à jour et procédures du Kit de développement logiciel (SDK) iOS pour Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr" />

#Kit de développement logiciel (SDK) iOS pour Azure Mobile Engagement

Cliquez ici pour obtenir tous les détails de l'intégration d'Azure Mobile Engagement dans une application iOS. Si vous souhaitez d’abord faire un essai, pensez à suivre notre [didacticiel de 15 minutes](mobile-engagement-ios-get-started.md).

Cliquez pour voir le [contenu du Kit de développement logiciel (SDK)](mobile-engagement-ios-sdk-content.md)

##Procédures d'intégration
1. Commencez ici : [Intégration de Mobile Engagement dans votre application iOS](mobile-engagement-ios-integrate-engagement.md)

2. Pour les notifications : [Intégration de Reach (Notifications) dans votre application iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Implémentation du plan de balise : [Utilisation de l'API avancée de balisage de Mobile Engagement dans votre application iOS](mobile-engagement-ios-use-engagement-api.md)


##Notes de publication

##3\.2.0 (10/08/2015)

-   Activation de Bitcode dans le Kit de développement logiciel (SDK) pour qu’il fonctionne avec **Xcode 7**.
-   Correction de bogues liés aux notifications dans l’application.
-   Modification des notifications dans l’application pour qu’elles soient plus fiables en cas de batterie faible et dans d’autres cas de figure.
-   Suppression des journaux de console supplémentaires générés par la bibliothèque tierce.

Pour les versions antérieures, consultez les [notes de publication complètes](mobile-engagement-ios-release-notes.md)

##Procédures de mise à niveau

Si vous avez déjà intégré une version antérieure d'Engagement dans votre application, vous devez prendre en compte les points suivants lors de la mise à niveau du Kit de développement logiciel.

Si vous avez manqué plusieurs versions du Kit de développement logiciel (SDK), vous devrez peut-être effectuer plusieurs procédures. Consultez la version complète des [Procédures mises à jour](mobile-engagement-ios-upgrade-procedure.md).

Pour chaque nouvelle version du Kit de développement logiciel, vous devez d'abord remplacer (supprimer et importer de nouveau dans xcode) les dossiers EngagementSDK et EngagementReach.

###Migration de 2.0.0 vers 3.0.0
Prise en charge d’iOS 4.X abandonnée. À partir de cette version, la cible de déploiement de votre application doit être au moins iOS 6.

Si vous utilisez Reach dans votre application, vous devez ajouter la valeur `remote-notification` au tableau `UIBackgroundModes` dans votre fichier Info.plist pour recevoir des notifications à distance.

La méthode `application:didReceiveRemoteNotification:` doit être remplacée par `application:didReceiveRemoteNotification:fetchCompletionHandler:` dans votre délégué d’application.

« AEPushDelegate.h » est une interface déconseillée et vous devez supprimer toutes les références. Cela inclut notamment la suppression de `[[EngagementAgent shared] setPushDelegate:self]` et les méthodes de délégation depuis votre délégué d’application :

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

<!---HONumber=Oct15_HO3-->