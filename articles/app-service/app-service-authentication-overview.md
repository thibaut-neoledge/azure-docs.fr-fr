<properties 
	pageTitle="Authentification dans les applications API et applications mobiles Azure App Service" 
	description="Découvrez comment configurer et utiliser l'authentification pour les applications API et les applications mobiles dans Azure App Service." 
	services="app-service" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="tdykstra"/>

# Authentification pour les applications d'API et les applications mobiles dans Azure App Service

## Vue d'ensemble

Cet article explique les fonctionnalités d'authentification intégrées pour les [applications API](../app-service-api/app-service-api-apps-why-best-platform.md) et les [applications mobiles](../app-service-mobile/app-service-mobile-value-prop-preview.md).

La section [Étapes suivantes](#next-steps) à la fin de cet article fournit des liens vers la documentation associée.

## Passerelle Azure App Service

Azure App Service offre des services d'authentification intégrés qui implémentent [OAuth 2.0](#oauth) et [OpenID Connect](#oauth) et fonctionnent avec plusieurs *fournisseurs d'identité*. Un fournisseur d'identité est un service externe, approuvé par Azure App Service, qui authentifie les utilisateurs de votre application. App Service prend en charge les fournisseurs d'identité les plus populaires :

* Azure Active Directory
* Compte Microsoft
* Google
* Twitter
* Facebook

### Architecture de la passerelle

Tout groupe de ressources Azure contenant des applications d’API ou des applications mobiles inclut une *passerelle*. La passerelle est une ressource Azure qui s'exécute dans une application web et gère les tâches d'administration, y compris l'authentification pour les applications d'API et les applications mobiles dans le groupe de ressources.

La passerelle gère les procédures de connexion, les jetons et empêche les appels non authentifiés d'atteindre les applications d’API [configurées pour exiger un accès authentifié](../app-service-api/app-service-api-dotnet-add-authentication.md#protect-the-api-app). La passerelle peut contrôler l'accès aux applications d'API car toutes les requêtes HTTP entrantes destinées aux applications d'API du groupe de ressources sont acheminées via la passerelle.

Le diagramme suivant illustre ces fonctions de passerelle.

![](./media/app-service-authentication-overview/gateway.png)

### Caractéristiques de la passerelle

Les services d'authentification de la passerelle offrent plusieurs avantages par rapport à votre propre implémentation d’OAuth 2.0 :

* Microsoft fournit des Kits de développement logiciel qui vous permettent d'effectuer des tâches d'authentification et d'autorisation à l'aide d'une syntaxe simplifiée.

* Comme App Service gère davantage de tâches d'authentification, le temps nécessaire au développement et au test est réduit, et vous êtes peu voire pas affecté par les modifications apportées aux implémentations de fournisseur d’OAuth.

* Si vous utilisez plusieurs applications de protection et que vous les conservez dans un groupe de ressources, il vous suffit d’un ID de client et d’une clé secrète client pour chaque fournisseur d'authentification, puisqu’il n’existe qu’une seule URL de redirection pour la passerelle.

* La surveillance et le dépannage sont plus faciles car vous pouvez surveiller le trafic d'authentification pour un groupe de ressources entier en surveillant la passerelle.

* Le débogage est plus facile car vous pouvez configurer un programme pour utiliser la passerelle lors de l'exécution en mode débogage localement, et vous n'êtes pas obligé de modifier les URL de redirection dans votre compte de fournisseur d'identité.

## Flux serveur et flux client

La passerelle App Service offre deux méthodes pour authentifier les clients : *flux client* et *flux serveur*. Dans les deux flux, l'application cliente envoie les informations d'identification de l'utilisateur (en général, un nom d'utilisateur et un mot de passe) directement au fournisseur d'identité. Ni la passerelle ni votre application ne reçoit les informations d'identification de l'utilisateur dans un de ces flux.

### Flux client

Flux client signifie que l'application cliente communique directement avec le fournisseur d'identité pour obtenir le jeton d'accès du fournisseur. L'application cliente envoie le jeton d'accès du fournisseur à la passerelle. La passerelle crée et envoie au client un jeton de contexte utilisateur. Ce jeton de contexte utilisateur est également appelé jeton Zumo, le nom de code d'origine d’[Azure Mobile Services](/documentation/services/mobile-services/). (Les services d'authentification pour API Apps et Mobile Apps reposent sur la même architecture développée à l'origine pour les Mobile Services).

Le diagramme suivant illustre ce flux.

![](./media/app-service-authentication-overview/clientflow.png)

Le client fournit ensuite le jeton Zumo dans les demandes HTTP lorsqu'il appelle des applications d’API ou des applications mobiles protégées. La passerelle stocke les jetons du fournisseur et effectue le suivi des jetons Zumo.

### Flux serveur

Flux serveur signifie que l'application cliente s'appuie sur la passerelle pour communiquer avec le fournisseur d'identité pour lancer la connexion. Le navigateur client accède à une URL de passerelle et la passerelle redirige la demande vers la oage de connexion du fournisseur d'identité. Une fois l'utilisateur connecté, la passerelle obtient le jeton du fournisseur d'identité, crée le jeton Zumo, puis envoie le jeton Zumo au client.

![](./media/app-service-authentication-overview/serverflow.png)

L’interaction suivante entre l'application cliente et les applications d'API ou les applications mobiles protégées est gérée comme pour le flux client : le client fournit le jeton Zumo dans les requêtes HTTP.

### Comment choisir entre flux client et flux serveur

Le flux client est généralement le meilleur choix si le fournisseur d'identité que vous souhaitez utiliser possède un Kit de développement logiciel pour la plateforme client à prendre en charge. Le flux client fournit la meilleure expérience utilisateur car il réduit le nombre de fois où l'utilisateur doit saisir des informations d'identification. Par exemple, si votre utilisateur possède un périphérique android, il dispose probablement d’un compte Google associé avec ce périphérique ; ainsi, s’il peut utiliser ce compte sans avoir à entrer à nouveau son nom d'utilisateur et son mot de passe, l’expérience utilisateur est encore meilleure. Le même principe s’applique à un compte Facebook sur un périphérique Android, iOS ou Windows Phone, ou à un compte Microsoft sur un bureau Windows ou Windows Phone.

Dans d'autres scénarios, le flux serveur peut être un meilleur choix :

- Il n'existe aucun Kit de développement logiciel client natif pour le fournisseur d'identité client et la plateforme cliente à prendre en charge.

- L’important est d’accélérer la mise en production et d’améliorer l'expérience utilisateur, si le temps le permet ultérieurement. Le flux serveur permet à Azure App Service de gérer plus de tâches d'authentification, ce qui réduit le temps que vous devez consacrer au développement et au test.

## Appels sortants vers les plateformes SaaS pour le compte d’un utilisateur

Vous pouvez écrire un code permettant de passer des appels sortants vers les plateformes Software-as-a-Service (SaaS) pour le compte d’un d'utilisateur connecté, ou utiliser une [application API de connecteur](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md). Par exemple, pour publier un tweet à partir du compte Twitter de l'utilisateur, vous pouvez utiliser [un Kit de développement logiciel Twitter](https://dev.twitter.com/overview/api/twitter-libraries), ou configurer un [connecteur Twitter](../app-service-mobile/app-service-logic-connector-twitter.md) dans votre abonnement Azure et appeller ce connecteur. Cette section décrit l'accès à une plateforme SaaS à partir d’un code qui s'exécute dans une application d'API ou une application mobile.

### <a id="obotoidprovider"></a>Utilisation du jeton de fournisseur d’identité 

La passerelle gère un *magasin de jetons* dans lequel elle associe un jeton Zumo à un ou plusieurs jetons d'accès à un fournisseur d’identité et jetons d'actualisation. Lorsqu'une demande HTTP avec un jeton Zumo valide est reçue, la passerelle sait à quel utilisateur les jetons du fournisseur d'identité correspondent.
  
Lorsque le code exécuté dans votre application d'API ou une application mobile doit passer un appel vers une ressource protégée pour le compte de l'utilisateur connecté, il peut récupérer et utiliser le jeton du fournisseur d'identité à partir du magasin de jetons de la passerelle, comme indiqué dans le diagramme suivant. Le schéma présente une situation où le client est déjà authentifié avec la passerelle et a le jeton Zumo.

![](./media/app-service-authentication-overview/idprovidertoken.png)

Par exemple, supposons que le fournisseur d'identité est Azure Active Directory (AAD) et que votre application d'API souhaite utiliser le jeton d'accès AAD pour appeler l'API AAD Graph ou demander l'accès à un site SharePoint auquel l'utilisateur à un droit d’accès. Vous pouvez envoyer une demande à la passerelle pour récupérer le jeton AAD, puis utiliser le jeton AAD pour appeler l'API Graph ou obtenir un jeton d’accès à pour le site SharePoint.

### <a id="obotosaas"></a>Obtenir le consentement de l’utilisateur pour accéder à d’autres ressources

La passerelle dispose également de fonctionnalités intégrées pour obtenir le consentement de l'utilisateur lorsque vous souhaitez accéder aux ressources sécurisées par un fournisseur autre que le fournisseur d'identité d'origine. Par exemple, pour un utilisateur qui se connecte à l'aide d'Azure Active Directory, vous pouvez accéder aux fichiers via le compte Dropbox de l’utilisateur.

La passerelle App Service prend en charge l'obtention du consentement de l'utilisateur pour un tel accès à partir des fournisseurs suivants :

* Box
* DropBox
* Facebook
* Google
* Office365
* OneDrive
* QuickBooks
* Salesforce
* SharePointOnline
* Twitter
* Yammer
* Azure Active Directory
* Compte Microsoft

Pour ces fournisseurs, la passerelle gère les jetons d’accès et les associe au jeton Zumo, comme pour le jeton d'accès du fournisseur d’identité. Le processus d’obtention du consentement de l'utilisateur et d’appel à une plateforme SaaS est illustré dans le diagramme suivant. Le schéma présente une situation où le client est déjà authentifié avec la passerelle et a le jeton Zumo.

![](./media/app-service-authentication-overview/saastoken.png)

La prise en charge du runtime App Service et les Kits de développement logiciel facilitent l’écriture du code qui accède aux ressources sécurisées par un de ces fournisseurs. Il existe une étape préliminaire non indiquée dans le diagramme : vous devez créer un compte auprès du fournisseur puis configurer l'ID et la clé secrète du client dans Azure App Service.

Pour ces fournisseurs (et bien d’autres), vous pouvez également accéder à des ressources sécurisées en utilisant une [application API de connecteur](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md) préconfigurée.

## Disponibilité du Kit de développement logiciel

Pour les applications d'API, le Kit de développement logiciel pour .NET fournit des fonctionnalités d'authentification :

* [Microsoft.Azure.AppService](http://www.nuget.org/packages/Microsoft.Azure.AppService) - pour une utilisation dans un client d'application API.  
* [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/) - à utiliser dans un projet d'API Web qui s'exécute dans une application API.
 
En outre, Visual Studio peut générer automatiquement le code qui fonctionne avec le Kit de développement pour .NET afin de simplifier davantage le code que vous écrivez pour appeler votre application API. Pour plus d’informations, consultez la rubrique [Utiliser une application API dans Azure App Service à partir d’un client .NET](../app-service-api/app-service-api-dotnet-consume.md).

Pour Mobiles Apps, les Kits de développement logiciel sont disponibles pour les plateformes suivantes :

- [.NET Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 
- [iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-preview.md)
- [Xamarin iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md)
- [Xamarin Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md)
- [Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md)
- JavaScript (didacticiel en cours de développement)

## Autres méthodes d'authentification

Si les services d'authentification de passerelle ne répondent pas aux besoins de votre application, vous pouvez gérer l'authentification vous-même, ou utiliser le service Gestion des API Azure.

### <a id="doityourself"></a>Authentification autonome (« Do-It-Yourself »)

Vous pouvez exécuter une infrastructure d'authentification comme [ASP.NET Identity](http://www.asp.net/identity) ou [Thinktecture](http://www.thinktecture.com/identityAndAccessControl) sur Azure. Ce vous permet de contrôler comment tout fonctionne, mais vous devez également passer plus de temps à développer et à tester les fonctionnalités d'authentification. En outre, si vous utilisez plusieurs applications à protéger avec plusieurs URL de redirection, vous devez configurer plusieurs ID et clés secrètes de client avec des fournisseurs d'authentification tiers, notamment Facebook, Google, Twitter.

À l'heure actuelle, App Service ne prend pas en charge l’utilisation d'une solution autonome (« Do-It-Yourself ») avec l'authentification de passerelle, comme cela est possible dans [Mobile Services](mobile-services-dotnet-backend-get-started-custom-authentication.md).

### <a id="apim"></a>Gestion des API Azure

Si vous avez des API existantes que vous souhaitez protéger avec l'authentification, vous pouvez le faire avec le service Gestion des API Azure. Pour plus d'informations sur l'utilisation de la gestion des API avec les applications API, consultez le blog publié par Panos Kefalidis : [Taking advantage of API Management for API Apps](http://www.kefalidis.me/2015/06/taking-advantage-of-api-management-for-api-apps/)

## Étapes suivantes

Cet article a décrit les services d'authentification fournis par Azure App Service pour API Apps et Mobile Apps. Voici quelques liens vers des ressources pour en savoir plus sur les protocoles d'authentification sous-jacents et la documentation sur l'utilisation des fonctionnalités d'authentification d’App Service.

* [OAuth 2.0, OpenID Connect et JSON Web Tokens (JWT)](#oauth)
* Ressources API Apps
	* [Flux client API Apps](#apiaclient)
	* [Flux serveur API Apps](#apiaserver)
	* [Appels API Apps pour le compte d’un tiers](#apiaobo)
* Ressources Mobile Apps
	* [Flux client Mobile Apps](#maclient)
	* [Flux serveur Mobile Apps](#maserver)
	* [Appels Mobile Apps pour le compte d’un tiers](#maobo)

### <a id="oauth"></a>OAuth 2.0, OpenID Connect et JSON Web Tokens (JWT)

* [Prise en main d’OAuth 2.0](http://shop.oreilly.com/product/0636920021810.do "Prise en main d’OAuth 2.0") 
* [Présentation d’OAuth2, OpenID Connect et JSON Web Tokens (JWT) - Cours PluralSight](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [Création et sécurisation d'une API RESTful pour plusieurs clients dans ASP.NET - cours PluralSight](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

### <a id="apiaclient"></a>Flux client API Apps

* [Protéger une application API](../app-service-api/app-service-api-dotnet-add-authentication.md) - La partie configuration d'une application API s'applique aux flux client et serveur, mais la partie test dans un navigateur concerne le flux serveur.
* [Utiliser une application API dans Azure App Service à partir d’un client .NET](../app-service-api/app-service-api-dotnet-consume.md) - L’exemple d’application d’un appel authentifié concerne le flux serveur, mais est suivi par une section de [flux client](../app-service-api/app-service-api-dotnet-consume.md#client-flow) avec un exemple de code.

### <a id="apiaserver"></a>Flux serveur API Apps

* [Protéger une application API](../app-service-api/app-service-api-dotnet-add-authentication.md) - La partie configuration d’une application API s’applique aux flux client et serveur, tandis que la partie test dans un navigateur concerne le flux serveur.
* [Utiliser une application API dans Azure App Service à partir d'un client .NET](../app-service-api/app-service-api-dotnet-consume.md) - L’exemple de code d’un appel authentifié concerne le flux serveur. 

### <a id="apiaobo"></a>Appels API Apps pour le compte d’un tiers

* [Déployer et configurer une application API de connecteur SaaS dans Microsoft Azure App Service](../app-service-api/app-service-api-connnect-your-app-to-saas-connector.md) - montre comment installer une application API de connecteur préconfigurée, la configurer et l'appeler à l'aide des outils du navigateur.
* [Se connecter à une plateforme SaaS à partir d’une application API ASP.NET dans Azure App Service](../app-service-api/app-service-api-dotnet-connect-to-saas.md) - montre comment écrire votre propre connecteur, c’est à dire comment approvisionner, configurer et écrire un code pour une application API personnalisée qui effectue des appels pour le compte d’un tiers vers un fournisseur SaaS.

### <a id="maclient"></a>Flux client Mobile Apps

* [Ajout de l'authentification unique Azure Active Directory à votre application iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-aad-sso-preview.md)

### <a id="maserver"></a>Flux serveur Mobile Apps

* [Ajout de l'authentification à votre application iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-users-preview.md)
* [Ajout de l'authentification à votre application Xamarin.iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md)
* [Ajout de l'authentification à votre application Xamarin.Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md)
* [Ajout de l'authentification à votre application Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-get-started-users-preview.md)

### <a id="maobo"></a>Appels Mobile Apps passés vers des ressources sécurisées pour le compte d’un tiers

* [Obtention d’un jeton d’accès et appel de l’API SharePoint dans une app mobile](../app-service-mobile/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise.md#obtain-token)

<!---HONumber=July15_HO3-->