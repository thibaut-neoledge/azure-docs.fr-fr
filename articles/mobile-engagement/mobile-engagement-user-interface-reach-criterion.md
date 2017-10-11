---
title: "Interface utilisateur d’Azure Mobile Engagement - Critère Reach"
description: "Utilisation des critères de ciblage pour envoyer des campagnes push à un sous-ensemble donné de vos utilisateurs à l’aide d’Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: a4ed03a0-55b1-4dd8-b0bd-c475005afb66
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 803b44721d0ab1ac7b5a8074e18857fc57adb724
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>Utilisation des critères de ciblage pour envoyer des campagnes push à un sous-ensemble donné de vos utilisateurs
Le ciblage de votre audience suivant des critères spécifiques à l'aide du bouton « Nouveau critère » est l'un des concepts les plus performants d'Azure Mobile Engagement qui vous permet d'envoyer des notifications Push pertinentes auxquelles les clients répondront, au lieu d'envoyer des courriers indésirables à tout le monde. Vous pouvez limiter votre audience selon des critères standard et simuler des notifications Push pour déterminer combien de personnes recevront ces notifications.

**Voir aussi :**

* [Documentation de l’interface utilisateur - Reach - Nouvelle campagne Push][Link 27]

## <a name="audience-criteria-can-include"></a>Les critères d’audience peuvent inclure :
* ** Technicals : ** vous pouvez cibler basé sur les mêmes informations techniques que vous pouvez voir dans les sections Analytique et d’analyse. **Voir aussi :** [Documentation de l’interface utilisateur - Analyse][Link 15], [Documentation de l’interface utilisateur - Moniteur][Link 16]
* **Emplacement :** les applications qui utilisent un « rapport de localisation en temps réel » avec géorepérage peuvent utiliser l’emplacement géographique en tant que critère pour cibler une audience à partir de l’emplacement GPS. Un « rapport de localisation de zone différé » peut également être utilisé pour cibler une audience à partir de la localisation du téléphone cellulaire (Le « rapport de localisation en temps réel » et le « rapport de localisation de zone différé » doivent être activés à partir du Kit de développement logiciel (SDK)). **Consultez également :** [Documentation du Kit de développement logiciel (SDK) - iOS - Intégration][Link 5], [Documentation relative au Kit de développement logiciel (SDK) - Android - Intégration][Link 5]
* **Commentaire Reach :** vous pouvez cibler votre audience selon les commentaires des précédentes notifications Reach via les commentaires Reach dans les sections Annonces, Sondages et Push de données. Cela vous permet de mieux cibler votre audience après deux ou trois campagnes Reach, en comparaison à la première. Cela peut aussi être utilisé pour filtrer les utilisateurs qui ont déjà reçu une notification avec un contenu similaire, en définissant une campagne à NE PAS envoyer aux utilisateurs ayant déjà reçu une campagne spécifique précédente. Vous pouvez même exclure les utilisateurs faisant partie d’une campagne spécifique toujours active, afin qu’ils ne reçoivent pas de nouvelles notifications Push. **Consultez également :** [Documentation sur l’interface utilisateur - Reach - Contenu Push][Link 29]
* **Installation du suivi :** vous pouvez suivre des informations selon l’emplacement où les utilisateurs ont installé votre application. **Consultez également :** [Documentation sur l’interface utilisateur - Paramètres][Link 20]
* **Profil utilisateur :** vous pouvez cibler votre audience en fonction des informations d’utilisateur standard et des informations d’application personnalisées que vous avez créées. Cela inclut les utilisateurs qui sont actuellement connectés et les utilisateurs qui ont répondu à des questions spécifiques que vous leur avez demandé de définir dans l’application même, plutôt que leurs réponses aux campagnes précédentes. Toutes les informations définies pour votre application sont indiquées dans cette liste.
* Segments : vous pouvez également cibler votre audience en fonction des segments que vous avez créés suivant le comportement spécifique des utilisateurs et contenant des critères multiples. Tous les segments définis pour votre application sont indiqués dans cette liste. **Consultez également :** [Documentation sur l’interface utilisateur - Segments][Link 18]
* **Informations relatives à l’application :** les balises d’informations personnalisées relatives à l’application peuvent être créées depuis la section « Paramètres » afin de suivre le comportement des utilisateurs. **Consultez également :** [Documentation sur l’interface utilisateur - Paramètres][Link 20]

## <a name="example"></a>Exemple :
Si vous souhaitez effectuer une transmission de type Push pour faire une annonce uniquement à un sous-ensemble de vos utilisateurs qui ont réalisé un achat dans l’application.

1. Rendez-vous sur la page des paramètres de l’application, sélectionnez le menu « Informations relatives à l’application » puis sélectionnez « Nouvelle information relative à l’application ».
2. Enregistrez une nouvelle information booléenne relative à l'application appelée « inAppPurchase ».
3. Cette information relative à l’application doit être définie sur « true » par votre application lorsque l’utilisateur réussit à réaliser un achat dans l’application (à l’aide de la fonction sendAppInfo (« inAppPurchase », ...)).
4. Si vous ne souhaitez pas faire cela depuis votre application, vous pouvez le faire depuis le serveur principal à l'aide de l'API de l'appareil.
5. Vous n'aurez ensuite qu'à créer votre annonce avec un critère limitant votre audience aux utilisateurs ayant « inAppPurchase » défini sur « vrai ».

> [!NOTE]
> Le ciblage basé sur un autre critère que les balises d'informations relatives à l'application nécessite un regroupement d'informations par Azure Mobile Engagement à partir des appareils de vos utilisateurs avant que la notification Push ne soit envoyée et qu'elle n'entraîne un retard. Les options de configuration de notifications Push complexes (telles que la mise à jour des badges) peuvent également entraîner des retards. L'utilisation de la campagne unique depuis l'API Push est la méthode Push la plus rapide dans Azure Mobile Engagement. L'utilisation des balises d'informations relatives à l'application en tant que critères Push pour une campagne Reach (depuis l'API Reach ou l'IU) est la deuxième méthode la plus rapide puisque ces balises d'informations sont stockées côté serveur. L'utilisation d'autres critères de ciblage pour une campagne Push est la méthode la plus flexible mais la plus lente, car Azure Mobile Engagement doit interroger les appareils afin d'envoyer la campagne.

![Reach-Criterion1][29] 

## <a name="criterion-options-apply-to"></a>Les options de critère s'appliquent à :
* **Informations techniques**     
* Nom du microprogramme : nom du microprogramme
* Version du microprogramme : version du microprogramme
* Modèle de l’appareil : modèle de l’appareil
* Fabricant de l’appareil : fabricant de l’appareil
* Version de l’application : version de l’application
* Nom de l’opérateur : nom de l’opérateur, non défini
* Pays de l’opérateur : pays de l’opérateur, non défini
* Type de réseau : type de réseau
* Paramètres régionaux : paramètres régionaux
* Taille de l’écran : taille de l’écran
* **Emplacement**      
* Dernière région connue : pays, région, localité
* Géofencing en temps réel : liste des POI (points d’intérêts) (nom, actions), POI circulaires (nom, latitude, longitude, rayon en mètres)
* **Commentaire Reach**     
* Commentaire de l’annonce : annonce, commentaire
* Commentaire du sondage : sondage, commentaire
* Commentaire de réponse du sondage : commentaire de réponse du sondage, question, choix
* Commentaire du Push de données : Push de données, commentaire
* **Installation du suivi**     
* Store : Store, non défini
* Source: source, non défini
* **Profil utilisateur**     
* Sexe : masculin ou féminin, non défini
* Date de naissance : opérateur, date, non défini
* Accepter : vrai ou faux, non défini
* **Informations sur l’application**      
* Chaîne : chaîne, non défini
* Date : opérateur, date, non défini
* Entier : opérateur, nombre, non défini
* Booléen : true ou false, non défini
* **Segment**    
* Nom des segments (de la liste déroulante), exclusion (utilisateurs cibles qui ne font pas partie de ce segment).

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

