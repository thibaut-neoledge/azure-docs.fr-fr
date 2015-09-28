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
	ms.date="09/04/2015"
	ms.author="dastrock"/>

# Version préliminaire d’Azure AD B2C : flux de code d'autorisation OAuth 2.0

L'octroi d'un code d'autorisation OAuth 2.0 peut servir dans les applications qui sont installées sur un périphérique pour accéder à des ressources protégées, comme des API Web. À l'aide de l’implémentation d’Azure AD B2C de OAuth 2.0, vous pouvez ajouter l’inscription, la connexion et d’autres tâches de gestion d’identité à vos applications mobiles et applications de bureau. Ce guide est indépendant du langage. Il explique comment envoyer et recevoir des messages HTTP sans utiliser l'une de nos bibliothèques open source.

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Le flux de code d’autorisation OAuth 2.0 est décrit dans la [section 4.1 de la spécification OAuth 2.0](http://tools.ietf.org/html/rfc6749). Il est possible de l’utiliser pour exécuter les activités d’authentification et d’autorisation avec la majorité des types d’applications, notamment les [applications Web](active-directory-b2c-apps.md#web-apps) et les [applications installées de façon native](active-directory-b2c-apps.md#mobile-and-native-apps). Il permet aux applications d’acquérir de manière sûre les **jetons d’accès** pouvant être utilisés pour accéder aux ressources sécurisées à l’aide d’un [serveur d’autorisation](active-directory-b2c-reference-protocols.md#the-basics). Ce guide décrit un aspect particulier du flux de code d'autorisation OAuth 2.0 : **les clients publics**. Un client public est une application cliente qui ne peut pas être approuvée pour maintenir de façon sécurisée l'intégrité d’un mot de passe secret. Cela inclut les applications mobiles, les applications de bureau et quasiment n'importe quelle application qui s'exécute sur un périphérique et a besoin d'obtenir des jetons d’accès. Si vous souhaitez ajouter la gestion des identités à une application Web à l'aide d'Azure AD B2C, vous devez utiliser [OpenID Connect](active-directory-b2c-reference-oidc.md) au lieu d’OAuth 2.0.

Azure AD B2C étend les flux OAuth 2.0 standard pour proposer plus qu’une simple authentification et une simple autorisation. Il introduit le [**paramètre de stratégie**](active-directory-b2c-reference-poliices.md), qui vous permet d'utiliser OAuth 2.0 pour ajouter des expériences utilisateur à votre application comme l’inscription, la connexion et la gestion des profils. Ici, nous allons découvrir comment utiliser OAuth 2.0 et des stratégies pour implémenter chacune de ces expériences dans vos applications natives, et obtenir des jetons d’accès afin d’accéder à des API Web.

Les demandes HTTP d'exemple ci-dessous utilisent notre répertoire B2C d’exemple **fabrikamb2c.onmicrosoft.com**, ainsi que notre applications et nos stratégies d’exemple. Vous êtes libre de tester ces demandes vous-même à l'aide de ces valeurs, ou de les remplacer par les vôtres. Découvrez comment [obtenir votre propre répertoire B2C, votre application et vos stratégies](#use-your-own-b2c-directory).

## 1\. Obtention d’un code d’autorisation
Le flux de code d’autorisation commence par le client dirigeant l’utilisateur vers le point de terminaison `/authorize`. Il s’agit de la partie interactive du flux, dans laquelle l'utilisateur agira réellement. Dans cette demande, le client indique les autorisations qu’il a besoin d’acquérir de l'utilisateur dans le paramètre `scope` et la stratégie à exécuter dans le paramètre `p`. Trois exemples sont fournis ci-dessous (avec des sauts de ligne pour une meilleure lisibilité), chacun utilisant une stratégie différente.

#### Utilisation d’une stratégie de connexion

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### Utilisation d’une stratégie d’inscription

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### Utilisation d’une stratégie de modification de profil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | required | L’Id d'application que le [portail Azure](https://portal.azure.com) a affecté à votre application. |
| response\_type | requis | Il doit inclure `code` pour le flux de code d’autorisation. |
| redirect\_uri | requis | L’URI de redirection de votre application, vers lequel où votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope | required | Une liste d’étendues séparées par des espaces. Une valeur d'étendue unique indique à Azure AD les deux autorisations demandées. L’étendue `openid` indique une autorisation pour connecter l'utilisateur et obtenir des données relatives à l'utilisateur sous la forme de jetons **id\_tokens** (plus d’informations à venir à ce sujet). L’étendue `offline_access` indique que votre application a besoin d’un **jeton d’actualisation** pour un accès durable aux ressources. |
| response\_mode | recommandé | Spécifie la méthode à utiliser pour envoyer le code d’autorisation résultant à votre application. Il peut s’agir de « query », « form\_post » ou de « fragment ».
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page sur laquelle il était positionné ou la politique en cours d’exécution. |
| p | required | Indique la stratégie à exécuter. Il s’agit du nom d'une stratégie créée dans votre répertoire B2C, dont la valeur doit commencer par « b2c\_1\_ ». En savoir plus sur les stratégies [ici](active-directory-b2c-reference-policies.md). |
| prompt | facultatif | Indique le type d’interaction utilisateur requis. La seule valeur valide pour l’instant est « login », ce qui oblige l’utilisateur à saisir ses informations d’identification sur cette requête. L’authentification unique ne prendra pas effet. |

À ce stade, l'utilisateur sera invité à exécuter le flux de travail de la stratégie. Il est possible que l'utilisateur doive saisir son nom d'utilisateur et son mot de passe, se connecter avec une identité sociale, s’inscrire au répertoire, etc. en fonction de la façon dont la stratégie est définie. Lorsque l’utilisateur exécute la stratégie, Azure AD renvoie une réponse à votre application à l’élément `redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`. La réponse sera exactement la même pour chacun des cas ci-dessus, indépendamment de la stratégie qui a été exécutée.

Une réponse réussie utilisant `response_mode=query` se présente ainsi :

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| code | Le code d’autorisation demandé par l’application. L’application peut utiliser ce code d’autorisation afin de demander un jeton d’accès pour une ressource cible. Les codes d’autorisation présentent une durée de vie très courte. Généralement, ils expirent au bout de 10 minutes. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri`, de manière à ce que l’application puisse les traiter de manière appropriée :

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |


## 2\. Obtention d’un jeton
Maintenant que vous avez acquis un code d’autorisation, vous pouvez échanger `code` contre un jeton sur la ressource souhaitée, en envoyant une demande `POST` au point de terminaison `/token`. Dans la version préliminaire d’Azure AD B2C, la seule ressource pour laquelle vous pouvez demander un jeton est l’API Web principale de votre application. La convention utilisée pour demander un jeton à vous-même consiste à faire appel à l'étendue `openid` :

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob"
}
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------------- |
| p | required | La stratégie qui a été utilisée pour obtenir le code d'autorisation. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. **Notez que ce paramètre est ajouté à la chaîne de requête**, et non dans le corps POST. |
| client\_id | required | L’Id d'application que le [portail Azure](https://portal.azure.com) a affecté à votre application. |
| grant\_type | required | Doit être `authorization_code` pour le flux de code d’autorisation. |
| scope | required | Une liste d’étendues séparées par des espaces. Une valeur d'étendue unique indique à Azure AD les deux autorisations demandées. L’étendue `openid` indique une autorisation pour connecter l'utilisateur et obtenir des données relatives à l'utilisateur sous la forme de jetons **id\_tokens**. Elle peut être utilisée afin d’obtenir des jetons pour l’API Web principale de votre application, représentée par le même Id d’application que le client. L’étendue `offline_access` indique que votre application a besoin d’un **jeton d’actualisation** pour un accès durable aux ressources. |
| code | required | Le code d’autorisation acquis dans le premier tronçon du flux. |
| redirect\_uri | required | Le redirect\_uri de l'application où vous avez reçu le code d’autorisation. |

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

> [AZURE.NOTE]Si, à ce stade, vous vous dites : « Où est le jeton d’accès ? », prenez en considération les éléments suivants. Lorsque vous demandez l’étendue `openid`, Azure AD émet un `id_token` JWT dans la réponse. Bien que ce `id_token` ne soit pas techniquement un jeton d’accès OAuth 2.0, il peut être utilisé en tant que tel lors de la communication avec le service principal de votre application, représenté par le même client\_id que le client. Le `id_token` est toujours un jeton du porteur JWT signé qui peut être envoyé à une ressource dans un en-tête d'autorisation HTTP et utilisé pour authentifier les demandes. La différence est qu'un `id_token` n'a pas de mécanisme permettant de limiter l’étendue de l’accès qu’une application cliente spécifique peut avoir. Toutefois, lorsque votre application cliente est le seul client qui est en mesure de communiquer avec votre service principal (comme c'est le cas avec la version préliminaire d’Azure AD B2C), un tel mécanisme d’étendue est inutile. Lorsque la version préliminaire d'Azure AD B2C ajoute la possibilité pour les clients de communiquer avec des ressources supplémentaires de premier plan ou tierces, des jetons d’accès sont introduits. Cependant, même dans ce cas, il est toujours recommandé d’utiliser `id_tokens` pour communiquer avec le service principal de votre application. Pour plus d'informations sur les types d'applications que vous pouvez créer avec la version préliminaire d'Azure AD B2C, consultez [cet article](active-directory-b2c-apps.md).

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

## 3\. Utilisation du jeton
Maintenant que vous êtes parvenu à acquérir un jeton `id_token`, vous pouvez l’utiliser dans les demandes dirigées vers vos API Web principales en l’incluant dans l’en-tête `Authorization` :

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## 4\. Actualisation du jeton
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
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob"
}
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | -------- |
| p | required | La stratégie qui a été utilisée pour obtenir le jeton d’actualisation d’origine. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. **Notez que ce paramètre est ajouté à la chaîne de requête**, et non dans le corps POST. |
| client\_id | required | L’Id d'application que le [portail Azure](https://portal.azure.com) a affecté à votre application. |
| grant\_type | required | Doit inclure `refresh_token` pour ce tronçon du flux de code d’autorisation. |
| scope | required | Une liste d’étendues séparées par des espaces. Une valeur d'étendue unique indique à Azure AD les deux autorisations demandées. L’étendue `openid` indique une autorisation pour connecter l'utilisateur et obtenir des données relatives à l'utilisateur sous la forme de jetons **id\_tokens**. Elle peut être utilisée afin d’obtenir des jetons pour l’API Web principale de votre application, représentée par le même Id d’application que le client. L’étendue `offline_access` indique que votre application a besoin d’un **jeton d’actualisation** pour un accès durable aux ressources. |
| redirect\_uri | required | Le redirect\_uri de l'application où vous avez reçu le code d’autorisation. |
| refresh\_token | required | Le jeton d’actualisation d’origine que vous avez acquis dans le second tronçon du flux. |

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

## Utilisation de votre propre répertoire B2C

Si vous souhaitez tester ces demandes par vous-même, vous devez suivre ces trois étapes, puis remplacer les valeurs d'exemple ci-dessus par les vôtres :

- [Créez un répertoire B2C](active-directory-b2c-get-started.md) et utilisez le nom de votre répertoire dans les demandes.
- [Créez une application](active-directory-b2c-app-registration.md) pour obtenir un Id d'application et un redirect\_uri. Vous pouvez inclure un **client natif** dans votre application.
- [Créez vos stratégies](active-directory-b2c-reference-policies.md) pour obtenir les noms de vos stratégies.

<!---HONumber=Sept15_HO3-->