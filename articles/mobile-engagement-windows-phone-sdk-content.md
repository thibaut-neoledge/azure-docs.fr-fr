<properties 
	pageTitle="Intégration du Kit de développement logiciel (SDK) Windows Phone pour Azure Mobile Engagement" 
	description="Découvrez le contenu du Kit de développement logiciel (SDK) Windows Phone pour Azure Mobile Engagement." 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />
	
#Contenu du Kit SDK

Ce document répertorie et décrit le contenu de l'archive du Kit SDK.

##Dossier `Root`

Ce dossier contient les copies des licences logicielles et un lien vers la documentation en ligne.

`documentation.html` : contient un lien vers la documentation en ligne du Kit SDK Engagement pour Windows Phone.

Ce dossier contient également les fichiers de licence.

##Dossier `/lib`

Ce dossier contient des informations indiquant où se procurer le Kit SDK Engagement.

`azuresdk-mobileengagement-windowsphone-X.X.X.nupkg` : package NuGet pour l'intégration du Kit SDK.

##Dossier `/Resources`

Ce dossier contient toutes les ressources requises par Engagement. Vous pouvez les personnaliser pour les adapter à votre application.

`EngagementIconNotification.png` : icône de marque affichée à gauche d'une notification.

`EngagementIconOk.png` : icône " OK " de l'élément ApplicationBar des pages Reach.

`EngagementIconCancel.png` : icône " Annuler " de l'élément ApplicationBar des pages Reach.

`EngagementIconCloseLight.png` : icône " Fermer " de la notification Reach d'Engagement pour le thème clair Windows Phone.

`EngagementIconCloseDark.png` : icône " Fermer " de la notification Reach d'Engagement pour le thème sombre Windows Phone.

`EngagementConfiguration.xml` : fichier de configuration d'Engagement dans lequel vous pouvez personnaliser les paramètres d'Engagement (chaîne de connexion Engagement, signalement des incidents...).

##Dossier `/src/agent`

Ce dossier contient l'élément EngagementPage.

`EngagementPage.cs` : classe de base des pages qui signalent automatiquement une activité à Engagement.

##Dossier `/src/reach`

Ce dossier comprend les valeurs XAML par défaut de chaque page (et les valeurs équivalentes en C\#).

Vous pouvez vous en servir comme valeurs de base pour vos propres pages. Examinez simplement les commentaires pour savoir exactement ce qu'il faut faire.

### TextViewAnnouncement

`EngagementDefaultTextViewAnnouncementPage.xaml`

`EngagementDefaultTextViewAnnouncementPage.xaml.cs`

### WebViewAnnouncement

`EngagementDefaultWebViewAnnouncementPage.xaml`

`EngagementDefaultWebViewAnnouncementPage.xaml.cs`

### Poll

`EngagementDefaultPollPage.xaml`

`EngagementDefaultPollPage.xaml.cs`

### Notification

`EngagementBasicNotificationView.xaml`

`EngagementBasicNotificationView.xaml.cs`

<!--HONumber=47-->
