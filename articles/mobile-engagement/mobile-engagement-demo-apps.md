<properties
	pageTitle="Application de démonstration d’Azure Mobile Engagement"
	description="Explique où télécharger, comment utiliser et tirer parti des avantages de l’application de démonstration Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2016"
	ms.author="piyushjo" />

# Application de démonstration d’Azure Mobile Engagement

Nous avons publié une application de démonstration d’Azure Mobile Engagement pour les plateformes **iOS**, **Android** et **Windows** afin de vous aider à trouver des ressources utiles et à en savoir plus sur Azure Mobile Engagement.

Grâce à cette application, vous pouvez :

1. Trouver facilement des liens utiles vers des ressources spécifiques d’Azure Mobile Engagement, telles que des vidéos, de la documentation, le forum d’assistance, le centre d’envoi de demandes de fonctionnalités, etc. 
2. Tester des exemples de notifications prises en charge par Azure Mobile Engagement afin de trouver des idées pour vos propres applications mobiles. 
3. Obtenir une implémentation de référence que vous pouvez utiliser pour étudier la manière d’implémenter Mobile Engagement dans votre propre application dans le but de : 

	- collecter des données d’analyse ; 
	- déployer des scénarios de notification avancés de type *Plein écran interstitiel* ou *Fenêtre contextuelle* ;
	- implémenter des enquêtes/sondages ;
	- implémenter des scénarios push/push données sans assistance.   

## Installation de l’application
Cette application est disponible dans les boutiques d’applications respectives :

1. **Windows Universal Demo App**

	- [Lien de téléchargement disponible dans le Windows App Store](https://www.microsoft.com/fr-FR/store/apps/azure-mobile-engagement/9nblggh4qmh2) 
	- L’application a été développée en tant qu’application universelle Windows 10 et son code source est disponible sur [Github](https://github.com/Azure/azure-mobile-engagement-app-ios)

2. **iOS Demo App**

	- [Lien de téléchargement disponible dans l’Apple Store](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090) 
	- L’application a été développée dans iOS Swift et son code source est disponible sur [Github](https://github.com/Azure/azure-mobile-engagement-app-ios)

3. **Android Demo App**

	- [Lien de téléchargement disponible dans Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement)
	- Le code source est disponible sur [Github](https://github.com/Azure/azure-mobile-engagement-app-android)

![][1]

![][2] ![][3]


## Usage

Vous pouvez utiliser ces applications de différentes manières :

**1) Téléchargez les applications sur votre appareil à partir des liens des boutiques d’applications fournis ci-dessus.**

>[AZURE.IMPORTANT] Vous n’avez pas besoin de compte Azure et n’avez pas à connecter l’application à un serveur principal. L’application fonctionne de façon indépendante.

- Une fois l’application téléchargée sur votre appareil, faites défiler les liens situés dans le menu de gauche pour rechercher toutes les ressources utiles concernant Azure Mobile Engagement. 
- Nous avons également incorporé le [flux RSS de notre service](https://aka.ms/azmerssfeed) directement dans cette application afin de vous tenir informé des dernières mises à jour de produits.
- Vous pouvez également parcourir les exemples de scénarios de notification pour découvrir les types de notifications prises en charge par Azure Mobile Engagement pour chacune des plateformes. Ces notifications peuvent être testées en local ; par exemple, vous pouvez cliquer sur les boutons des écrans pour obtenir une expérience de notifications identique à celle dont vous bénéficiez lorsque vous envoyez les notifications à partir de la plateforme Azure Mobile Engagement. 

![][4]
    
![][5] ![][6]

**(2) Téléchargez le code source à partir des liens Github fournis ci-dessus.**

- Une fois que vous avez téléchargé le code source, ouvrez-le dans votre environnement de développement, c’est-à-dire XCode pour iOS, Android Studio pour Android et Visual Studio pour Windows. 
- Nous vous invitons ensuite à suivre les [étapes de base de l’intégration du SDK](mobile-engagement-windows-store-dotnet-get-started.md) afin d’être en mesure de connecter cette application à sa propre instance de serveur principal Mobile Engagement. 
	- Vous devez configurer une chaîne de connexion dans l’application.  
	- Vous devez également configurer la plateforme de notifications push pour votre application. 
- Vous remarquerez que cette application est en soi instrumentée avec Azure Mobile Engagement. Par conséquent, lorsque vous ouvrez l’application après l’avoir connectée au serveur principal, la session utilisateur, les activités, les événements, etc. seront visibles sous l’onglet Surveillance. 
- Vous pourrez également envoyer des notifications à cette application à partir de votre propre instance Mobile Engagement, sans avoir à recourir à des notifications locales. 
	- Vous pouvez ici ajouter votre périphérique en tant que périphérique de test dans l’application à l’aide de l’élément de menu **Obtenir l’ID de périphérique**, de manière à obtenir un ID de périphérique que vous inscrirez ensuite en tant que périphérique de test auprès de l’instance de serveur principal de votre plateforme. 

	![][7]
	    
	![][8] ![][9]

## Principales fonctionnalités de l’application de démonstration

1. Comme nous l’avons indiqué ci-dessus, cette application réunit toutes les ressources essentielles liées à Azure Mobile Engagement. Vous pouvez faire défiler les liens dans le menu de gauche. 

2. Pour chaque plateforme, testez les notifications hors de l’application. Ces notifications peuvent être fournies sous forme de **notification uniquement** ; dans ce cas, le fait de cliquer sur la notification aura simplement pour effet d’ouvrir un écran natif de l’application (à l’aide de **liens profonds**) ; ou sous forme d’**annonce web**, auquel cas vous pouvez fournir du contenu HTML supplémentaire à partir du serveur principal Mobile Engagement qui sera affiché chaque fois que l’utilisateur cliquera sur la notification.

	![][29]

	
	- Dans iOS, vous devez fermer l’application pour voir les notifications hors de l’application ou les notifications push système. Vous pouvez examiner ici l’implémentation pour ajouter des **boutons d’action** comme ceux ajoutés ici à cette notification hors de l’application pour les fonctions *Commentaires* et *Partage* afin que l’utilisateur puisse prendre des mesures directement à partir de la notification. 
	    
	![][11] ![][14]
	
	
	- Sous Android, les options prises en charge par Android apparaissent dans la notification sous la forme d’un texte multiligne (**Big Text**) ou d’une image (**Big Picture**) ajouté(e) à la notification parallèlement aux **boutons d’action** pris en charge par iOS. 
	
	![][12] ![][15]
	
	
	- Sur Windows 10, vous pouvez visualiser les notifications telles qu’elles apparaîtront sur le PC. Cette notification s’affichera également dans le **Centre de notifications** Windows 10. L’ajout de **boutons d’action** n’est pour le moment pas pris en charge avec le Kit de développement logiciel Windows. 
	
	![][10] ![][13]

3. Pour chaque plateforme, testez les notifications dans l’application. Cette expérience se déroule en deux temps : vous allez voir tout d’abord une fenêtre **Notification** qui, lorsque vous cliquerez dessus, ouvrira une fenêtre **Annonce** en plein écran, comme illustré ci-dessous. Le contenu de cette annonce est dérivé de votre instance de serveur principal Mobile Engagement. Le SDK comporte les modèles de notification et d’annonce, que vous pouvez facilement personnaliser, par exemple en ajoutant un logo et de la couleur comme dans cette application de démonstration.

	![][16]
	
	![][17] **iOS** ![][18] **Android**

4. Vous pouvez également utiliser la fonction **Catégorie** d’Azure Mobile Engagement pour personnaliser cette expérience par défaut. Dans l’application de démonstration, nous avons présenté deux façons courantes de modifier l’expérience des notifications. Notez que la fonction Catégorie n’est pas encore prise en charge dans le Kit de développement logiciel Windows.

	**Plein écran interstitiel**
	
	![][30]

	![][21] ![][22]

	**Notification contextuelle**

	![][31]
	
	![][19] ![][20]

5. Azure Mobile Engagement prend également en charge un type particulier de notification dans l’application appelée **Sondages**, qui vous permet d’envoyer des enquêtes rapides à vos utilisateurs d’application segmentés. Vous pouvez ajouter des questions et des options pour chaque question, comme dans l’exemple ci-dessous, qui apparaîtront ensuite à l’utilisateur de l’application sous la forme d’une notification dans l’application.

	![][32]

	![][26]
	    
	![][27] **iOS** ![][28] **Android**

5. Azure Mobile Engagement prend également en charge l’envoi en mode silencieux de notifications **Push de données**, qui permettent d’envoyer certaines données de votre service, comme les données JSON dans l’exemple suivant, que vous pouvez gérer et exploiter directement dans votre application. Par exemple, comment modifier le prix d’un article de manière sélective à l’aide de la notification Push de données.

	![][33]

	![][23]
	    
	![][24] **iOS** ![][25] **Android**

> [AZURE.NOTE] Notez que vous pouvez afficher des instructions détaillées pour chacune de ces notifications en cliquant sur le lien *Cliquez ici pour obtenir des instructions sur l’envoi de ces notifications à partir de la plateforme Mobile Engagement* depuis n’importe quel écran d’exemple de notification.


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png

<!---HONumber=AcomDC_0504_2016-->