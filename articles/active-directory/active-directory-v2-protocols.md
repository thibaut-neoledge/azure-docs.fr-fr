<properties
	pageTitle="Modèle d’application v2.0 - Protocoles | Microsoft Azure"
	description="Les protocoles pris en charge par la version d’évaluation publique du modèle d’application Azure AD v2.0."
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
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Version d’évaluation du modèle d’application v2.0 : Protocoles - OAuth 2.0 et OpenID Connect

Le modèle d’application v2.0 fournit l’identité en tant que service pour vos applications en prenant en charge des protocoles standard, OpenID Connect et OAuth 2.0. Bien que ce service soit distribué en standard, vous pouvez constater de subtiles différences entre deux implémentations différentes de ces protocoles. Ces informations vous seront utiles si vous choisissez d’écrire votre code en envoyant ou en traitant directement des requêtes HTTP, non en utilisant l’une de nos bibliothèques open source. <!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]Ces informations s’appliquent à la version préliminaire publique du modèle d’application v2.0. Pour obtenir des instructions sur l’intégration avec le service Azure AD généralement disponible, consultez le [Guide du développeur Azure AD](active-directory-developers-guide.md).

## Jetons
L’implémentation d’OAuth 2.0 et OpenID Connect du modèle d’application v2.0 utilise massivement les jetons porteurs, y compris des jetons porteurs représentés sous forme de JWT. Un jeton porteur est un jeton de sécurité léger qui octroie l’accès à une ressource protégée au « porteur ». En ce sens, le « porteur » désigne toute partie qui peut présenter le jeton. Une partie doit certes d’abord s’authentifier auprès d’Azure AD pour recevoir le jeton porteur, mais si les mécanismes nécessaires à la sécurité du jeton lors de la transmission et du stockage ne sont pas en place, il peut être intercepté et utilisé par une partie non autorisée. Bien que certains jetons de sécurité intègrent un mécanisme de protection contre l’utilisation par des parties non autorisées, les jetons porteurs n’en sont pas dotés et doivent donc être acheminés sur un canal sécurisé, par exemple à l’aide du protocole TLS (HTTPS). Si un jeton porteur est transmis en clair, une partie malveillante peut utiliser une attaque d’intercepteur afin de s’approprier le jeton et de l’utiliser pour accéder sans autorisation à une ressource protégée. Les mêmes principes de sécurité s’appliquent au stockage ou à la mise en cache des jetons porteurs pour une utilisation ultérieure. Veillez systématiquement à ce que votre application transmette et stocke les jetons porteurs de manière sécurisée. Pour en savoir plus sur les aspects de sécurité des jetons porteurs, consultez [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

Pour plus d’informations sur les différents types de jetons utilisés dans le modèle d’application v2.0, consultez la page de [Référence sur les jetons du modèle d’application v2.0](active-directory-v2-tokens.md).

## Concepts de base
Toutes les applications qui utilisent le modèle d’application v2.0 doivent être inscrites à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Le processus d’inscription des applications collecte quelques valeurs et les affecte pour vos applications :

- un **ID d’application** qui identifie de manière unique votre application ;
- un **URI de redirection** ou un **identificateur de package** pouvant être utilisés pour diriger les réponses vers votre application ;
- quelques valeurs spécifiques au scénario. Pour plus de détails, découvrez comment [inscrire une application](active-directory-v2-app-registration.md).

Une fois inscrite, l’application communique avec Azure AD afin de transmettre les requêtes au point de terminaison v2.0 :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Dans presque tous les flux OAuth et OpenID Connect, quatre parties sont concernées par l’échange :

![Rôles OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- Le **serveur d’autorisation** est le point de terminaison v2.0. Il est chargé de garantir l’identité de l’utilisateur, l’octroi et la révocation de l’accès aux ressources et l’émission de jetons. Il est également connu sous le nom du fournisseur d’identité. Il traite de manière sécurisée les informations de l’utilisateur, leur accès et les relations de confiance entre les parties des flux.
- Le **propriétaire de la ressource** est généralement l’utilisateur final. Il s’agit de la partie détentrice des données, qui a le pouvoir d’autoriser les tierces parties à accéder à ces données ou à cette ressource.
- Le **Client OAuth** est votre application, identifiée par son ID d’application. Il s’agit généralement de la partie avec laquelle l’utilisateur final interagit ; elle demande des jetons provenant du serveur d’autorisation. Le client doit se voir octroyer une autorisation d’accès à la ressource par le propriétaire de cette dernière.
- Le **serveur de ressources** héberge la ressource ou les données. Il approuve le serveur d’autorisation pour authentifier et autoriser de manière sûre le client OAuth et utilise les jetons d’accès porteurs pour garantir l’octroi de l’accès à une ressource.

## Flux de code d’autorisation OAuth2
Le flux de code d’autorisation OAuth 2.0 est décrit dans la [section 4.1 de la spécification OAuth 2.0](http://tools.ietf.org/html/rfc6749). Il est utilisé pour exécuter les activités d’authentification et d’autorisation avec la majorité des types d’applications, notamment les [applications Web](active-directory-v2-flows.md#web-apps) et les [applications installées de façon native](active-directory-v2-flows.md#mobile-and-native-apps). Il permet aux applications d’acquérir de manière sûre les jetons d’accès pouvant être utilisés pour accéder aux ressources sécurisées à l’aide du modèle d’application v2.0.

Voici le flux complet pour une application native ; chaque requête est détaillée dans les sections ci-dessous : ![Flux de code d’authentification OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

#### Demander un code d’autorisation
Le flux de code d’autorisation commence par le client dirigeant l’utilisateur vers le point de terminaison `/authorize`. Dans cette requête, le client indique les autorisations dont il a besoin d’obtenir auprès de l’utilisateur :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=											   // See table below for scope parameter details.
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value provided by your app
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | required | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| response\_type | requis | Doit inclure `code` pour le flux de code d’autorisation. |
| redirect\_uri | requis | L’URI de redirection de votre application, vers lequel où votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope | required | Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique au point de terminaison v2.0 la ressource et les autorisations d’accès à cette ressource sollicitées. L’étendue prend la forme suivante : `<app identifier URI>/<scope value>`. Dans l’exemple ci-dessus, l’identificateur d’application associé à l’API Graph Azure AD est utilisé, `https://graph.windows.net`, et deux autorisations sont demandées : `directory.read` et `directory.write`. Pour obtenir une explication plus détaillée des étendues, reportez-vous à c. |
| response\_mode | recommandé | Spécifie la méthode à utiliser pour envoyer le code d’autorisation résultant à votre application. Il peut s’agir de « query », « form\_post » ou de « fragment ».
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| prompt | facultatif | Indique le type d’interaction utilisateur requis. La seule valeur valide pour l’instant est « login », ce qui oblige l’utilisateur à saisir ses informations d’identification sur cette requête. L’authentification unique ne prendra pas effet. |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification. Le point de terminaison v2.0 garantit également que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope`. Si l’utilisateur n’a pas accepté l’une ou plusieurs de ces autorisations, le point de terminaison lui demande de corriger ce manquement. Les détails sur les [autorisations, les consentements et les applications mutualisées sont disponibles ici](active-directory-v2-scopes.md).

Une fois que l’utilisateur a procédé à l’authentification et accordé son consentement, le point de terminaison v2.0 renvoie une réponse à votre application à l’élément `redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

Une réponse réussie utilisant `response_mode=query` se présente ainsi :

```
GET https://localhost/myapp/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  							// the value provided in the request
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| code | Le code d’autorisation demandé par l’application. L’application peut utiliser ce code d’autorisation pour demander un jeton d’accès pour la ressource cible. Les codes d’autorisation présentent une durée de vie très courte. Généralement, ils expirent au bout de 10 minutes. |
| session\_state | Une valeur unique identifiant la session utilisateur actuelle. Cette valeur est un GUID, mais doit être traitée comme une valeur opaque n’ayant fait l’objet d’aucune analyse. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri`, de manière à ce que l’application puisse les traiter de manière appropriée :

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

#### Demander un jeton d’accès
Maintenant que vous avez acquis un code d’autorisation et que l’utilisateur vous a octroyé une autorisation, vous pouvez échanger `code` contre un élément `access_token` sur la ressource souhaitée, en envoyant une demande `POST` au point de terminaison `/token` :

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
| client\_id | required | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| grant\_type | required | Doit être `authorization_code` pour le flux de code d’autorisation. |
| scope | required | Une liste d’étendues séparées par des espaces. Les étendues demandées dans ce tronçon doivent être équivalentes aux étendues demandées dans le premier tronçon, ou correspondre à un sous-ensemble de ces dernières. Si les étendues spécifiées dans cette requête couvrent plusieurs serveurs de ressources, le point de terminaison v2.0 renvoie un jeton pour la ressource spécifiée dans la première étendue. Pour obtenir une explication plus détaillée des étendues, consultez les [autorisations, consentements et étendues](active-directory-v2-scopes.md). |
| code | required | Le code d’autorisation acquis dans le premier tronçon du flux. |
| client\_secret | requis pour les applications Web | Le secret d’application que vous avez créé dans le portail d’inscription des applications pour votre application. Il ne doit pas être utilisé dans une application native, car les clés secrètes client ne peuvent pas être stockées de manière sûre sur les appareils. Il est requis pour les applications Web et les API Web, qui présentent la capacité de stocker de manière sûre les clés secrètes client sur le côté serveur. |

Une réponse de jeton réussie se présente ainsi :

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access\_token | Le jeton d’accès demandé. L’application peut utiliser ce jeton pour procéder à l’authentification sur la ressource sécurisée, comme une API Web. |
| token\_type | Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur. |
| expires\_in | La durée de validité (en secondes) du jeton d’accès. |
| expires\_on | L’heure d’expiration du jeton d’accès. La date est représentée en tant que nombre de secondes à partir de l’heure de l’époque. |
| scope | L’étendue de validité du jeton d’accès. |
| refresh\_token | Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons d’accès supplémentaires après l’expiration du jeton d’accès actuel. Les jetons d’actualisation sont durables, et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. Pour plus d’informations, consultez la page de [référence sur les jetons v2.0](active-directory-v2-tokens.md). |
| id\_token | Un jeton Web JSON non signé (JWT). L’application peut décoder les segments de ce jeton à l’aide d’un décodeur base64Url afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais ne peut aucunement les utiliser pour les limites d’autorisation ou de sécurité. Pour en savoir plus sur les jetons id-token, consultez la page de [référence des jetons du modèle d’application v2.0](active-directory-v2-tokens.md). |

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

#### Utiliser le jeton d’accès
Maintenant que vous êtes parvenu à acquérir un jeton `access_token`, vous pouvez utiliser le jeton dans les requêtes dirigées vers les API Web en l’incluant dans l’en-tête `Authorization` :

```
GET /contoso.onmicrosoft.com/users
Host: https://graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

#### Actualiser le jeton d’accès
Les jetons d’accès présentent une durée de vie courte. Après leur expiration, vous devez les actualiser afin de pouvoir continuer à accéder aux ressources. Pour ce faire, envoyez une nouvelle requête `POST` au point de terminaison `/authorize`, en fournissant l’élément `refresh_token` au lieu de l’élément `code`:

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
| client\_id | required | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| grant\_type | required | Doit inclure `refresh_token` pour ce tronçon du flux de code d’autorisation. |
| scope | required | Une liste d’étendues séparées par des espaces. Les étendues demandées dans ce tronçon doivent être équivalentes aux étendues demandées dans le tronçon de requête authorization\_code d’origine, ou correspondre à un sous-ensemble de ces dernières. Si les étendues spécifiées dans cette requête couvrent plusieurs serveurs de ressources, le point de terminaison v2.0 renvoie un jeton pour la ressource spécifiée dans la première étendue. Pour obtenir une explication plus détaillée des étendues, consultez les [autorisations, consentements et étendues](active-directory-v2-scopes.md). |
| refresh\_token | required | Le jeton d’actualisation que vous avez acquis dans le second tronçon du flux. |
| client\_secret | requis pour les applications Web | Le secret d’application que vous avez créé dans le portail d’inscription des applications pour votre application. Il ne doit pas être utilisé dans une application native, car les clés secrètes client ne peuvent pas être stockées de manière sûre sur les appareils. Il est requis pour les applications Web et les API Web, qui présentent la capacité de stocker de manière sûre les clés secrètes client sur le côté serveur. |

Une réponse de jeton réussie se présente ainsi :

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access\_token | Le jeton d’accès demandé. L’application peut utiliser ce jeton pour procéder à l’authentification sur la ressource sécurisée, comme une API Web. |
| token\_type | Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur. |
| expires\_in | La durée de validité (en secondes) du jeton d’accès. |
| expires\_on | L’heure d’expiration du jeton d’accès. La date est représentée en tant que nombre de secondes à partir de l’heure de l’époque. |
| scope | L’étendue de validité du jeton d’accès. |
| refresh\_token | Un nouveau jeton d’actualisation OAuth 2.0. Vous devez remplacer l’ancien jeton d’actualisation par ce nouveau jeton d’actualisation nouvellement acquis, afin de vous assurer que vos jetons d’actualisation demeurent valident le plus longtemps possible. |
| id\_token | Un jeton Web JSON non signé (JWT). L’application peut décoder les segments de ce jeton à l’aide d’un décodeur base64Url afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais ne peut aucunement les utiliser pour les limites d’autorisation ou de sécurité. Pour en savoir plus sur les jetons id\_tokens, consultez la page de [référence des jetons du modèle d’application v2.0](active-directory-v2-tokens.md). |

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






## Flux de connexion OpenID Connect
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) étend le protocole d’*autorisation* OAuth 2.0 pour l’utiliser en tant que protocole d’*authentification*, ce qui vous permet de procéder à une authentification unique à l’aide d’OAuth. Il introduit le concept de jeton `id_token`,qui est un jeton de sécurité permettant au client de vérifier l’identité de l’utilisateur et d’obtenir ses informations basiques de profil.

OpenID Connect pour le modèle d’application v2.0 constitue le moyen recommandé d’implémenter la connexion à une [application Web](active-directory-v2-flows.md#web-apps). Le flux de connexion le plus simple contient les étapes suivantes :

![Couloirs OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

#### Envoyer la requête de connexion
Lorsque votre application Web a besoin d’authentifier l’utilisateur, elle peut le diriger vers le point de terminaison `/authorize`. Cette requête est similaire au premier tronçon du [flux de code d’autorisation OAuth 2.0](#oauth2-authorization-code-flow), avec quelques différences importantes : - La requête doit inclure l’étendue `openid` dans le paramètre `scope`. - Le paramètre `response_type` doit inclure `id_token` - La requête doit inclure le paramètre `nonce`

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid										 // Translates to the 'Read your profile' permission
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | required | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| response\_type | required | Doit inclure `id_token` pour la connexion à OpenID Connect. Il peut également inclure d’autres types de réponses, comme décrit dans la section [Flux du code OpenID Connect avec OAuth](#OpenID-Connect-with-OAuth-Code-Flow). |
| redirect\_uri | required | L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope | required | Une liste d’étendues séparées par des espaces. Pour OpenID Connect, vous devez inclure l’étendue `openid`, qui correspond à l’autorisation de connexion et de lecture de votre profil dans l’interface utilisateur de consentement. Vous pouvez également inclure d’autres étendues dans cette requête pour solliciter le consentement, tel que décrit dans la section [Flux du code OpenID Connect avec OAuth](#OpenID-Connect-with-OAuth-Code-Flow). |
| nonce | required | Une valeur incluse dans la requête, générée par l’application, qui sera incluse dans le jeton id\_token résultant en tant que revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique, aléatoire pouvant être utilisé pour identifier l’origine de la requête. |
| response\_mode | recommandé | Spécifie la méthode à utiliser pour envoyer le code d’autorisation résultant à votre application. Il peut s’agir de « query », « form\_post » ou de « fragment ».  
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| prompt | facultatif | Indique le type d’interaction utilisateur requis. La seule valeur valide pour l’instant est « login », ce qui oblige l’utilisateur à saisir ses informations d’identification sur cette requête. L’authentification unique ne prendra pas effet. |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification. Le point de terminaison v2.0 garantit également que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope`. Si l’utilisateur n’a pas accepté l’une ou plusieurs de ces autorisations, le point de terminaison lui demande de corriger ce manquement. Les détails sur les [autorisations, les consentements et les applications mutualisées sont disponibles ici](active-directory-v2-scopes.md).

Une fois que l’utilisateur a procédé à l’authentification et accordé son consentement, le point de terminaison v2.0 renvoie une réponse à votre application à l’élément `redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

Une réponse réussie utilisant `response_mode=query` se présente ainsi :

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id\_token | Le jeton id-token que l’application a demandé. Vous pouvez utiliser ce jeton id\_token pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. Pour obtenir plus de détails sur les jetons id-token et leur contenu, consultez la page de [référence des jetons de point de terminaison v2.0](active-directory-v2-tokens.md). |
| session\_state | Une valeur unique identifiant la session utilisateur actuelle. Cette valeur est un GUID, mais doit être traitée comme une valeur opaque n’ayant fait l’objet d’aucune analyse. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri`, de manière à ce que l’application puisse les traiter de manière appropriée :

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

#### Valider le jeton id\_token
La réception du jeton id\_token ne suffit pas à authentifier l’utilisateur. Vous devez valider la signature du jeton id\_token et vérifier la conformité des revendications du jeton par rapport à la configuration requise de votre application. Le point de terminaison v2.0 utilise les [jetons Web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et le chiffrement de clés publiques pour signer les jetons et vérifier leur validité.

Le modèle d’application v2.0 présente un point de terminaison de métadonnées OpenID Connect, qui permet à une application de récupérer les informations relatives au modèle d’application v2.0 lors de l’exécution. Ces informations incluent les points de terminaison, le contenu des jetons et les clés de signature de jetons. Le point de terminaison des métadonnées contient un document JSON stocké à l’emplacement suivant :

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`

Une des propriétés de ce document de configuration est l’élément `jwks_uri`, dont la valeur pour le modèle d’application v2.0 est la suivante :

`https://login.microsoftonline.com/common/discovery/v2.0/keys`.

Vous pouvez utiliser les clés publiques RSA256 de ce point de terminaison pour valider la signature de ce jeton id\_token. En permanence, ce point de terminaison comporte plusieurs clés, chacune étant identifiée par un élément `kid`. L’en-tête de ce jeton id\_token contient également une revendication `kid`, qui identifie la clé utilisée pour la signature du jeton id\_token.

Pour plus d’informations, consultez la [référence des jetons du modèle d’application v2.0](active-directory-v2-tokens.md), comprenant notamment des données relatives aux [Jetons de validation](active-directory-v2-tokens.md#validating-tokens) et des [Informations importantes sur la substitution des clés de signature](active-directory-v2-tokens.md#validating-tokens). <!--TODO: Improve the information on this-->

Une fois que vous avez validé la signature du jeton id\_token, il vous faudra vérifier quelques revendications :

- Vous devez valider la revendication `nonce` afin d’empêcher les attaques par relecture de jetons. Sa valeur doit correspondre à ce que vous avez spécifié dans la requête de connexion.
- Vous devez valider la revendication `aud` afin de vous assurer que le jeton id\_token a été émis pour votre application. Sa valeur doit correspondre à l’élément `client_id` de votre application.
- Vous devez valider les revendications `iat` et `exp` afin de vous assurer que le jeton id\_token n’a pas expiré.

En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires. Voici quelques validations courantes :

- S’assurer que l’utilisateur/l’organisation s’est inscrit pour l’application.
- S’assurer que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
- S’assurer de l’utilisation d’une force certaine d’authentification, comme une authentification multifacteur.

Pour plus d’informations sur les revendications dans un jeton id\_token, consultez la page de [référence des jetons du modèle d’application v2.0](active-directory-v2-tokens.md).

Une fois que vous avez complètement validé le jeton id\_token, vous pouvez démarrer une session avec l’utilisateur et utiliser les revendications du jeton id\_token pour récupérer les informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour l’affichage, les enregistrements, les autorisations, etc.

#### Envoyer une requête de déconnexion

Le `end_session_endpoint` OpenIdConnect n’est pas actuellement pris en charge par la version d’évaluation du modèle d’application v2.0. Cela signifie que votre application ne peut pas envoyer une requête au point de terminaison v2.0 afin d’arrêter une session utilisateur et d’effacer les cookies définis par le point de terminaison v2.0. Pour déconnecter un utilisateur, il suffit à votre application de terminer sa propre session avec l’utilisateur, sans affecter aucunement la session de l’utilisateur avec le point de terminaison v2.0. Lors de la tentative de connexion suivante de l’utilisateur, une page l’invitant à sélectionner un compte s’affiche ; ses comptes de connexion actifs sont répertoriés. Sur cette page, l’utilisateur peut choisir de se déconnecter d’un compte, et de terminer la session avec le point de terminaison v2.0.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## Flux du code OpenID Connect avec OAuth
Beaucoup d’applications Web nécessitent une connexion de l’utilisateur, puis un accès au service Web pour le compte de cet utilisateur à l’aide d’OAuth. Ce scénario combine les deux sections précédentes. Il utilise OpenID Connect pour l’authentification de l’utilisateur tout en récupérant un code d’autorisation pouvant être sollicité pour obtenir des jetons d’accès à l’aide du flux de code d’autorisation OAuth :

![Couloirs OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

Ce flux diffère légèrement des sections ci-dessus, dans lesquelles vous envoyez la requête de connexion.

#### Envoyer la requête de connexion
Lorsque votre application Web a besoin d’authentifier l’utilisateur et d’obtenir les autorisations d’accès aux ressources, elle peut diriger l’utilisateur vers le point de terminaison `/authorize`. Dans ce cas, votre application doit demander un élément `id_token` et un élément `code` dans la réponse :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid%20										 // Include both 'openid' and scopes your app needs  
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | ----------------- |
| client\_id | required | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| response\_type | required | Doit inclure `id_token` et `code` pour ce scénario. |
| redirect\_uri | required | L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope | required | Une liste d’étendues séparées par des espaces. Pour OpenID Connect, vous devez inclure l’étendue `openid`, qui correspond à l’autorisation de connexion et de lecture de votre profil dans l’interface utilisateur de consentement. Vous devez également inclure des étendues pour les ressources auxquelles votre application doit accéder. Pour obtenir une explication plus détaillée des étendues, consultez les [autorisations, consentements et étendues](active-directory-v2-scopes.md). |
| nonce | required | Une valeur incluse dans la requête, générée par l’application, qui sera incluse dans le jeton id\_token résultant en tant que revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique, aléatoire pouvant être utilisé pour identifier l’origine de la requête. |
| response\_mode | recommandé | Spécifie la méthode à utiliser pour envoyer le code d’autorisation résultant à votre application. Il peut s’agir de « query », « form\_post » ou de « fragment ».  
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| prompt | facultatif | Indique le type d’interaction utilisateur requis. La seule valeur valide pour l’instant est « login », ce qui oblige l’utilisateur à saisir ses informations d’identification sur cette requête. L’authentification unique ne prendra pas effet. |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification. Le point de terminaison v2.0 garantit également que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope`. Si l’utilisateur n’a pas accepté l’une ou plusieurs de ces autorisations, le point de terminaison lui demande de corriger ce manquement. Les détails sur les [autorisations, les consentements et les applications mutualisées sont disponibles ici](active-directory-v2-scopes.md).

Une fois que l’utilisateur a procédé à l’authentification et accordé son consentement, le point de terminaison v2.0 renvoie une réponse à votre application à l’élément `redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

Une réponse réussie utilisant `response_mode=query` se présente ainsi :

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id\_token | Le jeton id-token que l’application a demandé. Vous pouvez utiliser ce jeton id\_token pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. Pour plus d’informations sur les jetons id\_token et leur contenu, consultez la page de [référence des jetons du modèle d’application v2.0](active-directory-v2-tokens.md). |
| code | Le code d’autorisation demandé par l’application. L’application peut utiliser ce code d’autorisation pour demander un jeton d’accès pour la ressource cible. Les codes d’autorisation présentent une durée de vie très courte. Généralement, ils expirent au bout de 10 minutes. |
| session\_state | Une valeur unique identifiant la session utilisateur actuelle. Cette valeur est un GUID, mais doit être traitée comme une valeur opaque n’ayant fait l’objet d’aucune analyse. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri`, de manière à ce que l’application puisse les traiter de manière appropriée :

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

#### Valider le jeton id\_token
Ce processus correspond exactement à celui décrit plus haut dans le [Flux de connexion OpenID Connect](#OpenID-Connect-Sign-In-Flow).

#### Envoyer une requête de déconnexion
Ce processus correspond exactement à celui décrit plus haut dans le [Flux de connexion OpenID Connect](#OpenID-Connect-Sign-In-Flow).

#### Demander un jeton d’accès
Ce processus correspond exactement à celui décrit plus haut dans le [Flux de code d’autorisation OAuth2.0](#oauth2-authorization-code-flow).

#### Utiliser le jeton d’accès
Ce processus correspond exactement à celui décrit plus haut dans le [Flux de code d’autorisation OAuth 2.0](#oauth2-authorization-code-flow).

#### Actualiser le jeton d’accès
Ce processus correspond exactement à celui décrit plus haut dans le [Flux de code d’autorisation OAuth 2.0](#oauth2-authorization-code-flow).





## Flux d’octroi des informations d’identification du client OAuth2
L’octroi des informations d’identification du client OAuth 2.0 est décrit dans la [spécification OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4). Il s’avère utile pour les processus de longue durée et d’autres scénarios de serveur à serveur, dans lesquels l’application peut s’authentifier sur une ressource à l’aide de son propre ID d’application, non de l’identité déléguée de l’utilisateur.

Ce flux n’est pas actuellement pris en charge par la version d’évaluation du modèle d’application v2.0. Pour observer son fonctionnement dans le service Azure AD disponible généralement, consultez cet [exemple de code Azure AD](https://github/com/AzureADSamples/Daemon-DotNet).

## Flux implicite OAuth2
Le flux implicite OAuth 2.0 est décrit dans la [spécification OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2). Il est souvent utilisé pour les applications à page unique/Javascript qui s’exécutent dans un navigateur.

Ce flux n’est pas actuellement pris en charge par la version d’évaluation du modèle d’application v2.0. Pour observer son fonctionnement dans le service Azure AD disponible généralement, consultez cet [exemple de code Azure AD](active-directory-devquickstarts-angular.md).

## Octroi des informations de mot de passe du propriétaire de la ressource OAuth2
L’octroi des informations de mot de passe du propriétaire de la ressource OAuth 2.0 est décrit dans la [spécification OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.3). Il permet à une application d’acquérir des jetons d’accès en fournissant un nom d’utilisateur et un mot de passe dans la demande de jeton.

Ce flux n’est pas actuellement pris en charge par la version d’évaluation du modèle d’application v2.0. Pour observer son fonctionnement dans le service Azure AD disponible généralement, consultez cet [exemple de code Azure AD](https://github.com/AzureADSamples/NativeClient-Headless-DotNet).

## Flux « Au nom de » OAuth2
Le flux « Au nom de », ou l’octroi des informations d’identification du porteur JWT, est décrit dans ce [brouillon sur l’octroi de l’extension OAuth 2.0](https://tools.ietf.org/html/draft-ietf-oauth-jwt-bearer-12). Il permet à une API Web recevant un jeton d’accès de l’utiliser en tant qu’information d’identification pour acquérir des jetons d’accès à d’autres ressources. Ainsi, une API Web peut appeler en toute sécurité une autre API Web en aval.

Ce flux n’est pas actuellement pris en charge par la version d’évaluation du modèle d’application v2.0. Pour observer son fonctionnement dans le service Azure AD disponible généralement, consultez cet [exemple de code Azure AD](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

<!---HONumber=Oct15_HO3-->