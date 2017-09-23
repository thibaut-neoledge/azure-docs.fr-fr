---
title: "Azure Active Directory B2C : Notes du développeur sur l’utilisation des stratégies personnalisées | Microsoft Docs"
description: "Notes à destination des développeurs pour configurer et maintenir Azure AD B2C avec des stratégies personnalisées"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/05/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: a5f222e5b11e05286152a9f1cc55d2c3fc27a9dc
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017

---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Notes de version pour la version préliminaire publique de la stratégie personnalisée Azure Active Directory B2C
L’ensemble de fonctionnalités de stratégie personnalisée est désormais disponible à des fins d’évaluation en préversion publique pour tous les clients Azure Active Directory B2C (Azure AD B2C). Cet ensemble de fonctionnalités est destiné aux développeurs d’identité avancés créant les solutions d’identité les plus complexes.  

À ce jour, cet ensemble de fonctionnalités exige que les développeurs configurent l’infrastructure d’expérience d’identité directement via la modification du fichier XML. Cette méthode de configuration est à la fois puissante et complexe. Les développeurs d’identité avancés qui utilisent l’infrastructure d’expérience d’identité doivent prévoir du temps pour suivre des procédures et lire des documents de référence. 

## <a name="features-included-in-this-public-preview"></a>Fonctionnalités incluses dans cette version préliminaire publique
Les nouvelles fonctionnalités introduites dans la préversion permettent aux développeurs d’effectuer les tâches suivantes :<br>

* Créer et charger des parcours utilisateur d’authentification personnalisés à l’aide de stratégies personnalisées. 
   * Décrire des parcours utilisateur étape par étape comme des échanges entre des fournisseurs de revendications. 
   * Définir le branchement conditionnel dans des parcours utilisateur. 
* Intégrer des services compatibles avec l’API REST dans vos parcours utilisateur d’authentification personnalisés.  
* Ajouter la fédération avec les fournisseurs d’identité conformes à la norme OpenIDConnect. <br>
* Ajouter la fédération avec les fournisseurs d’identité qui adhèrent au protocole SAML 2.0. 

## <a name="terms-of-the-public-preview"></a>Conditions de la version préliminaire publique

* Nous vous encourageons à utiliser les nouvelles fonctionnalités à des fins d’évaluation uniquement.<br>
* Les nouvelles fonctionnalités ne sont pas destinées à une utilisation dans un environnement de production.<br>
* Les contrats de niveau de service (SLA) ne s’appliquent pas aux nouvelles fonctionnalités. <br>
* Les demandes de support peuvent être déposées via les canaux de support habituels. <br>
* Il n’existe aucune date prévue pour une mise à disposition générale.<br>
* À notre discrétion et pour quelque raison que ce soit, Microsoft peut signaler et rejeter ou limiter des scénarios et des parcours utilisateur qui dépassent la portée de la charte du produit Azure AD B2C comme plateforme de gestion des accès et des identités des clients.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilités des développeurs de l’ensemble de fonctionnalités de stratégie personnalisée
La configuration de stratégie manuelle accorde le niveau d’accès minimal à la plateforme sous-jacente d’Azure AD B2C et entraîne la création d’une infrastructure approuvée unique et entièrement personnalisable. Les permutations possibles des fournisseurs d’identité personnalisés, les relations d’approbation, les intégrations aux services externes et les flux de travail étape par étape exigeront beaucoup de la part des développeurs avancés qui les utilisent.

Afin de tirer pleinement parti de la préversion publique, nous suggérons aux développeurs qui utilisent l’ensemble de fonctionnalités de stratégie personnalisée de respecter les consignes suivantes :
* Se familiariser au langage de configuration du moteur d’expérience d’identité et à la gestion clé/secrets.
* S’approprier les scénarios et les intégrations personnalisées.
* Effectuer un test de scénario méthodique.
* Suivre les meilleures pratiques de test et de développement de logiciels avec au minimum un environnement de développement et de test et un environnement de production.
* Suivre les nouveaux développements des services et fournisseurs d’identité que vous intégrez. Par exemple, suivre les modifications apportées aux secrets, ainsi que les modifications planifiées et non planifiées du service.
* Configurer la surveillance active et surveiller la réactivité des environnements de production.
* Garder les adresses e-mail de contact à jour et rester attentif aux e-mails de l’équipe de site en ligne Microsoft.
* Prendre les mesures adéquates lorsque cela est recommandé par l’équipe de site en ligne Microsoft. 


>[!NOTE]
>Ces fonctionnalités peuvent éventuellement être incluses à des stratégies intégrées Azure AD, ce qui les rend plus accessibles à tous les développeurs.

## <a name="next-steps"></a>Étapes suivantes
[Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

