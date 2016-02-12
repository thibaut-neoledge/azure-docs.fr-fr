<properties
	pageTitle="Vue d’ensemble d’Enterprise State Roaming | Microsoft Azure"
	description="Fournit des informations sur les paramètres Enterprise State Roaming dans les périphériques Windows. Enterprise State Roaming fournit aux utilisateurs une expérience unifiée sur leurs appareils Windows et réduit le temps nécessaire à la configuration d’un nouveau périphérique."
	services="active-directory"
    keywords="Qu’est-ce que Enterprise State Roaming, la synchronisation d’entreprise et Windows cloud"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

# Présentation d’Enterprise State Roaming

Avec Windows 10, les utilisateurs [Azure Active Directory (Azure AD)](active-directory-whatis.md) ont la possibilité de synchroniser en toute sécurité leurs paramètres utilisateur et les données de paramètres d’application dans le cloud. Enterprise State Roaming fournit aux utilisateurs une expérience unifiée sur leurs appareils Windows et réduit le temps nécessaire à la configuration d’un nouveau périphérique. Enterprise State Roaming fonctionne comme la [synchronisation des paramètres de consommateur](http://windows.microsoft.com/fr-FR/windows-8/sync-settings-pcs) présentée dans Windows 8. En outre, Enterprise State Roaming offre :

- **Séparation des données d’entreprise des données client** : les organisations maîtrisent leurs données, et les données d’entreprise ne sont pas mélangées dans le compte cloud client ou aucune donnée client ne figure dans le compte cloud d’entreprise. 
- **Sécurité renforcée** : les données sont automatiquement chiffrées avant de quitter l’appareil Windows 10 de l’utilisateur à l’aide d’Azure Rights Management (Azure RMS) et les données restent chiffrées au repos dans le cloud. Tout le contenu reste chiffré au repos dans le cloud, à l’exception des espaces de noms tels que les noms de paramètres et les noms d’application Windows.  
- **Services de gestion et surveillance** : davantage de contrôle et de visibilité sur les personnes qui synchronisent les paramètres de votre organisation et sur les périphériques concernés. 
- **Emplacement géographique des données dans le cloud** : les données seront stockées dans une région Azure en fonction du pays du domaine Azure AD. 



| Article | Description |
|--------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Activer Enterprise State Roaming dans Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md) | Enterprise State Roaming est disponible pour toute organisation ayant souscrit un abonnement premium à Azure Active Directory (Azure AD). Pour plus de détails sur l’obtention d’un abonnement Azure AD, consultez la page produit Azure AD. |
| [FAQ sur l’itinérance des paramètres et des données](active-directory-windows-enterprise-state-roaming-faqs.md) | Cette rubrique répond à certaines questions que les administrateurs informatiques peuvent se poser sur les paramètres et la synchronisation des données d’application. |
| [Paramètres de stratégie de groupe et de MDM pour la synchronisation des paramètres](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) | Windows 10 fournit les paramètres de stratégie de groupe et de gestion d’appareil mobile (MDM) pour limiter la synchronisation des paramètres. |
| [Référence des paramètres d’itinérance Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) | Voici une liste complète de tous les paramètres destinés à l’itinérance et/ou à la sauvegarde dans Windows 10. |

<!---HONumber=AcomDC_0204_2016-->