<properties 
	pageTitle="Contenu du Kit de développement logiciel (SDK) Azure Mobile Engagement Windows Store" 
	description="Dernières mises à jour et procédures du Kit de développement logiciel (SDK) Windows Store pour Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Contenu du Kit de développement logiciel (SDK)

Ce document répertorie et décrit le contenu de l'archive du SDK.

##Le dossier  `Root`

Ce dossier contient des copies des licences logicielles, ainsi qu'un lien vers la documentation en ligne.

`documentation.html`  : contient un lien vers la documentation en ligne du Kit de développement logiciel (SDK) Engagement pour Windows 8\Application Metro.

Ce dossier contient également des fichiers de licences.

##Le dossier  `/lib`

Ce dossier contient des informations concernant l'emplacement auquel obtenir le SDK Engagement.

`azuresdk-mobileengagement-windows-X.X.X.nupkg`  : Package Nuget pour l'intégration du SDK.

##Le dossier  `/Resources`

Ce dossier contient toutes les ressources dont Engagement a besoin. Vous pouvez également les personnaliser pour les adapter à votre application.

`EngagementConfiguration.xml`  : fichier de configuration d'Engagement. C'est dans ce fichier que vous pouvez personnaliser les paramètres d'Engagement (chaîne de connexion Engagement, rapport sur les incidents...).

### Dossier /html

`EngagementNotification.html`  : conception HTML de la vue web de la  `notification`.

`EngagementAnnouncement.html`  : conception HTML de la vue web de l' `annonce`.

### Dossier /images

`EngagementIconNotification.png`  : icône de marque affichée à la gauche des notifications.

`EngagementIconOk.png`  : icône  `OK` des pages de contenu de Couverture pour le bouton d'action ou de validation.

`EngagementIconNOK.png`  : icône  `OK` utilisée quand le bouton de validation des pages de contenu de Couverture est désactivé.

`EngagementIconClose.png`  : icône  `Fermer` des notifications et du contenu de couverture pour le bouton Ignorer.

### Dossier /overlay

`EngagementOverlayAnnouncement.xaml`  : conception XAML de l' `annonce`.

`EngagementOverlayAnnouncement.xaml.cs`  : code relatif à  `EngagementOverlayAnnouncement.xaml`.

`EngagementOverlayNotification.xaml`  : conception XAML de la  `notification`.

`EngagementOverlayNotification.xaml.cs`  : code relatif à  `EngagementOverlayNotification.xaml`.

`EngagementPageOverlay.cs`  : code d'affichage de la  `superposition` des annonces et des notifications.

##Le dossier  `/src/agent`

Ce dossier contient EngagementPage.

`EngagementPage.cs`  : classe de base des pages qui signalent automatiquement une activité à Engagement.

<!--HONumber=47-->
