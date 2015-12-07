<properties
	pageTitle="Flux implicite du modèle d’application v2.0 | Microsoft Azure"
	description="Création d’applications web à l’aide de l’implémentation d’Azure AD du flux implicite pour les applications à page unique."
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

# Version préliminaire du modèle d’application v2.0 : Protocoles - Flux implicite
Avec la version v2.0 du modèle d’application Azure AD, vous pouvez connecter des utilisateurs dans vos applications à page unique avec des comptes personnels et professionnels/scolaires de Microsoft. Les applications à page unique et autres applications JavaScript qui s’exécutent principalement dans un navigateur présentent des problématiques spécifiques liées à l’authentification :

- Les caractéristiques de sécurité de ces applications sont considérablement différentes de celles des applications web traditionnelles basées sur serveur.
- De nombreux serveurs d’autorisation et fournisseurs d’identité ne prennent pas en charge les demandes CORS, pour des raisons de sécurité bien documentées.
- Chacune des redirections à partir de l’application du navigateur plein écran perturbe de manière assez importante l’expérience utilisateur.

Pour ces applications (AngularJS, Ember.js, React.js, etc.), Azure AD prend en charge le flux d’octroi implicite OAuth 2.0. Le flux implicite est décrit dans la [spécification OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2). Il permet notamment à l’application d’obtenir des jetons d’Azure AD sans échanger les informations d’identification du serveur principal. L’application peut alors connecter l’utilisateur, maintenir la session et obtenir des jetons pour d’autres API web, le tout dans le code JavaScript client. Quelques points de sécurité importants sont à prendre en compte lorsque vous utilisez le flux implicite, particulièrement en ce qui concerne l’emprunt d’identité du [client](http://tools.ietf.org/html/rfc6749#section-10.3) et de l’[utilisateur](http://tools.ietf.org/html/rfc6749#section-10.3).

Si vous souhaitez recourir au flux implicite et à Azure AD pour ajouter de l’authentification à votre application JavaScript, nous vous recommandons d’utiliser notre bibliothèque JavaScript open source, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js). Pour vous aider à commencer, nous mettons [ici](active-directory-appmodel-v2-overview.md#getting-started) à votre disposition quelques didacticiels AngularJS.

Toutefois, si vous préférez ne pas utiliser de bibliothèque dans votre application à page unique et envoyer vous-même des messages de protocole, suivez la procédure générale ci-dessous.

> [AZURE.NOTE]Ces informations s’appliquent à la version préliminaire publique du modèle d’application v2.0. Pour obtenir des instructions sur l’intégration au service Azure AD, dont la disponibilité est désormais générale, consultez le [Guide du développeur Azure AD](active-directory-developers-guide.md).

## Envoyer la requête de connexion

Pour connecter initialement l’utilisateur à votre application, vous pouvez envoyer une demande d’autorisation [OpenID Connect](active-directory-v2-protocols-oidc.md) et obtenir un élément `id_token` à partir du point de terminaison v2.0 :

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=fragment
&scope=openid%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
&state=12345
&nonce=678910
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | required | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| response\_type | required | Doit inclure `id_token` pour la connexion à OpenID Connect. Il peut également inclure d’autres types de réponses, comme `code`. |
| redirect\_uri | required | L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope | required | Une liste d’étendues séparées par des espaces. Pour OpenID Connect, vous devez inclure l’étendue `openid`, qui correspond à l’autorisation de connexion et de lecture de votre profil dans l’interface utilisateur de consentement. Vous pouvez également inclure d’autres étendues dans cette requête, ceci pour solliciter le consentement. |
| nonce | required | Une valeur incluse dans la requête, générée par l’application, qui sera intégrée dans le jeton id\_token résultant en tant que revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique, aléatoire pouvant être utilisé pour identifier l’origine de la requête. |
| response\_mode | recommandé | Spécifie la méthode à utiliser pour envoyer le code d’autorisation résultant à votre application. Il peut s’agir de « query », « form\_post » ou de « fragment ».  
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| prompt | facultatif | Indique le type d’interaction utilisateur requis. Les seules valeurs valides à ce stade sont login, none et consent. `prompt=login` oblige l’utilisateur à saisir ses informations d’identification lors de cette requête, annulant de fait l’authentification unique. Avec `prompt=none`, c’est le comportement inverse. Cette valeur vous garantit qu’aucune invite interactive d’aucune sorte n’est présentée à l’utilisateur. Si la requête ne peut pas être exécutée en mode silencieux au moyen d’une authentification unique, le point de terminaison v2.0 renvoie une erreur. `prompt=consent` déclenche l’affichage de la boîte de dialogue de consentement OAuth après la connexion de l’utilisateur, afin de lui demander d’octroyer des autorisations à l’application. |
| login\_hint | facultatif | Peut être utilisé pour remplir au préalable le champ réservé au nom d’utilisateur/à l’adresse électronique de la page de connexion de l’utilisateur. |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification. Le point de terminaison v2.0 garantit également que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope`. Si l’utilisateur n’a pas accepté l’une ou plusieurs de ces autorisations, le point de terminaison lui demande de corriger ce manquement. Les détails sur les [autorisations, les consentements et les applications mutualisées sont disponibles ici](active-directory-v2-scopes.md).

Une fois que l’utilisateur a procédé à l’authentification et accordé son consentement, le point de terminaison v2.0 renvoie une réponse à votre application à l’élément `redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

Une réponse correcte utilisant `response_mode=fragment` se présente ainsi :

```
GET https://localhost/myapp/#
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
GET https://localhost/myapp/#
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

Vous pouvez décider de valider l’élément `id_token` dans le code du client, mais une pratique courante consiste à envoyer l’élément `id_token` vers un serveur principal, afin d’y appliquer la validation. Il existe de nombreuses bibliothèques disponibles pour l’analyse et la validation des jetons JWT. Une fois que vous avez validé la signature du jeton id\_token, il vous faudra vérifier quelques revendications :

- Vous devez valider la revendication `nonce` afin d’empêcher les attaques par relecture de jetons. Sa valeur doit correspondre à ce que vous avez spécifié dans la requête de connexion.
- Vous devez valider la revendication `aud` afin de vous assurer que le jeton id\_token a été émis pour votre application. Sa valeur doit correspondre à l’élément `client_id` de votre application.
- Vous devez valider les revendications `iat` et `exp` afin de vous assurer que le jeton id\_token n’a pas expiré.

En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires. Voici quelques validations courantes :

- S’assurer que l’utilisateur/l’organisation s’est inscrit pour l’application.
- S’assurer que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
- S’assurer de l’utilisation d’une force certaine d’authentification, comme une authentification multifacteur.

Pour plus d’informations sur les revendications dans un jeton id\_token, consultez la page de [référence des jetons du modèle d’application v2.0](active-directory-v2-tokens.md).

Une fois que vous avez complètement validé le jeton id\_token, vous pouvez démarrer une session avec l’utilisateur et utiliser les revendications du jeton id\_token pour récupérer les informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour l’affichage, les enregistrements, les autorisations, etc.

## Obtenir des jetons d’accès

Maintenant que vous avez connecté l’utilisateur dans votre application à page unique, vous pouvez obtenir des jetons d’accès afin d’appeler des API web sécurisées à l’aide d’Azure AD, comme [Microsoft Graph](https://graph.microsoft.io). Dans le flux normal OpenID Connect/OAuth, il vous faut transmettre une requête au point de terminaison `/token` v2.0. Toutefois, le point de terminaison v2.0 ne prend pas en charge les demandes CORS. Dès lors, il est hors de question d’effectuer des appels AJAX afin d’obtenir et d’actualiser des jetons. Au lieu de cela, vous pouvez utiliser le flux implicite d’un iFrame masqué afin d’obtenir de nouveaux jetons pour d’autres API web :

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=fragment
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read  // The space separated scope(s) you want an access token for
&state=12345
&prompt=none
&login_hint=joe.user@outlook.com
&domain_hint=consumers
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | required | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| response\_type | required | Doit être `token` pour le flux implicite. |
| redirect\_uri | required | L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope | required | Liste d’étendues séparées par des espaces , dont vous avez besoin dans le jeton d’accès. |
| response\_mode | recommandé | Spécifie la méthode à utiliser pour envoyer le code d’autorisation résultant à votre application. Il peut s’agir de « query », « form\_post » ou de « fragment ».  
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| prompt | required | Ici, nous utilisons `prompt=none` afin de préciser qu’Azure AD ne doit présenter aucune interface utilisateur. Étant donné que cette requête intervient dans un iFrame masqué, vous devez vous assurer qu’en cas d’impossibilité d’acquisition en mode silencieux du jeton, Azure doit renvoyer une erreur, non demander à l’utilisateur de se connecter. |
| login\_hint | required | Requis pour l'authentification en mode silencieux. Cette indication aide Azure AD à faire la distinction entre différents comptes, si l’utilisateur possède une session active avec plus d’un compte. La valeur fournie ici peut être la valeur exacte que vous recevez dans la revendication `preferred_uesrname`, dans le jeton id\_token. |
| domain\_hint | required | Requis pour l'authentification en mode silencieux. Pour ce flux, l’élément domain\_hint peut présenter l’une des deux valeurs suivantes : `consumers` ou `organizations`. Si la valeur de la revendication `tid` de l’élément `id_token` que vous avez reçue était exactement 9188040d-6c67-4c5b-b112-36a304b66dad, utilisez `consumers` en tant que domain\_hint. Sinon, utilisez `organizations`. |

Grâce au paramètre `prompt=none`, cette requête va immédiatement réussir ou échouer, avant de revenir vers votre application. Une réponse correcte sera envoyée à votre application, à l’`redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

Une réponse correcte utilisant `response_mode=fragment` se présente ainsi :

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the access_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&expires_in=3600
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read

```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access\_token | Le jeton que l’application a demandé. |
| session\_state | Une valeur unique identifiant la session utilisateur actuelle. Cette valeur est un GUID, mais doit être traitée comme une valeur opaque n’ayant fait l’objet d’aucune analyse. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |
| expires\_in | La durée de validité (en secondes) du jeton d’accès. |
| scope | Les étendues de validité du jeton d’accès. |

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri`, de manière à ce que l’application puisse les traiter de manière appropriée. Dans le cas de `prompt=none`, une erreur se présentera généralement ainsi :

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

Si vous recevez cette erreur dans la requête iFrame, l’utilisateur doit se connecter de nouveau de manière interactive, ceci pour récupérer un nouveau jeton. Vous êtes invité à gérer ce cas de la manière la plus appropriée pour votre application.

## Actualisation des jetons

Les `id_token` et les `access_token` expirant après une courte période, votre application doit être préparée à les actualiser de manière régulière. Pour actualiser chaque type de jeton, vous pouvez exécuter la requête iFrame ci-dessus à l’aide du paramètre `prompt=none` afin de contrôler le comportement d’Azure AD. Si vous souhaitez recevoir un nouvel élément `id_token`, veillez à utiliser `response_type=id_token` et `scope=openid`, ainsi qu’un paramètre `nonce`.


## Envoyer une requête de déconnexion

Le `end_session_endpoint` OpenIdConnect n’est actuellement pas pris en charge par la version préliminaire du modèle d’application v2.0. Cela signifie que votre application ne peut pas envoyer une requête au point de terminaison v2.0 afin d’arrêter une session utilisateur et d’effacer les cookies définis par le point de terminaison v2.0. Pour déconnecter un utilisateur, il suffit à votre application de terminer sa propre session avec l’utilisateur, sans affecter aucunement la session de l’utilisateur avec le point de terminaison v2.0. Lors de la tentative de connexion suivante de l’utilisateur, une page l’invitant à sélectionner un compte s’affiche ; ses comptes de connexion actifs sont répertoriés. Sur cette page, l’utilisateur peut choisir de se déconnecter d’un compte, et de terminer la session avec le point de terminaison v2.0.

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

<!---HONumber=AcomDC_1125_2015-->