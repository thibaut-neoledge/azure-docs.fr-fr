---
title: Interface utilisateur d&quot;Azure Mobile Engagement - Segments
description: "Découvrez comment créer et gérer des segments d&quot;utilisateurs afin d&quot;identifier des modèles d&quot;utilisation grâce à Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 6a4f2205-4a3c-406e-a04f-5e6f2a36653f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 555342e88c912a3f43c578a40dc34933996ade4c
ms.openlocfilehash: 84d1e7d704b6142005217625cfe505f382b4d938


---
# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>Comment créer et gérer des segments d'utilisateurs afin d'identifier des modèles d'utilisation
Cet article décrit l’onglet **SEGMENTS** du portail **Mobile Engagement**. Le portail **Mobile Engagement** sert à surveiller et à gérer vos applications mobiles.

La section Segments de l'interface utilisateur vous permet de segmenter vos utilisateurs en fonction des différents comportements et analyses que vous pouvez obtenir à partir de l'application et via l'API Segments. Les segments sont calculés une première fois 24 heures après leur création, puis recalculés toutes les 24 heures à partir des dernières informations d'analyse. Une fois un segment calculé, il affiche un graphique « Historique journalier » chaque jour.

> [!NOTE]
> De nombreuses sections de l’interface utilisateur du portail **Mobile Engagement** contiennent un bouton **AFFICHER L’AIDE**. Appuyez sur ce bouton pour obtenir des informations contextuelles supplémentaires sur une section.
> 
> 

## <a name="create-segments"></a>Créer des segments
Vous pouvez créer un segment à partir de 10 critères maximum sur une période passée donnée de 60 jours maximum à partir de la section Analyse. Par exemple, vous pouvez créer un segment à partir des personnes ayant consulté certaines pages ou recherché un contenu spécifique dans votre application au cours des 10 derniers jours. Ces informations sont disponibles dans la section Analyse. Vous pouvez donc les utiliser pour créer un segment, puis configurer une notification Push pour cibler ce sous-ensemble d'utilisateurs afin de les faire revenir à l'application. 

> [!NOTE]
> une fois un segment calculé, il ne peut pas être modifié. Il peut uniquement être cloné (copié) ou détruit (supprimé). Un segment peut être cloné dans la même application (avec le même AppID), mais aussi dans d'autres applications (avec un AppID différent). 

 ![segments1][35] 

## <a name="examples-segments"></a>Exemples de segments
 ![segments2][36]

L'outil Segments vous permet de segmenter les utilisateurs finaux de votre application.
La segmentation de vos utilisateurs est une stratégie marketing importante. Azure Mobile Engagement permet d'obtenir des données historiques et de créer des segments personnalisés. Cet outil puissant permet d'en savoir plus sur l'expérience de vos clients dans votre application. Vous pouvez facilement analyser vos segments et les utiliser comme cibles de notification Push.
Il est courant de vouloir envoyer une notification Push pour inviter les utilisateurs finaux à évaluer votre application dans Windows Store. Au lieu d'envoyer une notification à tous vos utilisateurs finaux, vous pouvez créer un segment contenant uniquement les utilisateurs ayant utilisé votre application tous les jours au cours du mois dernier et ayant une bonne expérience utilisateur. Avec des notifications Push moins nombreuses et extrêmement ciblées, vous obtenez un meilleur retour sur investissement.

 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>Segments que vous pouvez créer à partir des principaux éléments d'Azure Mobile Engagement :
* Événement : créez un segment qui cible un événement spécifique de l'application qui s'est produit plus de deux fois par semaine. 
* Session : créez un segment avec les utilisateurs ayant utilisé l'application plus de 5 fois au cours de la dernière semaine.
* Activité : créez un segment avec les utilisateurs ayant utilisé une page ou un contenu plus ou moins de 10 fois au cours du mois dernier.
* Tâche : créez un segment avec les utilisateurs ayant effectué une tâche plus de deux fois par jour.
* Incident : créez un segment avec tous les utilisateurs dont l'application s'est bloquée plus de 10 fois au cours de la dernière semaine. (Vous pourriez envoyer une notification Push à ce segment pour présenter des excuses ou offrir un coupon !)
* Erreur : créez un segment avec tous les utilisateurs ayant rencontré plus de 100 erreurs au cours des 3 derniers jours.
* Infos de l'application : créez un segment qui cible une information de l'application personnalisée au cours des 25 derniers jours.
  
  ![segments4][38]

Pour une utilisation optimale de l'outil Segments, vous devez effectuer une intégration personnalisée du Kit de développement logiciel (SDK) de votre application avec un plan de marquage des balises « Infos de l'application ».
Ensuite, accédez à la page d'accueil de l'interface, sélectionnez l'application désirée et cliquez sur la section « Segments ».

1. Sélectionnez la section « Segments ».
2. Cliquez sur « Nouveau segment » pour créer un segment.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>Exemple en conditions réelles : créer un segment simple en fonction des informations de « Session »
Créez un segment avec tous les utilisateurs finaux ayant utilisé votre application au moins 50 fois au cours de la dernière semaine. Ensuite, recherchez uniquement les utilisateurs finaux ayant passé au moins 30 secondes dans votre application par session. Vous obtiendrez tous les utilisateurs ayant une expérience positive avec votre application. Ensuite, vous pouvez utiliser le segment créé pour envoyer à ces utilisateurs finaux une notification leur demandant d'évaluer votre application dans Windows Store.

 ![segments5][39]

1. Nommez votre segment afin de le retrouver rapidement dans la liste « Segment ».
2. Cliquez sur le bouton « Créer ».
   
   ![segments6][40]

Sélectionnez Session.

 ![segments7][41]

1. Sélectionnez la période « Dernière semaine ».
2. Cliquez sur Suivant.
   
   ![segments8][42]
3. Sélectionnez l'opérateur pertinent dans la liste : =; ≥, ≤.
4. Entrez le nombre souhaité.
5. Sélectionnez l'occurrence désirée. 
6. Cliquez sur Suivant.
   Cet exemple est défini pour trouver les utilisateurs ayant effectué au moins 50 sessions au cours de la dernière semaine.
   
   ![segments9][43]

Pour la segmentation de session, vous pouvez choisir la longueur par session comme critère.

1. Sélectionnez l'opérateur dans la liste.
2. Indiquez la longueur par session.
3. Cliquez sur Suivant.
   Dans cet exemple, on indique que l'on veut sélectionner uniquement les utilisateurs ayant passé plus de 30 secondes par section, parmi toutes les sessions correspondant au segment de la section Occurrence.
   
   ![segments10][44]

Nommez votre critère afin de le récupérer dans la synthèse complète, puis cliquez sur Terminer.

 ![segments11][45]

Une fois votre critère défini, il apparaît dans la synthèse du segment.
Comme un segment est basé sur les données d'analyse, les segments sont calculés une fois par jour.
Dans cet exemple, 47,7 % des utilisateurs finaux répondent au critère. Il doit s'agir d'utilisateurs ayant une bonne expérience et susceptibles de fournir une meilleure évaluation si vous leur envoyez une notification leur demandant d'évaluer l'application dans Windows Store.

## <a name="see-also"></a>Voir aussi
* [Concepts][Lien 6]
* [Guide de résolution des problèmes - Service][Lien 24]

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
[Lien 1]: mobile-engagement-user-interface.md
[Lien 2]: mobile-engagement-troubleshooting-guide.md
[Lien 3]: mobile-engagement-how-tos.md
[Lien 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Lien 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Lien 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Lien 7]: https://account.windowsazure.com/PreviewFeatures
[Lien 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Lien 9]: http://azure.microsoft.com/services/mobile-engagement/
[Lien 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Lien 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Lien 12]: mobile-engagement-user-interface-navigation.md
[Lien 13]: mobile-engagement-user-interface-home.md
[Lien 14]: mobile-engagement-user-interface-my-account.md
[Lien 15]: mobile-engagement-user-interface-analytics.md
[Lien 16]: mobile-engagement-user-interface-monitor.md
[Lien 17]: mobile-engagement-user-interface-reach.md
[Lien 18]: mobile-engagement-user-interface-segments.md
[Lien 19]: mobile-engagement-user-interface-dashboard.md
[Lien 20]: mobile-engagement-user-interface-settings.md
[Lien 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Lien 22]: mobile-engagement-troubleshooting-guide-apis.md
[Lien 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Lien 24]: mobile-engagement-troubleshooting-guide-service.md
[Lien 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Lien 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Lien 27]: ../mobile-engagement-how-tos-first-push.md
[Lien 28]: ../mobile-engagement-how-tos-test-campaign.md
[Lien 29]: ../mobile-engagement-how-tos-personalize-push.md
[Lien 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Lien 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Lien 32]: ../mobile-engagement-how-tos-text-view.md
[Lien 33]: ../mobile-engagement-how-tos-web-view.md




<!--HONumber=Nov16_HO3-->


