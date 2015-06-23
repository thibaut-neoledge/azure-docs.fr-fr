<properties 
   pageTitle="Guide de dépannage d’Azure Mobile Engagement - Service" 
   description="Guides de dépannage pour Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>


# Guide de résolution des problèmes de service

Les éléments suivants sont des problèmes potentiels liés à l’exécution d’Azure Mobile Engagement.

## Interruptions de service

### Problème
- Problèmes qui semblent provenir des interruptions de service d’Azure Mobile Engagement.

### Causes
- Problèmes qui semblent provenir d'Azure Mobile Engagement des interruptions de Service peuvent être dû à plusieurs problèmes différents :
    - Problèmes isolés systémiques à l'origine sur l'ensemble d'Azure Mobile Engagement
    - Problèmes connus provoqués par des pannes de serveur (ne s'affichent pas toujours dans l'état du serveur) :
	- Retards de planification, erreurs de ciblage, problèmes de mise à jour de Badge, arrêt de la collecte des statistiques, arrêt du fonctionnement des notifications Push, arrêt du fonctionnement des API, impossibilité de créer des applications ou des utilisateurs, erreurs DNS et erreurs de délai d’attente dans l’interface utilisateur, les API ou les applications sur un périphérique.
    - Pannes de dépendance du cloud [État du service Azure](http://status.azure.com/), [État des services Amazon Web Services (AWS)](http://status.aws.amazon.com/) 
    - Pannes des dépendances des services de notification Push (PNS) [Google - Service](http://www.google.com/appsstatus#hl=en&v=status), [Apple - Service](http://www.apple.com/support/systemstatus/), [Android - Google GCM](http://developer.android.com/google/gcm/index.html), [Android - Amazon ADM](https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple - APN](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), [Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone - MPNS][LinkMPNS], [Windows - WNS](https://developer.windows.com/)
    - Pannes de l’App Store [GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Windows Phone Store](http://www.windowsphone.com/), [Windows Store](http://windows.microsoft.com/)

*Pour savoir si le problème est systémique, vous pouvez tester la même fonction à partir de différents emplacements :*

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
*Pour savoir si le problème affecte uniquement l’interface utilisateur ou les API :*

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[Documentation sur les API][Link 4], [Documentation de l’interface utilisateur][Link 1]

*Pour tester si le problème provient du réseau de votre téléphone cellulaire :*

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*Pour tester si le problème provient de votre périphérique :*

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[Documentation de l’interface utilisateur - Paramètres][Link 1]

*Pour tester si le problème provient de votre application :*

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

Pour tester si le problème avec les mises à niveau du système d'exploitation à l'utilisateur final les périphériques qui nécessitent une mise à niveau du Kit de développement logiciel pour résoudre :

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[Guide de dépannage - Kit de développement logiciel (SDK)][Link 2]
 
## Problèmes de connectivité et informations incorrectes

### Problème
- Problèmes de connexion à l'interface utilisateur d'Azure Mobile Engagement.
- Erreurs de connexion avec l'API d'Azure Mobile Engagement.
- Problèmes lors du chargement des balises d'informations d'application via l'API de l'appareil.
- Problèmes lors du téléchargement des journaux ou des données exportées à partir d'Azure Mobile Engagement.
- Informations incorrectes indiquées dans l'interface utilisateur de Azure Mobile Engagement.
- Informations incorrectes indiquées dans les journaux Azure Mobile Engagement.

### Causes
- Pour les problèmes de connectivité avec Azure Mobile Engagement :
    - Confirmez que votre compte d'utilisateur dispose des autorisations nécessaires pour effectuer la tâche.
    - Vérifiez que le problème n'est pas isolé sur un seul ordinateur ou votre réseau local.
    - Vérifiez que que le service Azure Mobile Engagement n'a pas signalé d'interruptions.
    - Vérifiez que vos fichiers de balise d'informations d'application respectent toutes ces règles :
        - Utilisez uniquement le jeu de caractères UTF8 (le jeu de caractères ANSI n'est pas pris en charge).
        - Utilisez une virgule « , » comme caractère de séparation (vous pouvez ouvrir une demande de service pour modifier le caractère de séparation .csv afin que ce ne soit plus une virgule (« , ») mais un autre caractère, par exemple un point-virgule « ; »).
        - Utilisez des minuscules pour les valeurs booléennes « true » et « false ».
        - Utilisez un fichier d'une taille inférieure à la taille maximale de 35 Mo.

### Voir aussi

[Documentation sur les API][Link 4], [Documentation de l’interface utilisateur - Accueil][Link 1]
 
## Demandes de fonctionnalités

### Problème
- La fonctionnalité que vous souhaitez utiliser n'apparaît pas encore dans Azure Mobile Engagement.

### Causes

Afin de proposer une nouvelle fonctionnalité pour Azure Mobile Engagement : ouvrez une demande de service Azure Mobile Engagement avec autant de détails que possible sur la nouvelle fonctionnalité que vous souhaiteriez voir dans Azure Mobile Engagement.

### Voir aussi

[Demandes de fonctionnalité ou commentaires pour Mobile Engagement](http://feedback.azure.com/forums/285737-mobile-engagement)

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
[LinkMPNS]: http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx

<!--HONumber=54--> 