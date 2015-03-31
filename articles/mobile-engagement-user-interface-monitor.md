<properties 
   pageTitle="Interface utilisateur d'Azure Mobile Engagement - Moniteur" 
   description="Vue d'ensemble de l'interface utilisateur pour la section Moniteur d'Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="mattgre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="required" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure Mobile Engagement - Interface utilisateur

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">Introduction</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">Navigation</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">Accueil</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">Mon compte</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">Analyse</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">Moniteur</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">Reach</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">Segments</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Tableau de bord</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">Paramètres</a>
</div>

# Surveiller
 
La section Moniteur de l'interface utilisateur offre des analyses en temps réel et vous permet de définir des alertes lorsque des seuils sont atteints pour la plupart des mêmes informations disponibles dans l'historique de la section " Analyse " de l'interface utilisateur. Le " Glossaire " dans la section " Concepts " comporte des définitions de termes et d'abréviations contenues dans Analyse et Surveillance, telles que : Utilisateur actif, nouvel utilisateur, utilisateur retenu, session, graphique du chemin d'accès de l'utilisateur, carte des utilisateurs, URL de suivi, tendances, activité, événement, tâche, erreur, information supplémentaire, incident et information relative à l'application.

**Voir aussi :** 

-  [Concepts - Glossaire][Link 6], [Guide de résolution des problèmes - Analyse][Link 2]

## Moniteur - Sessions, tâches, événements, erreurs et incidents :
Vous pouvez afficher le nombre d'utilisateurs actuellement dans une session, les écrans sur lesquels ils se trouvent et les actions spécifiques qu'ils effectuent. Vous pouvez afficher l'activité de l'utilisateur répartie selon les sessions, les tâches, les événements, les erreurs et les incidents. Vous pouvez voir l'information actuelle ainsi que les informations de la dernière heure, du dernier jour ou de la dernière semaine. Vous pouvez voir toutes les informations dans chaque catégorie ou les trier suivant la session, la tâche, l'événement, l'erreur ou l'incident.  La surveillance directe est utile lors d'événements tels qu'une campagne Push pour voir si l'action est en légère hausse juste après l'envoi de votre notification Push. 
 
![Monitor1][14]  

## Résolution des problèmes avec la section Surveillance - Événements - Détails :
La génération d'un événement dans votre application à partir de votre appareil de test et sa recherche dans Surveillance - Événements - Détails est l'un des moyens les plus faciles pour trouver l'ID de votre appareil pour votre appareil de test et pour confirmer que l'intégration des sections Analyse, Surveillance et Segments par Azure Mobile Engagement fonctionne à partir de votre application. Une fois que vous avez l'ID de l'appareil de votre appareil de test, vous pouvez l'ajouter à vos appareils de test dans " Mon compte - Appareils ". Si vous ne parvenez pas à générer un événement, assurez-vous qu'Azure Mobile Engagement est bien intégré à votre application Android, iOS, Web, Windows ou Windows Phone à l'aide du Kit de développement logiciel (SDK).

**Voir aussi :** 

-  [Documentation sur le Kit de développement logiciel (SDK)][Link 5]

![Monitor2][15]  

## Résolution des problèmes avec la section Surveillance - Incidents - Détails :
Vous pouvez consulter les informations relatives à l'incident de votre application dans Surveillance - Incidents - Détails pour vous aider à déterminer les raisons de l'incident survenu dans votre application. Nous vous invitons à chercher également des problèmes connus avec chaque version du Kit de développement logiciel (SDK) dans les notes de publication pour chaque version du Kit de développement logiciel (SDK) pour Android, iOS, Web, Windows et Windows Phone. 

**Voir aussi :** 

-  [Documentation sur le Kit de développement logiciel (SDK) - Notes de publication][Link 5]

![Monitor3][16] 

## Moniteur - Alertes :
Vous pouvez également indiquer des conditions pour des alertes qui vous seront automatiquement envoyées par courrier électronique ou message instantané. (Tout les services conformes à XMPP tels que Google Talk de Google ou iChat d'Apple sont pris en charge). Les alertes sont basées sur un seuil de détection prédéfini supérieur (>) ou inférieur à (<) un nombre spécifique de sessions, de tâches, d'événements, d'erreurs ou d'incidents par seconde, par minute ou par heure. Les alertes peuvent surveiller toutes les activités d'un type donné ou simplement surveiller l'activité d'une tâche, d'un événement ou d'une erreur spécifique. Vous pouvez également indiquer un taux de détection minimum qui correspond au temps minimum entre deux notifications pour la même alerte afin de vous assurer que lorsque l'alerte est déclenchée, vous ne recevez qu'une notification toutes les X minutes.
 
![Monitor4][17]

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
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
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
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
