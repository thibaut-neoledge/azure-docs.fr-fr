<properties 
   pageTitle="Interface utilisateur d'Azure Mobile Engagement - Reach" 
   description="Découvrez comment atteindre les utilisateurs de votre application avec des notifications Push grâce à Azure Mobile Engagement" 
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


# Comment atteindre les utilisateurs de votre application avec des notifications Push
La section Reach de l'interface utilisateur est l'outil de gestion des campagnes Push qui vous permet de créer, modifier, activer, terminer ou surveiller et obtenir des statistiques relatives aux campagnes de notifications Push et des fonctionnalités. Vous pouvez accéder à celles-ci via l'API Reach (ainsi que certains éléments de l'API Push de bas niveau). Que vous utilisiez ces API ou l'interface utilisateur, il vous faudra intégrer Azure Mobile Engagement et Reach à votre application pour chaque plateforme à l'aide du Kit de développement logiciel (SDK) avant de pouvoir utiliser les campagnes Reach.

### Voir aussi
-  [Documentation sur les API - API Reach][Link 4], [Documentation sur les API - API Push][Link 4], [Guide de résolution des problèmes - Push/Reach][Link 23]
-  [Reach - Campagne][Link 27], [Reach - Critère][Link 28], [Reach - Contenu][Link 29], [Reach - Procédure][Link 3]
 
## Quatre types de notifications Push
1.    Annonces : elles vous permettent d'envoyer aux utilisateurs des messages publicitaires qui les redirigent vers un autre emplacement dans votre application ou les envoient vers une page Web ou un Store en dehors de votre application. 
2.    Sondages : ces notifications vous permettent de regrouper des informations sur les utilisateurs finaux en leur posant des questions.
3.    Push de données : ces notifications vous permettent d'envoyer un fichier de données binaire ou base 64. Les informations contenues dans un Push de données sont envoyées à votre application pour modifier l'expérience actuelle des utilisateurs de votre application. Votre application doit être en mesure de traiter les données dans un Push de données.
4.    Vignettes (Windows Phone uniquement) : ces notifications vous permettent d'utiliser les services de notifications Push Microsoft (MPNS) pour envoyer des notifications Push Windows natives contenant des données XML. (Prises en charge depuis la version 0.9.0 du Kit de développement logiciel (SDK). La charge utile finale des vignettes ne peut excéder 32 kilo-octets.)

### Voir aussi
-  [Concepts - Glossaire][Link 6]

## Trois catégories de statistiques en temps réel sont indiquées pour chaque campagne
1.    Envoyées par notification Push : combien de notifications Push ont été envoyées selon les critères indiqués dans la campagne. 
2.    Répondues : combien d'utilisateurs ont répondu à la notification en l'ouvrant en dehors de l'application ou en la fermant dans l'application. 
3.    Activées : combien d'utilisateurs ont cliqué sur le lien dans la notification pour être redirigés vers un nouvel emplacement dans l'application, vers un Store ou un navigateur web. 

> Remarque : consultez plus de statistiques de campagne détaillées disponibles dans les statistiques de l'API Reach

### Voir aussi
-  [Concepts - Glossaire][Link 6], [Documentation sur les API - API Reach - Statistiques][Link 4]


## Informations relatives à la campagne
Vous pouvez modifier, cloner, supprimer ou activer les campagnes qui n'ont pas encore été activées en plaçant le curseur sur leurs noms ou en cliquant pour les ouvrir. Vous pouvez cloner les campagnes qui ont déjà été activées en plaçant le curseur sur leurs noms ou en cliquant pour les ouvrir. Toutefois, vous ne pouvez pas modifier une campagne une fois celle-ci activée.
 
![Reach1][18]

## Commentaire Reach
Vous pouvez passer de l'affichage des informations à l'affichage des statistiques d'une campagne ouverte qui a déjà été activée. Vous pouvez également passer d'un affichage simple à un affichage avancé des statistiques pour voir plus d'informations détaillées (selon vos autorisations). Vous pouvez aussi utiliser les informations du commentaire Reach d'une campagne précédente comme critères de ciblage d'une nouvelle campagne. Les statistiques du commentaire Reach peuvent également être regroupées à l'aide de « Statistiques » dans l'API Reach. Vous pouvez également personnaliser l'audience de vos campagnes Push en fonction des campagnes précédentes.


### Voir aussi 
-  [Documentation sur l'interface utilisateur (IU) - Reach - Nouvelle campagne Push][27], [Documentation sur les API - API Reach - Statistiques][Link 4]

![Reach2][19]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
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

<!--HONumber=54--> 