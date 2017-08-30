---
title: "Azure AD : vue d’ensemble de la réinitialisation de mot de passe en libre-service Azure AD | Microsoft Docs"
description: "En quoi la réinitialisation de mot de passe en libre-service d’Azure AD peut-elle être utile à votre organisation ?"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: 9f72a4900f20282827ff939aa41bc4f306e547a3
ms.contentlocale: fr-fr
ms.lasthandoff: 05/20/2017

---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>La réinitialisation de mot de passe en libre-service Azure AD pour les professionnels de l’informatique

« Libre-service » est un terme qui est actuellement très employé dans un grand nombre de services informatiques du monde entier, avec toutefois des significations différentes. Le marché regorge de produits permettant de gérer les groupes locaux, les mots de passe ou les profils utilisateur dans le cloud ou en local.

La réinitialisation de mot de passe libre-service (SSPR) Azure Active Directory (Azure AD) se distingue par sa facilité d’utilisation et de déploiement. La réinitialisation de mot de passe libre-service Azure AD intègre un ensemble de fonctionnalités qui offrent les avantages suivants :

* Possibilité pour les utilisateurs de gérer leur propre mot de passe
  * Depuis n’importe quel appareil
  * De n’importe quel endroit
  * Quel que soit le moment
* Possibilité de préserver la conformité avec les stratégies que vous définissez en qualité d’administrateur

Si vous êtes prêt, vous pouvez commencer à utiliser Azure AD SSPR en vous aidant de notre [guide de démarrage rapide](active-directory-passwords-getting-started.md) et permettre ainsi à vos utilisateurs de réinitialiser rapidement leur propre mot de passe.

## <a name="what-is-possible"></a>Ce qui est possible

* **Modification des mots de passe en libre-service** : les utilisateurs finaux ou les administrateurs peuvent modifier leur mot de passe sans faire appel à un administrateur.
* **Déverrouillage de compte en libre-service** : les utilisateurs finaux peuvent déverrouiller leur propre compte sans faire appel à l’aide d’un administrateur.
* **Réinitialisation des mots de passe en libre-service** : les utilisateurs finaux ou les administrateurs peuvent réinitialiser automatiquement leur mot de passe sans faire appel à un administrateur. La réinitialisation de mot de passe en libre-service nécessite Azure AD Premium ou Basic - [Éditions d’Azure Active Directory](active-directory-editions.md).
* **Réinitialisation de mot de passe initiée par l’administrateur** : permet à un administrateur de réinitialiser le mot de passe d’un utilisateur final ou d’un autre administrateur à partir du [portail Azure](https://docs.microsoft.com/azure/azure-portal-overview).
* **Rapports d’activité de gestion des mots de passe** : donnent aux administrateurs un aperçu de l’activité d’inscription et de réinitialisation des mots de passe dans leur organisation - [Rapports de gestion](active-directory-passwords-reporting.md).
* **Écriture différée de mot de passe** : autorise la gestion des mots de passe locaux à partir du cloud, si bien que tous les scénarios mentionnés ci-dessus peuvent être exécutés par ou pour des utilisateurs fédérés ou synchronisés par mot de passe. L’écriture différée de mot de passe nécessite [Azure AD Premium](active-directory-get-started-premium.md).

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Pourquoi opter pour la réinitialisation de mot de passe en libre-service Azure AD

* **Réduction des coûts** : la réinitialisation de mot de passe assistée par le support technique représente généralement 20 % des dépenses d’un service informatique.
* **Amélioration de l’expérience des utilisateurs finaux** et **diminution des besoins en support technique** : les utilisateurs finaux peuvent résoudre leurs propres problèmes de mot de passe en une seule fois sans avoir à faire appel au support technique ou à formuler une demande de support.
* **Encouragement à la mobilité** : les utilisateurs peuvent réinitialiser leur mot de passe où qu’ils se trouvent.

## <a name="azure-ad-self-service-password-reset-availability"></a>Disponibilité de la réinitialisation de mot de passe en libre-service Azure AD

La réinitialisation de mot de passe libre-service Azure AD est disponible dans trois niveaux, selon votre abonnement.

* **Azure AD Gratuit** : les administrateurs chargés uniquement du cloud peuvent réinitialiser leur propre mot de passe.
* **Azure AD De base** ou tout **abonnement Office 365 payé** : les clients utilisant uniquement le cloud et les administrateurs chargés uniquement du cloud peuvent réinitialiser leur propre mot de passe.
* **Azure AD Premium** : n’importe quel utilisateur ou administrateur, notamment les clients utilisant uniquement le cloud et les utilisateurs fédérés ou synchronisés par mot de passe, peuvent réinitialiser leur propre mot de passe. L’écriture différée doit être activée pour les mots de passe locaux.

## <a name="azure-ad-self-service-password-reset-a-sum-of-the-parts"></a>Composants de la réinitialisation de mot de passe en libre-service Azure AD

La réinitialisation de mot de passe en libre-service dans Azure AD est constituée des composants suivants :

* **Portail de configuration de la gestion des mots de passe** : vous permet de contrôler les options de gestion des mots de passe dans votre locataire via le portail Azure.
* **Portail d’inscription à la réinitialisation de mot de passe** : permet aux utilisateurs de s’inscrire à la réinitialisation de mot de passe.
* **Portail de réinitialisation de mot de passe** : permet aux utilisateurs de réinitialiser leur mot de passe à partir des tests définis par l’administrateur et des réponses qu’ils y ont apportées.
* **Portail de changement de mot de passe utilisateur** : permet aux utilisateurs de changer leur propre mot de passe en entrant leur ancien mot de passe et en indiquant le nouveau.
* **Rapports de gestion des mots de passe** : permettent aux administrateurs de consulter et d’analyser les rapports d’activité sur les mots de passe de leurs locataires dans le portail Azure.
* **Écriture différée de mot de passe en local à l’aide d’Azure AD Connect** : vous permet d’activer la gestion des utilisateurs locaux, fédérés ou synchronisés par mot de passe à partir du cloud.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Tarifs, contrat SLA, mises à jour et feuille de route Azure AD

Vous trouverez davantage de détails sur ces questions dans les pages suivantes :

* [**Détails concernant les tarifs d’Azure AD**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Tarifs d’Office 365**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Contrats de niveau de service Azure**](https://azure.microsoft.com/support/legal/sla/)
* [**Contrat SLA Microsoft Online Services**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Mises à jour Azure**](https://azure.microsoft.com/updates/)
* [**Feuille de route Azure**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Étapes suivantes

Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD.

* [**Démarrage rapide**](active-directory-passwords-getting-started.md) : soyez rapidement opérationnel avec la gestion des mots de passe en libre-service Azure AD. 
* [**Licences**](active-directory-passwords-licensing.md) : configurez vos licences Azure AD.
* [**Données**](active-directory-passwords-data.md) : comprenez mieux les données requises et leur utilisation dans la gestion des mots de passe.
* [**Déploiement**](active-directory-passwords-best-practices.md) : planifiez et déployez la réinitialisation de mot de passe en libre-service pour vos utilisateurs grâce aux conseils figurant ici.
* [**Personnalisation**](active-directory-passwords-customize.md) : personnalisez l’apparence de l’interface de réinitialisation de mot de passe en libre-service de votre société.
* [**Rapports**](active-directory-passwords-reporting.md) : découvrez si, quand et où vos utilisateurs accèdent aux fonctionnalités de réinitialisation de mot de passe en libre-service.
* [**Présentation technique approfondie**](active-directory-passwords-how-it-works.md) : découvrez ce qui se passe sous le capot pour mieux comprendre le fonctionnement.
* [**Forum Aux Questions (FAQ)**](active-directory-passwords-faq.md) : Comment ? Pourquoi ? Quoi ? Où ? Qui ? Quand ? - Les réponses aux questions que vous vouliez poser depuis toujours.
* [**Résolution des problèmes**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec la réinitialisation de mot de passe en libre-service.


