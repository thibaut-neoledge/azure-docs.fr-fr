<properties 
   pageTitle="Guide de dépannage d’Azure Mobile Engagement - Analyse" 
   description="Dépannage des problèmes d’analyse, de surveillance, de segmentation et de tableau de bord dans Azure Mobile Engagement" 
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

# Guide de dépannage pour les problèmes d’analyse, de surveillance, de segmentation et de tableau de bord

Les éléments suivants sont des problèmes potentiels liés à la façon dont Azure Mobile Engagement rassemble des informations sur vos applications, périphériques et utilisateurs.

## Informations manquantes/retardées

### Problème
- Les informations sont retardées dans l'analyse, la segmentation ou le tableau de bord.
- Il manque des informations de surveillance.
- Il manque des informations d'analyse, de segmentation ou du tableau de bord.
- Limites de segmentation atteinte.

### Causes

- Vous pouvez utiliser l'API Analytics, Monitor et Segments pour voir si des données manquantes dans l'IU sont visibles avec les API.
- Si le Kit de développement logiciel (SDK) Azure Mobile Engagement n'est pas correctement intégrée dans votre application, vous ne serez pas en mesure de voir les informations dans Analyse, Segmentation, Surveillance ou Tableaux de bord.
- Les segments ne peuvent pas être modifiée après avoir été créés, ils peuvent uniquement être « clonés » (copiés) ou « détruits » (supprimés). Les segments peuvent uniquement contenir 10 critères.
- La meilleure façon de tester les informations manquantes de l'analyse (configurer un périphérique de test, désinstaller et/ou réinstaller l'application sur le périphérique de test).
- Les informations sont actualisées toutes les 24 heures pour l'analyse, la segmentation ou les tableaux de bord.
- Les informations figurant dans les nouveaux segments ne peuvent pas être affichées au cours de 24 heures qui suivent leur création (même si le segment est basé sur des informations antérieures).
- Le filtrage de vos données d’analyse, dans l’interface utilisateur, affiche tous les exemples de ce type, et ce, quelle que soit la version de votre application (par exemple les « Incidents » filtrés par nom s’afficheront depuis la version 1 et la version 2 de votre application).
- La période de l'analyse est basée sur la date des paramètres des périphériques des utilisateurs ; ainsi, un utilisateur dont le téléphone a une date incorrecte pourrait s'afficher dans une période incorrecte.
- Aucune donnée côté serveur n'est enregistrée lorsque vous utilisez le bouton pour « tester » les notifications Push. Les données sont enregistrées uniquement pour les vraies campagnes de notification Push.

### Voir aussi

- [Guide de dépannage - Kit de développement logiciel (SDK)][Link 25], [Documentation sur les API][Link 4], [Documentation sur l’interface utilisateur - Segments][Link 18]

## Impossible de trouver des éléments dans l'interface utilisateur

### Problème
- Impossible de créer des segments en fonction de certains critères de balise d'informations d'application intégrés ou personnalisés.
- Impossible de trouver certains critères de balise d'informations d'application intégrés ou personnalisés dans l'analyse, la surveillance ou les tableaux de bord.
- Impossible d'interpréter les données de l'analyse, de la surveillance, de la segmentation ou des tableaux de bord.

### Causes

- Certains éléments intégrés et certaines balises d'informations d'application sont uniquement disponibles en tant que critères push mais ne peuvent pas être ajoutés à un segment ou être visibles depuis l'analyse, la surveillance ou le tableau de bord. 
- Pour les éléments intégrés et les balises d'informations d'application qui ne peuvent pas être ajoutés à un segment, vous devrez configurer la liste des critères de ciblage dans chaque campagne pour effectuer la même fonction que le ciblage basé sur un segment.
- Consultez les menus contextuels dans les sections Analyse, Surveillance, Segmentation et Tableaux de bord de l'interface utilisateur d'Azure Mobile Engagement pour obtenir de l'aide de l'aide et consignes.

### Voir aussi

- [Documentation de l’interface utilisateur - Critère de nouveau Push Reach pour cibler un public][Link 28]
 
## Dépannage d'incident

### Problème
- Incidents d'application apparaissant dans l'analyse, la surveillance ou le tableau de bord.

### Causes

- Pour résoudre les problèmes d'incident d'application dans Analyse, Surveillance ou Tableau de bord, veillez à consulter les notes de publication pour les problèmes connus avec les versions précédentes du Kit de développement logiciel (SDK).
- Pour mieux résoudre les incidents d'application, effectuez un événement à partir d'un périphérique de test avec votre application installée et recherchez l'ID de votre périphérique dans la section « Moniteur – Événements » de l'interface utilisateur d'Azure Mobile Engagement. Effectuez ensuite l'événement à l'origine de l'incident de votre application et cherchez des informations supplémentaires dans la section « Moniteur – Incident » de l'interface utilisateur d'Azure Mobile Engagement. 

### Voir aussi

- [Concepts - FAQ][Link 6], [Concepts - Glossaire][Link 6], [Documentation de l’interface utilisateur][Link 1], [Documentation du Kit de développement logiciel (SDK) - Notes de publication][Link 5], [Documentation du Kit de développement logiciel (SDK) - Guides de mise à jour][Link 5]

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

<!--HONumber=54--> 