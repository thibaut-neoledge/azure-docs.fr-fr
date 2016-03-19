<properties 
	pageTitle="Contenu du Kit de développement logiciel (SDK) des applications Windows Universal" 
	description="Découvrez le contenu du Kit de développement logiciel (SDK) des applications Windows Universal pour Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

#Contenu du Kit de développement logiciel (SDK) des applications Windows Universal

Ce document répertorie et décrit le contenu déployé par le Kit de développement dans votre application.

##Le dossier `/Resources`

Ce dossier contient toutes les ressources dont Mobile Engagement a besoin. Vous pouvez également les personnaliser pour les adapter à votre application.

- `EngagementConfiguration.xml` : fichier de configuration de Mobile Engagement. C'est dans ce fichier que vous pouvez personnaliser les paramètres de Mobile Engagement (chaîne de connexion Mobile Engagement, rapport sur les incidents, etc.).

### Dossier /html

- `EngagementNotification.html` : la conception HTML de la vue web de `Notification`.

- `EngagementAnnouncement.html` : la conception HTML de la vue web de `Announcement`.

### Dossier /images

- `EngagementIconNotification.png` : l’icône de marque affichée à gauche d'une notification. Remplacez celle-ci par l'icône de votre marque.

- `EngagementIconOk.png` : l’icône `Ok` des pages de contenu de Couverture pour le bouton d'action ou de validation.

- `EngagementIconNOK.png` : l’icône `NOK` utilisée quand le bouton de validation des pages de contenu de Couverture est désactivé.
 
- `EngagementIconClose.png` : l’icône `Close` des notifications et du contenu de couverture pour le bouton Ignorer.

### Dossier /overlay

- `EngagementBaseOverlay.cs` : le code de base utilisé par les superpositions `Announcement` et `Notification`.

- `EngagementOverlayAnnouncement.xaml` : la conception XAML de `Announcement`.

- `EngagementOverlayAnnouncement.xaml.cs` : le code relatif à `EngagementOverlayAnnouncement.xaml`.
 
- `EngagementOverlayNotification.xaml` : la conception XAML de `Notification`.
 
- `EngagementOverlayNotification.xaml.cs` : le code relatif à `EngagementOverlayNotification.xaml`.
 
- `EngagementPageOverlay.cs` : le code d'affichage de la superposition des annonces et des notifications `Overlay`.
  

<!---HONumber=AcomDC_0302_2016-->