---
title: "Résolution des problèmes d’accès Azure Active Directory | Microsoft Docs"
description: "Découvrez les étapes permettant de résoudre les problèmes d’accès aux ressources en ligne de votre organisation."
services: active-directory
keywords: "accès conditionnel en fonction de l’appareil, inscription de l’appareil, activer l’inscription de l’appareil, inscription de l’appareil et GPM"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/11/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd2076f22c6048fda83d6da3b069e2805afb453f


---
# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Résolution des problèmes d’accès Azure Active Directory
Vous essayez d’accéder à l’intranet SharePoint Online de votre organisation, et vous obtenez un message d’erreur « accès refusé ». Que faire dans cette situation ?


Cet article décrit les étapes qui vous aideront à résoudre les problèmes d’accès aux ressources en ligne de votre organisation.

Pour résoudre les problèmes d’accès Azure Active Directory (Azure AD), accédez à la section de l’article qui traite de la plate-forme d’appareil :

* Appareil Windows
* Appareil iOS (revenez plus tard pour obtenir des instructions pour iPhone et iPad.)
* Appareil Android (revenez plus tard pour obtenir des instructions pour les téléphones et tablettes Android.)

## <a name="access-from-a-windows-device"></a>Accès à partir d’un appareil Windows
Si votre appareil exécute l’une des plate-formes suivantes, recherchez dans les sections suivantes le message d’erreur qui s’affiche lorsque vous essayez d’accéder à une application ou à un service :

* Windows 10
* Windows 8.1
* Windows 8
* Windows 7
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2

### <a name="device-is-not-registered"></a>L’appareil n’est pas inscrit
Si votre appareil n’est pas inscrit avec Azure AD et que votre application est protégée à l’aide d’une stratégie basée sur l’appareil, une page présentant les messages d’erreur suivants peut s’afficher :

![Messages d’accès refusé aux appareils non enregistrés](./media/active-directory-conditional-access-device-remediation/01.png "Scenario")

Si votre appareil appartient à un domaine lié à Active Directory dans votre organisation, procédez comme suit :

1. Vérifiez que vous êtes connecté à Windows à l’aide de votre compte professionnel (votre compte Active Directory).
2. Connectez-vous à votre réseau d’entreprise via un réseau privé virtuel (VPN) ou DirectAccess.
3. Une fois connecté, appuyez sur la touche Windows + la touche L pour verrouiller votre session Windows.
4. Saisissez les informations d’identification de votre compte de travail pour déverrouiller votre session Windows.
5. Attendez une minute, puis essayez d’accéder à nouveau à l’application ou au service.
6. Si la même page s’affiche, cliquez sur **More details (Plus d'informations)** puis contactez votre administrateur et fournissez les informations demandées.

Si votre appareil n’appartient pas à un domaine et exécute Windows 10, deux possibilités s’offrent à vous :

* Exécuter Azure AD Join
* Ajouter votre compte professionnel ou scolaire à Windows

Pour plus d’informations sur les différences entre ces options, consultez la section [Utilisation d’appareils Windows 10 sur votre lieu de travail](active-directory-azureadjoin-windows10-devices.md).

Pour exécuter Azure AD Join, procédez comme suit pour la plate-forme sur laquelle votre appareil s’exécute. (Azure AD Join n’est pas disponible sur les téléphones Windows.)

**Mise à jour Anniversaire de Windows 10**

1. Ouvrez l’application **Paramètres** .
2. Cliquez sur **Comptes** > **Access work or school** (Accès professionnel ou scolaire).
3. Cliquez sur **Connecter**.
4. Cliquez sur **Joindre cet appareil à Azure AD**.
5. Authentifiez-vous auprès de votre organisation, fournissez une authentification multifacteur si nécessaire, et suivez la procédure indiquée.
6. Déconnectez-vous puis reconnectez-vous à l’aide de votre compte professionnel.
7. Essayez d’accéder à nouveau à l’application.

**Mise à jour Windows 10 de novembre 2015**

1. Ouvrez l’application **Paramètres** .
2. Cliquez sur **Système** > **À propos de**.
3. Cliquez sur **Joindre Azure AD**.
4. Authentifiez-vous auprès de votre organisation, fournissez une authentification multifacteur si nécessaire, et suivez la procédure indiquée.
5. Déconnectez-vous et reconnectez-vous à l’aide de votre compte professionnel (votre compte Azure AD).
6. Essayez d’accéder à nouveau à l’application.

Pour ajouter votre compte professionnel ou scolaire, suivez la procédure suivante :

**Mise à jour Anniversaire de Windows 10**

1. Ouvrez l’application **Paramètres** .
2. Cliquez sur **Comptes** > **Access work or school** (Accès professionnel ou scolaire).
3. Cliquez sur **Connecter**.
4. Authentifiez-vous auprès de votre organisation, fournissez une authentification multifacteur si nécessaire, et suivez la procédure indiquée.
5. Essayez d’accéder à nouveau à l’application.

**Mise à jour Windows 10 de novembre 2015**

1. Ouvrez l’application **Paramètres** .
2. Cliquez sur **Comptes** > **Vos comptes**.
3. Cliquez sur **Ajouter un compte professionnel ou scolaire**.
4. Authentifiez-vous auprès de votre organisation, fournissez une authentification multifacteur si nécessaire, et suivez la procédure indiquée.
5. Essayez d’accéder à nouveau à l’application.

Si votre appareil n’est pas joint au domaine et exécute Windows 8.1, vous pouvez procéder à une jonction d’espace de travail et vous inscrire auprès de Microsoft Intune en appliquant la procédure suivante :

1. Ouvrez **Paramètres du PC**.
2. Cliquez sur **Réseau** > **Espace de travail**.
3. Cliquez sur **Joindre**.
4. Authentifiez-vous auprès de votre organisation, fournissez une authentification multifacteur si nécessaire, et suivez la procédure indiquée.
5. Cliquez sur **Activer**.
6. Essayez d’accéder à nouveau à l’application.

### <a name="browser-is-not-supported"></a>Le navigateur n’est pas pris en charge
L’accès peut vous être refusé si vous tentez d’accéder à une application ou à un service à l’aide d’un des navigateurs suivants :

* Chrome, Firefox ou navigateur autre que Microsoft Edge ou Microsoft Internet Explorer dans Windows 10 ou Windows Server 2016
* Firefox dans Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2

Vous verrez une page d’erreur semblable à celle-ci :

![Messages d’accès refusé aux navigateurs non pris en charge](./media/active-directory-conditional-access-device-remediation/02.png "Scenario")

La seule possibilité consiste à utiliser un navigateur pris en charge par l’application sur la plateforme de votre appareil.

## <a name="next-steps"></a>Étapes suivantes
[Accès conditionnel Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=Nov16_HO2-->


