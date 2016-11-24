---
title: 'Azure MFA : cloud ou serveur | Microsoft Docs'
description: "Choisissez la solution de sécurité d’authentification multifacteur la plus appropriée pour vous en définissant les éléments à protéger et l’emplacement des utilisateurs.  Choisissez ensuite le cloud, le serveur MFA ou AD FS."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: ec2270ea-13d7-4ebc-8a00-fa75ce6c746d
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: b160a4f4ad4d1e383b460a3d17078908da1f4ef1
ms.openlocfilehash: bcf8a709b9c457f517386b4a4baf9fdf5864cc65


---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Choisissez la solution Azure Multi-Factor Authentication qui vous convient
Étant donné qu’il existe plusieurs types d’Azure Multi-Factor Authentication (MFA), nous devons répondre à quelques questions pour connaître la version utiliser.  Ces questions sont les suivantes :

* [les éléments à protéger](#what-am-i-trying-to-secure)
* [l’emplacement des utilisateurs](#where-are-the-users-located)
* [quelles sont les fonctionnalités nécessaires ?](#what-featured-do-i-need)

Les sections suivantes fournissent des conseils sur la détermination de ces réponses.

## <a name="what-am-i-trying-to-secure"></a>Les éléments à protéger.
Afin de déterminer la solution de vérification en deux étapes appropriée, nous devons tout d’abord connaître les éléments à protéger avec une seconde méthode d’authentification.  S’agit-il d’une application dans Azure ?  Ou d’un système d’accès à distance ?  En déterminant les éléments à protéger, nous pouvons répondre à la question de savoir où l’authentification multifacteur doit être activée.  

| Les éléments que vous souhaitez protéger | MFA dans le cloud | Serveur MFA |
| --- |:---:|:---:|
| Applications Microsoft internes |● |● |
| Applications SaaS dans la galerie d’applications |● |● |
| Applications IIS publiées via le proxy d'application Azure AD |● |● |
| Applications IIS non publiées via le proxy d'application Azure AD | |● |
| Accès à distance comme VPN, RDG | |● |

## <a name="where-are-the-users-located"></a>l’emplacement des utilisateurs
Ensuite, selon l’emplacement de nos utilisateurs, nous pouvons déterminer la solution appropriée à utiliser, que ce soit dans le cloud ou en local avec le serveur MFA.

| Emplacement de l'utilisateur | MFA dans le cloud | Serveur MFA |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD et AD local à l'aide de la fédération avec AD FS |● |● |
| Azure AD et AD local à l'aide de DirSync, Azure AD Sync, Azure Connect AD : aucune synchronisation de mot de passe |● |● |
| Azure AD et AD local à l'aide de DirSync, Azure AD Sync, Azure Connect AD : avec synchronisation de mot de passe |● | |
| Active Directory local | |● |

## <a name="what-features-do-i-need"></a>Quelles sont les fonctionnalités nécessaires ?
Le tableau suivant présente une comparaison des fonctionnalités disponibles de Multi-Factor Authentication dans le cloud et du serveur Multi-Factor Authentication.

| Fonctionnalité | MFA dans le cloud | Serveur MFA |
| --- |:---:|:---:|
| Notification d'application mobile comme second facteur | ● | ● |
| Code de vérification d'application mobile comme second facteur | ● | ● |
| Appel téléphonique comme second facteur | ● | ● |
| SMS unidirectionnel comme second facteur | ● | ● |
| SMS bidirectionnel comme second facteur | | ● |
| Jetons matériels comme second facteur | | ● |
| Mots de passe d'application pour les clients qui ne prennent pas en charge MFA | ● | |
| Contrôle d'administration sur les méthodes d'authentification | | ● |
| Mode du code PIN | | ● |
| Alerte de fraude |● | ● |
| Rapports MFA |● | ● |
| Contournement à usage unique | ● | ● |
| Messages de bienvenue personnalisés pour les appels téléphoniques | ● | ● |
| ID d'appelant personnalisable pour les appels téléphoniques | ● | ● |
| Adresses IP approuvées | ● | ● |
| Mémoriser MFA pour les appareils fiables | ● | |
| Accès conditionnel | ● | ● |
| Cache | ● | ● |

Maintenant que nous avons déterminé s'il faut utiliser l'authentification multifacteur de cloud ou le serveur MFA local, nous pouvons commencer à configurer et à utiliser Azure Multi-Factor Authentication. **Sélectionnez l'icône qui représente votre scénario !**

<center>




[![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Vérification](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>



<!--HONumber=Nov16_HO3-->


