<properties
	pageTitle="Vue d’ensemble du protocole Azure AD .NET | Microsoft Azure"
	description="Cet article explique comment utiliser des messages HTTP pour autoriser l’accès aux applications web et API web dans votre client à l’aide d’Azure Active Directory et d’OpenID Connect."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>


# Autoriser l’accès aux applications web à l’aide d’OpenID Connect et d’Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) est une couche d’identité simple basée sur le protocole OAuth 2.0. OAuth 2.0 définit des mécanismes permettant d’obtenir et utiliser des **jetons d’accès** pour accéder à des ressources protégées ; en revanche, ces mécanismes ne définissent aucune méthode standard pour fournir des informations d’identité. OpenID Connect implémente l’authentification en tant qu’extension du processus d’autorisation OAuth 2.0, en fournissant des informations concernant l’utilisateur final sous la forme d’un `id_token` qui vérifie l’identité de l’utilisateur tout en fournissant des informations sur le profil de base de l’utilisateur.

Nous recommandons OpenID Connect si vous concevez une application web hébergée sur un serveur et accessible par le biais d’un navigateur.

## Flux d’authentification à l’aide d’OpenID Connect

Le flux de connexion le plus simple comprend les étapes suivantes (chacune d’elles est décrite en détail ci-dessous).

![Flux d’authentification OpenID Connect](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)


## Envoyer la requête de connexion

Lorsque votre application web a besoin d’authentifier l’utilisateur, elle doit le diriger vers le point de terminaison `/authorize`. Cette requête est similaire au premier tronçon du [flux de code d’autorisation OAuth 2.0 ](active-directory-protocols-oauth-code.md). Notons toutefois quelques distinctions importantes :

- La requête doit inclure l’étendue `openid` dans le paramètre `scope`.
- Le paramètre `response_type` doit inclure `id_token`.
- La requête doit inclure le paramètre `nonce`.

Voici donc un exemple de requête :

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| locataire | required | La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application. Les valeurs autorisées sont les identificateurs du client, par exemple `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` ou `common` pour les jetons indépendants du client. |
| client\_id | required | L’ID d’application attribué à votre application lorsque vous l’avez inscrite auprès d’Azure AD. Il est disponible sur le Portail Azure Classic. Cliquez sur **Active Directory**, sélectionnez le répertoire et l’application, puis cliquez sur **Configurer**. |
| response\_type | required | Doit inclure `id_token` pour la connexion à OpenID Connect. Il peut inclure d’autres types de réponses, comme `code`. |
| scope | required | Une liste d’étendues séparées par des espaces. Pour OpenID Connect, vous devez inclure l’étendue `openid`, qui correspond à l’autorisation de connexion dans l’interface utilisateur de consentement. Vous pouvez inclure d’autres étendues dans cette requête pour solliciter le consentement. |
| nonce | required | Une valeur incluse dans la requête (générée par l’application) qui est intégrée au jeton `id_token` obtenu sous la forme d’une revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique et aléatoire ou un GUID pouvant être utilisé pour identifier l’origine de la requête. |
| redirect\_uri | recommandé | L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| response\_mode | recommandé | Spécifie la méthode à utiliser pour envoyer le code d’autorisation résultant à votre application. Les valeurs prises en charge sont `form_post` pour une *requête HTTP POST de type formulaire* ou `fragment` pour un *fragment d’URL*. Pour les applications web, nous vous recommandons d’utiliser `response_mode=form_post` pour garantir le transfert le plus sécurisé des jetons à votre application.  
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher les falsifications de requête intersite](http://tools.ietf.org/html/rfc6749#section-10.12). La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| prompt | facultatif | Indique le type d’interaction utilisateur requis. Les seules valeurs valides à ce stade sont login, none et consent. `prompt=login` oblige l'utilisateur à saisir ses informations d'identification lors de cette requête, annulant de fait l'authentification unique. Avec `prompt=none`, c’est le comportement inverse. Cette valeur vous garantit qu'aucune invite interactive d'aucune sorte n'est présentée à l'utilisateur. Si la requête ne peut pas être exécutée en mode silencieux au moyen d’une authentification unique, le point de terminaison renvoie une erreur. `prompt=consent` déclenche l’affichage de la boîte de dialogue de consentement OAuth après la connexion de l’utilisateur, afin de lui demander d’octroyer des autorisations à l’application. |
| login\_hint | facultatif | Peut être utilisé pour remplir au préalable le champ réservé au nom d’utilisateur/à l’adresse électronique de la page de connexion de l’utilisateur si vous connaissez déjà son nom d’utilisateur. Les applications utilisent souvent ce paramètre au cours de la réauthentification, après avoir extrait le nom d’utilisateur à partir d’une connexion précédente à l’aide de la revendication `preferred_username`. |


À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification.

### Exemple de réponse

Exemple de réponse obtenue après l’authentification de l’utilisateur :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id\_token | Le jeton `id_token` que l’application a demandé. Vous pouvez utiliser ce jeton `id_token` pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

### Réponse d’erreur
Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri`, de manière à ce que l’application puisse les traiter de manière appropriée :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |


## Valider le jeton id\_token


La réception du jeton `id_token` ne suffit pas à authentifier l’utilisateur. Vous devez valider la signature et vérifier la conformité des revendications du jeton `id_token` par rapport à la configuration requise de votre application. Le point de terminaison Azure AD utilise les jetons web JSON (JWT) et le chiffrement de clés publiques pour signer les jetons et vérifier leur validité.

Vous pouvez décider de valider l’élément `id_token` dans le code du client, mais une pratique courante consiste à envoyer l’élément `id_token` vers un serveur principal, afin d’y appliquer la validation. Une fois que vous avez validé la signature du jeton `id_token`, il vous faudra vérifier quelques revendications.

En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires. Voici quelques validations courantes :

- S’assurer que l’utilisateur/l’organisation s’est inscrit pour l’application.
- S’assurer que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
- S’assurer de l’utilisation d’une force certaine d’authentification, comme une authentification multifacteur.

Une fois que vous avez complètement validé le jeton `id_token`, vous pouvez démarrer une session avec l’utilisateur et utiliser les revendications du jeton `id_token` pour récupérer les informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour l’affichage, les enregistrements, les autorisations, etc. Pour plus d’informations sur les types de jetons et les revendications, consultez la page [Types de jeton et de revendication pris en charge](active-directory-token-and-claims.md).

## Envoi d’une demande de déconnexion

Lorsque vous souhaitez déconnecter l'utilisateur de l'application, la suppression des cookies de votre application ou l’arrêt de la session de l’utilisateur ne suffisent pas. Vous devez également rediriger l’utilisateur vers `end_session_endpoint` pour suivre la procédure de déconnexion. Si vous n’y parvenez pas, l’utilisateur sera en mesure de se ré-authentifier à votre application sans avoir à saisir de nouveau ses informations d’identification, car il disposera d’une session d’authentification unique valide auprès du point de terminaison Azure AD.

Vous pouvez simplement rediriger l’utilisateur vers le `end_session_endpoint` répertorié dans le document de métadonnées OpenID Connect :

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post\_logout\_redirect\_uri | recommandé | L’URL vers laquelle l'utilisateur doit être redirigé après la déconnexion. Si elle n’est pas incluse, l’utilisateur verra un message générique. |

## Acquisition de jeton

Beaucoup d’applications web nécessitent une connexion de l’utilisateur, puis un accès au service web pour le compte de cet utilisateur à l’aide d’OAuth. Ce scénario utilise OpenID Connect pour l’authentification de l’utilisateur tout en récupérant un `authorization_code` pouvant être sollicité pour obtenir des jetons `access_tokens` à l’aide du flux de code d’autorisation OAuth.

## Obtenir des jetons d’accès

Pour acquérir des jetons d’accès, vous devez modifier la requête de connexion ci-dessus :

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=form_post						      // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F									 
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

En incluant des étendues d’autorisation dans la requête et en utilisant `response_type=code+id_token`, le point de terminaison `authorize` garantit que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope` et renvoie un code d’autorisation à votre application afin de l’échanger contre un jeton d’accès.

### Réponse correcte

Une réponse correcte utilisant `response_mode=form_post` se présente ainsi :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id\_token | Le jeton `id_token` que l’application a demandé. Vous pouvez utiliser ce jeton `id_token` pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. |
| code | Le code d’autorisation demandé par l’application. L’application peut utiliser ce code d’autorisation pour demander un jeton d’accès pour la ressource cible. Les codes d’autorisation présentent une durée de vie très courte. Généralement, ils expirent au bout de 10 minutes. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

### Réponse d’erreur

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri`, de manière à ce que l’application puisse les traiter de manière appropriée :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

Une fois que vous avez obtenu une autorisation `code` et un `id_token`, vous pouvez connecter l’utilisateur et obtenir des jetons d’accès pour son compte. Pour connecter l’utilisateur, vous devez valider le `id_token` exactement comme décrit ci-dessus. Pour obtenir des jetons d’accès, vous pouvez suivre la procédure décrite dans la [documentation du protocole OAuth](active-directory-protocols-oauth-code.md#Use-the-Authorization-Code-to-Request-an-Access-Token).

<!---HONumber=AcomDC_0601_2016-->