---
title: "Qu’est-ce qu’Azure Active Directory ?"
description: "Azure Active Directory permet d&quot;étendre vos identités locales existantes dans le cloud ou de développer des applications intégrées Azure AD."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: b247388bff35fb755c8363eb48695e47f2510574
ms.openlocfilehash: a5f8adfa278b096914239357c675654fd9fc2908


---
# <a name="what-is-azure-active-directory"></a>Qu’est-ce qu’Azure Active Directory ?
Azure Active Directory (Azure AD) est le service Microsoft de gestion des annuaires et des identités basé sur le cloud mutualisé.

Azure AD fournit une solution économique pour donner aux employés et aux partenaires commerciaux un accès par authentification unique à [des milliers d’Applications SaaS de cloud](http://blogs.technet.com/b/ad/archive/2014/09/03/50-saas-apps-now-support-federation-with-azure-ad.aspx) , comme Office 365, Salesforce.com, DropBox et Concur.

Azure AD permet également aux développeurs d’applications de se concentrer sur le développement de leur application en simplifiant et en accélérant son intégration dans une solution de gestion des identités de classe mondiale utilisée par des millions d’organisations dans le monde.

Azure AD inclut également une suite complète de fonctionnalités de gestion d’identité, comme l’authentification multifacteur, l’inscription d’appareil, la gestion de mot de passe libre-service, la gestion de groupes libre-service, la gestion des comptes privilégiés, le contrôle d’accès en fonction du rôle, la surveillance de l’utilisation de l’application, ainsi que la création d’audits complets, la surveillance de la sécurité et la création d’alertes. Ces fonctions permettent d’améliorer la sécurité des applications basées sur le cloud, de simplifier les processus informatiques, de réduire les coûts et de garantir le respect des objectifs de conformité aux normes du secteur.

De plus, en [quatre clics](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx), Azure AD peut être intégré à une instance existante de Windows Server Active Directory, ce qui offre aux entreprises la possibilité d’exploiter leurs investissements d’identité locale pour gérer l’accès aux applications SaaS basées sur le cloud.

Si vous êtes un client Office 365, Azure ou Dynamics CRM Online, il est possible que vous ne réalisiez pas que vous utilisez déjà Azure AD. Chaque client Office 365, Azure et Dynamics CRM est déjà un client Azure AD. Chaque fois que vous le souhaitez, vous pouvez utiliser ce client pour gérer l’accès aux milliers d’autres applications de cloud avec lesquelles s’intègre Azure AD !

![Pile Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>Quelle est la fiabilité d’Azure AD ?
La conception d’Azure AD pour l’architecture mutualisée géolocalisée à haute disponibilité signifie que vous pouvez vous reposer sur cette solution pour répondre à vos besoins les plus critiques. L’exécution de 28 centres de données dans le monde entier avec basculement automatisé vous offre le confort de savoir qu’Azure AD est hautement disponible et que, même si un centre de données tombe en panne, des copies de vos données d’annuaire sont disponibles dans des centres de données situés dans au moins deux régions, pour un accès instantané.

Pour plus d’informations, consultez la rubrique [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).

## <a name="what-are-the-benefits-of-azure-ad"></a>Quels sont les avantages d’Azure AD ?
Votre organisation peut utiliser Azure AD pour améliorer la productivité de ses employés, rationaliser les processus informatiques, améliorer la sécurité et réduire les coûts de différentes façons :

* En adoptant rapidement les services cloud, en fournissant aux employés et aux partenaires une expérience d’authentification unique simple basée sur la gestion des accès aux applications SaaS ainsi qu’aux fonctionnalités de services d’approvisionnement complètement automatisées.
* En permettant aux employés d’accéder à des applications cloud et des fonctionnalités libre-service de classe mondiale où qu’ils soient et sur les appareils de leur choix.
* En gérant facilement et en toute sécurité l’accès employé et fournisseur à vos comptes de réseaux sociaux d’entreprise.
* En améliorant la sécurité des applications grâce à l’authentification multifacteur et l’accès conditionnel Azure AD.
* En implémentant une gestion des accès aux applications cohérente et en libre-service, pour permettre aux responsables d’agir rapidement tout en réduisant les coûts informatiques et les frais généraux.
* En surveillant l’utilisation des applications et en protégeant votre entreprise contre les menaces avancées, grâce à des fonctionnalités de création de rapports et de surveillance.
* En fournissant un accès mobile (distant) aux applications locales.

## <a name="how-does-azure-ad-compare-to-on-premises-active-directory-domain-services-ad-ds"></a>Quelles sont les différences entre Azure AD et Active Directory Domain Services local (AD DS) ?

Azure Active Directory (Azure AD) et Active Directory local (Active Directory Domain Services ou AD DS) sont tous deux des systèmes qui stockent des données d’annuaire et gèrent les communications entre les utilisateurs et les ressources, notamment les processus d’ouverture de session, d’authentification des utilisateurs et de recherche dans l’annuaire.

AD DS est un rôle serveur sur Windows Server, ce qui signifie qu’il peut être déployé sur des ordinateurs physiques ou des machines virtuelles. Il a une structure hiérarchique basée sur X.500. Il utilise le système DNS pour la localisation d’objets, peut être contacté par le biais du protocole LDAP et utilise principalement Kerberos pour l’authentification. Active Directory admet les unités d’organisation (UO) et les objets de stratégie de groupe (GPO) en plus de joindre des ordinateurs au domaine ; les approbations sont créées entre domaines.

Azure AD est un service d’annuaire public multiclient, ce qui signifie que vous pouvez créer un locataire pour vos applications comme Office 365 et vos serveurs cloud dans Azure AD. Les utilisateurs et les groupes sont créés selon une structure plate sans unités d’organisation ou objets de stratégie de groupe. L’authentification s’effectue suivant des protocoles tels que SAML, WS-Federation et OAuth. Il est possible d’interroger Azure AD mais, au lieu d’utiliser LDAP, vous devez utiliser une API REST nommée API Graph AD. Toutes fonctionnent sur HTTP et HTTPS.

Vous pouvez utiliser Azure AD Connect pour synchroniser vos identités locales avec Azure AD.

---

### <a name="authentication-and-authorization-details"></a>Détails sur l’authentification et l’autorisation

#### <a name="azure-ad"></a>Azure AD
`SAML`, `WS-Federation`, interactif avec les identifiants pris en charge, OAuth 2.0, OpenID Connect 

#### <a name="on-premises-ad-ds"></a>AD DS local
`SAML`, `WS-Federation`, NTLM, Kerberos, MD5, De base

---

### <a name="object-repository-details"></a>Détails sur le référentiel d’objets

#### <a name="azure-ad"></a>Azure AD
Accès par Azure AD Graph et Microsoft Graph 

#### <a name="on-premises-ad-ds"></a>AD DS local
X.500 LDAP  

---


### <a name="programmatic-access-details"></a>Détails de l’accès par programme

#### <a name="azure-ad"></a>Azure AD
API REST MS/Azure AD Graph 

#### <a name="on-premises-ad-ds"></a>AD DS local
LDAP 

---

### <a name="sso-to-applications-details"></a>Détails de l’authentification unique aux applications

#### <a name="azure-ad"></a>Azure AD
`OpenID Connect`, `SAML` 

#### <a name="on-premises-ad-ds"></a>AD DS local
`Open-ID Connect`, `SAML`, WS-Fed 

---

### <a name="access-management-details"></a>Détails de la gestion des accès

#### <a name="azure-ad"></a>Azure AD
Étendue définie par les ressources et contrôle d’accès en fonction du rôle, autorisations déléguées et d’application définies par le client, infrastructure de consentement (impose le consentement de l’utilisateur / l’administrateur approprié, comme défini / demandé par la ressource / le client) 

Par le biais du rôle d’application, peut être appliqué individuellement ou par groupes, prend en charge : géré par un administrateur, accès aux applications en libre-service, consentement de l’utilisateur

#### <a name="on-premises-ad-ds"></a>AD DS local
Par le biais des listes de contrôle d’accès, peut être appliqué individuellement ou par groupes, prend en charge : géré par un administrateur 

---

### <a name="group-management-details"></a>Détails de la gestion des groupes

#### <a name="azure-ad"></a>Azure AD
`Admin managed`, gestion par règles / dynamique, gestion des groupes en libre-service 

#### <a name="on-premises-ad-ds"></a>AD DS local
`Admin managed`, système externe (FIM ou autre) nécessaire pour Gestion par règles / dynamique

---

### <a name="supported-credentials-details"></a>Détails des informations d’identification prises en charge

#### <a name="azure-ad"></a>Azure AD
`Username + password`, `Smartcard` 

#### <a name="on-premises-ad-ds"></a>AD DS local
`Username + password`, `Smartcard` 
 
---







## <a name="how-can-i-get-started"></a>Comment faire pour démarrer ?

**Si vous êtes un administrateur informatique :**

* [Faites un essai.](https://azure.microsoft.com/trial/get-started-active-directory/) Vous pouvez vous inscrire pour une période d’essai gratuit de 30 jours, puis déployer votre première solution cloud en moins de 5 minutes.

* Lisez la page « Prise en main d’Azure Active Directory » pour accélérer l’installation et la configuration d’un client Azure AD.

**Si vous êtes un développeur :**
 
* Consultez notre [Guide pour les développeurs](active-directory-developers-guide.md) pour Azure Active Directory

* [Démarrer une version d’évaluation](https://azure.microsoft.com/trial/get-started-active-directory/) : abonnez-vous à un essai gratuit pendant 30 jours dès aujourd’hui et commencez à intégrer vos applications à Azure AD

## <a name="where-can-i-learn-more"></a>Où en savoir plus ?
Nous pouvons vous proposer une multitude de ressources en ligne très utiles pour vous aider à tout savoir sur Azure AD. Voici une liste d’articles complets pour vous aider à démarrer :

* [Activation de votre annuaire pour la gestion hybride avec Azure AD Connect](active-directory-aadconnect.md)
* [Sécurité supplémentaire pour un monde toujours connecté](../multi-factor-authentication/multi-factor-authentication.md)
* [Automatisation de l’approvisionnement et de la l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Prise en main de la création de rapports Azure AD](active-directory-reporting-getting-started.md)
* [Gestion de vos mots de passe en tout lieu](active-directory-passwords.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Automatisation de l’approvisionnement et de la l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md)
* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Qu’est-ce que la gestion des licences Microsoft Azure Active Directory ?](active-directory-licensing-what-is.md)
* [Comment puis-je détecter les applications cloud non approuvées utilisées au sein de mon organisation ?](active-directory-cloudappdiscovery-whatis.md)



<!--HONumber=Dec16_HO3-->


