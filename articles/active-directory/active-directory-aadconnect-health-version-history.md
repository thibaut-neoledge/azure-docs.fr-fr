---
title: Historique de publication des versions d’Azure AD Connect Health
description: Ce document décrit les versions d’Azure AD Connect Health et ce qui a été inclus dans ces versions.
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: billmath

---
# <a name="azure-ad-connect-health:-version-release-history"></a>Azure AD Connect Health : historique de publication des versions
L’équipe Azure Active Directory met régulièrement à jour Azure AD Connect Health avec de nouvelles fonctions et fonctionnalités. 

Cet article est conçu pour vous aider à effectuer le suivi des versions qui ont été publiées.

## <a name="july-2016"></a>Juillet 2016
**Nouvelles fonctionnalités préliminaires :**

* [Utilisation d’Azure AD Connect Health pour AD DS](active-directory-aadconnect-health-adds.md).

## <a name="january-2016"></a>Janvier 2016
**Mise à jour de l’agent :**

* agent Azure AD Connect Health pour AD FS (version 2.6.91.1512)

**Nouvelles fonctionnalités :**

* [Outil de test de connectivité pour les agents Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Novembre 2015
**Nouvelles fonctionnalités :**

* Prise en charge du [Contrôle d’accès en fonction du rôle](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)

**Nouvelles fonctionnalités préliminaires :**

* [Azure AD Connect Health pour la synchronisation](active-directory-aadconnect-health-sync.md).

**Problèmes résolus :**

* Correctifs pour les erreurs survenant pendant les enregistrements d’agent. 

## <a name="september-2015"></a>Septembre 2015
**Nouvelles fonctionnalités :**

* Rapport incorrect de mot de passe/nom d’utilisateur pour AD FS 
* Prise en charge pour configurer le serveur proxy HTTP non authentifié 
* Prise en charge pour configurer l’agent sur Server core
* Améliorations apportées aux alertes pour AD FS 
* Améliorations dans Azure Agent AD Connect Health pour AD FS pour la connectivité et le chargement des données. 

**Problèmes résolus :**

* Correctifs de bogues dans les informations d’utilisation pour les types d’erreur AD FS. 

## <a name="june-2015"></a>Juin 2015
**Version initiale d’Azure AD Connect Health pour AD FS et le proxy AD FS.**

**Nouvelles fonctionnalités :**

* Alertes de surveillance des serveurs AD FS et de proxy AD FS avec les notifications par e-mail. 
* Accès facile à la topologie et aux modèles AD FS dans les compteurs de performances AD FS. 
* Tendances des demandes de jeton réussies sur les serveurs AD FS regroupés par applications, méthodes d’authentification, demande d’emplacement réseau, etc.. 
* Tendances des demandes ayant échoué sur les serveurs AD FS regroupés par applications, types d’erreur, etc..
* Déploiement d’agent plus simple à l’aide des informations d’identification d’administrateur général Azure AD.  

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [Surveillez votre infrastructure d’identité locale et vos services de synchronisation dans le cloud](active-directory-aadconnect-health.md).

<!--HONumber=Oct16_HO2-->


