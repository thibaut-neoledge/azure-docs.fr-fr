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
	ms.topic="hero-article"
	ms.date="12/04/2015"
	ms.author="tdykstra"/>

# Authentification et autorisation pour API Apps dans Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Vue d’ensemble 

Cet article décrit les options d’authentification et d’autorisation pour les applications API dans Azure App Service.

Le diagramme suivant illustre certaines caractéristiques clés de l’authentification du Service d’application :

* Il prétraite les demandes d’API entrantes, ce qui vous offre plusieurs options en fonction de la tâche d’authentification que vous souhaitez exécuter dans votre propre programme. 
* Vous avez le choix entre cinq fournisseurs d’authentification : Azure Active Directory, Facebook, Google, Twitter et Compte Microsoft.
* Il fonctionne aussi bien pour l’authentification de l’utilisateur final que pour celle du principal de service. 
* Il fonctionne de la même façon pour les applications API, les applications Web et les applications mobiles.

![](./media/app-service-api-authentication/api-apps-overview.png)

## Prétraitement des demandes entrantes

App Service empêche les demandes HTTP anonymes d’atteindre votre application API, ou authentifie celles qui disposent de jetons avant qu’elles atteignent votre application API. Pour une application API, vous pouvez configurer l’une des trois options :

1. Autoriser uniquement les demandes authentifiées à accéder à votre application API.

	Si une demande anonyme est reçue depuis un navigateur, l’application de service est redirigée vers une page d’ouverture de session.

	Cette procédure fonctionne si vous connaissez à l’avance le fournisseur d’authentification (Google, Twitter, etc.) que vous souhaitez utiliser. Vous pouvez configurer le Service d’application afin qu’il gère le processus d’ouverture de session pour vous. Vous pouvez également spécifier votre URL et faire en sorte qu’App Service redirige vers elle les demandes anonymes. Vous pouvez ensuite offrir aux utilisateurs un choix de fournisseurs d’authentification.

	Avec cette option, vous n’avez pas besoin d’écrire de code d’authentification dans votre application, et l’autorisation est simplifiée parce que les revendications les plus importantes sont fournies dans les en-têtes HTTP.

2. Autorise toutes les demandes à atteindre votre application API, mais valide les demandes authentifiées et transmet les informations d’authentification dans les en-têtes HTTP.

	Cette option offre davantage de souplesse dans le traitement des demandes anonymes et facilite l’écriture de code nécessitant l’accès aux revendications les plus courantes. Contrairement à l’option 1, vous devez écrire un programme si vous souhaitez empêcher les utilisateurs anonymes d’utiliser votre API.

3. Autoriser toutes les demandes à atteindre votre API, sans recours aux informations d’authentification dans les requêtes.

	Cette option laisse la gestion des tâches d’authentification et d’autorisation au code d’application.

Dans le [portail Azure](https://portal.azure.com/), vous sélectionnez l’option souhaitée dans le panneau **l’authentification/autorisation**.

![](./media/app-service-api-authentication/authblade.png)

Pour les options 1 et 2, activez l’**authentification App Service** puis, dans la liste déroulante **Action à entreprendre lorsque la demande n’est pas authentifiée**, choisissez **Connecter** ou **Autoriser la requête (aucune action)**. Si vous choisissez **Se connecter**, vous devez sélectionner un fournisseur d’authentification et le configurer.

![](./media/app-service-api-authentication/actiontotake.png)
 
## Langage non spécifié

Le traitement de l’authentification App Service se produit avant que les demandes atteignent votre application API, ce qui signifie que les fonctionnalités d’authentification fonctionnent pour les applications API, quels que soient le langage ou l’infrastructure dans lesquels elles sont rédigées. Votre API peut être basée sur ASP.NET, Java, Node.js ou une infrastructure prise en charge par App Service.

App Service transmet le jeton JWT dans l’en-tête d’autorisation d’une requête HTTP et le code peut obtenir les informations dont il a besoin à partir du jeton rédigé et ce, quel que soit son langage ou son infrastructure. En outre, App Service offre un accès plus facile aux revendications utilisées le plus fréquemment en définissant des en-têtes spéciaux, tels que les éléments suivants :

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
Dans une API .NET, vous pouvez utiliser l’attribut `Authorize` et, pour une autorisation affinée, vous pouvez facilement écrire du code basé sur des revendications, car les données de cette dernière sont renseignées pour vous dans les classes .NET.

## Authentification d’un principal du service

Vous pouvez également utiliser l’authentification du Service d’application pour les scénarios internes, notamment pour l’appel d’une application API à une autre application API. Dans ce scénario, vous pouvez utiliser des informations d’identification correspondant à un compte de service et non les informations d’identification d’authentification de l’utilisateur final. Un compte de service est également appelé *principal du service*, et l’authentification à l’aide de ce compte est également appelée « scénario de service à service ».

Pour les scénarios internes, vous pouvez protéger l’application API en utilisant Azure Active Directory et fournir un jeton d’autorisation du principal de service AAD lorsque vous appelez l’application API. Vous pouvez demander le jeton en fournissant l’ID client et la clé secrète du client à partir de l’application AAD. Aucun code spécial propre à Azure n’est nécessaire, comme c’était le cas pour la gestion du jeton Zumo Mobile Services. Vous trouverez un exemple de ce scénario à l’aide de ASP.NET API apps dans le didacticiel [Authentification par principal du service pour les applications API](app-service-api-dotnet-service-principal-auth.md).

Si vous souhaitez gérer un scénario de service à service sans utiliser l’authentification App Service, vous pouvez utiliser les certificats clients ou l’authentification de base. Pour plus d’informations sur les certificats clients dans Azure, consultez [Configuration de l’authentification mutuelle TLS pour une application Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md).

L’authentification du principal du service à partir d’une application logique du Service d’application pour une application API est un cas particulier qui est expliqué dans [Utilisation de votre API personnalisée hébergée sur App Service avec les applications logiques](../app-service-logic/app-service-logic-custom-hosted-api.md).

## Plus d’informations

Pour plus d’informations sur les services d’authentification et d’autorisation dans Azure App Service, consultez [Extension de l’authentification/autorisation App Service](/blog/announcing-app-service-authentication-authorization/).

## Étapes suivantes

Cet article a décrit les fonctionnalités d’authentification et d’autorisation des applications d’App Service API apps.

Si vous suivez la série des didacticiels de prise en main pour ASP.NET et API Apps, testez ces fonctionnalités dans le didacticiel suivant, [Authentification utilisateur dans App Service API Apps](app-service-api-dotnet-user-principal-auth.md).

Pour plus d’informations sur l’utilisation du nœud et Java dans Azure App Service, consultez le [Centre de développement Node.js](/develop/nodejs/) et le [centre de développement Java](/develop/java/).

<!---HONumber=AcomDC_1210_2015-->