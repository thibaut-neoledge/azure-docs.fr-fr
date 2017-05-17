---
title: "Azure Active Directory B2C : notes du développeur sur l’utilisation des stratégies personnalisées | Microsoft Docs"
description: "Notes à destination des développeurs pour configurer et maintenir B2C avec des stratégies personnalisées"
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
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 72447a234c5889f36b10b828d28775fc5995c4d4
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Notes de version pour la version préliminaire publique de la stratégie personnalisée Azure Active Directory B2C
L’ensemble de fonctionnalités de **stratégie personnalisée** est désormais disponible à des fins d’évaluation en version préliminaire publique pour tous les clients Azure Active Directory B2C (Azure AD B2C).  Cet ensemble de fonctionnalités est destiné aux développeurs d’identité avancés créant les solutions d’identité les plus complexes.  

À ce jour, cet ensemble de fonctionnalités exige que le développeur configure l’infrastructure d’expérience d’identité directement via la modification du fichier XML.  Cette méthode de configuration est à la fois puissante et complexe.  Les développeurs d’identité avancés qui utilisent l’infrastructure d’expérience d’identité doivent prévoir du temps pour suivre des procédures et lire des documents de référence, afin de parfaire leurs connaissances. 

## <a name="features-included-in-this-public-preview"></a>Fonctionnalités incluses dans cette version préliminaire publique
Les nouvelles fonctionnalités introduites dans la version publique permettent aux développeurs d’effectuer les tâches suivantes :
1. Créer et charger des parcours utilisateur d’authentification personnalisés à l’aide de stratégies personnalisées
* Décrire des parcours utilisateur étape par étape comme des échanges entre des fournisseurs de revendications
* Définir le branchement conditionnel dans des parcours utilisateur
2. Intégrer des services compatibles avec l’API REST dans vos parcours utilisateur d’authentification personnalisés
3. Ajouter la fédération avec les fournisseurs d’identité conformes à la norme OpenIDConnect
4. Ajouter la fédération avec les fournisseurs d’identité qui adhèrent au protocole SAML 2.0



## <a name="terms-of-the-public-preview"></a>Conditions de la version préliminaire publique
1. Nous vous conseillons d’utiliser les nouvelles fonctionnalités à des fins d’évaluation uniquement.
2. Les nouvelles fonctionnalités ne sont pas destinées à une utilisation dans un environnement de production.
3. Les contrats de niveau de service (SLA) ne s’appliquent pas aux nouvelles fonctionnalités4. Les demandes de support peuvent être déposées via les canaux de support habituels.
5. Il n’existe aucune date prévue pour une mise à disposition générale.
6. À notre discrétion et pour quelque raison que ce soit, Microsoft peut signaler et rejeter ou limiter des scénarios et des parcours utilisateur qui dépassent la portée de la charte du produit Azure AD B2C comme plate-forme de gestion des accès et des identités des clients.

## <a name="responsibilities-for-custom-policy-feature-set-developers"></a>Responsabilités des développeurs de l’ensemble de fonctionnalités de stratégie personnalisée
La configuration de stratégie manuelle accorde le niveau d’accès minimal à la plateforme sous-jacente d’Azure AD B2C et entraîne la création d’une infrastructure approuvée unique et entièrement personnalisable.  Les permutations quasi illimitées des fournisseurs d’identité personnalisés, les relations d’approbation, les intégrations aux services externes et les flux de travail étape par étape exigeront beaucoup de la part des développeurs avancés qui les utilisent.
Afin de tirer pleinement parti de la version préliminaire publique, nous suggérons aux développeurs qui utilisent l’ensemble de fonctionnalités de stratégie personnalisée d’adhérer à ce qui suit :
* Se familiariser au langage de configuration du moteur d’expérience d’identité et à la gestion clé/secrets
* S’approprier les scénarios et les intégrations personnalisées
* Effectuer un test de scénario méthodique
* Suivre les bonnes pratiques de test et de développement de logiciels avec au minimum un environnement de développement/test et un environnement de production
* Rester informé des nouveaux développements avec les fournisseurs d’identité et les services que vous intégrez, par exemple, suivre les modifications des secrets, les modifications planifiées/non planifiées apportées au service, etc. Configurer une surveillance active et surveiller la réactivité de leurs environnements de production
* Garder les e-mails de contact à jour et rester attentif aux e-mails de l’équipe de site en ligne Microsoft
* Prendre les mesures adéquates lorsque cela est recommandé par l’équipe de site en ligne Microsoft 


>[!NOTE]
>Ces fonctionnalités peuvent éventuellement être incluses à des stratégies intégrées Azure AD, ce qui les rend plus accessibles à tous les développeurs.

## <a name="next-steps"></a>Étapes suivantes
[Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

