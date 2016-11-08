---
title: Utilisation d’appareils Windows 10 sur votre lieu de travail | Microsoft Docs
description: Fournit une vue d’ensemble des fonctionnalités offertes aux utilisateurs et services informatiques, en mettant en contraste les différentes manières dont un appareil peut être approvisionné et utilisé dans une entreprise dotée de Windows 10.
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''
tags: azure-classic-portal

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila

---
# <a name="using-windows-10-devices-in-your-workplace"></a>Utilisation d’appareils Windows 10 sur votre lieu de travail
S’applique à : PC Windows 10

Windows 10 propose pour les entreprises trois modèles permettant aux utilisateurs d’accéder à leurs ressources de travail de manière sécurisée et pratique.

* **Azure Active Directory Join** (Azure AD Join), pour les employés qui accèdent essentiellement à des ressources hébergées dans le cloud (par exemple, Office 365). Azure AD Join est un nouveau service Windows 10 qui offre une expérience d’approvisionnement de ressources de travail en libre-service.
* La **jonction de domaine**, pour les organisations qui ont investi dans des applications et des ressources en local. La jonction de domaine offre aux utilisateurs de Windows 10 qui se connectent à Azure AD une expérience améliorée.
* **Une nouvelle expérience BYOD simplifiée**, pour les utilisateurs qui souhaitent ajouter un compte professionnel ou scolaire à Windows afin d’accéder facilement à leurs ressources sur des appareils personnels.

Le tableau suivant présente un aperçu des fonctionnalités offertes aux utilisateurs et administrateurs informatiques, en mettant en contraste les différentes façons dont un appareil peut être approvisionné et utilisé dans une entreprise dotée de Windows 10 :

|  | jonction de domaine | Azure AD Join | Appareil personnel |
| --- | --- | --- | --- |
| Connexion d’appareils Windows pour les comptes professionnels ou scolaires. |Oui |Oui |Non |
| Authentification unique (SSO) de l’utilisateur aux applications Office 365 et Azure AD. L’authentification unique permet de se connecter une seule fois pour accéder aux ressources de l’organisation. |Oui |Oui |Oui |
| Authentification unique de l’utilisateur sur les applications Kerberos/NTLM. |Oui |Limité |Oui, via VPN |
| Authentification forte et connexion pratique pour les comptes professionnels ou scolaires avec Microsoft Passport et Windows Hello. |Oui |Oui |Oui |
| Accès au Windows Store pour entreprises à l’aide d’un compte professionnel ou scolaire (aucun compte Microsoft). |Oui |Oui |Oui |
| Itinérance des paramètres utilisateur compatible pour l’entreprise sur tous les appareils en utilisant un compte professionnel ou scolaire. |Oui |Oui |Oui |
| Possibilité de restreindre l’accès aux applications professionnelles aux seuls appareils conformes aux stratégies de l’organisation en matière de gestion des appareils. |Oui |Oui |Oui |
| Approvisionnement d’appareils en libre-service par l’utilisateur « pour travailler n’importe où ». |Non |Oui |Oui |
| Possibilité de gérer des appareils. |Oui, via la stratégie de groupe/SCCM |Oui |Oui |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>Utilisation d’appareils professionnels avec Azure AD Join et la jonction de domaine dans Windows 10
Windows 10 propose deux façons d’utiliser les appareils professionnels pour accéder aux ressources de travail :

* Azure AD Join
* Jonction de domaine
  
  Ces deux options peuvent être pertinentes en fonction des besoins et exigences de l’organisation. Dans certains cas, les entreprises peuvent trouver pertinent de mettre en place ces deux méthodes de déploiement.

## <a name="when-to-use-azure-active-directory-join"></a>Quand utiliser Azure Active Directory Join ?
Azure AD Join est une nouvelle expérience d’approvisionnement de ressources de travail en libre-service dans Windows 10.  Elle s’adresse aux professionnels qui accèdent essentiellement à des ressources de travail hébergées dans le cloud, comme Office 365. Il s’agit d’une méthode légère permettant de configurer des ordinateurs, des tablettes et des téléphones pour l’entreprise. Les appareils sont gérés via la gestion des périphériques mobiles (GPM), avec des contrôles homogènes entre les différentes plateformes Windows.

**Voici plusieurs raisons d’utiliser Azure AD Join**:

* Vous souhaitez permettre l’approvisionnement en libre-service d’appareils pour les professionnels qui sont souvent amenés à se déplacer.
* Vous fournissez aux utilisateurs des appareils professionnels, comme des tablettes et téléphones.
* Vous souhaitez gérer un ensemble d’utilisateurs dans Azure AD, au lieu d’Active Directory, par exemple pour les travailleurs saisonniers, sous-traitants ou étudiants.
* Vous souhaitez fournir des fonctionnalités de jonction aux travailleurs dans des succursales distantes avec une infrastructure locale limitée.
* Vous ne disposez pas d’instance Active Directory en local.

Certaines entreprises utilisent Azure AD Join comme principale méthode de déploiement des appareils professionnels, notamment lorsqu’ils migrent toutes leurs ressources ou la plupart de leurs ressources dans le cloud. Les organisations hybrides, qui disposent à la fois d’Active Directory et d’Azure AD, peuvent également choisir de déployer une méthode ou l’autre en fonction de l’utilisateur ou du département.

Les circonscriptions scolaires et les universités, par exemple, peuvent utiliser Active Directory pour la gestion du personnel et Azure AD pour la gestion des étudiants. Certaines entreprises peuvent souhaiter gérer leurs bureaux distants ou leurs services commerciaux dans Azure AD. Les deux méthodes (Azure AD Join et jonction de domaine) peuvent être utilisées au sein d’une organisation hybride. Azure AD Join peut être un bon complément de la jonction de domaine dans le cas de déploiement d’appareils dans un environnement de travail.

**Si ses ressources d’entreprise sont essentiellement accessibles via le cloud, votre organisation peut bénéficier d’avantages supplémentaires si**:

* vous pouvez supprimer des dépendances sur une infrastructure d’identité locale ;
* vous pouvez simplifier votre modèle de déploiement d’appareils en remplaçant les solutions de création d’images par une configuration en libre-service ;
* vous pouvez utiliser la gestion des périphériques mobiles pour gérer tous vos appareils sur différentes plateformes.

Pour plus d’informations sur Azure AD Join, consultez [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-overview.md).

## <a name="when-to-use-domain-join-(or-keep-using-it)"></a>Quand utiliser la jonction de domaine (ou continuer à l’utiliser) ?
Depuis 15 ans, de nombreuses organisations utilisent la jonction de domaine pour connecter leurs appareils de travail. La jonction de domaine permet aux utilisateurs de se connecter à leurs appareils à partir de leur compte Active Directory professionnel ou scolaire. Elle également aux départements informatiques de gérer pleinement ces appareils de manière centralisée. En règle générale, les organisations s’appuient sur des méthodes de création d’images pour approvisionner des appareils et utilisent souvent System Center Configuration Manager (SCCM) ou les stratégies de groupe pour les gérer.

**Les raisons suivantes peuvent vous encourager à utiliser (ou continuer d’utiliser) la jonction de domaine dans votre entreprise**:

* Vous disposez d’applications Win32 déployées sur ces appareils qui utilisent NTLM/Kerberos.
* Vous avez besoin de stratégies de groupe ou de SCCM/DCM pour gérer les appareils.
* Vous souhaitez continuer à utiliser des solutions de création d’images pour configurer les appareils de vos employés.

**La jonction de domaine dans Windows 10 vous offre également les avantages suivants lors de votre connexion à Azure AD**:

* authentification forte liée à l’appareil et connexion pratique pour les comptes professionnels ou scolaires avec Microsoft Passport et Windows Hello ;
* accès au Windows Store d’entreprise pour les appareils qui utilisent des comptes professionnels ou scolaires (aucun compte Microsoft requis) ;
* itinérance des paramètres utilisateur compatible pour l’entreprise sur tous les appareils qui utilisent un compte professionnel ou scolaire (aucun compte Microsoft requis) ;
* Possibilité de restreindre l’accès aux applications professionnelles aux seuls appareils conformes aux stratégies de l’organisation en matière de gestion des appareils.

Pour plus d’informations sur Azure AD Join, consultez [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-overview.md).

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>Activation de la jonction d’appareils personnels pour un usage professionnel ou scolaire
Pour prendre en charge le BYOD dans l’entreprise, Windows 10 offre à l’utilisateur la possibilité d’ajouter un compte professionnel ou scolaire sur son ordinateur, sa tablette ou son téléphone. Une fois le compte professionnel ou scolaire ajouté, l’appareil est automatiquement inscrit auprès d’Azure AD, voire éventuellement dans le système de gestion de périphériques mobiles que l’organisation a configuré. Le répertoire affiche ces appareils comme « Inscrits », par opposition à « Joints à Azure AD ». Les administrateurs informatiques peuvent appliquer différentes stratégies en fonction de ces informations, en allégeant éventuellement les stratégies appliquées sur un appareil personnel, par rapport à celles appliquées sur un appareil professionnel.

Les utilisateurs peuvent ajouter très facilement un compte professionnel ou scolaire à leur appareil personnel. Cette procédure peut s’effectuer lors du premier accès à une application de travail, avec également la possibilité le faire manuellement via le menu Paramètres. Ce compte fournit une authentification unique aux ressources de l’organisation.

Pour plus d’informations sur Azure AD Join, consultez [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## <a name="enable-domain-join-or-azure-ad-join"></a>Activation de la jonction de domaine ou d’Azure AD Join
L’expérience utilisateur de Windows 10 offre des points d’entrée pour chacune des méthodes décrites précédemment (jonction de domaine, Azure AD Join et ajout d’un compte professionnel ou scolaire). Toutes ces méthodes supposent toutefois de faire appel à un administrateur informatique afin d’activer la fonctionnalité dans l’infrastructure au préalable.

## <a name="requirements-for-deploying-azure-ad-join"></a>Configuration requise pour le déploiement d’Azure AD Join
Pour déployer Azure AD joindre pour un ensemble d’utilisateurs, vous avez besoin des éléments suivants :

* Un abonnement Azure AD.
* Pour accéder à davantage de fonctionnalités, vous avez besoin d’un abonnement Azure AD Premium, par exemple l’inscription automatique au système de gestion des appareils mobiles.
* Un système de gestion des appareils mobiles, par exemple, un abonnement Microsoft Intune, un système de gestion des appareils mobiles pour Office 365 ou un système offert par l’un des fournisseurs de gestion d’appareils mobiles partenaires et qui soit intégré à Azure AD (consultez la section [Forum Aux Questions](#frequently-asked-questions) à la fin de cet article pour plus d’informations).

Dans le cas d’installations hybrides, il est vivement recommandé de déployer Azure AD Connect pour étendre le répertoire local vers Azure AD.

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>Configuration requise pour l’utilisation de la jonction de domaine avec Azure AD
La jonction de domaine conserve le même principe de fonctionnement. Pour tirer pleinement parti des avantages d’Azure AD, vous aurez toutefois besoin des éléments suivants :

* Un abonnement Azure AD.
* Un déploiement d’Azure AD Connect pour étendre l’annuaire local à Azure AD.
* Une stratégie autorisant l’accès aux appareils joints à un domaine, afin de bénéficier d’un accès conditionnel à Azure AD.
* Une stratégie qui autorise l’accès aux appareils joints à un domaine, si vous souhaitez être en mesure de limiter l’accès à certains appareils.
* System Center Configuration Manager version 1509 pour la version d’évaluation technique, afin d’activer les règles imposant l’utilisation d’appareils compatibles (voir la documentation TechNet et le billet de blog).

Pour plus d’informations sur la jonction de domaines dans Windows 10, consultez <link-to-DJ-in-Win10-deployment-guide>.

## <a name="requirements-for-using-byod-and-"add-a-work-or-school-account""></a>Conditions requises pour l’utilisation du BYOD et pour « l’ajout d’un compte professionnel ou scolaire »
Pour autoriser l’utilisation d’appareils personnels (autrement dit le BYOD ou « Bring Your Own Device) avec des comptes professionnels ou scolaires, vous avez besoin des éléments suivants :

* Un abonnement Azure AD.
* Pour accéder à davantage de fonctionnalités, vous avez besoin d’un abonnement Azure AD Premium, par exemple l’inscription automatique au système de gestion des appareils mobiles.

## <a name="requirements-for-using-microsoft-passport"></a>Conditions d’utilisation de Microsoft Passport
Pour activer Microsoft Passport, vous devez disposer des éléments suivants :

* Une infrastructure à clés publiques (PKI) pour la prise en charge de l’authentification basée sur le certificat à l’aide de Microsoft Passport.
* Un abonnement Intune pour la prise en charge de l’authentification basée sur le certificat à l’aide de Microsoft Passport pour Azure AD Join et les comptes professionnels ou scolaires.
* System Center Configuration Manager version 1509 pour la version d’évaluation technique (voir la documentation et le billet de blog TechNet) pour la prise en charge de l’authentification basée sur le certificat à l’aide de Microsoft Passport pour la jonction de domaine.
* Une stratégie d’activation de Microsoft Passport dans l’organisation.

Plutôt que de recourir à une infrastructure à clés publiques, vous pouvez activer une instance Microsoft Passport basée sur des clés en procédant comme suit :

* Déployez des contrôleurs de domaine Windows Server 2016 « Production Preview 1 » (pas besoin de niveau fonctionnel de domaine ou forêt ; deux contrôleurs de domaine pour la redondance desservant chaque site Active Directory suffisent).
* Définissez la stratégie pour activer Microsoft Passport dans l’organisation.

Pour plus d’informations sur Microsoft Passport et Windows Hello dans Windows 10, consultez <lien vers le document sur MS Passport et Windows Hello>.

## <a name="frequently-asked-questions"></a>Forum Aux Questions
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad?"></a>Quels sont les produits de gestion de périphériques mobiles partenaires intégrés à Azure AD ?
Les produits des fournisseurs suivants s’intègrent avec Azure AD pour l’inscription unifiée et des accès conditionnel dans Windows 10 :

* AirWatch par VMware
* Citrix Xenmobile
* Lightspeed Mobile Manager
* Gestion en local des périphériques mobiles SOTI

### <a name="what-about-workplace-join-in-windows-10?"></a>Qu’en est-il de la jonction d’espace de travail dans Windows 10 ?
La jonction d’espace de travail a été utilisée dans Windows 8.1 pour activer le BYOD. Dans Windows 10, le BYOD est activé via l’ajout d’un compte professionnel ou scolaire, comme expliqué précédemment dans ce document. Pour les organisations qui n’intègrent pas leur système de gestion des appareils mobiles à Azure AD, les utilisateurs peuvent inscrire manuellement l’appareil auprès du système de gestion via **Paramètres** > **Comptes** > **Accès professionnel**.

### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10?"></a>Les utilisateurs peuvent-ils connecter leur compte Microsoft à leur compte de domaine dans Windows 10 ?
Pas dans Windows 10. Dans Windows 8.1, les utilisateurs d’appareils joints à un domaine pouvaient « connecter » leur compte Microsoft (par exemple, Hotmail, Live, Outlook, XBox, etc.) à leur compte de domaine pour activer certaines expériences comme l’authentification unique sur les services Live, l’utilisation de Windows Store et l’itinérance des paramètres utilisateur entre les appareils. Dans Windows 10, la fonctionnalité de connexion de compte Microsoft a été retirée. L’utilisateur peut ajouter un ou plusieurs comptes Microsoft comme comptes supplémentaires pour activer l’authentification unique sur les services utilisateur tels que le Windows Store. Cette opération est effectuée dans **Paramètres** > **Comptes** > **Votre compte**.

Les utilisateurs procédant à une mise à niveau depuis des appareils Windows 8.1 avec une jonction de domaine et un compte Microsoft connecté verront automatiquement leur compte Microsoft connecté ajouté à la liste des comptes supplémentaires qu’ils utilisent.

## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!--HONumber=Oct16_HO2-->


