<properties
	pageTitle="Notes de publication du Kit de développement logiciel (SDK) Azure Mobile Engagement pour iOS"
	description="Dernières mises à jour et procédures du Kit de développement logiciel (SDK) iOS pour Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr"/>

#Notes de publication

##3\.1.0 (26/08/2015)

-   Résolution d’un bogue de compatibilité iOS 9 avec une bibliothèque tierce. Ce bogue provoquait des blocages pendant l’envoi des résultats des sondages, d’informations sur l’application ou de données supplémentaires.

##3\.0.0 (19/06/2015)

-   Mobile Engagement utilise des notifications Push Silent.
-   Prise en charge d’iOS 4.X abandonnée. À partir de cette version, la cible de déploiement de votre application doit être au moins iOS 6.

##2\.2.0 (21/05/2015)

-   L’ID d’appareil Mobile Engagement pour les appareils iOS version 6 et inférieure est désormais basé sur un GUID généré au moment de l’installation.

##2\.1.0 (24/04/2015)

-   Compatibilité Swift ajoutée.
-   Lorsque vous cliquez sur une notification, l'URL d’action s’exécute maintenant juste après l'ouverture de l'application.
-   Ajout du fichier d'en-tête manquant dans le package du Kit de développement logiciel (SDK).
-   Correction d’un problème lorsque le générateur de rapport d’incident d’Engagement Mobile a été désactivé.

##2\.0.0 (17/02/2015)

-   Version initiale d'Azure Engagement Mobile
-   La configuration d'appId/sdkKey est remplacée par une configuration de chaîne de connexion.
-   Suppression de l'API pour envoyer et recevoir des messages XMPP arbitraires d'entités XMPP arbitraires.
-   Suppression de l'API pour envoyer et recevoir des messages entre périphériques.
-   Améliorations de sécurité.
-   Suppression du suivi SmartAd.

<!---HONumber=August15_HO9-->