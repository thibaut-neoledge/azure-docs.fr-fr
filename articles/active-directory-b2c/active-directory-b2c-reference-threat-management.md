---
title: Gestion des menaces pour Azure B2C | Microsoft Docs
description: "Techniques de détection et d’atténuation des attaques par déni de service et de mot de passe dans Azure B2C."
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: Ajith Alexander
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5cca5fcd445e70b4eab9197258bb783e20016f76
ms.lasthandoff: 03/31/2017


---
# <a name="azure-ad-b2c-threat-management"></a>Azure AD B2C : Gestion des menaces
La Gestion des menaces inclut une protection contre les attaques des systèmes et des réseaux. Une attaque par déni de service (DoS) peut affecter la disponibilité de la ressource et rendre celle-ci indisponible pour les utilisateurs auxquels elle est destinée. Une attaque de mot de passe peut mener à un accès non autorisé aux ressources. Microsoft Azure Active Directory B2C dispose de fonctionnalités intégrées pour protéger vos données contre ces menaces de plusieurs façons. 

## <a name="denial-of-service-attack"></a>Attaque par déni de service

Azure AD B2C utilise des techniques de détection et d’atténuation telles que les cookies SYN ainsi que les limites de débit et de connexion pour protéger les ressources sous-jacentes contre ces attaques.  

## <a name="password-attacks"></a>Attaques de mot de passe

Azure AD B2C dispose également de dispositifs d’atténuation pour contrer les attaques de mot de passe.  Cette technique couvre tant les attaques de mot de passe par force brute que les attaques de mot de passe basées sur un dictionnaire.  Les mots de passe définis par les utilisateurs doivent être d’une complexité raisonnable.  Azure AD B2C analyse l’intégrité des demandes afin de différencier intelligemment, à l’aide de différents signaux, les utilisateurs prévus des pirates et autres botnets. B2C offre une stratégie sophistiquée de verrouillage des comptes basée sur les mots de passe entrés et sur la probabilité d’attaque.

[Informations supplémentaires sur la Gestion des menaces de Microsoft](https://www.microsoft.com/trustcenter/security/threatmanagement)
