<properties
	pageTitle="Vue d’ensemble du modèle d’application v2.0 | Microsoft Azure"
	description="Introduction à la création d’applications avec une connexion de compte Microsoft et Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/18/2016"
	ms.author="dastrock"/>

# Version préliminaire du modèle d’application v2.0 : connecter les utilisateurs de compte Microsoft et d’Azure AD dans une même application

> [AZURE.NOTE]
	Ces informations s’appliquent à la version préliminaire publique du modèle d’application v2.0. Pour obtenir des instructions sur l’intégration au service Azure AD disponible au grand public, consultez le [Guide du développeur Azure Active Directory](active-directory-developers-guide.md).

Auparavant, un développeur d’application qui souhaitait prendre en charge à la fois les comptes Microsoft et Azure Active Directory devait opérer une intégration avec deux systèmes distincts. Désormais, le modèle d’application v2.0 permet de connecter les utilisateurs avec les deux types de compte. Moyennant une intégration unique, vous pouvez toucher un public qui représente plusieurs millions d’utilisateurs titulaires aussi bien de comptes personnels que de professionnels/scolaires.

Vos applications peuvent aussi exploiter un [ensemble d’API Office 365 REST](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) en utilisant n’importe lequel de ces types de compte. Actuellement, ces API incluent les API Courrier, Contacts et Calendriers d’Outlook. Des services supplémentaires seront ajoutés dans un avenir proche.<!-- TODO: customer reference article --><!-- Several apps have already begun to bridge the gap between consumer and enterprise accounts, including: [Boomerang](), [TripIt](), & [Uber](). -->

Le modèle d’application v2.0 est en version préliminaire. Nous accueillons favorablement vos retours d’expérience tout au long de la période d’évaluation du nouveau modèle d’application. Nous tenons compte de ces commentaires pour apporter des modifications importantes au service et l’améliorer. Au cours de cette période, il est déconseillé de publier une application de production à l’aide du modèle d’application v2.0.<!-- TODO: Get approval on how it looks  -->

## Mise en route
Il existe deux façons de rendre sa propre application opérationnelle avec le modèle d'application v2.0. Vous pouvez choisir d’envoyer directement des messages de protocole à l’aide d’[OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) ou d’[Open ID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). Nos bibliothèques peuvent aussi faire le travail à votre place : il vous suffit de choisir votre plateforme préférée ci-dessous et de démarrer.<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## Nouveautés
Consultez régulièrement cette page pour en savoir plus sur les futures modifications de la version préliminaire publique du modèle d’application v2.0. Nous communiquerons également les mises à jour sur Tweeter via @AzureAD.

- Si vous avez créé une application pendant la période d’évaluation du modèle d’application v2.0 2015, [prenez connaissance des modifications apportées à ce protocole](active-directory-v2-preview-oidc-changes.md) pour vous assurer que votre application continue à fonctionner.
- Découvrez les [différents types d’application que vous pouvez créer avec le modèle d’application v2.0](active-directory-v2-flows.md).
- Si vous êtes développeur et que vous connaissez bien Azure Active Directory, prenez connaissance des [mises à jour concernant nos protocoles, ainsi que des différences qui caractérisent le modèle d’application v2.0](active-directory-v2-compare.md).
- [Limites, restrictions et contraintes de la version préliminaire](active-directory-v2-limitations.md) actuelle.

## Référence
Les liens ci-dessous vous seront utiles pour explorer la plateforme en profondeur :

- Pour obtenir une aide sur le dépassement de capacité de pile, utilisez les mots-clés [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal).
- Dites-nous ce que vous pensez de la version préliminaire sur le [forum des utilisateurs](https://feedback.azure.com/forums/169401-azure-active-directory/), votre avis nous intéresse ! Ajoutez l’intitulé « AppModelv2 : » dans le titre de votre publication, cela nous aidera à la repérer.
- [Informations de référence sur les protocoles du modèle d’application v2.0](active-directory-v2-protocols.md)
- [Informations de référence sur les jetons du modèle d’application v2.0](active-directory-v2-tokens.md)
- [Informations de référence sur les API Office 365 REST](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Étendues et consentement dans le point de terminaison v2](active-directory-v2-scopes.md)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
-->

<!---HONumber=AcomDC_0128_2016-->