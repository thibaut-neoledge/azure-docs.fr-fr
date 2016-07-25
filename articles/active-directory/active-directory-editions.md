<properties
	pageTitle="Éditions d’Azure Active Directory | Microsoft Azure"
	description="Rubrique qui décrit les éditions gratuites et payantes d’Azure Active Directory. Azure Active Directory Standard est une version gratuite tandis qu’Azure Active Directory Premium est une version payante."
	services="active-directory"
	documentationCenter=""
	authors="MarkusVi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/08/2016"
	ms.author="markvi"/>

# Éditions d’Azure Active Directory

Tous les services d'entreprise Microsoft Online reposent sur Azure Active Directory pour l'authentification et autres besoins d’identification. Si vous vous abonnez à un service d’entreprise Microsoft Online (par exemple, Office 365, Microsoft Azure, etc.), vous obtenez Azure Active Directory (Azure AD) et un accès à toutes les fonctionnalités gratuites décrites ci-dessous.


Azure Active Directory est un service qui fournit des fonctionnalités complètes de gestion de l’accès et de l’identité dans le cloud pour vos employés, partenaires et clients. Il combine des services d’annuaire, de gouvernance avancée de l’identité, une importante plateforme basée sur des normes pour les développeurs et une gestion de l’accès à l’application pour vos propres applications intégrées ou une des milliers d’autres disponibles. L’édition gratuite d’Azure Active Directory vous permet de gérer les utilisateurs et les groupes, d’effectuer une synchronisation avec les annuaires locaux, d’obtenir l’authentification unique entre Azure, Office 365 et des milliers d’applications SaaS populaires telles que Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox, etc. Pour en savoir plus sur Azure Active Directory, consultez [Présentation d'Azure AD](active-directory-whatis.md).



Pour enrichir votre expérience Azure Active Directory, vous pouvez ajouter des fonctionnalités payées à l'aide des éditions Azure Active Directory de base et Premium. Les versions payées Azure Active Directory se superposent à votre annuaire gratuit existant et vous offrent de nombreuses fonctionnalités de classe entreprise : libre-service, surveillance améliorée, rapports de sécurité, authentification multifacteur MFA (Multi-Factor Authentication) et accès sécurisé pour votre personnel mobile.

Les abonnements Office 365 incluent les fonctionnalités Azure Active Directory supplémentaires décrites dans le tableau comparatif ci-dessous.


> [AZURE.NOTE] Pour les options de tarification de ces éditions, consultez la [tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Les éditions Azure Active Directory Premium et Azure Active Directory de base ne sont actuellement pas prises en charge en Chine. Pour plus d’informations, contactez-nous via le Forum Azure Active Directory.


- **Azure Active Directory - version de base** : Conçue pour les employés avec des besoins principalement centrés sur le cloud, cette édition offre un accès aux applications avec des besoins principalement centrés sur le cloud ainsi que des solutions de gestion des identités en libre-service. Avec l’édition de base d’Azure Active Directory, vous disposez de fonctionnalités qui améliorent votre productivité et réduisent vos coûts comme la gestion d’accès basée sur les groupes, la réinitialisation libre-service du mot de passe pour les applications cloud et un proxy d’application Azure Active Directory (pour publier des applications web locales à l’aide d’Azure Active Directory). Le tout, avec un contrat SLA pour professionnel qui fournit 99,9 % de disponibilité.
 
- **Azure Active Directory - version Premium** : Conçue pour permettre aux entreprises avec des besoins d’identification et de gestion des accès plus élevés, la version Premium d’Azure Active Directory ajoute des fonctionnalités de gestion des identités de niveau professionnel et permet aux utilisateurs hybrides d'accéder en toute transparence aux fonctionnalités locaux et disponibles sur le cloud. Cette édition inclut tout ce dont vous avez besoin pour les travailleurs de l'information et les administrateurs d'identités dans les environnements hybrides grâce à un accès inter-applications, à une gestion des identités et de l’accès en libre-service (IAM), à la protection des identités et à la sécurité dans le cloud. Elle prend en charge des ressources d'administration et de délégation avancées telles que des groupes dynamiques et la gestion de groupes en libre-service. Elle inclut Microsoft Identity Manager (une suite de gestion locale des identités et des accès) et fournit des fonctionnalités d'écriture différée sur le cloud offrant aux utilisateurs locaux des solutions comme la réinitialisation en libre-service du mot de passe du service.

Pour vous connecter et commencer à utiliser Active Directory Premium dès aujourd’hui, consultez la rubrique [Prise en main d’Azure Active Directory Premium (AD)](active-directory-get-started-premium.md).


> [AZURE.NOTE] 
Plusieurs fonctionnalités Azure Active Directory sont disponibles via les éditions de « paiement à l’utilisation » :
>
>- Active Directory B2C constitue la solution de gestion des identités et des accès pour vos applications grand public. Pour plus d’informations, consultez [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
 
>-	L'authentification multifacteur MFA (Multi-Factor Authentication) Azure peut être effectuée par utilisateur ou par le biais de fournisseurs d'authentification. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Multi-Factor Authentication ?](../multi-factor-authentication/multi-factor-authentication.md)


##Comparaison des fonctionnalités mises à la disposition générale

> [AZURE.NOTE] Pour bénéficier d’un affichage différent de ces données, consultez les [fonctionnalités d’Azure Active Directory](https://www.microsoft.com/en/server-cloud/products/azure-active-directory/features.aspx).

| | Azure AD Gratuit | Azure AD Standard | Azure AD Premium |
| ---                      | :-:           | :-:            | :-:              |
| Fonctionnalités communes | ![Vérification][12] | ![Vérification][12] | ![Vérification][12] |
| Fonctionnalités de base | | ![Vérification][12] | ![Vérification][12] |
| Fonctionnalités Premium | | | ![Vérification][12] |





**Fonctionnalités communes**

- [Objets d’annuaire](#directory-objects)

- [Gestion des groupes/utilisateurs (ajout/mise à jour/suppression)/ approvisionnement basé sur l’utilisateur, inscription d’appareil](#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)

- [Authentification unique (SSO)](#single-sign-on-sso)

- [Modification libre-service du mot de passe pour les utilisateurs du cloud](#self-service-password-change-for-cloud-users)

- [Connect (moteur de synchronisation qui étend les annuaires locaux dans Azure Active Directory)](#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory)

- [Rapports de sécurité/d’utilisation](#securityusage-reports)



**Fonctionnalités de base**

- [Approvisionnement/gestion des accès par groupe](#group-based-access-managementprovisioning)

- [Réinitialisation libre-service du mot de passe pour les utilisateurs du cloud](#self-service-password-reset-for-cloud-users)

- [Image de l’entreprise (personnalisation des pages de connexion/volet d’accès)](#company-branding-logon-pagesaccess-panel-customization)

- [Proxy d’application](#application-proxy)

- [Un contrat SLA à 99,9 %](#sla-999)


**Fonctionnalités Premium**

- [Gestion d’applications et de groupes libre-service/ajout d’applications libre-service/groupes dynamiques](#self-service-group-and-app-managementself-service-application-additions-dynamic-groups)

- [Réinitialisation, modification et déverrouillage de mot de passe libre-service avec écriture différée locale](#self-service-password-resetchangeunlock-with-on-premises-write-back)

- [Multi-Factor Authentication (dans le cloud et localement - MFA Server)](#multi-factor-authentication-cloud-and-on-premises-mfa-server)

- [Licence d’accès client MIM + serveur MIM](#mim-cal-mim-server)

- [Cloud App Discovery](#cloud-app-discovery)

- [Connect Health](#connect-health)

- [Substitution automatique des mots de passe pour les comptes de groupe](#automatic-password-rollover-for-group-accounts)


**Azure Active Directory Join - Fonctionnalités propres à Windows 10**

- [Joindre un appareil à Azure AD, Desktop SSO, Microsoft Passport pour Azure AD, récupération Bitlocker de l’administrateur](#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery)

- [Inscription automatique GPM, récupération Bitlocker libre-service, administrateurs locaux supplémentaires pour appareils Windows 10 par le biais d’Azure AD Join](#mdm-auto-enrolment-self-service-bitlocker-recovery-additional-local-administrators-to-windows-10-devices-via-azure-ad-join)






## Fonctionnalités communes
#### Objets d’annuaire 

**Type :** Fonctionnalités communes

Le quota d’utilisation par défaut est de 150 000 objets. Un objet est une entrée du service d’annuaire, représentée par son nom unique. Exemple d’objet : entrée utilisateur utilisée à des fins d’authentification. Si vous deviez dépasser ce quota par défaut, contactez le support. La limite de 500 000 objets ne s’applique pas à Office 365, Microsoft Intune ni aux autres services en ligne Microsoft payants qui reposent sur Azure Active Directory pour les services d’annuaires.


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| Jusqu’à 500 000 objets| Aucune limite d’objets| Aucune limite d’objets| Aucune limite d’objets pour les comptes d’utilisateurs Office 365|



#### Gestion des groupes/utilisateurs (ajout/mise à jour/suppression)/ approvisionnement basé sur l’utilisateur, inscription d’appareil

**Type :** Fonctionnalités communes

**Disponibilité :**


| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| ![Vérification][12]| ![Vérification][12]| ![Vérification][12]| ![Vérification][12]|

**Détails supplémentaires :**

- [Administration de votre annuaire Azure AD](active-directory-administer.md)
- [Vue d’ensemble du service Azure Active Directory Device Registration](active-directory-conditional-access-device-registration-overview.md)




#### Authentification unique (SSO)

**Type :** Fonctionnalités communes


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| 10 applications par utilisateur (1) | 10 applications par utilisateur (1) | Aucune limite (2) | 10 applications par utilisateur (1)|

1. Avec les niveaux Gratuit et De base d’Azure AD, les utilisateurs finaux qui bénéficient d’un accès aux applications SaaS peuvent visualiser jusqu’à 10 applications dans leur volet d’accès et y accéder par le biais de l’authentification unique. Dans ces deux niveaux, les administrateurs peuvent configurer l’authentification unique et attribuer aux utilisateurs un accès à un nombre illimité d’applications SaaS. À noter toutefois que les utilisateurs ne pourront afficher que 10 applications à la fois dans leur volet d’accès.

2. Intégration libre-service de n’importe quelle application prenant en charge SAML, SCIM ou l’authentification par formulaire à l’aide de modèles fournis dans le menu de la galerie d’applications. Pour en savoir plus, consultez la page [Configuration de l’authentification unique pour les applications ne faisant pas partie de la galerie d’applications Azure Active Directory](active-directory-saas-custom-apps.md).

**Détails supplémentaires :**

- [Gestion des applications avec Azure Active Directory (AD)](active-directory-enable-sso-scenario.md)



#### Modification libre-service du mot de passe pour les utilisateurs du cloud

**Type :** Fonctionnalités communes

**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| ![Vérification][12]| ![Vérification][12]| ![Vérification][12]| ![Vérification][12]|

**Détails supplémentaires :**

- [Comment mettre à jour votre mot de passe](active-directory-passwords-update-your-own-password.md)




#### Connect (moteur de synchronisation qui étend les annuaires locaux dans Azure Active Directory) 

**Type :** Fonctionnalités communes


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| ![Vérification][12]| ![Vérification][12]| ![Vérification][12]| ![Vérification][12]|

**Détails supplémentaires :**

- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)



#### Rapports d’utilisation/de sécurité

**Type :** Fonctionnalités communes


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| 3 rapports de base| 3 rapports de base| Rapports avancés| 3 rapports de base|

**Détails supplémentaires :**

- [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md)




## Fonctionnalités Premium et de base
#### Approvisionnement/gestion des accès par groupe

**Type :** Fonctionnalités de base


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | ![Vérification][12]| ![Vérification][12]| |

**Détails supplémentaires :**

- [Utilisation d’un groupe pour gérer l’accès aux applications SaaS](active-directory-accessmanagement-group-saasapps.md)



#### Réinitialisation libre-service du mot de passe pour les utilisateurs du cloud

**Type :** Fonctionnalités de base


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | ![Vérification][12]| ![Vérification][12]| ![Vérification][12]|

**Détails supplémentaires :**

- [Réinitialisation du mot de passe Azure AD pour les utilisateurs et les administrateurs](active-directory-passwords.md)



#### Image de l’entreprise (personnalisation des pages de connexion/volet d’accès)

**Type :** Fonctionnalités de base


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | ![Vérification][12]| ![Vérification][12]| ![Vérification][12]|

**Détails supplémentaires :**

- [Ajout de votre société à vos pages de connexion et du panneau d’accès](active-directory-add-company-branding.md)



#### Proxy d’application

**Type :** Fonctionnalités de base


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | ![Vérification][12]| ![Vérification][12]| |

**Détails supplémentaires :**

- [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md)



#### Un contrat SLA à 99,9 %

**Type :** Fonctionnalités de base


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | ![Vérification][12]| ![Vérification][12]| ![Vérification][12]|

**Détails supplémentaires :**

- [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/)




## Fonctionnalités Premium
#### Gestion d’applications et de groupes libre-service/ajout d’applications libre-service/groupes dynamiques

**Type :** Fonctionnalités Premium


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | | ![Vérification][12]| |




         
#### Réinitialisation, modification et déverrouillage de mot de passe libre-service avec écriture différée locale

**Type :** Fonctionnalités Premium


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | | ![Vérification][12]| |





#### Multi-Factor Authentication (dans le cloud et localement - MFA Server)

**Type :** Fonctionnalités Premium


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | | ![Vérification][12]| Limité au cloud uniquement pour les applications Office 365|

**Détails supplémentaires :**

- [Présentation d'Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)



#### Licence d’accès client MIM + serveur MIM 

Les droits du logiciel Microsoft Identity Manager Server sont octroyés avec les licences Windows Server (toutes les éditions). Étant donné que Microsoft Identity Manager s’exécute sur un système d’exploitation Windows Server, tant que le serveur exécute une copie valide et sous licence de Windows Server, Microsoft Identity Manager peut être installé et utilisé sur ce serveur. Aucune autre licence séparée n’est nécessaire pour Microsoft Identity Manager Server.

**Type :** Fonctionnalités Premium


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | | ![Vérification][12]| |





#### Cloud App Discovery 

**Type :** Fonctionnalités Premium


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | | ![Vérification][12]| |

**Détails supplémentaires :**

- [Détection des applications cloud non gérées avec Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)



#### Connect Health

**Type :** Fonctionnalités Premium


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | | ![Vérification][12]| |

**Détails supplémentaires :**

- [Surveillez votre infrastructure d’identité locale et vos services de synchronisation dans le cloud](active-directory-aadconnect-health.md)



#### Substitution automatique des mots de passe pour les comptes de groupe

**Type :** Fonctionnalités Premium


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | | ![Vérification][12]| |




## Azure Active Directory Join - Fonctionnalités propres à Windows 10
#### Joindre un appareil à Azure AD, Desktop SSO, Microsoft Passport pour Azure AD, récupération Bitlocker de l’administrateur

**Type :** Azure Active Directory Join - Fonctionnalités propres à Windows 10


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| ![Vérification][12]| ![Vérification][12]| ![Vérification][12]| ![Vérification][12]|




#### Inscription automatique GPM, récupération Bitlocker libre-service, administrateurs locaux supplémentaires pour appareils Windows 10 par le biais d’Azure AD Join

**Type :** Azure Active Directory Join - Fonctionnalités propres à Windows 10


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | | ![Vérification][12]| |


#### Enterprise State Roaming

**Type :** Azure Active Directory Join - Fonctionnalités propres à Windows 10


**Disponibilité :**

| Édition gratuite| Édition de base| Édition Premium| Applications Office 365 uniquement |
| :-: | :-: | :-: | :-: |
| | | ![Vérification][12]| |

**Détails supplémentaires :**

- [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)


## Fonctionnalités préliminaires d’Azure AD
Outre les fonctionnalités généralement disponibles des éditions gratuite, de base et Premium, Azure AD vous offre également un ensemble de fonctionnalités préliminaires. Vous pouvez les utiliser pour avoir un aperçu des nouveautés à venir dans un avenir proche et déterminer si ces fonctionnalités peuvent vous aider à améliorer votre environnement.

**Fonctionnalités préliminaires disponibles :**

- [B2B Collaboration](active-directory-b2b-collaboration-overview.md)
- [Unités administratives](active-directory-administrative-units-management.md)
- Privileged Identity Management
- [Intégration d’applications HR](active-directory-saas-workday-inbound-tutorial.md)
- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
- [Authentification par certificat sur iOS](active-directory-certificate-based-authentication-ios.md)
- [Authentification par certificat sur Android](active-directory-certificate-based-authentication-android.md)
 





## Étapes suivantes

- [Prise en main d’Azure Active Directory Premium (AD)](active-directory-get-started-premium.md)
- [Ajout d’une marque de société aux pages de connexion et du volet d’accès](active-directory-add-company-branding.md)
- [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md)

<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

<!---HONumber=AcomDC_0713_2016-->