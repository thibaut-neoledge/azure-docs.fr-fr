<properties
	pageTitle="Authentification et autorisation pour API Apps dans Azure App Service | Microsoft Azure"
	description="En savoir plus sur les services d’authentification et d’autorisation qu’Azure App Service fournit pour API Apps."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="rachelap"/>

# Authentification et autorisation pour API Apps dans Azure App Service

## Vue d'ensemble 

> [AZURE.NOTE] Cette rubrique sera migrée vers une rubrique d’[authentification/autorisation App Service](../app-service/app-service-authentication-overview.md) consolidée, qui couvre les applications web, mobile et les API Apps.

Azure App Service offre des services d’authentification et d’autorisation intégrés qui implémentent [OAuth 2.0](#oauth) et [OpenID Connect](#oauth). Cet article décrit les services et options disponibles pour API Apps dans Azure App Service.

Le diagramme suivant illustre certaines caractéristiques clés de l’authentification du Service d’application :

* Il prétraite les demandes entrantes de l’API, ce qui signifie qu’il est compatible avec n’importe quel langage ou framework pris en charge par App Service.
* Il vous offre plusieurs options en fonction de la tâche d’authentification que vous souhaitez exécuter dans votre propre code.
* Il fonctionne aussi bien pour l’authentification de l’utilisateur final que pour celle du compte de service.
* Il prend en charge cinq fournisseurs d’identité : Azure Active Directory, Facebook, Google, Twitter et Compte Microsoft.
* Il fonctionne de la même façon pour les applications API, les applications Web et les applications mobiles.

![](./media/app-service-api-authentication/api-apps-overview.png)

## Langage non spécifié

Le traitement de l’authentification App Service se produit avant que les demandes atteignent votre application API, ce qui signifie que les fonctionnalités d’authentification fonctionnent pour les applications API, quels que soient le langage ou l’infrastructure dans lesquels elles sont rédigées. Votre API peut être basée sur ASP.NET, Java, Node.js ou une infrastructure prise en charge par App Service.

App Service transmet le jeton web JSON (JWT) dans l’en-tête d’autorisation d’une requête HTTP et le code peut obtenir les informations dont il a besoin à partir du jeton rédigé et ce, quel que soit son langage ou son infrastructure. En outre, App Service offre un accès plus facile aux revendications utilisées le plus fréquemment en définissant des en-têtes spéciaux, tels que les éléments suivants :

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
Dans une API .NET, vous pouvez utiliser l’attribut `Authorize` et, pour une autorisation affinée, vous pouvez facilement écrire du code basé sur des revendications, car les données de cette dernière sont renseignées pour vous dans les classes .NET.

## Plusieurs options de protection

App Service peut empêcher les requêtes HTTP anonymes d’atteindre votre application API ; il peut également transmettre toutes les demandes et valider les jetons pour les demandes dans lesquelles ils sont inclus, ou bien acheminer toutes les demandes sans aucune action :

1. Autoriser uniquement les demandes authentifiées à accéder à votre application API.

	Si une demande anonyme est reçue depuis un navigateur, App Service la redirige vers la page de connexion vers le fournisseur d’authentification (Azure AD, Google, Twitter, etc.) de votre choix.

	Avec cette option, vous n’avez pas besoin d’écrire de code d’authentification dans votre application, et le code d’autorisation est simplifié parce que les revendications les plus importantes sont fournies dans les en-têtes HTTP.

2. Autorise toutes les demandes à atteindre votre application API, mais valide les demandes authentifiées et transmet les informations d’authentification dans les en-têtes HTTP.

	Cette option offre plus de souplesse pour le traitement des demandes anonymes, mais vous devez écrire du code si vous souhaitez empêcher que des utilisateurs anonymes se servent de votre API. Étant donné que les revendications les plus populaires sont transmises dans les en-têtes des demandes HTTP, le code d’autorisation est relativement simple.
	
3. Autoriser toutes les demandes à atteindre votre API, sans recours aux informations d’authentification dans les requêtes.

	Cette option laisse la gestion des tâches d’authentification et d’autorisation au code d’application.

Dans le [portail Azure](https://portal.azure.com/), vous sélectionnez l’option souhaitée dans le panneau **l’authentification/autorisation**.

![](./media/app-service-api-authentication/authblade.png)

Pour les options 1 et 2, activez l’**authentification App Service** puis, dans la liste déroulante **Action à entreprendre lorsque la demande n’est pas authentifiée**, choisissez **Connecter** ou **Autoriser la requête (aucune action)**. Si vous choisissez **Se connecter**, vous devez sélectionner un fournisseur d’authentification et le configurer.

![](./media/app-service-api-authentication/actiontotake.png)

Pour plus d’informations sur la procédure de configuration de l’authentification, consultez l’article [Configurer votre application App Service pour utiliser la connexion Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Cet article s’applique aux applications API et aux applications mobiles, et fait référence à des articles relatifs à d’autres fournisseurs d’authentification.
 
## <a id="internal"></a> Authentification du compte de service

L’authentification App Service est utile pour les scénarios internes, notamment pour l’appel d’une application API à une autre application API. Dans ce scénario, vous pouvez obtenir un jeton en utilisant les informations d’identification correspondant à un compte de service au lieu des informations d’identification de l’utilisateur final. Un compte de service est également appelé *principal de service* dans Azure Active Directory, et l’authentification pratiquée avec ce type de compte est également appelée « scénario de service à service ».

Pour les scénarios service à service, protégez l’application API en utilisant Azure Active Directory et fournir un jeton d’autorisation du principal de service AAD lorsque vous appelez l’application API. Pour obtenir le jeton, renseignez l’ID client et la clé secrète du client à partir de l’application AAD. Aucun code spécial propre à Azure n’est nécessaire, comme c’était le cas pour la gestion du jeton Zumo Mobile Services. Vous trouverez un exemple de ce scénario utilisant ASP.NET API apps dans le didacticiel [Authentification par principal du service pour les applications API](app-service-api-dotnet-service-principal-auth.md).

Si vous souhaitez gérer un scénario de service à service sans utiliser l’authentification App Service, vous pouvez utiliser les certificats clients ou l’authentification de base. Pour plus d’informations sur les certificats clients dans Azure, consultez [Configuration de l’authentification mutuelle TLS pour une application Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Pour plus d’informations sur l’authentification de base dans ASP.NET, consultez [Filtres d’authentification dans ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

L’authentification du compte de service à partir d’une application logique du Service d’application pour une application API est un cas particulier qui est expliqué dans [Utilisation de votre API personnalisée hébergée sur App Service avec les applications logiques](../app-service-logic/app-service-logic-custom-hosted-api.md).

## Authentification du client mobile

Pour plus d’informations sur la gestion de l’authentification des clients mobiles, consultez la [documentation relative à l’authentification pour les applications mobiles](../app-service-mobile/app-service-mobile-ios-get-started-users.md). L’authentification App Service fonctionne de la même façon pour les applications mobiles et les applications API.
  
## Plus d’informations

Pour en savoir plus sur l’authentification et l’autorisation dans Azure App Service, consultez les ressources suivantes :

* [Extension de l’authentification/autorisation App Service](/blog/announcing-app-service-authentication-authorization/)
* [Configurer votre application App Service pour utiliser une connexion Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md) (inclut des liens vers d’autres fournisseurs d’authentification en haut de la page).

Pour plus d’informations sur OAuth 2.0, OpenID Connect et les jetons web JSON (JWT), consultez les ressources suivantes :

* [Prise en main d’OAuth 2.0](http://shop.oreilly.com/product/0636920021810.do "Prise en main d’OAuth 2.0")
* [Présentation d’OAuth2, OpenID Connect et JSON Web Tokens (JWT) - Cours PluralSight](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction)
* [Création et sécurisation d'une API RESTful pour plusieurs clients dans ASP.NET - cours PluralSight](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

Pour plus d’informations sur Azure Active Directory, consultez les ressources suivantes :

* [Scénarios Azure AD](http://aka.ms/aadscenarios)
* [Guide du développeur Azure AD](http://aka.ms/aaddev)
* [Exemples Azure AD](http://aka.ms/aadsamples)

## Étapes suivantes

Cet article a décrit les fonctionnalités d’authentification et d’autorisation d’App Service que vous pouvez utiliser pour API Apps. Dans le prochain didacticiel de la série « Prise en main », vous allez apprendre à implémenter [l’authentification utilisateur dans App Service API Apps](app-service-api-dotnet-user-principal-auth.md).

<!---HONumber=AcomDC_0713_2016-->