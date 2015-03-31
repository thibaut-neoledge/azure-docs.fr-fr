<properties 
   pageTitle="Interface utilisateur d'Azure Mobile Engagement - Analyses" 
   description="Présentation de l'interface utilisateur d'Azure Mobile Engagement" 
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
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">Analyses</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">Écran</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">Reach</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">Segments</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Tableau de bord</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">Paramètres</a>
</div>

# Analytics
 
La section Analyses de l'interface utilisateur fournit des informations globales sur votre application basées sur des données historiques qui sont mises à jour toutes les 24 heures. Les informations sont affichées sur différents tableaux de bord composés de graphiques linéaires/à barres/en secteurs, de grilles et de cartes. Les données peuvent également être téléchargées sous forme de fichiers .csv. La plupart de ces mêmes informations sont disponibles en temps réel dans la section Écran de l'interface utilisateur, et peuvent également être consultées à partir de l'API Analyses. Le " Glossaire " situé dans " Concepts " comporte des définitions et des abréviations des termes dans Analyses et Surveillance, tels que : Utilisateur actif, nouvel utilisateur, utilisateur retenu, session, graphique du chemin d'accès utilisateur , mappage utilisateur, URL de suivi, tendances, activité, événement, tâche, erreur, informations supplémentaires, incident et informations sur l'application.

**Voir aussi :** 

-  [Concepts - Glossaire][Link 6],[Guide de dépannage - Analyses][Link 2]

## Analyses standard et personnalisées :
Azure Mobile Engagement fournit un ensemble d'informations d'analyses standard, de base, sur vos applications qui peuvent être représentées graphiquement dès que vous intégrez votre application au Kit de développement logiciel. Azure Mobile Engagement permet également de recueillir des informations supplémentaires d'analyses personnalisées que vous désirez obtenir sur le comportement de vos utilisateurs finaux. Vous pouvez réaliser cela en créant un plan de balises des " balises d'informations relatives aux applications " personnalisées, réalisées à partir des " Paramètres " de manière à ce qu'Azure Mobile Engagement puisse collecter ces données supplémentaires pour vous.

**Voir aussi :** 
-  [Documentation de l'interface utilisateur - Paramètres][Link 20]
 
## Entête de l'analyse :
- Nom de l'élément : Étiquette l'élément comptabilisé
- Afficher l'aide : Affiche des informations contextuelles sur cette section
- Versions : Vous permet d'afficher différentes informations d'analyse relatives à chaque version de votre application ou à toutes les versions. Remarque : Le filtrage de vos données d'analyse, dans l'interface utilisateur, affiche tous les exemples de ce type, et ce, quelle que soit la version de votre application. Par exemple, les " Incidents " filtrés par nom s'afficheront depuis la version 1 et la version 2 de votre application.)
- Période : Derniers 7 jours, derniers 30 jours, tout le temps, personnalisé
- Tarif : Par heure, par jour, par semaine, par mois
- Affichage : Graphique linéaire, grille, envoyer vers le tableau de bord, télécharger le fichier CSV
 
![Analytics1][10] 

## Analyses :
- Tableau de bord : Affiche des informations générales sur vos utilisateurs (nouveaux ou bien actifs) et leurs tendances.
- Utilisateurs : Les utilisateurs sont identifiés par l'identifiant de leur appareil : cet identifiant est unique pour chaque appareil(un nouvel utilisateur représente en réalité un nouvel appareil). Un utilisateur est considéré comme nouveau sur une période donnée s'il a effectué sa première session durant cette période. Un utilisateur est considéré comme retenu s'il a effectué au moins une session durant les derniers 7 jours. Les utilisateurs actifs sont ceux qui ont effectué au moins une session durant une période donnée. Vous pouvez les trier par mois, par semaine, par jour ou par heure. Tous les graphiques se ressemblent, mais ils vous permettent de filtrer via différentes fonctionnalités, telles que la version de votre application, puis de trier par période. Les informations standard recueillies en intégrant le Kit de développement logiciel comprennent les éléments suivants : Les utilisateurs actifs, le nouvel utilisateur, le nombre de sessions, la durée de chaque session, les informations techniques sur le pays, les habitants, l'emplacement, l'opérateur de langues, les appareils, le microprogramme, le réseau (WiFi), les versions de l'application et du Kit de développement logiciel utilisées par les clients. Cette information peut être consultée en temps réel depuis la section Écran. 
- Le suivi par source affiche le nombre d'utilisateurs nouveaux qui ont téléchargé l'application à la suite d'une campagne promotionnelle spécifique. Les utilisateurs sont identifiés par l'identifiant de leur appareil : cet identifiant est unique pour chaque appareil(un nouvel utilisateur représente en réalité un nouvel appareil). Un utilisateur est considéré comme nouveau sur une période donnée s'il a effectué sa première session durant cette période.
- Le suivi par magasin affiche le nombre d'utilisateurs nouveaux qui ont téléchargé l'application depuis un magasin spécifique. Les utilisateurs sont identifiés par l'identifiant de leur appareil : cet identifiant est unique pour chaque appareil(un nouvel utilisateur représente en réalité un nouvel appareil). Un utilisateur est considéré comme nouveau sur une période donnée s'il a effectué sa première session durant cette période.

> Remarque : La période est calculée en fonction de la date indiquée dans les paramètres de l'appareil de l'utilisateur, de manière à ce qu'un utilisateur, disposant d'un téléphone sur lequel la date est mal réglée, puisse apparaître dans la mauvaise période.

- Rétention : Un utilisateur est considéré comme retenu sur une période donnée s'il a effectué sa première session durant cette période. Vous pouvez modifier les périodes durant lesquelles les utilisateurs retenus (et les nouveaux utilisateurs) sont comptabilisés à l'heure, au jour, à la semaine ou au mois. L'analyse de rétention de l'utilisateur est réalisée sur des cohortes. Une cohorte représente l'ensemble de tous les nouveaux utilisateurs détectés à une période donnée (c'est-à-dire, l'ensemble des utilisateurs qui effectuent leur première session durant cette période). Nous utilisons des cohortes d'un jour, de deux jours, de quatre jours, de sept jours et d'un mois. Azure Mobile Engagement calcule tous les jours, tous les 2 jours, tous les 4 jours, tous les 7 jours ou tous les mois, l'ensemble des utilisateurs qui appartiennent à la cohorte et qui sont toujours actifs (c'est-à-dire l'ensemble des utilisateurs qui ont effectué au moins une session durant la période). Cet ensemble d'utilisateurs est appelé une version de cohorte. (Azure Mobile Engagement peut vous indiquer combien de vos utilisateurs utilisent encore votre application, mais seul le magasin de la plateforme spécifique peut vous signaler combien de vos utilisateurs ont désinstallé votre application (par exemple, GooglePlay, iTunes, Windows Store, etc.)). 
- Sessions : Une utilisation de l'application par un utilisateur. Des sessions sont générées à partir de la séquence des activités effectuées par les utilisateurs (une activité est généralement associée à l'utilisation d'un écran de l'application, mais cela peut varier en fonction de la manière dont le Kit de développement logiciel a été intégré dans l'application). Un utilisateur ne peut effectuer qu'une activité à la fois : une session commence dès que l'utilisateur démarre sa première activité et elle s'arrête lorsqu'il termine sa dernière. Lorsqu'un utilisateur n'effectue aucune activité durant quelques secondes, sa séquence d'activités est divisée en deux sessions distinctes.
- Activités : Le nom de chaque écran de votre application et le temps passé sur chacun d'entre eux. Les activités représentent une option analytique personnalisée qui correspond aux balises d'" informations relatives aux applications " que vous avez mises en place pour votre propre application :
- Chemin d'accès utilisateur :  Vous indique comment vos utilisateurs naviguent à travers les différentes activités de votre application (écrans). Vous pouvez déplacer le curseur pour ajuster le niveau des détails. Les nœuds bleus représentent les activités de votre application. Leur taille est proportionnelle au temps passé sur chacune d'entre elles. Les nœuds blancs représentent le début et la fin de la session. Les nœuds rouges représentent les incidents. Les liens représentent les transitions existantes entre les différentes activités de votre application (ou entre les activités et les incidents). Cliquez sur un nœud ou sur un lien pour afficher une info-bulle qui vous fournit des informations supplémentaires sur vos données : le temps passé sur un écran spécifique, le nombre de transitions et le pourcentage de transitions de l'activité source à l'activité de destination. (A --- 60 % ---> B signifie qu'une fois l'activité A terminée, les utilisateurs optent, dans 60 % des cas, pour l'activité B). Vous pouvez réorganiser, comme bon vous semble, le graphique pour le rendre plus compréhensible ; sa position est enregistrée chaque fois que vous effectuez une modification. Vous pouvez afficher ou masquer les incidents pour alléger le graphique.
- Événements : Des actions spécifiques prises par un utilisateur dans l'application. La distribution des événements est représentée par le nombre d'événements effectués par utilisateur et par session. Un événement représente une action instantanée, par exemple, un clic sur un bouton ou la réception d'une notification. (La signification des événements dépend de la manière dont le Kit de développement logiciel a été intégré dans l'application.) Un événement peut survenir durant une session ou bien une tâche,ou peut être autonome.
- Tâches : Similaires à des événements, sauf qu'ils se concentrent sur la durée de l'action. Par exemple, des tâches peuvent vous fournir des informations techniques sur la durée de chargement d'un contenu ou d'un appel vers le service Web. Ceci peut également vous indiquer combien de temps un utilisateur a-t-il besoin pour remplir un formulaire, créer un compte, ou réaliser un achat. Une tâche représente la durée d'une tâche, par exemple, la durée d'une tâche de téléchargement ou le temps d'affichage d'une bannière à l'écran. (La signification des tâches dépend de la manière dont le Kit de développement logiciel SDK a été intégré dans l'application.) Les tâches sont généralement associées à des tâches en arrière-plan qui sont effectuées hors de la portée d'une session (c'est-à-dire, sans aucune activité effectuée par l'utilisateur).
- Informations techniques : Des informations techniques relatives aux appareils des utilisateurs de votre application que vous pouvez suivre, telles que les paramètres régionaux, l'opérateur, le réseau, l'appareil, le microprogramme, la taille de l'écran des appareils des utilisateurs, la version de votre application et la version du Kit de développement logiciel utilisées dans votre application.
- Erreurs : Informations relatives aux erreurs techniques survenant dans l'application, qui ne provoquent aucun incident. Une erreur représente un problème instantané, par exemple, une défaillance réseau ou une mauvaise manipulation. (La signification des événements dépend de la manière dont le Kit de développement logiciel a été intégré dans l'application.) Une erreur peut survenir durant une session ou bien une tâche, ou peut être autonome.
- Incidents : Informations relatives aux erreurs qui provoquent l'arrêt brutal de votre application. Un incident est un événement inattendu qui survient lorsque certaines fonctionnalités de l'application cessent de fonctionner et que celle-ci doit être arrêtée. Un incident est généralement dû à un bogue se produisant dans l'application.
 
![Analytics2][11] 

## Accès à la présentation de la rétention
 
![Analytics3][12] 

La présentation de la rétention est décomposée en plusieurs cartes, qui affichent chacune la présentation durant une certaine période de rétention. Une période de rétention de 2 jours est utilisée dans cet exemple. Les autres cartes affichent des périodes de rétention de 4 et 7 jours. 

## Explication des cartes de présentation de la rétention
 
![Analytics4][13] 

Chaque carte est composée de trois éléments principaux :

1. 1 : La cohorte et la période prise en compte
2. 2-4 : La rétention de la période actuelle
3. 5 : Un graphique sparkline de l'historique

**Voici des informations détaillées sur chaque élément :**

1.    Cohorte et période : Ce titre indique le type de cohorte. " Période de 2 jours " signifie que nous examinerons ici le comportement des utilisateurs durant 2 jours, l'arrivée de nouveaux utilisateurs durant cette période de 2 jours, et que nous observerons s'ils se connectent durant ces deux jours. L'exemple ci-dessus tient compte de l'activité des utilisateurs observée entre le 21 et le 22 novembre.
2.    Il vous indique le taux de rétention, durant le 21 et le 22 novembre, des utilisateurs arrivant le 19 et le 20 novembre. Sur les 3 nouveaux utilisateurs présents entre le 19 le 20 novembre, un seul était actif entre le 21 et le 22 novembre.
3.    Cet indicateur visuel fournit les mêmes informations que celles représentées ci-dessus dans ce graphique. (La partie nº 3 de ce cercle indique que cela représente 33 % des utilisateurs.) Les couleurs de ce graphique vous fournissent également des informations supplémentaires : La couleur verte vous indique que ce nombre est plus élevé que lors du calcul précédent. La couleur jaune signifie qu'il est identique et la couleur rouge, qu'il est plus faible.
4.    Il vous indique les valeurs utilisées pour le calcul.
5.    Ceci est un graphique sparkline de l'historique des valeurs de rétention. Il vous permet de découvrir les valeurs précédentes ainsi que leurs évolutions.

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
