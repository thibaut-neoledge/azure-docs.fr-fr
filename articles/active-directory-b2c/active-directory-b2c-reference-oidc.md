<properties
	pageTitle="Version d’évaluation d’Azure AD B2C | Microsoft Azure"
	description="Création d'applications Web à l'aide de l’implémentation du protocole d'authentification OpenID Connect d’Azure AD."
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
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Version préliminaire d’Azure AD B2C : connexion Web avec OpenID Connect

OpenID Connect est un protocole d'authentification basé sur OAuth 2.0, qu’il est possible d’utiliser pour connecter de façon sécurisée des utilisateurs à des applications Web. À l'aide de l’implémentation d’Azure AD B2C d’OpenID Connect, vous pouvez sous-traiter l’inscription, la connexion et d’autres tâches de gestion des identités dans vos applications Web à Azure AD. Ce guide vous explique comme procéder, indépendamment du langage. Il indique comment envoyer et recevoir des messages HTTP sans utiliser l'une de nos bibliothèques open-source.

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) étend le protocole d’*autorisation* OAuth 2.0 pour l’utiliser en tant que protocole d’*authentification*, ce qui vous permet de procéder à une authentification unique à l’aide d’OAuth. Il introduit le concept de jeton `id_token`, qui est un jeton de sécurité permettant au client de vérifier l’identité de l’utilisateur et d’obtenir ses informations de base de profil. Parce qu’il étend OAuth 2.0, il permet aux applications d’acquérir de manière sûre les **jetons d’accès** pouvant être utilisés pour accéder aux ressources sécurisées à l’aide d’un [serveur d’autorisation](active-directory-b2c-reference-protocols.md#the-basics). Nous recommandons OpenID Connect si vous concevez une application Web hébergée sur un serveur et accessible par le biais d’un navigateur. Si vous souhaitez ajouter la gestion des identités à votre application mobile ou à votre application de bureau à l'aide d'Azure AD B2C, vous devez utiliser [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) au lieu d’OpenID Connect.

Azure AD B2C étend le protocole OpenID Connect standard pour proposer plus qu’une simple authentification et une simple autorisation. Il introduit le [**paramètre de stratégie**](active-directory-b2c-reference-poliices.md), qui vous permet d'utiliser OpenID Connect pour ajouter des expériences utilisateur à votre application comme l’inscription, la connexion et la gestion des profils. Ici, nous allons découvrir comment utiliser OpenID Connect et des stratégies pour implémenter chacune de ces expériences dans vos applications Web, et obtenir des jetons d’accès afin d’accéder à des API Web.

Les demandes HTTP d'exemple ci-dessous utilisent notre répertoire B2C d’exemple **fabrikamb2c.onmicrosoft.com**, ainsi que notre application ****https://aadb2cplayground.azurewebsites.net** et nos stratégies d’exemple. Vous êtes libre de tester ces demandes vous-même à l'aide de ces valeurs, ou de les remplacer par les vôtres. Découvrez comment [obtenir votre propre répertoire B2C, votre application et vos stratégies](#use-your-own-b2c-directory).

## Envoi de demandes d’authentification
Lorsque votre application Web a besoin d’authentifier l’utilisateur et d’exécuter une stratégie, elle peut le diriger vers le point de terminaison `/authorize`. Il s’agit de la partie interactive du flux, dans laquelle l'utilisateur agira réellement en fonction de la stratégie. Dans cette demande, le client indique les autorisations qu’il a besoin d’acquérir de l'utilisateur dans le paramètre `scope` et la stratégie à exécuter dans le paramètre `p`. Trois exemples sont fournis ci-dessous (avec des sauts de ligne pour une meilleure lisibilité), chacun utilisant une stratégie différente. Pour avoir une idée du fonctionnement de chaque demande, essayez de coller la demande dans un navigateur et exécutez-la.

#### Utilisation d’une stratégie de connexion

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### Utilisation d’une stratégie d’inscription

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### Utilisation d’une stratégie de modification de profil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | required | L’Id d'application que le [portail Azure](https://portal.azure.com/) a affecté à votre application. |
| response\_type | required | Doit inclure `id_token` pour OpenID Connect. Si votre application Web a également besoin de jetons pour appeler une API Web, vous pouvez utiliser `code+id_token`, comme nous l'avons fait ici. |
| redirect\_uri | requis | L’URI de redirection de votre application, vers lequel où votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope | required | Une liste d’étendues séparées par des espaces. Une valeur d'étendue unique indique à Azure AD les deux autorisations demandées. L’étendue `openid` indique une autorisation pour connecter l'utilisateur et obtenir des données relatives à l'utilisateur sous la forme de jetons **id\_tokens** (plus d’informations à venir à ce sujet). L’étendue `offline_access` est facultative pour les applications Web. Elle indique que votre application a besoin d’un **jeton d’actualisation** pour un accès durable aux ressources. |
| response\_mode | recommandé | Spécifie la méthode à utiliser pour envoyer le code d’autorisation résultant à votre application. Il peut s’agir de « query », « form\_post » ou de « fragment ». |
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page sur laquelle il était positionné. |
| nonce | required | Une valeur incluse dans la requête, générée par l’application, qui sera incluse dans le jeton id\_token résultant en tant que revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique, aléatoire pouvant être utilisé pour identifier l’origine de la requête. |
| p | required | Indique la stratégie à exécuter. Il s’agit du nom d'une stratégie créée dans votre répertoire B2C, dont la valeur doit commencer par « b2c\_1\_ ». En savoir plus sur les stratégies [ici](active-directory-b2c-reference-policies.md). |
| prompt | facultatif | Indique le type d’interaction utilisateur requis. La seule valeur valide pour l’instant est « login », ce qui oblige l’utilisateur à saisir ses informations d’identification sur cette requête. L’authentification unique ne prendra pas effet. |

À ce stade, l'utilisateur sera invité à exécuter le flux de travail de la stratégie. Il est possible que l'utilisateur doive saisir son nom d'utilisateur et son mot de passe, se connecter avec une identité sociale, s’inscrire au répertoire, etc. en fonction de la façon dont la stratégie est définie. Lorsque l’utilisateur exécute la stratégie, Azure AD renvoie une réponse à votre application à l’élément `redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`. La réponse sera exactement la même pour chacun des cas ci-dessus, indépendamment de la stratégie qui a été exécutée.

Une réponse réussie utilisant `response_mode=query` se présenterait ainsi :

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id\_token | Le jeton id-token que l’application a demandé. Vous pouvez utiliser ce jeton id\_token pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. Pour obtenir plus de détails sur les jetons id-token et leur contenu, consultez la page de [référence des jetons Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| code | Le code d’autorisation demandé par l’application, si vous avez utilisé `response_type=code+id_token`. L’application peut utiliser ce code d’autorisation afin de demander un jeton d’accès pour une ressource cible. Les codes d’autorisation présentent une durée de vie très courte. Généralement, ils expirent au bout de 10 minutes. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri`, de manière à ce que l’application puisse les traiter de manière appropriée :

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |


## Valider le jeton id\_token
La réception du jeton id\_token ne suffit pas à authentifier l’utilisateur. Vous devez valider la signature du jeton id\_token et vérifier la conformité des revendications du jeton par rapport à la configuration requise de votre application. Azure AD B2C utilise les [jetons Web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et le chiffrement de clés publiques pour signer les jetons et vérifier leur validité. Il existe de nombreuses bibliothèques open source pour valider les jetons JWT en fonction de votre langage préféré. Nous vous recommandons d'explorer ces options plutôt que d'implémenter votre propre logique de validation. Ces informations seront utiles pour savoir comment utiliser correctement ces bibliothèques.

Azure AD B2C présente un point de terminaison de métadonnées OpenID Connect, qui permet à une application de récupérer les informations relatives à Azure AD B2C lors de l’exécution. Ces informations incluent les points de terminaison, le contenu des jetons et les clés de signature de jetons. Il existe un document de métadonnées JSON pour chaque stratégie dans votre répertoire B2C. Par exemple, le document de métadonnées pour la stratégie `b2c_1_sign_in` dans `fabrikamb2c.onmicrosoft.com` se trouve à l'emplacement suivant :

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

L’une des propriétés de ce document de configuration est l’élément `jwks_uri`, dont la valeur pour la même stratégie serait la suivante :

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Afin de déterminer la stratégie utilisée pour la connexion d’un jeton id\_token (et l’emplacement à partir duquel extraire les métadonnées), deux options sont possibles. Tout d'abord, le nom de la stratégie est inclus dans la revendication `acr` du jeton id\_token. Pour plus d'informations sur la façon d'analyser les revendications à partir d'un jeton id\_token, consultez la [référence des jetons Azure AD B2C](active-directory-b2c-reference-tokens.md). L'autre option consiste à coder la stratégie dans la valeur du paramètre `state` lors de l’émission de la requête, puis à la décoder pour déterminer la stratégie qui a été utilisée. Les deux méthodes sont parfaitement valides.

Une fois que vous avez acquis le document de métadonnées à partir du point de terminaison de métadonnées OpenID Connect, vous pouvez utiliser les clés publiques RSA256 à ce point de terminaison pour valider la signature du jeton id\_token. En permanence, ce point de terminaison peut comporter plusieurs clés, chacune étant identifiée par un élément `kid`. L’en-tête de ce jeton id\_token contient également une revendication `kid`, qui identifie la clé utilisée pour la signature du jeton id\_token. Pour plus d’informations, consultez la [référence des jetons Azure AD B2C](active-directory-b2c-reference-tokens.md), comprenant notamment des données relatives aux [Jetons de validation](active-directory-b2c-reference-tokens.md#validating-tokens) et des [Informations importantes sur la substitution des clés de signature](active-directory-b2c-reference-tokens.md#validating-tokens).
<!--TODO: Improve the information on this-->

Une fois que vous avez validé la signature du jeton id\_token, il vous faudra vérifier quelques revendications :

- Vous devez valider la revendication `nonce` afin d’empêcher les attaques par relecture de jetons. Sa valeur doit correspondre à ce que vous avez spécifié dans la requête de connexion.
- Vous devez valider la revendication `aud` afin de vous assurer que le jeton id\_token a été émis pour votre application. Sa valeur doit correspondre à l’Id d’application de votre application.
- Vous devez valider les revendications `iat` et `exp` afin de vous assurer que le jeton id\_token n’a pas expiré.

En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires. Voici quelques validations courantes :

- S’assurer que l’utilisateur/l’organisation s’est inscrit pour l’application.
- S’assurer que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
- S’assurer de l’utilisation d’une force certaine d’authentification, comme une authentification multifacteur.

Pour plus d’informations sur les revendications dans un jeton id\_token, consultez la page de [référence des jetons Azure AD B2C](active-directory-b2c-reference-tokens.md).

Une fois que vous avez complètement validé le jeton id\_token, vous pouvez démarrer une session avec l’utilisateur et utiliser les revendications du jeton id\_token pour récupérer les informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour l’affichage, les enregistrements, les autorisations, etc.

## Obtention d’un jeton
Si votre application Web doit uniquement exécuter des stratégies, vous pouvez ignorer les sections suivantes. Ces sections s’appliquent uniquement aux applications Web qui doivent effectuer des appels authentifiés à une API Web, qui sont également protégés par Azure AD B2C.

Vous pouvez échanger le code d’autorisation que vous avez acquis (en utilisant `response_type=code+id_token`) contre un jeton sur la ressource souhaitée, en envoyant une demande `POST` au point de terminaison `/token`. Dans la version préliminaire d’Azure AD B2C, la seule ressource pour laquelle vous pouvez demander un jeton est l’API Web principale de votre application. La convention utilisée pour demander un jeton à vous-même consiste à faire appel à l'étendue `openid` :

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------------- |
| p | required | La stratégie qui a été utilisée pour obtenir le code d'autorisation. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. **Notez que ce paramètre est ajouté à la chaîne de requête**, et non dans le corps POST. |
| client\_id | required | L’Id d'application que le [portail Azure](https://portal.azure.com/) a affecté à votre application. |
| grant\_type | required | Doit être `authorization_code` pour le flux de code d'autorisation. |
| scope | required | Une liste d’étendues séparées par des espaces. Une valeur d'étendue unique indique à Azure AD les deux autorisations demandées. L’étendue `openid` indique une autorisation pour connecter l'utilisateur et obtenir des données relatives à l'utilisateur sous la forme de jetons **id\_tokens**. Elle peut être utilisée afin d’obtenir des jetons pour l’API Web principale de votre application, représentée par le même Id d’application que le client. L’étendue `offline_access` indique que votre application a besoin d’un **jeton d’actualisation** pour un accès durable aux ressources. |
| code | required | Le code d’autorisation acquis dans le premier tronçon du flux. |
| redirect\_uri | required | Le redirect\_uri de l'application où vous avez reçu le code d’autorisation. |
| client\_secret | required | Le secret d'application que vous avez généré dans le [portail Azure](https://portal.azure.com/). Ce secret d'application est un artefact de sécurité important qui doit être stocké de façon sécurisée sur votre serveur. Vous devez également veiller à renouveler cette clé secrète client régulièrement. |

Une réponse de jeton réussie se présente ainsi :

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| not\_before | Heure à laquelle le jeton est considéré comme valide, en heure epoch. |
| token\_type | Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur. |
| id\_token | Le jeton JWT signé que vous avez demandé. |
| scope | Les étendues pour lesquelles le jeton est valide, qui peuvent être utilisées dans le but de mettre en cache des jetons pour une utilisation ultérieure. |
| id\_token\_expires\_in | La durée de validité (en secondes) du jeton id\_token. |
| profile\_info | Une chaîne JSON codée en base 64 qui peut contenir des informations utiles sur l'utilisateur pour l'affichage dans votre application native. Son contenu exact dépend des revendications de l’application que vous avez configurées dans votre stratégie. |
| refresh\_token | Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton actuel. Les jetons d’actualisation sont durables, et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. Pour plus d’informations, consultez la page de [référence des jetons B2C](active-directory-b2c-reference-tokens.md). Remarque : vous devez avoir utilisé l'étendue `offline_access` dans les demandes d’autorisation et de jeton pour recevoir un jeton d'actualisation. |
| refresh\_token\_expires\_in | La durée maximale pendant laquelle un jeton d'actualisation peut être valide (en secondes). Le jeton d'actualisation peut toutefois devenir non valide à tout moment. |

> [AZURE.NOTE]
	Si, à ce stade, vous vous dites : « Où est le jeton d’accès ? », prenez en considération les éléments suivants. Lorsque vous demandez l’étendue `openid`, Azure AD émet un `id_token` JWT dans la réponse. Bien que ce `id_token` ne soit pas techniquement un jeton d’accès OAuth 2.0, il peut être utilisé en tant que tel lors de la communication avec le service principal de votre application, représenté par le même client\_id que le client. Le `id_token` est toujours un jeton du porteur JWT signé qui peut être envoyé à une ressource dans un en-tête d'autorisation HTTP et utilisé pour authentifier les demandes. La différence est qu'un `id_token` n'a pas de mécanisme permettant de limiter l’étendue de l’accès qu’une application cliente spécifique peut avoir. Toutefois, lorsque votre application cliente est le seul client qui est en mesure de communiquer avec votre service principal (comme c'est le cas avec la version préliminaire d’Azure AD B2C), un tel mécanisme d’étendue est inutile. Lorsque la version préliminaire d'Azure AD B2C ajoute la possibilité pour les clients de communiquer avec des ressources supplémentaires de premier plan ou tierces, des jetons d’accès sont introduits. Cependant, même dans ce cas, il est toujours recommandé d’utiliser `id_tokens` pour communiquer avec le service principal de votre application. Pour plus d'informations sur les types d'applications que vous pouvez créer avec la version préliminaire d'Azure AD B2C, consultez [cet article](active-directory-b2c-apps.md).

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

## Utilisation du jeton
Maintenant que vous êtes parvenu à acquérir un jeton `id_token`, vous pouvez l’utiliser dans les demandes dirigées vers vos API Web principales en l’incluant dans l’en-tête `Authorization` :

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## Actualisation du jeton
Les jetons id\_token présentent une durée de vie courte. Après leur expiration, vous devez les actualiser afin de pouvoir continuer à accéder aux ressources. Pour ce faire, envoyez une nouvelle demande `POST` au point de terminaison `/token`, en fournissant l’élément `refresh_token` au lieu de l’élément `code`:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | -------- |
| p | required | La stratégie qui a été utilisée pour obtenir le jeton d’actualisation d’origine. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. **Notez que ce paramètre est ajouté à la chaîne de requête**, et non dans le corps POST. |
| client\_id | required | L’Id d'application que le [portail Azure](https://portal.azure.com/) a affecté à votre application. |
| grant\_type | required | Doit inclure `refresh_token` pour ce tronçon du flux de code d'autorisation. |
| scope | required | Une liste d’étendues séparées par des espaces. Une valeur d'étendue unique indique à Azure AD les deux autorisations demandées. L’étendue `openid` indique une autorisation pour connecter l'utilisateur et obtenir des données relatives à l'utilisateur sous la forme de jetons **id\_tokens**. Elle peut être utilisée afin d’obtenir des jetons pour l’API Web principale de votre application, représentée par le même Id d’application que le client. L’étendue `offline_access` indique que votre application a besoin d’un **jeton d’actualisation** pour un accès durable aux ressources. |
| redirect\_uri | required | Le redirect\_uri de l'application où vous avez reçu le code d’autorisation. |
| refresh\_token | required | Le jeton d’actualisation d’origine que vous avez acquis dans le second tronçon du flux. Remarque : vous devez avoir utilisé l'étendue `offline_access` dans les demandes d’autorisation et de jeton pour recevoir un jeton d'actualisation. |
| client\_secret | required | Le secret d'application que vous avez généré dans le [portail Azure](https://portal.azure.com/). Ce secret d'application est un artefact de sécurité important qui doit être stocké de façon sécurisée sur votre serveur. Vous devez également veiller à renouveler cette clé secrète client régulièrement. |

Une réponse de jeton réussie se présente ainsi :

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| not\_before | Heure à laquelle le jeton est considéré comme valide, en heure epoch. |
| token\_type | Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur. |
| id\_token | Le jeton JWT signé que vous avez demandé. |
| scope | Les étendues pour lesquelles le jeton est valide, qui peuvent être utilisées dans le but de mettre en cache des jetons pour une utilisation ultérieure. |
| id\_token\_expires\_in | La durée de validité (en secondes) du jeton id\_token. |
| profile\_info | Une chaîne JSON codée en base 64 qui peut contenir des informations utiles sur l'utilisateur pour l'affichage dans votre application native. Son contenu exact dépend des revendications de l’application que vous avez configurées dans votre stratégie. |
| refresh\_token | Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton actuel. Les jetons d’actualisation sont durables, et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. Pour plus d’informations, consultez la page de [référence des jetons B2C](active-directory-b2c-reference-tokens.md). |
| refresh\_token\_expires\_in | La durée maximale pendant laquelle un jeton d'actualisation peut être valide (en secondes). Le jeton d'actualisation peut toutefois devenir non valide à tout moment. |

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


<!-- 

Here is the entire flow for a native  app; each request is detailed in the sections below:

![OAuth Auth Code Flow](./media/active-directory-b2c-reference-oauth-code/convergence_scenarios_native.png) 

-->

## Envoi d’une demande de déconnexion

Lorsque vous souhaitez déconnecter l'utilisateur de l'application, la suppression des cookies de votre application ou l’arrêt de la session de l’utilisateur ne suffisent pas. Vous devez également rediriger l'utilisateur vers Azure AD. Si vous n’y parvenez pas, l'utilisateur pourra être en mesure de se ré-authentifier à votre application, sans entrer ses informations d'identification à nouveau, car il disposera d’une session d’authentification unique valide avec Azure AD.

Vous pouvez simplement rediriger l'utilisateur vers le `end_session_endpoint` répertorié dans le même document de métadonnées OpenID Connect décrit [ci-dessus](#validate-the-id-token) :

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | ------------ |
| p | required | La stratégie que l'utilisateur a récemment utilisée pour se connecter à votre application. |
| post\_logout\_redirect\_uri | recommandé | L’URL vers laquelle l'utilisateur doit être redirigé après la déconnexion. Si elle n’est pas incluse, l'utilisateur verra un message générique d’Azure AD B2C. |

> [AZURE.NOTE]
	Bien que la redirection de l'utilisateur vers le `end_session_endpoint` efface l’état d’authentification unique de certains utilisateurs avec Azure AD, elle ne déconnecte en réalité pas efficacement l'utilisateur. Au lieu de cela, l'utilisateur sélectionne l'IDP avec lequel il souhaite se connecter. Ensuite, il sera authentifié à nouveau sans entrer ses informations d'identification. Dans le cas des IDP sociaux, il s’agit du comportement attendu. Si un utilisateur souhaite se déconnecter de votre répertoire B2C, cela ne signifie pas nécessairement qu'il souhaite se déconnecter complètement de son compte Facebook. Toutefois, dans le cas de comptes locaux, il doit être possible de mettre fin à la session de l’utilisateur correctement. En raison d’une [limitation](active-directory-b2c-limitations.md) connue de la version préliminaire d'Azure AD, la déconnexion d’un compte local ne fonctionne pas correctement. Une solution de contournement pour l’instant présent consiste à envoyer le paramètre `&prompt=login` dans chaque demande d'authentification, qui aura l'apparence du comportement souhaité, mais qui interrompra l'authentification unique entre les applications de votre répertoire B2C.

## Utilisation de votre propre répertoire B2C

Si vous souhaitez tester ces demandes par vous-même, vous devez suivre ces trois étapes, puis remplacer les valeurs d'exemple ci-dessus par les vôtres :

- [Créez un répertoire B2C](active-directory-b2c-get-started.md) et utilisez le nom de votre répertoire dans les demandes.
- [Créez une application](active-directory-b2c-app-registration.md) pour obtenir un Id d'application et un redirect\_uri. Vous pouvez inclure une **application Web/API Web** dans votre application et éventuellement créer un **secret d'application**.
- [Créez vos stratégies](active-directory-b2c-reference-policies.md) pour obtenir les noms de vos stratégies.

<!--

TODO

OpenID Connect for the v2.0 app model is the recommended way to implement sign-in for a [web  app](active-directory-v2-flows.md#web-apps).  The most basic sign-in flow contains the following steps:

image goes here

-->

<!---HONumber=AcomDC_0128_2016-->