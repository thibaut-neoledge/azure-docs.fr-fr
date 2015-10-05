<properties 
	pageTitle="Vue d'ensemble de Microsoft Passport et détails de cette nouvelle authentification basée sur la certification. | Microsoft Azure" 
	description="Rubrique qui explique comment les utilisateurs peuvent configurer Azure AD Join lors de l’introduction de l’interface logicielle lors de la première utilisation." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="femila"/>

# Authentification des identités sans mot de passe avec Microsoft Passport

Les méthodes actuelles d’authentification avec un simple mot de passe ne sont pas suffisantes pour protéger les utilisateurs. Les utilisateurs réutilisent et oublient les mots de passe. Les mots de passe peuvent faire l’objet d’une violation, de tentatives de phishing, de piratage ou être faciles à deviner. Ils sont également difficiles à mémoriser et sujets aux attaques de type « pass-the-hash ».

## Qu’est-ce que Microsoft Passport ?
Microsoft Passport est une nouvelle approche d’authentification par clé publique/privée ou basée sur les certificats pour les organisations et les consommateurs qui ne nécessite pas de passer par un mot de passe. Cette forme d’authentification s’appuie sur des informations d’identification constituées d’une paire de clés, qui peuvent remplacer les mots de passe et être résistantes aux failles, aux vols et au phishing. Microsoft Passport permet aux utilisateurs de s’authentifier auprès d’un compte Microsoft, d’un compte Active Directory, d’un compte Microsoft Azure Active Directory (AD) ou d’un service non-Microsoft qui prend en charge l’authentification Fast ID Online (FIDO). Après une vérification initiale en deux étapes lors de l’inscription à Microsoft Passport, Microsoft Passport est configuré sur l’appareil de l’utilisateur et l’utilisateur définit un mouvement, de type Windows Hello ou code confidentiel. L’utilisateur effectue le mouvement qui permet de vérifier son identité. Windows utilise ensuite Microsoft Passport pour authentifier l’utilisateur et l’aider à accéder aux ressources et services protégés.

La clé privée est accessible uniquement par le biais d’un mouvement utilisateur comme un code confidentiel, une empreinte biométrique, un appareil distant (comme une carte à puce) dont l’utilisateur s’est servi pour se connecter à l’appareil et cette information est liée à un certificat ou à une paire de clés asymétrique. Cette clé privée est vérifiée par le biais de matériel si l’appareil a une puce Module de plateforme sécurisée (TPM). La clé privée ne quitte jamais l’appareil.

La clé publique est inscrite auprès d’Azure Active Directory et de Windows Server Active Directory (en local). Les fournisseurs d’identité valident l’utilisateur en mappant sa clé publique avec la clé privée et fournissent des informations de connexion via un mécanisme de notification tel que le mot de passe à usage unique ou Phonefactor.
## Pourquoi les entreprises doivent-elles adopter Microsoft Passport ?
En activant Microsoft Passport, les entreprises peuvent sécuriser davantage leurs ressources comme suit :

* en configurant Microsoft Passport avec une option de matériel préféré, ce qui signifie que les clés sont générées sur TPM 1.2 ou TPM 2.0 lorsque ces derniers sont disponibles ou par logiciel lorsque TPM n’est pas disponible ; 

* en définissant la complexité et la longueur du code confidentiel et en indiquant si l’utilisation de Hello est activée dans votre organisation ;

* en configurant Microsoft Passport pour prendre en charge les scénarios de type carte à puce utilisant l’approbation par certificat.

## Comment cela fonctionne-t-il ?
1. Les clés sont générées sur le matériel. Un grand nombre d’ordinateurs ont une puce Module de plateforme sécurisée (TPM) intégrée qui sécurise le matériel en intégrant des clés de chiffrement dans les appareils. TPM 1.2 ou TPM 2.0 permet de générer des clés ou des certificats qui seront issus des clés générées.

2. Ces clés liées au matériel sont sanctionnées par le Module de plateforme sécurisée (TPM).

3. Un mouvement de déverrouillage unique déverrouille l’appareil et ce mouvement sera autorisé pour obtenir un accès à plusieurs ressources si l’appareil est joint au domaine ou à Azure AD.

## Cycle de vie Microsoft Passport
Cycle de vie de l’authentification Microsoft Passport![](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png) Le schéma ci-dessus illustre la paire de clés publique-privée et la validation par le fournisseur d’identité. Chacune de ces étapes est décrite en détail ci-dessous :

1. L’utilisateur prouve son identité via plusieurs méthodes de vérification intégrées (mouvements, cartes à puce physiques, authentification multifacteur) et envoie ces informations au fournisseur d’identité comme Azure Active Directory ou Active Directory.

2.  Ensuite, l’appareil crée et atteste la clé, prend la partie publique de cette clé, l’associe aux instructions du poste de travail, se connecte et l’envoie au fournisseur d’identité pour que celui-ci inscrive cette clé.

3. Dès que la partie publique de la clé est inscrite dans le fournisseur d’identité, celui-ci demande à l’appareil de se connecter avec la partie privée de la clé. Le fournisseur d’identité valide et émet ensuite le jeton d’authentification qui permet à l’utilisateur d’accéder aux ressources protégées.

4. Dès que la partie publique de la clé est inscrite dans le fournisseur d’identité, celui-ci demande à l’appareil de se connecter avec la partie privée de la clé.

5. Le fournisseur d’identité valide et émet ensuite le jeton d’authentification qui permet à l’utilisateur et à l’appareil d’accéder aux ressources protégées. Le fournisseur d’identité peut écrire des applications multiplateformes ou utiliser des navigateurs pris en charge via les API JS/Webcrypto pour créer et utiliser des informations d’identification Microsoft Passport pour les utilisateurs.

## Conditions requises du déploiement
Au niveau de l’entreprise
---------------------------
* Abonnement Azure

Au niveau de l’utilisateur
-------------------------------------------------------------
* L’ordinateur doit exécuter Windows 10 Professionnel ou Entreprise

## Informations supplémentaires

* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-overview.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Sept15_HO4-->