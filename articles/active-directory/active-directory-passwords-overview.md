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
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: f577ee95c34f9bc3065c026bc943d30622b9d658
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>La réinitialisation de mot de passe en libre-service Azure AD pour les professionnels de l’informatique

La réinitialisation de mot de passe en libre-service (SSPR) d’Azure Active Directory (Azure AD) permet aux utilisateurs de réinitialiser eux-mêmes leurs mots de passe quand et où ils le souhaitent, tout en permettant aux administrateurs de contrôler le mode de réinitialisation de ces mots de passe. Les utilisateurs n’ont plus besoin d’appeler le support technique pour réinitialiser leur mot de passe.

* **Modification de mot de passe en libre-service** : l’utilisateur connaît son mot de passe, mais il souhaite le modifier.
* **Réinitialisation de mot de passe en libre-service** : l’utilisateur ne parvient pas à se connecter et il souhaite réinitialiser son mot de passe à l’aide d’une ou de plusieurs des méthodes d’authentification validées suivantes.
   * SMS sur un téléphone mobile validé
   * Appel téléphonique sur un téléphone mobile ou de bureau validé
   * E-mail à un compte de messagerie secondaire validé
   * Réponses aux questions de sécurité
* **Déverrouillage de compte en libre-service** : l’utilisateur ne parvient pas à se connecter avec son mot de passe à son compte qui a été verrouillé et il souhaite le déverrouiller sans recourir à l’administrateur à l’aide de ses méthodes d’authentification.

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Pourquoi opter pour la réinitialisation de mot de passe en libre-service Azure AD

* **Réduction des coûts** : la réinitialisation de mot de passe assistée par le support technique représente généralement 20 % des appels d’un service informatique. 
* **Amélioration de l’expérience des utilisateurs finaux** et **diminution des besoins en support technique** : les utilisateurs finaux peuvent résoudre leurs propres problèmes de mot de passe en une seule fois sans avoir à faire appel au support technique ou à formuler une demande de support.
* **Encouragement à la mobilité** : les utilisateurs peuvent réinitialiser leur mot de passe où qu’ils se trouvent.
* **Maintien du contrôle** de la stratégie de sécurité : les administrateurs peuvent continuer à appliquer les stratégies en place.

Si vous êtes prêt, vous pouvez commencer à utiliser Azure AD SSPR en vous aidant de notre [guide de démarrage rapide](active-directory-passwords-getting-started.md) et permettre ainsi à vos utilisateurs de réinitialiser rapidement leur propre mot de passe.

## <a name="azure-ad-self-service-password-reset-availability"></a>Disponibilité de la réinitialisation de mot de passe en libre-service Azure AD

La réinitialisation de mot de passe libre-service Azure AD est disponible dans trois niveaux, selon votre abonnement.

* **Azure Active Directory Free** : les administrateurs cloud uniquement peuvent réinitialiser leur mot de passe.
* **Azure AD Basic** ou tout **abonnement Office 365 payant** : les clients utilisant uniquement le cloud peuvent réinitialiser leur propre mot de passe.
* **Azure AD Premium** : n’importe quel utilisateur ou administrateur, notamment les clients utilisant uniquement le cloud et les utilisateurs fédérés ou synchronisés par mot de passe, peuvent réinitialiser leur propre mot de passe. L’écriture différée doit être activée pour les mots de passe locaux.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Tarifs, contrat SLA, mises à jour et feuille de route Azure AD

Vous trouverez plus d’informations sur les licences, les prix et les plans futurs sur les sites suivants.

* [**Détails concernant les tarifs d’Azure AD**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Tarifs d’Office 365**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Contrats de niveau de service Azure**](https://azure.microsoft.com/support/legal/sla/)
* [**Contrat SLA Microsoft Online Services**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Mises à jour Azure**](https://azure.microsoft.com/updates/)
* [**Feuille de route Azure**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Étapes suivantes

* Prêt à vous lancer avec SSPR ? [Réinitialisez le mot de passe en libre-service d’Azure AD](active-directory-passwords-getting-started.md).
* Planifiez un déploiement SSPR réussi pour vos utilisateurs en suivant les conseils de notre [**guide de lancement**](active-directory-passwords-best-practices.md).
* [Réinitialisez ou modifiez votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [Inscrivez-vous pour la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md).