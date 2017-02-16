---
title: "Authentification des identités sans mot de passe avec Microsoft Passport | Microsoft Docs"
description: "Fournit une vue d&quot;ensemble de Microsoft Passport et des informations supplémentaires sur le déploiement de Microsoft Passport."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: f907bb90-8776-46ca-9e12-279949af66ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: befad7c8b40792d93ddef44a8ce0c8deb4dfe8e4


---
# <a name="authenticating-identities-without-passwords-through-microsoft-passport"></a>Authentification des identités sans mot de passe avec Microsoft Passport
Les méthodes actuelles d’authentification avec un simple mot de passe ne sont pas suffisantes pour protéger les utilisateurs. Les utilisateurs réutilisent et oublient les mots de passe. Les mots de passe peuvent faire l’objet d’une violation, de tentatives de phishing, de piratage ou être faciles à deviner. Ils sont également difficiles à mémoriser et sujets aux attaques de type «[pass-the-hash](https://technet.microsoft.com/dn785092.aspx)».

## <a name="about-microsoft-passport"></a>À propos de Microsoft Passport
Microsoft Passport est une approche d’authentification par clé publique/privée ou basée sur les certificats pour les organisations et les consommateurs qui ne nécessite pas de passer par un mot de passe. Cette forme d’authentification s’appuie sur des informations d’identification constituées d’une paire de clés, qui peuvent remplacer les mots de passe et sont résistantes aux failles, aux vols et au phishing.

 Microsoft Passport permet aux utilisateurs de s’authentifier auprès d’un compte Microsoft, d’un compte Windows Server Active Directory, d’un compte Microsoft Azure Active Directory (AD) ou d’un service non-Microsoft qui prend en charge l’authentification FIDO (Fast ID Online). Après une vérification initiale en deux étapes lors de l’inscription à Microsoft Passport, Microsoft Passport est configuré sur l’appareil de l’utilisateur et l’utilisateur définit un mouvement, de type Windows Hello ou code confidentiel. L’utilisateur effectue le mouvement qui permet de vérifier son identité. Windows utilise ensuite Microsoft Passport pour authentifier l’utilisateur et l’aider à accéder aux ressources et services protégés.

La clé privée est accessible uniquement par le biais d’un mouvement utilisateur comme un code confidentiel, une empreinte biométrique ou un appareil distant (comme une carte à puce) dont l’utilisateur se sert pour se connecter à l’appareil. Cette information est liée à un certificat ou une paire de clés asymétrique. La clé privée est certifiée par le biais de matériel si l’appareil a une puce de module de plateforme sécurisée (TPM). La clé privée ne quitte jamais l’appareil.

La clé publique est inscrite auprès d’Azure Active Directory et de Windows Server Active Directory (en local). Les fournisseurs d’identité valident l’utilisateur en mappant sa clé publique à la clé privée et fournissent des informations de connexion via un mécanisme de notification tel que le mot de passe à usage unique ou Phonefactor, entre autres.

## <a name="why-enterprises-should-adopt-microsoft-passport"></a>Pourquoi les entreprises doivent-elles adopter Microsoft Passport ?
En activant Microsoft Passport, les entreprises peuvent sécuriser davantage leurs ressources comme suit :

* en configurant Microsoft Passport avec une option de matériel préféré, ce qui signifie que les clés sont générées sur TPM 1.2 ou TPM 2.0 quand ces derniers sont disponibles ou par logiciel lorsque TPM n’est pas disponible ; 
* en définissant la complexité et la longueur du code confidentiel et en indiquant si l’utilisation de Hello est activée dans votre organisation ;
* en configurant Microsoft Passport pour prendre en charge les scénarios de type carte à puce utilisant l’approbation par certificat.

## <a name="how-microsoft-passport-works"></a>Fonctionnement de Microsoft Passport
1. Les clés sont générées sur le matériel par TPM ou par logiciel. Un grand nombre d’appareils ont une puce TPM intégrée qui sécurise le matériel en intégrant des clés de chiffrement aux appareils. TPM 1.2 ou TPM 2.0 génère des clés ou des certificats qui sont issus des clés générées.
2. Le module de plateforme sécurisée certifie ces clés liées au matériel.
3. Un mouvement de déverrouillage unique déverrouille l’appareil. Ce mouvement permet l’accès à plusieurs ressources si l’appareil est joint au domaine ou à Azure AD.

## <a name="how-the-microsoft-passport-lifecycle-works"></a>Fonctionnement du cycle de vie Microsoft Passport
![Cycle de vie Microsoft Passport](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

Le schéma ci-dessus illustre la paire de clés publique/privée et la validation par le fournisseur d’identité. Chacune de ces étapes est décrite en détail ci-dessous :

1. L’utilisateur prouve son identité via plusieurs méthodes de vérification intégrées (mouvements, cartes à puce physiques, authentification multifacteur) et envoie ces informations au fournisseur d’identité comme Azure Active Directory ou Active Directory local.
2. Ensuite, l’appareil crée et certifie la clé, prend la partie publique de cette clé, l’associe aux instructions du poste de travail, se connecte et l’envoie au fournisseur d’identité pour que celui-ci inscrive cette clé.
3. Dès que le fournisseur d’identité a inscrit la partie publique de la clé, il demande à l’appareil de se connecter avec la partie privée de la clé.
4. Le fournisseur d’identité valide et émet ensuite le jeton d’authentification qui permet à l’utilisateur et à l’appareil d’accéder aux ressources protégées. Le fournisseur d’identité peut écrire des applications multiplateformes ou utiliser des navigateurs pris en charge via les API JavaScript/Webcrypto pour créer et utiliser des informations d’identification Microsoft Passport pour les utilisateurs.

## <a name="the-deployment-requirements-for-microsoft-passport"></a>Conditions requises du déploiement pour Microsoft Passport
### <a name="at-the-enterprise-level"></a>Au niveau de l’entreprise
* L’entreprise dispose d’un abonnement Azure.

### <a name="at-the-user-level"></a>Au niveau de l’utilisateur
* L’ordinateur de l’utilisateur exécute Windows 10 Professionnel ou Entreprise.

Pour obtenir des instructions de déploiement détaillées, consultez [Activer Microsoft Passport for Work dans l’organisation](active-directory-azureadjoin-passport-deployment.md).

## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)




<!--HONumber=Dec16_HO4-->


