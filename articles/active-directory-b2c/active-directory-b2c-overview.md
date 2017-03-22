---
title: "Azure Active Directory B2C : vue d’ensemble | Microsoft Docs"
description: "Développement d’applications accessibles aux consommateurs avec Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 851319639f1443c062b87305bbde351f41846433
ms.lasthandoff: 03/10/2017


---
# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure Active Directory B2C : inscription et connexion de consommateurs à vos applications
Azure Active Directory B2C est une solution de gestion des identités de cloud complète pour vos applications web et mobiles grand public. Il s’agit d’un service global hautement disponible dont l’échelle s’adapte à des centaines de millions d’identités. Basé sur une plateforme sécurisée à l’échelle de l’entreprise, Azure Active Directory B2C protège vos applications, votre entreprise et vos consommateurs.

Dans le passé, les développeurs d'applications qui souhaitaient inscrire et connecter les consommateurs à leurs applications écrivaient leur propre code. Et ils auraient utilisé des systèmes ou des bases de données locaux pour stocker les noms d'utilisateur et les mots de passe. Azure Active Directory B2C offre aux développeurs un meilleur moyen d'intégrer la gestion des identités des consommateurs dans leurs applications à l'aide d'une plateforme sécurisée basée sur des normes et d'un ensemble complet de stratégies extensibles. Lorsque vous utilisez Azure Active Directory B2C, vos consommateurs peuvent s’inscrire auprès de vos applications à l’aide de leurs comptes sociaux existants (Facebook, Google, Amazon, LinkedIn) ou en créant des informations d’identification (adresse de messagerie et mot de passe, ou nom d’utilisateur et mot de passe) que nous appelons « comptes locaux ».

## <a name="get-started"></a>Prise en main
Pour générer une application acceptant l’inscription et la connexion des consommateurs, vous devez commencer par inscrire cette application auprès d’un client Azure Active Directory B2C. Obtenez votre propre client en suivant la procédure décrite dans [Création d’un client Azure AD B2C](active-directory-b2c-get-started.md).

Vous pouvez écrire votre application dans le service Azure Active Directory B2C en envoyant directement des messages de protocole à l’aide [d’OAuth 2.0 ou d’Open ID Connect](active-directory-b2c-reference-protocols.md) ou en utilisant nos bibliothèques pour qu’elles fassent le travail à votre place. Choisissez votre plateforme préférée dans le tableau suivant et commencez.

[!INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>Nouveautés
Consultez régulièrement cette page pour en savoir plus sur les futures modifications apportées à Azure Active Directory B2C. Nous communiquerons également les mises à jour sur Tweeter via @AzureAD.

* Découvrez notre [infrastructure de stratégie extensible](active-directory-b2c-reference-policies.md) et les types de stratégies que vous pouvez créer et utiliser dans vos applications.
* Placez un signet sur notre [blog relatif au service](https://blogs.msdn.microsoft.com/azureadb2c/) pour recevoir des notifications sur les problèmes mineurs, les mises à jour, l’état et les mesures d’atténuation du service. Continuez également de surveiller le [Tableau de bord Statut Azure](https://azure.microsoft.com/status/) .
* [Limites, restrictions et contraintes du service](active-directory-b2c-limitations.md)actuelles.
* Enfin, [exemple de code](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c) utilisant Azure AD B2C et ASP.NET Core.

## <a name="how-to-articles"></a>Articles sur les procédures
Découvrez comment utiliser des fonctionnalités spécifiques d’Azure Active Directory B2C :

* Configurer des comptes [Facebook](active-directory-b2c-setup-fb-app.md), [Google+](active-directory-b2c-setup-goog-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) et [LinkedIn](active-directory-b2c-setup-li-app.md) à utiliser dans vos applications grand public.
* [Utiliser des attributs personnalisés pour recueillir des informations sur vos consommateurs](active-directory-b2c-reference-custom-attr.md).
* [Activer Azure Multi-Factor Authentication dans vos applications grand public](active-directory-b2c-reference-mfa.md).
* [Configurer une réinitialisation de mot de passe libre-service pour vos consommateurs](active-directory-b2c-reference-sspr.md).
* [Personnaliser l’aspect des pages d’inscription, de connexion et des autres pages accessibles aux consommateurs](active-directory-b2c-reference-ui-customization.md) prises en charge par Azure Active Directory B2C.
* [Utiliser l’API Azure Active Directory Graph pour créer, lire, mettre à jour et supprimer des consommateurs par programme](active-directory-b2c-devquickstarts-graph-dotnet.md) dans votre client Azure Active Directory B2C.

## <a name="next-steps"></a>Étapes suivantes
Les liens ci-dessous vous seront utiles pour explorer le service en profondeur :

* Consultez les [informations de tarification relatives à Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
* Consultez notre [exemples de code](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c) pour Azure Active Directory B2C. 
* Pour obtenir de l’aide sur Stack Overflow, utilisez la balise [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c).
* Faites-nous part de vos commentaires sur le [forum d’Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c), votre avis nous intéresse !
* Consultez [l’article de référence sur les protocoles Azure AD B2C](active-directory-b2c-reference-protocols.md).
* Consultez [l’article de référence sur les jetons Azure AD B2C](active-directory-b2c-reference-tokens.md).
* Consultez les [Forums Aux Questions sur Azure Active Directory B2C](active-directory-b2c-faqs.md).
* [Dépôt de demandes de support pour Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Obtenir les mises à jour de sécurité de nos produits
Nous vous encourageons à activer les notifications d’incidents de sécurité en vous rendant sur [cette page](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.


