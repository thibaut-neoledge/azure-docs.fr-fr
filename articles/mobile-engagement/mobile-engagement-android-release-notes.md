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
	ms.date="03/10/2016"
	ms.author="piyushjo" />


#Notes de publication

##4\.2.0 (03/11/2016)

- Le Kit de développement logiciel (SDK) est désormais sous licence MIT.
- Autorise la spécification d’un identificateur d’appareil personnalisé au moment de l’initialisation du Kit de développement logiciel (SDK).

##4\.1.5 (01/02/2016)

- Améliorations de la stabilité.

##4\.1.4 (26/01/2016)

- Améliorations de la stabilité.

##4\.1.3 (12/9/2015)

- Améliorations de la stabilité.

##4\.1.2 (25/11/2015)

- Améliorations de la stabilité.

##4\.1.1 (11/04/2015)

- Améliorations de la stabilité.

##4\.1.0 (25/08/2015)

- Gestion du nouveau modèle d’autorisation pour Android M.
- Vous pouvez désormais configurer les fonctionnalités de localisation lors de l’exécution au lieu d’utiliser `AndroidManifest.xml`.
- Résolution d’un bogue d’autorisation : si vous utilisez `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` n’est plus nécessaire.
- Améliorations de la stabilité.

##4\.0.0 (06/07/2015)

-   Modifications de protocole interne pour rendre les analyses et la diffusion plus fiables.
-   Native Push (GCM/ADM) est désormais également utilisé pour les notifications dans l’application. Vous devez donc configurer les informations d’identification Native Push pour tout type de campagne push.
-   Correction de la notification de la vue d’ensemble : elle ne s’affichait que 10 s après avoir été transmise.
-   Résolution d’un bogue dans l’affichage web : un clic sur un lien exécutait également l’URL de l’action par défaut.
-   Résolution d’un blocage rare lié à la gestion du stockage local.
-   Correction de la gestion des chaînes de configuration dynamique.
-   Mise à jour du CLUF.

##3\.0.0 (17/02/2015)

-   Version initiale d'Azure Engagement Mobile
-   La configuration d'appId est remplacée par la configuration d'une chaîne de connexion.
-   Suppression de l'API pour envoyer et recevoir des messages XMPP arbitraires d'entités XMPP arbitraires.
-   Suppression de l'API pour envoyer et recevoir des messages entre appareils.
-   Améliorations de sécurité.
-   Suppression du suivi de Google Play et SmartAd.

<!---HONumber=AcomDC_0316_2016-->