<properties 
   pageTitle="Interface utilisateur d'Azure Engagement Mobile - Accueil" 
   description="Présentation de l'interface utilisateur de la section Accueil d'Azure Mobile Engagement" 
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
<a href="../mobile-engagement-user-interface-reach" title="Reach">Atteindre</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">Segments</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Tableau de bord</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">Paramètres</a>
</div>

# Accueil
 
La section Accueil de l'interface utilisateur contient la liste de toutes vos applications dans Mes Applications, ainsi que la possibilité d'accorder d'autres autorisations à vos applications. Toute personne peut accéder à la page d'accueil de l'interface utilisateur en créant un compte, mais vous devez accorder des autorisations aux autres utilisateurs pour qu'ils puissent accéder à vos applications personnalisées dans Mes projets.

**Voir aussi :**

-  [Guide de dépannage - Service][Link 2]
 
## Mes applications :
 
![Home1][2]

Cette vue d'ensemble rapide de vos applications vous permet de sélectionner les application que vous souhaitez ouvrir pour afficher les options détaillées du ruban. Vous pouvez cliquez sur le nom de votre application pour revenir à l'emplacement du ruban que vous avez visité le plus récemment dans votre application, ou cliquer sur l'icône d'engrenage pour accéder directement à la page " Paramètres " de votre application. Vous pouvez rechercher, filtrer ou trier les informations affichées sur les tables d'application. Vous pouvez également glisser-déposer les en-têtes de colonne pour modifier l'ordre. 
 
La vue d'ensemble de vos applications inclut :

- Nombre total d'utilisateurs (nombre Total d'utilisateurs)
- Tendances des nouveaux utilisateurs (évolution des nouveaux utilisateurs au cours des deux dernières semaines)
- Utilisateurs actifs (nombre d'utilisateurs actifs au cours des 30 derniers jours)
- Tendance des utilisateurs actifs (évolution des utilisateurs actifs au cours des deux dernières semaines)
 
Vous pouvez également voir un graphique comparatif de vos applications.

- Afficher le graphique de comparaison (peut être utilisé pour afficher les données d'application sous forme de graphique)
- Cases à cocher (ajouter/supprimer cette application à/depuis le tableau comparatif)
 
![Home2][3]

## Mes projets :
 
![Home5][6]

Vous pouvez utiliser des projets pour grouper vos applications et accorder des autorisations à vos applications. Le Bouton Nouveau projet permet de créer un nouveau projet en entrant uniquement le " nom " et la " description " de votre projet. Une fois un projet créé, vous pouvez cliquer sur le nom du projet pour modifier le nom et la description de votre produit et sélectionner toutes les applications que vous souhaitez voir dans ce projet. Vous pouvez également supprimer ce projet, ce qui ne détruira pas les applications qu'il référence. Toutefois, vous perdrez l'accès à toutes les applications dont vous n'êtes pas propriétaire et qui ne sont pas accessibles à partir d'un autre projet. Par conséquent, soyez prudent ! 
Vous pouvez également inviter un utilisateur à participer à votre projet en fonction de son adresse de messagerie. Les utilisateurs doivent avoir déjà créé un compte dans Azure Mobile Engagement avant que vous ne puissiez leur accorder des autorisations. 

**Les rôles sont les suivants :**

- Visionneuse : Une visionneuse est un utilisateur qui peut afficher uniquement les applications associées à un projet. Une visionneuse permet d'accéder aux données de l'analyse et du moniteur et d'examiner les résultats Reach. Une visionneuse ne peut pas modifier d'informations, ni gérer des applications ou des utilisateurs. Une visionneuse ne peut pas créer ou modifier le statut de la campagne Reach.
- Developer : Un développeur est un utilisateur qui peut faire une visionneuse peut effectuer, ainsi que gérer des Applications. Un développeur peut activer et désactiver les applications, modifier les informations des applications (telles que le package et la signature) et créer des campagnes Reach. Un développeur ne peut pas gérer les utilisateurs. 
- Administrateur : Un administrateur est un utilisateur qui peut faire tout ce qu'un développeur peut effectuer, ainsi que gérer les utilisateurs. Un administrateur peut inviter des utilisateurs à participer à un projet, modifier les rôles des utilisateurs et les informations du projet. Les autorisations de niveau application peuvent également être définies dans " paramètres ".
 
**Voir aussi :**

-  [Documentation de l'interface utilisateur - paramètres][Link 20]

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
