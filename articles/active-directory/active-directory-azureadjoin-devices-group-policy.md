---
title: "Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10 | Microsoft Docs"
description: "Explique comment les administrateurs peuvent configurer une stratégie de groupe pour permettre à des appareils d’être joints à un domaine du réseau d’entreprise."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 2ff29f3e-5325-4f43-9baa-6ae8d6bad3e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 39bd4fcbe1b9a197bcf5b8bb33bf6fffae2063fc
ms.lasthandoff: 03/11/2017


---
# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10
La jonction de domaine est la méthode que les entreprises utilisent traditionnellement depuis une quinzaine d’années ou plus pour connecter leurs appareils professionnels. Elle permet aux utilisateurs de se connecter à leurs appareils avec leur compte professionnel ou scolaire Windows Server Active Directory et au service informatique de gérer intégralement ces appareils. En règle générale, les entreprises s'appuient sur des méthodes de création d'images pour mettre des appareils à la disposition des utilisateurs et utilisent System Center Configuration Manager (SCCM) ou une stratégie de groupe pour les gérer.


La jonction de domaine dans Windows 10 vous offre les avantages suivants après la connexion des appareils à Azure Active Directory (Azure AD) :

* authentification unique (SSO) aux ressources Azure AD depuis n’importe où ;
* accès au Windows Store pour entreprises à l’aide de comptes professionnels ou scolaires (aucun compte Microsoft requis) ;
* itinérance des paramètres utilisateur compatible pour l’entreprise sur tous les appareils avec un compte professionnel ou scolaire (aucun compte Microsoft requis) ;
* authentification forte et connexion pratique pour les comptes professionnels ou scolaires avec Windows Hello Entreprise et Windows Hello ;
* possibilité de restreindre l’accès aux appareils conformes aux paramètres de stratégie de groupe des appareils de l’organisation.

## <a name="prerequisites"></a>Composants requis
La jonction de domaine demeure utile. Toutefois, pour bénéficier des avantages d’Azure AD pour l’authentification unique, de l’itinérance des paramètres avec un compte professionnel ou scolaire et de l’accès au Windows Store avec un compte professionnel ou scolaire, vous aurez besoin des éléments suivants :

* Abonnement Azure AD
* Azure AD Connect pour étendre l’annuaire local à Azure AD
* Stratégie définie pour connecter des appareils joints au domaine à Azure AD
* Version de Windows 10 (version 10551 ou ultérieure) pour les appareils

Pour activer Windows Hello Entreprise et Windows Hello, vous aurez également besoin des éléments suivants :

- **Infrastructure de clé publique (PKI)** pour l’émission de certificats utilisateur.

- **System Center Configuration Manager Current Branch** - Vous devez installer la version 1606 ou une version ultérieure.  
Pour plus d'informations, consultez les pages suivantes : 
    - [Documentation de System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx)
    - [Blog de l’équipe System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)
    - [Les paramètres Windows Hello Entreprise dans System Center Configuration Manager](https://docs.microsoft.com/sccm/protect/deploy-use/windows-hello-for-business-settings)

En alternative à l'exigence de déploiement d'infrastructure à clé publique, vous pouvez procéder comme suit :

* Avoir quelques contrôleurs de domaine avec les services de domaine Active Directory Windows Server 2016.

Pour permettre un accès conditionnel, vous pouvez créer des paramètres de stratégie de groupe qui autorisent l’accès aux appareils joints au domaine sans déploiements supplémentaires. Pour gérer le contrôle d’accès basé sur la conformité de l’appareil, vous aurez besoin des éléments suivants :

* System Center Configuration Manager Current Branch (version&1606; ou ultérieure) pour les scénarios Windows Hello Entreprise

## <a name="deployment-instructions"></a>Instructions de déploiement

Pour effectuer le déploiement, procédez comme indiqué dans [Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="next-step"></a>Étape suivante
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)


