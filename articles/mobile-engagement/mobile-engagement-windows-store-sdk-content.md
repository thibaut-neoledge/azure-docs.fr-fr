---
title: Contenu du Kit de développement logiciel (SDK) des applications Windows Universal
description: Découvrez le contenu du Kit de développement logiciel (SDK) des applications Windows Universal pour Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo

---
# Contenu du Kit de développement logiciel (SDK) des applications Windows Universal
Ce document répertorie et décrit le contenu déployé par le Kit de développement dans votre application.

## Le dossier `/Resources`
Ce dossier contient toutes les ressources dont Mobile Engagement a besoin. Vous pouvez également les personnaliser pour les adapter à votre application.

* `EngagementConfiguration.xml` : fichier de configuration de Mobile Engagement. C'est dans ce fichier que vous pouvez personnaliser les paramètres de Mobile Engagement (chaîne de connexion Mobile Engagement, rapport sur les incidents, etc.).

### Dossier /html
* `EngagementNotification.html` : conception html de la vue web `Notification` pour les bannières dans l'application.
* `EngagementAnnouncement.html` : conception html de la vue web `Announcement` pour les bannières dans les vues interstitielles.

### Dossier /images
* `EngagementIconNotification.png` : l’icône de marque affichée à gauche d'une notification. Remplacez celle-ci par l'icône de votre marque.
* `EngagementIconOk.png` : l’icône `Ok` des pages de contenu de Couverture pour le bouton d'action ou de validation.
* `EngagementIconNOK.png` : l’icône `NOK` utilisée quand le bouton de validation des pages de contenu de Couverture est désactivé.
* `EngagementIconClose.png` : l’icône `Close` des notifications et du contenu de couverture pour le bouton Ignorer.

### Dossier /overlay
* `EngagementPageOverlay.cs` : page de superposition responsable de l'ajout de l'interface utilisateur Engagement Reach dans l'application à son enfant.

<!---HONumber=AcomDC_0824_2016-->