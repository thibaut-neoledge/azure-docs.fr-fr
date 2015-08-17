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


#Notes de publication

##4\.0.0 (06/07/2015)

-   Modifications de protocole interne pour rendre les analyses et la diffusion plus fiables.
-   Native Push (GCM/ADM) est désormais également utilisé pour les notifications dans l’application. Vous devez donc configurer les informations d’identification Native Push pour tout type de campagne push.
-   Correction de la notification de la vue d’ensemble : elle ne s’affichait que 10 s après avoir été transmise.
-   Correction du clic sur un lien dans une annonce web qui comprend une URL d’action par défaut.
-   Résolution d’un blocage rare lié à la gestion du stockage local.
-   Correction de la gestion des chaînes de configuration dynamique.
-   Mise à jour du CLUF.

##3\.0.0 (17/02/2015)

-   Version initiale d'Azure Engagement Mobile
-   La configuration d'appId est remplacée par la configuration d'une chaîne de connexion.
-   Suppression de l'API pour envoyer et recevoir des messages XMPP arbitraires d'entités XMPP arbitraires.
-   Suppression de l'API pour envoyer et recevoir des messages entre périphériques.
-   Améliorations de sécurité.
-   Suppression du suivi de Google Play et SmartAd.

 

<!---HONumber=August15_HO6-->