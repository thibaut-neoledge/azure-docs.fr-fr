<properties
	pageTitle="Protocoles d’authentification Active Directory | Microsoft Azure"
	description="Cet article fournit une vue d’ensemble des différents protocoles d’authentification et d’autorisation pris en charge par Azure Active Directory."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="priyamo"/>

# Protocoles d’authentification Active Directory

Azure Active Directory (Azure AD) prend en charge plusieurs protocoles d’authentification et d’autorisation parmi ceux les plus couramment utilisés.

Cette série d’articles décrit les protocoles pris en charge et leur implémentation dans Azure AD. Nous y présenterons des exemples de requêtes et de réponses. Les protocoles étant directement intégrés, ces articles sont totalement indépendants du langage.

- [OAuth 2.0 dans Azure AD](active-directory-protocols-oauth-code.md) : découvrez le flux d’octroi d’un code d’autorisation OAuth2.0 et son implémentation dans Azure AD.
- [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) : découvrez comment utiliser le protocole d’autorisation OpenID Connect dans Azure AD.
- [Informations de référence sur le protocole SAML](active-directory-saml-protocol-reference.md) : apprenez à utiliser le protocole SAML pour la prise en charge de l’[authentification unique](active-directory-single-sign-on-protocol-reference.md) et de la [déconnexion unique](active-directory-single-sign-out-protocol-reference.md) dans Azure AD.


## Références et résolution des problèmes

Cette série d’articles fournit des informations supplémentaires qui peuvent vous être utiles non seulement pour résoudre les problèmes liés à vos applications Azure AD, mais aussi pour vous aider à mieux comprendre le fonctionnement d’Azure AD.

- [Métadonnées de fédération](active-directory-federation-metadata.md) : découvrez comment trouver et interpréter les documents de métadonnées générés par Azure AD.
- [Types de jeton et de revendication pris en charge](active-directory-token-and-claims.md) : découvrez les différentes revendications des jetons émis par Azure AD.
- [Substitution des clés de signature dans Azure AD](active-directory-signing-key-rollover.md) : découvrez le rythme de substitution des clés d’Azure AD et apprenez à mettre à jour la clé pour les scénarios d’application les plus courants.
- [Dépannage des protocoles d’authentification](active-directory-error-handling.md) : apprenez à interpréter et résoudre les erreurs les plus courantes liées à l’utilisation d’OAuth 2.0 et d’Azure AD.
- [Meilleures pratiques pour OAuth 2.0 dans Azure AD](active-directory-oauth-best-practices.md) : familiarisez-vous avec les bonnes pratiques d’utilisation d’OAuth 2.0 dans Azure AD et apprenez à éviter les pièges courants.

<!---HONumber=AcomDC_0608_2016-->