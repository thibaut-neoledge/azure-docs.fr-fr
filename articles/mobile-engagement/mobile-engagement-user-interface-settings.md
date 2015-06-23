<properties 
   pageTitle="Interface utilisateur d'Azure Mobile Engagement : Paramètres" 
   description="Découvrez comment gérer les paramètres globaux de votre application grâce à Azure Mobile Engagement" 
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

# Comment gérer les paramètres globaux de votre application
Les options de menu Paramètres disponibles pour une application dépendent de la plateforme de l'application et des autorisations qui vous ont été accordées pour cette application. Les paramètres suivants sont inclus : Détails, Projets, Native Push, Push Speed, Kit de développement logiciel (SDK), Suivi, Informations sur l'application, Pression commerciale et Autorisations. Seule l'option de menu Informations sur l'application de la section Paramètres de l'interface utilisateur contient des éléments qui peuvent être gérés avec la fonctionnalité « Balise » de l'API de périphérique. Le « Glossaire » situé dans « Concepts » comprend des définitions de termes et abréviations telles que les suivantes : Service de notifications Push Apple (APNS), GCM, IDFA, API, Kit de développement logiciel (SDK), Clé API, Clé du Kit de développement logiciel (SDK), ID de l'application, ID de l'AppStore, Plan de balise, ID d'utilisateur, ID de périphérique, Délégué de l'application, Trace de la pile et Lien profond.

### Voir aussi
- [Documentation sur les API - API d'appareil][Link 4], [Concepts - Glossaire][Link 6], [Guide de résolution des problèmes - Service][Link 24]

  ![settings1][46]

## Détails
Permet de modifier le nom et la description de votre application, d'afficher le propriétaire de votre application et vos autorisations de rôle. Configuration d'analyse : vous pouvez afficher ou modifier le jour du Début de semaine et le Temps de rétention en jour(s).
 
  ![settings2][47]
 
## Projets
Vous pouvez sélectionner tous les projets que vous souhaitez voir apparaître dans votre application. Vous pouvez également rechercher un projet et afficher le nom, la description, le propriétaire et vos autorisations de rôle pour n'importe quel projet dont votre application fait partie.

### Voir aussi
-    [Documentation de l'interface utilisateur - Accueil][Link 13]
 
  ![settings3][48]

## Native Push
Vous pouvez inscrire un nouveau certificat ou supprimer un certificat existant pour une utilisation avec Native Push. Native Push permet à Azure Mobile Engagement de transmettre des opérations push à votre application à tout moment, même lorsqu'elle n'est pas en cours d'exécution. Après avoir fourni des informations d'identification ou des certificats pour au moins un service Native Push, vous pouvez sélectionner « Tout le temps » lors de la création de campagnes Reach et également utiliser le paramètre « Notification » dans l'API de PUSH.

### Voir aussi
- [Documentation sur les API - API Reach][Link 4], [Documentation sur les API - API Push][Link 4], [Documentation de l'interface utilisateur (IU) - Nouvelle campagne Push][Link 27]

### Service de notifications Push Apple (APNS)
Pour activer Native Push à l'aide du Service de notifications Push Apple (APNS), vous devez enregistrer votre certificat. Vous devrez spécifier le type de certificat, soit de développement (DEV), soit de production (PROD). Vous allez ensuite devoir charger votre certificat et définir son mot de passe.

### Voir aussi
- [Documentation du Kit de développement logiciel (SDK) - iOS - Préparation de votre Application pour les notifications Push Apple][Link 5]
 
![settings4][49]
 
### Service de notifications Push Windows (WPNS)
Pour activer Native Push à l'aide du Service de notifications Push Windows (WPNS), vous devez fournir les informations d'identification de votre application. Vous aurez besoin de votre ID de sécurité (SID) de Package et la clé secrète.
 
![settings5][50]
 
### Google Cloud Messaging pour Android (GCM)
Pour activer le Push Native à l'aide de GCM, vous devez suivre les instructions fournies par Google. Puis vous devez coller une clé d'API simple de serveur, configurée sans restriction d'adresse IP. Requiert l'intégration avec le Kit de développement logiciel (SDK) pour Android v1.12.0 +.

### Voir aussi
- [Documentation du Kit de développement logiciel (SDK) - Android - Intégrer Google Cloud Messaging (GCM)][Link 5], [Google Developer - Guide GCM](http://developer.android.com/guide/google/gcm/gs.html), [Google Developer - Documentation GCM](http://developer.android.com/google/gcm/index.html)
 
### Amazon Device Messaging pour Android (ADM)
Pour activer Native Push avec Amazon Device Messaging (ADM), vous devez fournir des <OAuth credentials> Amazon qui consistent en un ID Client et Secret Client (requiert l'intégration avec le Kit de développement logiciel (SDK) pour Android v2.1.0 +).

### Voir aussi
- [Documentation du Kit de développement logiciel (SDK) - Android - Intégrer Google Cloud Messaging (GCM)][Link 5], [Amazon Developer - Documentation ADM](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## Push Speed
Vous pouvez afficher la vitesse de transmission actuelle de votre application et définir la vitesse de transmission de votre application. La vitesse de transmission définit le nombre maximal d'opérations Push par seconde effectuées par le module Reach. Cela peut être utile dans le cas où vos propres serveurs sont surchargés par un trop grand nombre de requêtes par seconde (/s) après une activation de la campagne.
 
  ![settings7][52]

## Suivi
La fonctionnalité de suivi vous permet d'effectuer le suivi de l'origine des installations de vos applications iOS et Android. Il vous permet de savoir où vos utilisateurs ont téléchargé votre application (par exemple : à partir de quelle application) et quelle source les a amenés ici (par exemple : une campagne de publicité, un blog, un site Web, un courrier électronique, un SMS, etc.). La fonctionnalité de Suivi d'Azure Mobile Engagement doit être intégrée dans votre application depuis le Kit de développement logiciel (SDK) comme une étape distincte.

### Voir aussi
- [Documentation du Kit de développement logiciel (SDK) - Android - Intégration][Link 5], [Documentation du Kit de développement logiciel (SDK) - Android - iOS - Intégration][Link 5]
 
### Magasins
Vous pouvez enregistrer tous les magasins à partir desquels votre application peut être téléchargée en fonction de leurs noms et leurs URL de téléchargement associés. Cela vous permet de créer des emplacements pour le suivi des URL de l'hôte. Les magasins peuvent être créés ou supprimés de cette page. À l'aide de l'icône pour créer une nouvelle URL de suivi, vous pouvez assurer le suivi de pages URL abordé ci-dessous. Il existe plusieurs façons de suivre depuis quel endroit vos utilisateurs téléchargent votre application :

-    Utilisez un serveur de publicité tiers (Azure Mobile Engagement prend actuellement en charge [SmartAd](http://smartadserver.fr/) et [Surikate](http://www.surikate.com/)).
-    Utilisez un service d'attribution tiers (Azure Mobile Engagement prend actuellement en charge le [suivi d'application mobile](http://www.mobileapptracking.com/) et [Trademob](http://www.trademob.com/) sera bientôt pris en charge.)
-    Utilisez un référent d'installation tiers (Azure Mobile Engagement prend actuellement en charge [Google Play Install Referrer](https://developers.google.com/app-conversion-tracking/docs/third-party-trackers/) - Android uniquement).
-    Utilisez les rapports manuels.
 
  ![settings8][53]
 
### Campagnes de publicité
Vous pouvez créer des campagnes de publicité consistant en un nom de serveur de publicité, un ID de campagne et la Source d'où votre application peut être téléchargée.
 
  ![settings9][54]
 
### Suivi des URL
Vous pouvez générer des URL de suivi à utiliser en tant qu'URL cibles dans vos sources (campagne de publicité, blog, site Web, courrier électronique, SMS, etc.) qui peuvent rediriger les utilisateurs vers les magasins où ils peuvent télécharger votre application. Vous pouvez aussi d'afficher les URL de suivi que vous avez déjà créées. Une URL de suivi peut être utilisée en tant qu'URL d'action d'une campagne de publicité ou d'une annonce Reach pour inviter les utilisateurs à télécharger une de vos applications à partir d'une autre application. Une URL de suivi redirige vers l'URL de téléchargement associée au magasin sélectionné, tout en permettant à notre système de suivi d'enregistrer le magasin à partir duquel l'application est téléchargée au moment de l'installation. Si une source est fournie, notre système de suivi l'enregistre également, ce qui vous permet de faire la distinction entre les différentes campagnes que vous créez pour votre application.

Pour la création d'une nouvelle URL de suivi, vous devez spécifier un magasin et la source None, Client, ou Ajouter un serveur.

-    Une source None crée une URL de suivi par défaut.
-    Une source Personnalisé vous permet de spécifier une URL sur un serveur externe pour télécharger votre application.
-    Une source Serveur de publicité crée une URL de suivi sur un serveur nommé par défaut Serveur de publicité.
 
### Voir aussi
- [Documentation de l'interface utilisateur - Reach - Nouvelle campagne Push][Link 27] 
 
### Créer une URL de suivi
Vous pouvez également créer une URL de suivi permettant aux utilisateurs de télécharger l'une de vos applications à l'intérieur de la section de contenu d'une nouvelle campagne Reach.

### Voir aussi
- [Documentation de l'interface utilisateur - Reach - Contenu Push][Link 29]

![settings10][55]

## Information de l'application
Vous pouvez enregistrer des informations supplémentaires associées aux utilisateurs de votre application. Ces informations peuvent être injectées par votre application (à l'aide du Kit de développement logiciel (SDK)) ou par votre serveur principal (à l'aide de l'API de périphérique).

### Voir aussi
- [Documentation des API - API d'appareil][Link 4]

L'inscription des balises d'informations d'application vous permet de segmenter vos campagnes Reach en créant des critères Reach spécifiques au public à partir de ces derniers. Vous pouvez afficher le nom et le type de balises d'informations d'application existantes ou ajouter une nouvelle information d'application basée sur le nom et le type de Chaîne, Date, Entier ou Booléen.

### Voir aussi
- [Documentation de l'interface utilisateur - Reach - Nouvelle campagne Push][Link 27]
 
![settings11][56]
 
## Pression commerciale
Les quotas de Push vous permettent de définir le nombre maximal de fois qu'un périphérique accepte les opérations push sur une période donnée. Les quotas de Push ne sont utilisés que par les campagnes Reach dont l'option « Appliquer les quotas de Push » est activée. Les quotas de Push peuvent être gérés par segment ou par défaut. Les quotas peuvent être définis pour envoyer uniquement un nombre maximal d'opérations push sur une période glissante en heure, jour, semaine ou mois.

### Voir aussi
- [Documentation sur l'interface utilisateur (IU) - Reach - Nouvelle campagne Push][Link 27], [Documentation sur l'interface utilisateur (IU) - Segments][Link 18]

### Quotas :
- Quota par défaut : ce quota est appliqué aux utilisateurs auxquels aucun segment ne correspond dans la section « Quota par segment » ci-dessous. Par défaut, aucun quota n'est défini.
- Quota par segment : comme un quota doit s'appliquer à un groupe d'utilisateurs, vous devez créer un segment pour identifier les utilisateurs auxquels appliquer ce quota. Il peut s'agir d'un segment d'« utilisateurs importants » défini par le nombre de sessions qu'ils créent, ou tout ce que vous jugerez intéressant pour définir le groupe d'utilisateurs. Si un périphérique correspond à plusieurs segments avec un quota défini, seul le quota ayant le nombre le plus élevé d'opérations push par heure est appliqué.

![settings12][57]
 
## Autorisations
Vous pouvez rechercher et afficher le courrier électronique, le nom, l'organisation et le niveau d'autorisation des utilisateurs de votre application. Le concept d'autorisations est en supplément du rôle de projet. Il vous permet d'associer un jeu d'autorisations à un utilisateur spécifique qui a accès à votre application.

### Les niveaux d'autorisation actuellement disponibles sont :
-    « Créateur de campagne Reach » (un utilisateur qui peut créer des campagnes Reach) 
-    « Gestionnaire de campagne Reach » (un utilisateur qui peut créer des campagnes Reach et les activer, les interrompre, les terminer ou les détruire)

> Remarque : lorsqu'un utilisateur a un rôle de projet et un jeu d'autorisations pour une application donnée, le concept plus permissif est utilisé. Par conséquent, nous suggérons que lors de l'utilisation des autorisations, vous définissiez le rôle de vos utilisateurs à « Témoin » (rôle moins permissif) et ajoutez des autorisations plus permissives au niveau de l'application.

### Voir aussi
- [Documentation de l'interface utilisateur (IU) - Accueil][Link 13]  
 
![settings13][58]

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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md

<!--HONumber=54--> 