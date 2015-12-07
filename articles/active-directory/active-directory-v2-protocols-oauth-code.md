
<properties
	pageTitle="Protocole OAuth du modèle d'application v2.0 | Microsoft Azure"
	description="Création d'applications Web à l'aide de la mise en œuvre du protocole d'authentification OAuth 2.0 d'Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/19/2015"
	ms.author="dastrock"/>

# Modèle d’application v2.0 en version préliminaire : protocoles - flux du code d'autorisation OAuth 2.0

L'octroi d'un code d'autorisation OAuth 2.0 peut servir dans les applications qui sont installées sur un périphérique pour accéder à des ressources protégées, comme des API Web. Avec la mise en œuvre du modèle d'application de v2.0 d'OAuth 2.0, vous pouvez ajouter une connexion et un accès API à vos applications mobiles et de bureau. Ce guide est indépendant du langage. Il explique comment envoyer et recevoir des messages HTTP sans utiliser l'une de nos bibliothèques open source.

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]Ces informations s’appliquent à la version préliminaire publique du modèle d’application v2.0. Pour obtenir des instructions sur l’intégration avec le service Azure AD généralement disponible, consultez le [Guide du développeur Azure AD](active-directory-developers-guide.md).

Le flux de code d'autorisation OAuth 2.0 est décrit dans la [section 4.1 de la spécification OAuth 2.0](http://tools.ietf.org/html/rfc6749). Il est utilisé pour exécuter des activités d'authentification et d'autorisation dans la majorité des types d'applications, notamment les [applications Web](active-directory-v2-flows.md#web-apps) et les [applications installées de façon native](active-directory-v2-flows.md#mobile-and-native-apps). Il permet aux applications d’acquérir de manière sûre les jetons d’accès pouvant être utilisés pour accéder aux ressources sécurisées à l’aide du modèle d’application v2.0.



## Demander un code d’autorisation
Le flux de code d'autorisation commence par le client dirigeant l'utilisateur vers le point de terminaison `/authorize`. Dans cette requête, le client indique les autorisations dont il a besoin d’obtenir auprès de l’utilisateur :

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		  // Your registered Application Id
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob 	  // Your registered Redirect Uri, url encoded
&response_mode=query							        // 'query', 'form_post', or 'fragment'
&scope=											     // See table below
openid%20
offline_access%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				   // Any value provided by your app
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | required | L'ID d'application que le portail d'inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| response\_type | requis | Doit inclure `code` pour le flux de code d'autorisation. |
| redirect\_uri | requis | L’URI de redirection de votre application, vers lequel où votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope | required | Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique au point de terminaison v2.0 la ressource et les autorisations d’accès à cette ressource sollicitées. L'étendue prend la forme suivante : `<app identifier URI>/<scope value>`. Dans l'exemple ci-dessus, l'identificateur d'application associé à l'API Graph Azure AD est utilisé, `https://graph.windows.net`, et deux autorisations sont demandées : `directory.read` et `directory.write`. Pour obtenir une explication plus détaillée des étendues, consultez la [référence sur l'étendue du modèle d'application v2.0](active-directory-v2-scopes.md). |
| response\_mode | recommandé | Spécifie la méthode à utiliser pour envoyer le code d’autorisation résultant à votre application. Il peut s’agir de « query », « form\_post » ou de « fragment ».
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| prompt | facultatif | Indique le type d’interaction utilisateur requis. Les seules valeurs valides à ce stade sont login, none et consent. `prompt=login` oblige l'utilisateur à saisir ses informations d'identification lors de cette requête, annulant de fait l'authentification unique. Avec `prompt=none`, c’est le comportement inverse. Cette valeur vous garantit qu'aucune invite interactive d'aucune sorte n'est présentée à l'utilisateur. Si la requête ne peut pas être exécutée en mode silencieux au moyen d'une authentification unique, le point de terminaison v2.0 renvoie une erreur. `prompt=consent` déclenche l'affichage de la boîte de dialogue de consentement OAuth après la connexion de l'utilisateur, afin de lui demander d'octroyer des autorisations à l'application. |
| login\_hint | facultatif | Peut être utilisé pour remplir au préalable le champ réservé au nom d'utilisateur/à l'adresse électronique de la page de connexion de l'utilisateur. |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification. Le point de terminaison v2.0 garantit également que l'utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope`. Si l’utilisateur n’a pas accepté l’une ou plusieurs de ces autorisations, le point de terminaison lui demande de corriger ce manquement. Les détails sur les [autorisations, les consentements et les applications mutualisées sont disponibles ici](active-directory-v2-scopes.md).

Une fois que l'utilisateur a procédé à l'authentification et accordé son consentement, le point de terminaison v2.0 renvoie une réponse à votre application à l'élément `redirect_uri` indiqué, à l'aide de la méthode spécifiée dans le paramètre `response_mode`.

Une réponse réussie utilisant `response_mode=query` se présente ainsi :

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												      // the value provided in the request
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| code | Le code d’autorisation demandé par l’application. L’application peut utiliser ce code d’autorisation pour demander un jeton d’accès pour la ressource cible. Les codes d’autorisation présentent une durée de vie très courte. Généralement, ils expirent au bout de 10 minutes. |
| session\_state | Une valeur unique identifiant la session utilisateur actuelle. Cette valeur est un GUID, mais doit être traitée comme une valeur opaque n’ayant fait l’objet d’aucune analyse. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

Des réponses d'erreur peuvent également être envoyées à l'élément `redirect_uri`, de manière à ce que l'application puisse les traiter de manière appropriée :

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

## Demander un jeton d’accès
Maintenant que vous avez acquis un code d'autorisation (authorization\_code) et que l'utilisateur vous a octroyé une autorisation, vous pouvez échanger `code` contre un élément `access_token` sur la ressource souhaitée, en envoyant une demande `POST` au point de terminaison `/token` :

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------------- |
| client\_id | required | L'ID d'application que le portail d'inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| grant\_type | required | Doit être `authorization_code` pour le flux de code d'autorisation. |
| scope | required | Une liste d’étendues séparées par des espaces. Les étendues demandées dans ce tronçon doivent être équivalentes aux étendues demandées dans le premier tronçon, ou correspondre à un sous-ensemble de ces dernières. Si les étendues spécifiées dans cette requête couvrent plusieurs serveurs de ressources, le point de terminaison v2.0 renvoie un jeton pour la ressource spécifiée dans la première étendue. Pour obtenir une explication plus détaillée des étendues, consultez les [autorisations, consentements et étendues](active-directory-v2-scopes.md). |
| code | required | Le code d’autorisation acquis dans le premier tronçon du flux. |
| client\_secret | requis pour les applications Web | Le secret d’application que vous avez créé dans le portail d’inscription des applications pour votre application. Il ne doit pas être utilisé dans une application native, car les clés secrètes client ne peuvent pas être stockées de manière sûre sur les appareils. Il est requis pour les applications Web et les API Web, qui présentent la capacité de stocker de manière sûre les clés secrètes client sur le côté serveur. |

Une réponse de jeton réussie se présente ainsi :

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
	"id_token_expires_in": "3599"
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access\_token | Le jeton d’accès demandé. L’application peut utiliser ce jeton pour procéder à l’authentification sur la ressource sécurisée, comme une API Web. |
| token\_type | Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur. |
| expires\_in | La durée de validité (en secondes) du jeton d’accès. |
| scope | L’étendue de validité du jeton d’accès. |
| refresh\_token | Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons d’accès supplémentaires après l’expiration du jeton d’accès actuel. Les jetons d’actualisation sont durables, et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. Pour plus d'informations, consultez la page de [référence sur les jetons v2.0](active-directory-v2-tokens.md). |
| id\_token | Un jeton Web JSON non signé (JWT). L’application peut décoder les segments de ce jeton à l’aide d’un décodeur base64Url afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais ne peut aucunement les utiliser pour les limites d’autorisation ou de sécurité. Pour en savoir plus sur les id\_tokens, consultez la page de [référence des jetons du modèle d’application v2.0](active-directory-v2-tokens.md). |
| id\_token\_expires\_in | La durée de validité (en secondes) de l'id\_token. |


Les réponses d’erreur se présentent comme suit :

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

## Utiliser le jeton d’accès
Maintenant que vous avez acquis un jeton `access_token`, vous pouvez l'utiliser dans des requêtes dirigées vers des API Web en l'incluant dans l'en-tête `Authorization` :

```
GET /contoso.onmicrosoft.com/users
Host: https://graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## Actualiser le jeton d’accès
Les jetons d’accès présentent une durée de vie courte. Après leur expiration, vous devez les actualiser afin de pouvoir continuer à accéder aux ressources. Pour ce faire, envoyez une nouvelle requête `POST` au point de terminaison `/token` en fournissant l'élément `refresh_token` au lieu de l'élément `code` :

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | -------- |
| client\_id | required | L'ID d'application que le portail d'inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| grant\_type | required | Doit inclure `refresh_token` pour ce tronçon du flux de code d'autorisation. |
| scope | required | Une liste d’étendues séparées par des espaces. Les étendues demandées dans ce tronçon doivent être équivalentes aux étendues demandées dans le tronçon de requête authorization\_code d’origine, ou correspondre à un sous-ensemble de ces dernières. Si les étendues spécifiées dans cette requête couvrent plusieurs serveurs de ressources, le point de terminaison v2.0 renvoie un jeton pour la ressource spécifiée dans la première étendue. Pour obtenir une explication plus détaillée des étendues, consultez les [autorisations, consentements et étendues](active-directory-v2-scopes.md). |
| refresh\_token | required | Le jeton d’actualisation que vous avez acquis dans le second tronçon du flux. |
| client\_secret | requis pour les applications Web | Le secret d’application que vous avez créé dans le portail d’inscription des applications pour votre application. Il ne doit pas être utilisé dans une application native, car les clés secrètes client ne peuvent pas être stockées de manière sûre sur les appareils. Il est requis pour les applications Web et les API Web, qui présentent la capacité de stocker de manière sûre les clés secrètes client sur le côté serveur. |

Une réponse de jeton réussie se présente ainsi :

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
	"id_token_expires_in": "3599"
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access\_token | Le jeton d’accès demandé. L’application peut utiliser ce jeton pour procéder à l’authentification sur la ressource sécurisée, comme une API Web. |
| token\_type | Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur. |
| expires\_in | La durée de validité (en secondes) du jeton d’accès. |
| scope | L’étendue de validité du jeton d’accès. |
| refresh\_token | Un nouveau jeton d’actualisation OAuth 2.0. Vous devez remplacer l’ancien jeton d’actualisation par ce nouveau jeton d’actualisation nouvellement acquis, afin de vous assurer que vos jetons d’actualisation demeurent valident le plus longtemps possible. |
| id\_token | Un jeton Web JSON non signé (JWT). L’application peut décoder les segments de ce jeton à l’aide d’un décodeur base64Url afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais ne peut aucunement les utiliser pour les limites d’autorisation ou de sécurité. Pour en savoir plus sur les id\_tokens, consultez la page de [référence des jetons du modèle d’application v2.0](active-directory-v2-tokens.md). |
| id\_token\_expires\_in | La durée de validité (en secondes) de l'id\_token. |

Les réponses d’erreur se présentent comme suit :

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

## Résumé
À un niveau élevé, le flux d'authentification complet pour une application native/mobile ressemble est semblable à l'illustration suivante :

![Flux de code d’authentification OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

<!---HONumber=AcomDC_1125_2015-->