---
title: "Éditions d’Azure Active Directory | Microsoft Docs"
description: "Cet article détaille l’édition gratuite et les éditions payantes d’Azure Active Directory. Azure Active Directory de base, Azure Active Directory Premium P1 et Azure Active Directory Premium P2 sont les éditions payantes."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: dcaf8939-7633-40a8-bd76-27dedbb6083a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: b875c83ba7d87a572e78625e4038840aadcf9c85
ms.lasthandoff: 03/10/2017


---
# <a name="azure-active-directory-editions"></a>Éditions d’Azure Active Directory
Tous les services d'entreprise Microsoft Online reposent sur Azure Active Directory (Azure AD) pour l'authentification et autres besoins d’identification. Si vous vous abonnez à un service d’entreprise Microsoft Online (par exemple, Office 365, Microsoft Azure), vous obtenez Azure AD et un accès à toutes les fonctionnalités gratuites décrites ci-dessous.  

Azure Active Directory est une solution cloud complète et hautement disponible de gestion des identités et des accès qui associe des services d'annuaire principaux, une gouvernance avancée des identités et la gestion de l'accès aux applications. Azure Active Directory offre également une riche plateforme reposant sur des standards, qui permet aux développeurs de contrôler l'accès à leurs applications, grâce à des stratégies et des règles centralisées. L’édition gratuite d’Azure Active Directory vous permet de gérer les utilisateurs et les groupes, d’effectuer une synchronisation avec les annuaires locaux, d’obtenir l’authentification unique entre Azure, Office 365 et des milliers d’applications SaaS populaires telles que Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox, etc. Pour en savoir plus sur Azure Active Directory, consultez [Présentation d'Azure AD](active-directory-whatis.md)

Pour enrichir votre expérience Azure Active Directory, vous pouvez ajouter des fonctionnalités payées à l'aide des éditions Azure Active Directory de base, Premium P1 et Premium P2. Les versions payées Azure Active Directory se superposent à votre annuaire gratuit existant et vous offrent de nombreuses fonctionnalités de classe entreprise : libre-service, surveillance améliorée, rapports de sécurité, authentification multifacteur MFA (Multi-Factor Authentication) et accès sécurisé pour votre personnel mobile.

Les abonnements Office 365 incluent les fonctionnalités Azure Active Directory supplémentaires décrites dans le tableau comparatif ci-dessous.

> [!NOTE]
> Pour les options de tarification de ces éditions, consultez la [tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Les éditions Azure Active Directory, Premium P1, Premium P2 et Azure Active Directory de base ne sont actuellement pas prises en charge en Chine. Pour plus d’informations, contactez-nous via le Forum Azure Active Directory.
>
>

* **Azure Active Directory - version de base** : Conçue pour les employés avec des besoins principalement centrés sur le cloud, cette édition offre un accès aux applications avec des besoins principalement centrés sur le cloud ainsi que des solutions de gestion des identités en libre-service. Avec l’édition de base d’Azure Active Directory, vous disposez de fonctionnalités qui améliorent votre productivité et réduisent vos coûts comme la gestion d’accès basée sur les groupes, la réinitialisation libre-service du mot de passe pour les applications cloud et un proxy d’application Azure Active Directory (pour publier des applications web locales à l’aide d’Azure Active Directory). Le tout, avec un contrat SLA pour professionnel qui fournit 99,9 % de disponibilité.
* **Azure Active Directory - version Premium P1** : Conçue pour permettre aux entreprises avec des besoins d’identification et de gestion des accès plus élevés, la version Premium d’Azure Active Directory ajoute des fonctionnalités de gestion des identités de niveau professionnel et permet aux utilisateurs hybrides d'accéder en toute transparence aux fonctionnalités locaux et disponibles sur le cloud. Cette édition inclut tout ce dont vous avez besoin pour les travailleurs de l'information et les administrateurs d'identités dans les environnements hybrides grâce à un accès inter-applications, à une gestion des identités et de l’accès en libre-service (IAM), à la protection des identités et à la sécurité dans le cloud. Elle prend en charge des ressources d'administration et de délégation avancées telles que des groupes dynamiques et la gestion de groupes en libre-service. Elle inclut Microsoft Identity Manager (une suite de gestion locale des identités et des accès) et fournit des fonctionnalités d'écriture différée sur le cloud offrant aux utilisateurs locaux des solutions comme la réinitialisation en libre-service du mot de passe du service.
* **Azure Active Directory Premium P2** - conçu avec la protection avancée pour tous vos utilisateurs et administrateurs, cette nouvelle offre inclut toutes les fonctionnalités d’Azure AD Premium P1, ainsi que nos nouvelles solutions Identity Protection et Privileged Identity Management. Active Directory Azure Identity Protection exploite des milliards de signaux pour fournir un accès conditionnel basé sur les risques à vos applications et données d’entreprise critiques. Nous vous aidons également ç gérer et protéger les comptes privilégiés avec Azure Active Directory Privileged Identity Management afin que vous puissiez découvrir, restreindre et surveiller les administrateurs et leur accès aux ressources, et fournir un accès juste-à-temps quand cela est nécessaire.  

Pour vous connecter et commencer à utiliser Active Directory Premium dès aujourd’hui, consultez la rubrique [Prise en main d’Azure Active Directory Premium (AD)](active-directory-get-started-premium.md).

> [!NOTE]
> Plusieurs fonctionnalités Azure Active Directory sont disponibles via les éditions de « paiement à l’utilisation » :
>
> * Active Directory B2C constitue la solution de gestion des identités et des accès pour vos applications grand public. Pour plus d’informations, consultez [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * L'authentification multifacteur MFA (Multi-Factor Authentication) Azure peut être effectuée par utilisateur ou par le biais de fournisseurs d'authentification. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Multi-Factor Authentication ?](../multi-factor-authentication/multi-factor-authentication.md)
>
>

## <a name="comparing-generally-available-features"></a>Comparaison des fonctionnalités mises à la disposition générale
> [!NOTE]
> Pour bénéficier d’un affichage différent de ces données, consultez les [fonctionnalités d’Azure Active Directory](https://www.microsoft.com/en/server-cloud/products/azure-active-directory/features.aspx).
>
>

**Fonctionnalités communes**

* [Objets d’annuaire](#directory-objects)
* [Gestion des groupes/utilisateurs (ajout/mise à jour/suppression)/ approvisionnement basé sur l’utilisateur, inscription d’appareil](#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)
* [Authentification unique (SSO)](#single-sign-on-sso)
* [Modification libre-service du mot de passe pour les utilisateurs du cloud](#self-service-password-change-for-cloud-users)
* [Connect (moteur de synchronisation qui étend les annuaires locaux dans Azure Active Directory)](#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory)
* [Rapports de sécurité/d’utilisation](#securityusage-reports)

**Fonctionnalités de base**

* [Approvisionnement/gestion des accès par groupe](#group-based-access-managementprovisioning)
* [Réinitialisation libre-service du mot de passe pour les utilisateurs du cloud](#self-service-password-reset-for-cloud-users)
* [Image de l’entreprise (personnalisation des pages de connexion/volet d’accès)](#company-branding-logon-pagesaccess-panel-customization)
* [Proxy d’application](#application-proxy)
* [Un contrat SLA à 99,9 %](#sla-999)

**Fonctionnalités de Premium P1**

* [Gestion d’applications et de groupes libre-service/ajout d’applications libre-service/groupes dynamiques](#self-service-group)
* [Réinitialisation, modification et déverrouillage de mot de passe libre-service avec écriture différée locale](#self-service-password-resetchangeunlock-with-on-premises-write-back)
* [Multi-Factor Authentication (dans le cloud et localement - MFA Server)](#multi-factor-authentication-cloud-and-on-premises-mfa-server)
* [Licence d’accès client MIM + serveur MIM](#mim-cal-mim-server)
* [Cloud App Discovery](#cloud-app-discovery)
* [Connect Health](#connect-health)
* [Substitution automatique des mots de passe pour les comptes de groupe](#automatic-password-rollover-for-group-accounts)

**Fonctionnalités de Premium P2**

* [Identity Protection](active-directory-identityprotection.md)
* [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

**Azure Active Directory Join - Fonctionnalités propres à Windows 10**

* [Joindre un appareil à Azure AD, Desktop SSO, Microsoft Passport pour Azure AD, récupération Bitlocker de l’administrateur](#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery)
* [Inscription automatique GPM, récupération Bitlocker libre-service, administrateurs locaux supplémentaires pour appareils Windows 10 par le biais d’Azure AD Join](#mdm-auto-enrollment)

## <a name="common-features"></a>Fonctionnalités communes
#### <a name="directory-objects"></a>Objets d’annuaire
**Type :** Fonctionnalités communes

Le quota d’utilisation par défaut est de 150 000 objets. Un objet est une entrée du service d’annuaire, représentée par son nom unique. Exemple d’objet : entrée utilisateur utilisée à des fins d’authentification. Si vous deviez dépasser ce quota par défaut, contactez le support. La limite de 500 000 objets ne s’applique pas à Office 365, Microsoft Intune ni aux autres services en ligne Microsoft payants qui reposent sur Azure Active Directory pour les services d’annuaires.

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| Jusqu’à 500 000 objets |Aucune limite d’objets |Aucune limite d’objets |Aucune limite d’objets pour les comptes d’utilisateurs Office 365 |

#### <a name="usergroup-management-addupdatedelete-user-based-provisioning-device-registration"></a>Gestion des groupes/utilisateurs (ajout/mise à jour/suppression), approvisionnement basé sur l’utilisateur, inscription d’appareil
**Type :** Fonctionnalités communes

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| ![Vérification][12] |![Vérification][12] |![Vérification][12] |![Vérification][12] |

**Détails supplémentaires :**

* [Administration de votre annuaire Azure AD](active-directory-administer.md)
* [Vue d’ensemble du service Azure Active Directory Device Registration](active-directory-conditional-access-device-registration-overview.md)

#### <a name="single-sign-on-sso"></a>Authentification unique (SSO)
**Type :** Fonctionnalités communes

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| 10 applications par utilisateur (1) |10 applications par utilisateur (1) |Aucune limite (2) |10 applications par utilisateur (1) |

1. Avec Azure AD Gratuit et Azure AD De base, les utilisateurs finaux bénéficient d’un accès avec connexion unique jusqu’à 10 applications.
2. Intégration libre-service de n’importe quelle application prenant en charge SAML, SCIM ou l’authentification par formulaire à l’aide de modèles fournis dans le menu de la galerie d’applications. Pour en savoir plus, consultez la page [Configuration de l’authentification unique pour les applications ne faisant pas partie de la galerie d’applications Azure Active Directory](active-directory-saas-custom-apps.md).

**Détails supplémentaires :**

* [Gestion des applications avec Azure Active Directory (AD)](active-directory-enable-sso-scenario.md)

#### <a name="self-service-password-change-for-cloud-users"></a>Modification libre-service du mot de passe pour les utilisateurs du cloud
**Type :** Fonctionnalités communes

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| ![Vérification][12] |![Vérification][12] |![Vérification][12] |![Vérification][12] |

**Détails supplémentaires :**

* [Comment mettre à jour votre mot de passe](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password)

#### <a name="connect--sync-engine-that-extends-on-premises-directories-to-azure-active-directory"></a>Connect (moteur de synchronisation qui étend les annuaires locaux dans Azure Active Directory)
**Type :** Fonctionnalités communes

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| ![Vérification][12] |![Vérification][12] |![Vérification][12] |![Vérification][12] |

**Détails supplémentaires :**

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

#### <a name="securityusage-reports"></a>Rapports d’utilisation/de sécurité
**Type :** Fonctionnalités communes

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| 3 rapports de base |3 rapports de base |Rapports avancés |3 rapports de base |

**Détails supplémentaires :**

* [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md)

## <a name="premium-and-basic-features"></a>Fonctionnalités Premium et de base
#### <a name="group-based-access-managementprovisioning"></a>Approvisionnement/gestion des accès par groupe
**Type :** Fonctionnalités de base

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Vérification][12] | ![Vérification][12] | &nbsp; |

**Détails supplémentaires :**

* [Utilisation d’un groupe pour gérer l’accès aux applications SaaS](active-directory-accessmanagement-group-saasapps.md)

#### <a name="self-service-password-reset-for-cloud-users"></a>Réinitialisation libre-service du mot de passe pour les utilisateurs du cloud
**Type :** Fonctionnalités de base

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Vérification][12] |![Vérification][12] | ![Vérification][12] |

**Détails supplémentaires :**

* [Réinitialisation du mot de passe Azure AD pour les utilisateurs et les administrateurs](active-directory-passwords.md)

#### <a name="company-branding-logon-pagesaccess-panel-customization"></a>Image de l’entreprise (personnalisation des pages de connexion/volet d’accès)
**Type :** Fonctionnalités de base

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Vérification][12] |![Vérification][12] | ![Vérification][12] |

**Détails supplémentaires :**

* [Ajout de votre société à vos pages de connexion et du panneau d’accès](active-directory-add-company-branding.md)

#### <a name="application-proxy"></a>Proxy d’application
**Type :** Fonctionnalités de base

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Vérification][12] | ![Vérification][12] | &nbsp; |

**Détails supplémentaires :**

* [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md)

#### <a name="sla-999"></a>Un contrat SLA à 99,9 %
**Type :** Fonctionnalités de base

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; |![Vérification][12] |![Vérification][12] | ![Vérification][12] |

**Détails supplémentaires :**

* [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/)

## <a name="premium-features"></a>Fonctionnalités Premium


#### <a name="self-service-group"></a>Gestion d’applications et de groupes libre-service/ajout d’applications libre-service/groupes dynamiques
**Type :** Fonctionnalités Premium

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Vérification][12]| &nbsp; |

#### <a name="self-service-password-resetchangeunlock-with-on-premises-write-back"></a>Réinitialisation, modification et déverrouillage de mot de passe libre-service avec écriture différée locale
**Type :** Fonctionnalités Premium

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Vérification][12] | &nbsp; |

#### <a name="multi-factor-authentication-cloud-and-on-premises-mfa-server"></a>Multi-Factor Authentication (dans le cloud et localement - MFA Server)
**Type :** Fonctionnalités Premium

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; |![Vérification][12] |Limité au cloud uniquement pour les applications Office 365 |

**Détails supplémentaires :**

* [Présentation d'Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)


#### <a name="mim-cal-mim-server"></a>Licence d’accès client MIM + serveur MIM
Les droits du logiciel Microsoft Identity Manager Server sont octroyés avec les licences Windows Server (toutes les éditions). Comme Microsoft Identity Manager s’exécute sur le système d’exploitation Windows Server, tant que le serveur exécute une copie sous licence valide de Windows Server, Microsoft Identity Manager peut être installé et utilisé sur ce serveur. Aucune autre licence séparée n’est nécessaire pour Microsoft Identity Manager Server.

**Type :** Fonctionnalités Premium

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; |![Vérification][12] | &nbsp; |

#### <a name="cloud-app-discovery"></a>Cloud App Discovery
**Type :** Fonctionnalités Premium

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Vérification][12] | &nbsp; |

**Détails supplémentaires :**

* [Détection des applications cloud non gérées avec Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)

#### <a name="azure-ad-connect-health"></a>Azure AD Connect Health
**Type :** Fonctionnalités Premium

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Vérification][12] | &nbsp; |

**Détails supplémentaires :**

* [Surveillez votre infrastructure d’identité locale et vos services de synchronisation dans le cloud](active-directory-aadconnect-health.md)

#### <a name="automatic-password-rollover-for-group-accounts"></a>Substitution automatique des mots de passe pour les comptes de groupe
**Type :** Fonctionnalités Premium

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Vérification][12] | &nbsp; |

#### <a name="identity-protection"></a>Identity Protection
**Type :** Fonctionnalités Premium

| Édition gratuite | Édition de base | Édition Premium P2 | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Vérification][12] | &nbsp; |

#### <a name="privileged-identity-management"></a>Privileged Identity Management
**Type :** Fonctionnalités Premium

| Édition gratuite | Édition de base | Édition Premium P2 | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Vérification][12] | &nbsp; |

## <a name="azure-active-directory-join--windows-10-only--related-features"></a>Azure Active Directory Join - Fonctionnalités propres à Windows 10
#### <a name="join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery"></a>Joindre un appareil à Azure AD, Desktop SSO, Microsoft Passport pour Azure AD, récupération Bitlocker de l’administrateur
**Type :** Azure Active Directory Join - Fonctionnalités propres à Windows 10

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| ![Vérification][12] |![Vérification][12] |![Vérification][12] |![Vérification][12] |


#### <a name="mdm-auto-enrollment"></a>Inscription automatique GPM, récupération Bitlocker libre-service, administrateurs locaux supplémentaires pour appareils Windows 10 par le biais d’Azure AD Join
**Type :** Azure Active Directory Join - Fonctionnalités propres à Windows 10

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Vérification][12] | &nbsp; |

#### <a name="enterprise-state-roaming"></a>Enterprise State Roaming
**Type :** Azure Active Directory Join - Fonctionnalités propres à Windows 10

**Disponibilité :**

| Édition gratuite | Édition de base | Éditions Premium (P1 et P2) | Applications Office 365 uniquement |
|:---:|:---:|:---:|:---:|
| &nbsp; | &nbsp; | ![Vérification][12] | &nbsp; |

**Détails supplémentaires :**

* [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)

## <a name="azure-ad-preview-features"></a>Fonctionnalités préliminaires d’Azure AD
Outre les fonctionnalités généralement disponibles des éditions gratuite, de base et Premium (P1 et P2), Azure AD vous offre également un ensemble de fonctionnalités préliminaires. Vous pouvez les utiliser pour avoir un aperçu des nouveautés à venir dans un avenir proche et déterminer si ces fonctionnalités peuvent vous aider à améliorer votre environnement.

**Fonctionnalités préliminaires disponibles :**

* [B2B Collaboration](active-directory-b2b-collaboration-overview.md)
* [Unités administratives](active-directory-administrative-units-management.md)
* [Intégration d’applications HR](active-directory-saas-workday-inbound-tutorial.md)
* [Authentification par certificat sur iOS](active-directory-certificate-based-authentication-ios.md)
* [Authentification par certificat sur Android](active-directory-certificate-based-authentication-android.md)

## <a name="next-steps"></a>Étapes suivantes
* [Prise en main d’Azure Active Directory Premium (AD)](active-directory-get-started-premium.md)
* [Ajout de votre société à vos pages de connexion et du panneau d’accès](active-directory-add-company-branding.md)
* [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md)

<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

