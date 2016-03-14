<properties
   pageTitle="Gestion des identités pour les applications multi-locataires | Microsoft Azure"
   description="Meilleures pratiques pour la gestion de l’authentification, de l’autorisation et de l’identité dans les applications multi-locataires."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Gestion des identités pour les applications multi-locataires dans Microsoft Azure

Cette série décrit les meilleures pratiques pour les applications multi-locataires, lors de l’utilisation d’Azure AD pour l’authentification et la gestion des identités.

Lorsque vous générez une les application multi-locataire, l’une des premières difficultés est la gestion des identités utilisateur, car désormais chaque utilisateur appartient à un client. Par exemple :

- Les utilisateurs se connectent avec les informations d’identification de leur organisation.
- Les utilisateurs doivent avoir accès aux données de leur organisation, mais pas aux données appartenant à d’autres clients.
- Une organisation peut s’inscrire auprès de l’application, puis affecter des rôles d’application à ses membres.

Azure Active Directory (Azure AD) possède des fonctionnalités qui prennent en charge tous ces scénarios.

Pour accompagner cette série d’articles, nous avons également créé une [implémentation de bout en bout][tailspin] et complète d’une application multi-locataire. Les articles reflètent ce que nous avons appris au cours de la création de l’application. Pour vous familiariser avec l’application, consultez [Exécution de l’application Surveys](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).


## Sommaire

- [Introduction](guidance-multitenant-identity-intro.md)
- [À propos de l’application Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
- [Authentification avec Azure AD](guidance-multitenant-identity-authenticate.md)
- [Utilisation des identités basées sur les revendications](guidance-multitenant-identity-claims.md)
- [Inscription et intégration du client](guidance-multitenant-identity-signup.md)
- [Définition et gestion des rôles d’application](guidance-multitenant-identity-app-roles.md)
- [Autorisation](guidance-multitenant-identity-authorize.md)
- [Sécurisation d’une API web principale](guidance-multitenant-identity-web-api.md)
- [Mise en cache des jetons d’accès OAuth2](guidance-multitenant-identity-token-cache.md)
- [Fédération avec l’AD FS d’un client](guidance-multitenant-identity-adfs.md)
- [Utilisation d’une assertion du client pour obtenir des jetons d’accès d’Azure AD](guidance-multitenant-identity-client-assertion.md)
- [Utilisation d’Azure Key Vault pour protéger la confidentialité des secrets d’application](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->