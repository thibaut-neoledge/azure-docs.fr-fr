---
title: "Nouveautés d’Azure Active Directory | Microsoft Docs"
description: "Utilisez ces notes de version pour vous maintenir au courant sur les **versions les plus récentes**, les **problèmes connus**, les **résolution des bogues**, les **fonctionnalités déconseillées** et les **modifications planifiées** dans Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 0361d5b48af876af2ccc522351484d369554bfca
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Nouveautés d’Azure Active Directory

Nous améliorons Azure Active Directory de manière continue. Pour vous permettre de rester à jour avec les développements les plus récents, cette rubrique fournit des informations sur les éléments suivants :

-   Versions les plus récentes 
-   Problèmes connus 
-   Résolution des bogues 
-   Fonctionnalités déconseillées 
-   Modifications planifiées 

Consultez cette page régulièrement, car nous la mettons à jour chaque mois.


## <a name="october-2017"></a>Octobre 2017

**Type :** modification planifiée  
**Catégorie de service :** création de rapports  
**Fonctionnalité produit :** gestion du cycle de vie des identités  


**Dépréciation des API des rapports Azure AD (version bêta) sous le nœud `https://graph.windows.net/<tenant-name>/reports/`** 

Le portail Azure vous offre :

- Une nouvelle console d’administration Azure Active Directory 
- De nouvelles API pour les rapports sur l’activité et la sécurité
 
En raison de ces nouvelles fonctionnalités, les API des rapports sous le point de terminaison **/reports** seront supprimées le 10 décembre 2017. 

---

**Type :** corrigé   
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** authentification unique  


Azure Active Directory prend en charge la détection automatique de champ de connexion pour les applications qui affichent un champ de nom d’utilisateur et de mot de passe HTML.  Ces étapes sont documentées sous [Comment capturer automatiquement les champs de connexion d’une application](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Vous pouvez rechercher cette fonctionnalité en ajoutant une application *ne provenant pas de la galerie* sur la page **Applications d’entreprise** du [portail Azure](http://aad.portal.azure.com). En outre, vous pouvez configurer le mode **Authentification unique** pour cette nouvelle application sur **Authentification unique par mot de passe**, en entrant une URL web et en enregistrant la page.
 
En raison d’un problème de service, cette fonctionnalité a été temporairement désactivée pour une période donnée. Le problème a été résolu et la détection automatique de champ de connexion est à nouveau disponible.

---

**Type :** nouvelle fonctionnalité  
**Catégorie de service :** authentification multifacteur  
**Fonctionnalité produit :** sécurité et protection de l’identité  


Dans le monde actuel, l’authentification multifacteur (MFA) est essentielle pour protéger votre organisation. L’équipe dédiée à l’identité de Microsoft fait évoluer l’authentification multifacteur pour rendre les informations d’identification plus adaptatives et l’expérience plus simple. Aujourd'hui, j’ai le plaisir d’annoncer deux étapes importantes de cette stratégie : 

- Intégration des résultats de la demande d’accès multifacteur directement dans le rapport de connexion Azure AD, notamment l’accès par programme aux résultats de l’authentification multifacteur

- Intégration plus avancée de la configuration de l’authentification multifacteur dans l’expérience de configuration Azure AD principale dans le portail Azure

Avec cette préversion publique, la gestion et les rapports de l’authentification multifacteur sont une partie intégrante de l’expérience de configuration Azure AD principale, ce qui vous permet de gérer la fonctionnalité de portail de gestion de l’authentification multifacteur au sein de l’expérience Azure AD.


---
**Type :** nouvelle fonctionnalité  
**Catégorie de service :** conditions d’utilisation  
**Fonctionnalité de produit :** gouvernance  


La fonctionnalité **Conditions d’utilisation d’Azure AD** vous offre une méthode simple pour présenter des informations aux utilisateurs finaux. Cela permet de garantir qu’ils se voient présenter les clauses d’exclusion de responsabilité nécessaires au respect des conditions légales ou de conformité.

Vous pouvez utiliser la fonctionnalité Conditions d’utilisation d’Azure AD dans les scénarios suivants :

- Conditions d’utilisation générales pour tous les utilisateurs de votre organisation. 

- Conditions d’utilisation spécifiques en fonction des attributs d’un utilisateur (par exemple, docteurs/infirmières ou employés nationaux/internationaux dans des groupes dynamiques). 

- Conditions d’utilisation spécifiques pour l’accès aux applications à fort impact commercial, comme Salesforce.

Pour plus d’informations, consultez [Conditions d’utilisation d’Azure Active Directory](active-directory-tou.md).


---
**Type :** nouvelle fonctionnalité  
**Catégorie de service :** PIM  
**Fonctionnalité produit :** Privileged Identity Management  


Avec Azure Active Directory Privileged Identity Management (PIM), vous pouvez maintenant gérer, contrôler et surveiller l’accès aux ressources Azure (préversion) au sein de votre organisation. Cela inclut les abonnements, les groupes de ressources et même les machines virtuelles. Toutes les ressource dans le portail Azure tirant parti de la fonctionnalité de contrôle d’accès en fonction du rôle (RBAC) de Azure peuvent utiliser toutes les capacités de gestion de la sécurité et des cycles de vie offertes par Azure AD PIM, ainsi que certaines nouvelles fonctionnalités que nous prévoyons d’ajouter bientôt aux rôles de Azure AD.

Pour plus d’informations, consultez [PIM pour les ressources Azure](privileged-identity-management/azure-pim-resource-rbac.md).


---
**Type :** nouvelle fonctionnalité  
**Catégorie de service :** révisions d’accès  
**Fonctionnalité de produit :** gouvernance  


Nouveautés d’Azure AD

Les révisions d’accès (préversion) permettent aux organisations de gérer efficacement les appartenances à des groupes et les accès aux applications d’entreprise : 

- Vous pouvez le recertifier l’accès utilisateur invité à l’aide des révisions d’accès de leur accès aux applications et appartenances à des groupes. Les informations fournies par les révisions d’accès permettent aux réviseurs de décider efficacement si des utilisateurs invités doivent bénéficier d’un accès ininterrompu.

- Vous pouvez recertifier l’accès d’employés à des applications et l’appartenance à des groupes à l’aide de révisions d’accès.

Vous pouvez collecter les contrôles de révision d’accès dans des programmes importants pour votre organisation afin de suivre les révisions de conformité ou les applications sensibles aux risques.

Pour plus d’informations, consultez [Révisions d’accès Azure AD](active-directory-azure-ad-controls-access-reviews-overview.md).


---
**Type :** nouvelle fonctionnalité  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** authentification unique  


**Possibilité de masquer les applications tierces à partir de My Apps et du lanceur d’applications Office 365**

Vous pouvez désormais mieux gérer les applications qui s’affichent sur vos portails d’utilisateur avec une nouvelle propriété permettant de **masquer l’application**. Cela est utile dans les cas où des vignettes d’applications s’affichent pour des services principaux ou des vignettes en double, et finissent par encombrer les lanceurs d’application de l’utilisateur. Le bouton bascule se trouve dans la section des propriétés de l’application tierce et est étiqueté **Visible pour l’utilisateur ?**. Vous pouvez également masquer une application par programme par le biais de PowerShell. 

Pour plus d’informations, consultez [Masquer une application tierce de l’expérience utilisateur dans Azure Active Directory](active-directory-coreapps-hide-third-party-app.md). 


**Qu’est-ce qui est disponible ?**

 Dans le cadre de la transition vers la nouvelle console d’administration, nous avons créé 2 API pour récupérer les journaux d’activité Azure AD disponibles. Le nouvel ensemble d’API fournit une fonctionnalité plus riche de filtrage et de tri en plus de fournir des activités d’audit et de connexion plus avancées. Les données précédemment disponibles via les rapports de sécurité sont désormais accessibles via l’API d’événements à risque Identity Protection dans Microsoft Graph.


## <a name="september-2017"></a>Septembre 2017

**Type :** fonctionnalité modifiée  
**Catégorie de service :** Microsoft Identity Manager  
**Fonctionnalité produit :** gestion du cycle de vie des identités  


Un package correctif cumulatif (build 4.4.1642.0) est disponible depuis le 25 septembre 2017 pour Microsoft Identity Manager (MIM) 2016 Service Pack 1 (SP1). Ce package cumulatif :

- Résout les problèmes et ajoute des améliorations
- Est une mise à jour cumulative qui remplace toutes les mises à jour de MIM 2016 SP1 jusqu’à la version 4.4.1459.0 pour Microsoft Identity Manager 2016. 
- Requiert que vous disposiez de **Microsoft Identity Manager 2016 build 4.4.1302.0.** 

Pour plus d’informations, consultez [Package correctif cumulatif (build 4.4.1642.0) disponible pour Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/en-us/help/4021562). 

---