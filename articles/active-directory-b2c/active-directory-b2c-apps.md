<properties
	pageTitle="Version préliminaire d'Azure AD B2C | Microsoft Azure"
	description="Les types d'applications que vous pouvez créer dans la version préliminaire d'Azure AD B2C."
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/04/2015"
	ms.author="dastrock"/>

# Version préliminaire d'Azure AD B2C : types d'applications

Azure AD B2C prend en charge l'authentification pour de multiples architectures modernes d'application, toutes basées sur les protocoles standard [OAuth 2.0](active-directory-b2c-reference-protocols.md) et/ou [OpenID Connect](active-directory-b2c-reference-protocols.md). Ce document décrit brièvement les types d’applications pouvant être créées, indépendamment de la langue ou de la plate-forme souhaitées. Il vous aidera à comprendre les scénarios de niveau supérieur avant d'[entrer dans le code](active-directory-b2c-overview.md#getting-started).

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Concepts de base
Chaque application qui utilise Azure AD B2C devra être enregistrée dans votre [répertoire B2C](active-directory-b2c-get-started.md) via le [portail Azure en version préliminaire](https://portal.azure.com). Le processus d’inscription des applications collecte quelques valeurs et les affecte pour vos applications :

- un **ID d'application** qui identifie de manière unique votre application ;
- un **URI de redirection** pouvant être utilisé pour diriger les réponses vers votre application ;
- quelques valeurs spécifiques au scénario. Pour plus de détails, découvrez comment [inscrire une application](active-directory-b2c-app-registration.md).

Une fois inscrite, l'application communique avec Azure AD en transmettant les requêtes au point de terminaison Azure AD v2.0 :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Chaque requête qui est envoyée à Azure AD B2C spécifie une **stratégie**. Une stratégie contrôle le comportement d'Azure AD et vous permet d'utiliser ces points de terminaison pour créer un ensemble d'expériences utilisateur hautement personnalisable. Certaines stratégies courantes comprennent les stratégies d'inscription, les stratégies de connexion et les stratégies de modification de profil. Si vous n'êtes pas familiarisé avec les stratégies, consultez l'[infrastructure de stratégie extensible](active-directory-b2c-reference-policies.md) d'Azure AD B2C avant de continuer.

Chaque interaction d’une application avec le point de terminaison v2.0 suit un modèle similaire de niveau supérieur :

1. L'application dirige l'utilisateur vers le point de terminaison v2.0 pour exécuter une [stratégie](active-directory-b2c-reference-policies.md).
2. L'utilisateur exécute la stratégie en fonction de la définition de celle-ci.
4. L’application reçoit un jeton de sécurité quelconque du point de terminaison v2.0.
5. L’application utilise le jeton de sécurité pour accéder aux informations ou à la ressource protégées.
6. Le serveur de ressources valide le jeton de sécurité afin de garantir l’octroi de l’accès.
7. L’application actualise régulièrement le jeton de sécurité.

<!-- TODO: Need a page for libraries to link to -->
Chacune de ces étapes peut varier légèrement en fonction du type d'application conçue. Nous mettons à votre disposition des bibliothèques open source qui vous accompagnent dans ce processus.

## Web Apps
Pour les applications web (.NET, PHP, Java, Ruby, Python, nœud, etc.) qui sont hébergées sur un serveur et accessibles à l'aide d'un navigateur, Azure AD B2C prend en charge [OpenID Connect](active-directory-b2c-reference-protocols.md) pour toutes les expériences utilisateur, y compris la connexion, l'abonnement et la gestion des profils. Dans la mise en œuvre Azure AD B2C de OpenID Connect, votre application web déclenche ces expériences utilisateur en émettant des requêtes d'authentification à Azure AD. Le résultat de la requête est un `id_token`, un jeton de sécurité qui représente l'identité de l'utilisateur et vérifie les informations le concernant sous la forme de revendications :

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
	"name": "John Smith",
	"email": "john.smith@gmail.com",
	"oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
	...
}
```

Pour en savoir plus sur les différents types de jetons et de revendications disponibles pour une application, consultez la page de [référence sur les jetons B2C](active-directory-b2c-reference-tokens.md).

Dans les applications web, chaque exécution d'une [stratégie](active-directory-b2c-reference-policies.md) suit ces étapes de haut niveau :

![Images de couloirs d’application Web](./media/active-directory-b2c-apps/webapp.png)

La validation du jeton id\_token à l'aide d'une clé de signature publique reçue d'Azure AD suffit à garantir l'identité de l'utilisateur. Par ailleurs, cette opération permet de définir un cookie de session pouvant être utilisé pour identifier l'utilisateur sur les demandes de page suivantes.

Pour voir ce scénario en action, exécutez l'un des exemples de code de connexion d'application Web de la section [Prise en main](active-directory-b2c-overview.md#getting-started).

En plus de la connexion simple, une application de serveur Web peut également nécessiter l'accès à un service Web principal. Dans ce cas, l'application web peut exécuter un [flux OpenID Connect](active-directory-b2c-reference-oidc.md) légèrement différent et acquérir des jetons à l'aide de codes d'autorisation et de jetons d'actualisation. Ce scénario est représenté ci-dessous dans la [section API Web](#web-apis).

<!--, and in our [WebApp-WebAPI Getting Started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## API Web
Vous pouvez également utiliser Azure AD B2C pour sécuriser les services Web, comme l'API Web RESTful de votre application. Les API Web peuvent utiliser OAuth 2.0 pour sécuriser leurs données et authentifier les requêtes HTTP entrantes à l'aide de jetons. L'appelant d'une API Web ajoute un jeton dans l'en-tête d'autorisation d'une requête HTTP :

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L'API Web peut ensuite utiliser le jeton pour vérifier l'identité de l'appelant de l'API et extraire les informations à son sujet, à partir des revendications encodées dans le jeton. Pour en savoir plus sur les différents types de jetons et de revendications disponibles pour une application, consultez la page de [référence sur les jetons Azure AD B2C](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]Actuellement, la version préliminaire d'Azure AD B2C prend en charge uniquement les API Web qui sont accessibles par leurs clients connus. Par exemple, votre application dans son ensemble peut inclure une application iOS, une application Android et une API Web principale. Cette architecture est entièrement prise en charge. Ce qui n'est actuellement pas pris en charge c'est d'autoriser un client tiers, comme une autre application iOS, à accéder également à la même API Web. En effet, tous les composants de votre application complète doivent partager un ID d'application unique.

Une API Web peut recevoir des jetons de tous types de clients, notamment des applications Web, des applications de bureau et mobiles, des applications de page unique, des démons côté serveur, et même d'autres API Web. Par exemple, examinons le flux complet d'une application Web appelant une API Web.

![Images de couloirs d’API Web d’application Web](./media/active-directory-b2c-apps/webapi.png)

Pour en savoir plus sur les codes d'autorisation, les jetons d'actualisation et la procédure détaillée de récupération des jetons, consultez cette rubrique sur le [protocole OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Pour savoir comment sécuriser une API Web avec Azure AD B2C, consultez les didacticiels de l'API Web dans notre [section de prise en main](active-directory-b2c-overview.md#getting-started).
	
## Applications mobiles et natives
Les applications installées sur un appareil, comme les applications de bureau et les applications mobiles nécessitent bien souvent l'accès à des services principaux ou des API Web pour le compte d'un utilisateur. Vous pouvez ajouter des expériences de gestion d'identité personnalisée à vos applications natives et appeler en toute sécurité les services principaux à l'aide d'Azure AD B2C et du [flux de code d'autorisation OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Dans ce flux, l'application exécute des [stratégies](active-directory-b2c-reference-policies.md) et reçoit un code d'autorisation d'Azure AD lorsque l'utilisateur exécute la stratégie. Le code d'autorisation représente l'autorisation de l'application d'appeler les services principaux pour le compte de l'utilisateur actuellement connecté. L'application peut ensuite échanger le code d'autorisation dans l'arrière-plan, contre un jeton d'ID et un jeton d'actualisation. L'application peut utiliser le jeton d'ID pour s'authentifier sur une API Web principale dans les requêtes HTTP et solliciter le jeton d'actualisation afin de récupérer de nouveaux jetons d'ID une fois les anciens expirés.

> [AZURE.NOTE]Actuellement, la version préliminaire d'AD B2C Azure prend en charge uniquement l'obtention de jetons d'ID qui sont utilisés pour accéder au service web de l'application. Par exemple, votre application dans son ensemble peut inclure une application iOS, une application Android et une API Web principale. Cette architecture est entièrement prise en charge. Ce qui n'est actuellement pas pris en charge c'est d'autoriser votre application iOS à accéder à une API web tierce à l'aide de jetons d'accès OAuth 2.0. En effet, tous les composants de votre application complète doivent partager un ID d'application unique.

![Images de couloirs d’application native](./media/active-directory-b2c-apps/native.png)

## Limites actuelles de la version préliminaire
Ces types d'applications ne sont actuellement pas pris en charge par la version préliminaire d'Azure AD B2C, mais seront pris en charge lors de la mise à disposition générale. Les autres limites et restrictions associées à la version préliminaire d'Azure AD B2C sont décrites dans l'[article sur les limites](active-directory-b2c-limitations.md).

### Applications à page unique (Javascript)
Plusieurs applications modernes présentent une application frontale à page unique écrite principalement en Javascript, utilisant bien souvent des infrastructures d’application à page unique comme AngularJS, Ember.js, Durantal, etc. Le service Azure AD disponible généralement prend en charge ces applications à l’aide du flux implicite OAuth 2.0. Néanmoins, ce flux n'est pas encore disponible dans Azure AD B2C. Il le sera très prochainement.

### Applications côté démons/serveur
Les applications qui contiennent des processus de longue durée ou qui fonctionnent sans la présence d’un utilisateur doivent également disposer d’un moyen d’accès aux ressources sécurisées, comme les API Web. Ces applications peuvent s'authentifier et récupérer des jetons à l'aide de l'identité d'application (plutôt qu'avec l'identité déléguée d'un utilisateur), avec le flux des informations d'identification du client OAuth 2.0.

Ce flux n'est pas actuellement pas pris en charge par Azure AD B2C. Concrètement, cela signifie que les applications peuvent récupérer des jetons uniquement après l'exécution d'un flux interactif utilisateur. Le flux des informations d’identification du client sera ajouté très prochainement.

### Chaînes d'API Web (On-Behalf-Of)
De nombreuses architectures incluent une API Web qui doit appeler une autre API Web en aval, toutes deux sécurisées par Azure AD. Ce scénario est courant dans les clients natifs qui disposent d'une API Web principale, qui à son tour appelle un service Microsoft Online, comme l'API Graph Azure AD.

Ce scénario d'API Web chaînée peut être pris en charge à l'aide de la concession des informations d'identification du porteur OAuth 2.0 Jwt, également appelé flux On-Behalf-Of. Toutefois, le flux On-Behalf-Of n'est pas actuellement implémenté dans la version préliminaire d'Azure AD B2C.

<!---HONumber=Sept15_HO3-->