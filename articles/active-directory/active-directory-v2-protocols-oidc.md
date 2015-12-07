
<properties
	pageTitle="Protocole du modèle d’application v2.0 OpenID Connect | Microsoft Azure"
	description="Création d'applications Web à l'aide de l’implémentation du protocole d'authentification OpenID Connect d’Azure AD."
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

# Version préliminaire du modèle d’application v2.0 : Protocoles - OpenID Connect
OpenID Connect est un protocole d'authentification basé sur OAuth 2.0, qu’il est possible d’utiliser pour connecter de façon sécurisée des utilisateurs à des applications Web. En vous appuyant sur l’implémentation du modèle d’application v2.0 d’OpenID Connect, vous pouvez ajouter l’accès à la connexion et aux API à vos applications web. Ce guide vous explique comme procéder, indépendamment du langage. Il indique comment envoyer et recevoir des messages HTTP sans utiliser l'une de nos bibliothèques open-source.

> [AZURE.NOTE]Ces informations s’appliquent à la version préliminaire publique du modèle d’application v2.0. Pour obtenir des instructions sur l’intégration au service Azure AD mis à la disposition générale, consultez le [Guide du développeur Azure AD](active-directory-developers-guide.md).

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) étend le protocole d’*autorisation* OAuth 2.0 pour l’utiliser en tant que protocole d’*authentification*, ce qui vous permet de procéder à une authentification unique à l’aide d’OAuth. Il introduit le concept de jeton `id_token`, qui est un jeton de sécurité permettant au client de vérifier l’identité de l’utilisateur et d’obtenir ses informations de base de profil. Parce qu’il étend OAuth 2.0, il permet aux applications d’acquérir de manière sûre les **jetons d’accès** pouvant être utilisés pour accéder aux ressources sécurisées à l’aide d’un [serveur d’autorisation](active-directory-v2-protocols.md#the-basics). Nous recommandons OpenID Connect si vous concevez une [application web](active-directory-v2-flows.md#web-apps) hébergée sur un serveur et accessible par le biais d’un navigateur.

## Envoyer la requête de connexion
Lorsque votre application web a besoin d’authentifier l’utilisateur, elle peut le diriger vers le point de terminaison `/authorize`. Cette requête est similaire au premier tronçon du [flux de code d’autorisation OAuth 2.0 ](active-directory-v2-protocols-oauth-code.md). Notons toutefois quelques distinctions importantes :

- La requête doit inclure l’étendue `openid` dans le paramètre `scope`.
- Le paramètre `response_type` doit inclure `id_token`.
- La requête doit inclure le paramètre `nonce`.

```
// Line breaks for legibility only

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
| client\_id | required | L’ID d’application affecté par le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) à votre application. |
| response\_type | required | Doit inclure `id_token` pour la connexion à OpenID Connect. Il peut inclure d’autres types de réponses, comme `code`. |
| redirect\_uri | required | L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope | required | Une liste d’étendues séparées par des espaces. Pour OpenID Connect, vous devez inclure l’étendue `openid`, qui correspond à l’autorisation de connexion et de lecture de votre profil dans l’interface utilisateur de consentement. Vous pouvez inclure d’autres étendues dans cette requête pour solliciter le consentement. |
| nonce | required | Une valeur incluse dans la requête, générée par l’application, qui sera intégrée dans le jeton id\_token résultant en tant que revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique, aléatoire pouvant être utilisé pour identifier l’origine de la requête. |
| response\_mode | recommandé | Spécifie la méthode à utiliser pour envoyer le code d’autorisation résultant à votre application. Il peut s’agir de « query », « form\_post » ou de « fragment ».  
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| prompt | facultatif | Indique le type d’interaction utilisateur requis. Les seules valeurs valides à ce stade sont login, none et consent. `prompt=login` oblige l'utilisateur à saisir ses informations d'identification lors de cette requête, annulant de fait l'authentification unique. Avec `prompt=none`, c’est le comportement inverse. Cette valeur vous garantit qu'aucune invite interactive d'aucune sorte n'est présentée à l'utilisateur. Si la requête ne peut pas être exécutée en mode silencieux au moyen d'une authentification unique, le point de terminaison v2.0 renvoie une erreur. `prompt=consent` déclenche l'affichage de la boîte de dialogue de consentement OAuth après la connexion de l'utilisateur, afin de lui demander d'octroyer des autorisations à l'application. |
| login\_hint | facultatif | Peut être utilisé pour remplir au préalable le champ réservé au nom d'utilisateur/à l'adresse électronique de la page de connexion de l'utilisateur. |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification. Le point de terminaison v2.0 garantit également que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope`. Si l’utilisateur n’a pas accepté l’une ou plusieurs de ces autorisations, le point de terminaison lui demande de corriger ce manquement. Les détails sur les [autorisations, les consentements et les applications mutualisées sont disponibles ici](active-directory-v2-scopes.md).

Une fois que l’utilisateur a procédé à l’authentification et accordé son consentement, le point de terminaison v2.0 renvoie une réponse à votre application à l’élément `redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

Une réponse correcte utilisant `response_mode=query` se présente ainsi :

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&id_token_expires_in=3600

```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id\_token | Le jeton id-token que l’application a demandé. Vous pouvez utiliser ce jeton id\_token pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. Pour obtenir plus de détails sur les jetons id-token et leur contenu, consultez la page de [référence des jetons de point de terminaison v2.0](active-directory-v2-tokens.md). |
| session\_state | Une valeur unique identifiant la session utilisateur actuelle. Cette valeur est un GUID, mais doit être traitée comme une valeur opaque n’ayant fait l’objet d’aucune analyse. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |
| id\_token\_expires\_in | La durée de validité (en secondes) du jeton id\_token. |


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

## Valider le jeton id\_token
La réception du jeton id\_token ne suffit pas à authentifier l’utilisateur. Vous devez valider la signature du jeton id\_token et vérifier la conformité des revendications du jeton par rapport à la configuration requise de votre application. Le point de terminaison v2.0 utilise les [jetons web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et le chiffrement de clés publiques pour signer les jetons et vérifier leur validité.

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

## Envoyer une requête de déconnexion

Le `end_session_endpoint` OpenIdConnect n’est pas actuellement pris en charge par la version préliminaire du modèle d’application v2.0. Cela signifie que votre application ne peut pas envoyer une requête au point de terminaison v2.0 afin d’arrêter une session utilisateur et d’effacer les cookies définis par le point de terminaison v2.0. Pour déconnecter un utilisateur, il suffit à votre application de terminer sa propre session avec l’utilisateur, sans affecter aucunement la session de l’utilisateur avec le point de terminaison v2.0. Lors de la tentative de connexion suivante de l’utilisateur, une page l’invitant à sélectionner un compte s’affiche ; ses comptes de connexion actifs sont répertoriés. Sur cette page, l’utilisateur peut choisir de se déconnecter d’un compte, et de terminer la session avec le point de terminaison v2.0.

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

## Se connecter au diagramme récapitulatif
Le flux de connexion le plus simple contient les étapes suivantes :

![Couloirs OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## Obtenir des jetons d’accès
Beaucoup d’applications web nécessitent une connexion de l’utilisateur, puis un accès au service web pour le compte de cet utilisateur à l’aide d’OAuth. Ce scénario utilise OpenID Connect pour l’authentification de l’utilisateur tout en récupérant un code d’autorisation pouvant être sollicité pour obtenir des jetons d’accès à l’aide du flux de code d’autorisation OAuth. Pour acquérir des jetons d’accès, vous devez modifier légèrement la requête de connexion ci-dessus :


```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes your app needs  
offline_access%20										 
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

En incluant des étendues d’autorisation dans la requête et en utilisant `response_type=code+id_token`, le point de terminaison v2.0 garantit que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope` et renvoie un code d’autorisation à votre application afin de l’échanger contre un jeton d’accès.

Une réponse correcte utilisant `response_mode=query` se présente ainsi :

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&id_token_expires_in=3599

```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id\_token | Le jeton id-token que l’application a demandé. Vous pouvez utiliser ce jeton id\_token pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. Pour plus d’informations sur les jetons id\_token et leur contenu, consultez la page de [référence des jetons du modèle d’application v2.0](active-directory-v2-tokens.md). |
| code | Le code d’autorisation demandé par l’application. L’application peut utiliser ce code d’autorisation pour demander un jeton d’accès pour la ressource cible. Les codes d’autorisation présentent une durée de vie très courte. Généralement, ils expirent au bout de 10 minutes. |
| session\_state | Une valeur unique identifiant la session utilisateur actuelle. Cette valeur est un GUID, mais doit être traitée comme une valeur opaque n’ayant fait l’objet d’aucune analyse. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |
| id\_token\_expires\_in | La durée de validité (en secondes) du jeton id\_token. |

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

Une fois que vous avez obtenu une autorisation `code` et un `id_token`, vous pouvez connecter l’utilisateur et obtenir des jetons d’accès pour son compte. Pour connecter l’utilisateur, vous devez valider le `id_token` exactement comme décrit [ci-dessus](#validating-the-id-token). Pour obtenir des jetons d’accès, vous pouvez suivre la procédure décrite dans la [documentation du protocole OAuth](active-directory-v2-protocols-oidc.md#request-an-access-token).

## Diagramme de synthèse de l’acquisition des jetons

Pour référence, le flux complet de connexion OpenID Connect et d’acquisition des jetons se présente ainsi :

![Couloirs OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

<!---HONumber=AcomDC_1125_2015-->