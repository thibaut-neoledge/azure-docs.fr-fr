<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Création d’applications web à l’aide de l’implémentation du protocole d’authentification OpenID Connect d’Azure Active Directory."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>


# <a name="azure-active-directory-b2c:-oauth-2.0-authorization-code-flow"></a>Azure Active Directory B2C : flux de code d’autorisation OAuth 2.0

L'octroi d'un code d'autorisation OAuth 2.0 peut servir dans les applications qui sont installées sur un périphérique pour accéder à des ressources protégées, comme des API Web. À l’aide de l’implémentation d’Azure Active Directory (Azure AD) B2C de OAuth 2.0, vous pouvez ajouter l’inscription, la connexion et d’autres tâches de gestion d’identité à vos applications mobiles et applications de bureau. Ce guide est indépendant du langage. Il explique comment envoyer et recevoir des messages HTTP sans utiliser l’une de nos bibliothèques open source.

<!-- TODO: Need link to libraries -->

Le flux de code d’autorisation OAuth 2.0 est décrit dans la [section 4.1 des spécifications OAuth 2.0](http://tools.ietf.org/html/rfc6749). Vous pouvez l’utiliser pour exécuter les activités d’authentification et d’autorisation avec la majorité des types d’applications, notamment les [applications web](active-directory-b2c-apps.md#web-apps) et les [applications installées de façon native](active-directory-b2c-apps.md#mobile-and-native-apps). Il permet aux applications d’acquérir de manière sûre les jetons d’accès **access_tokens**, qui peuvent être utilisés pour accéder aux ressources sécurisées à l’aide d’un [serveur d’autorisation](active-directory-b2c-reference-protocols.md#the-basics).

Ce guide décrit un aspect particulier du flux de code d’autorisation OAuth 2.0 :**les clients publics**. Un client public est une application cliente qui ne peut pas être approuvée pour maintenir de façon sécurisée l'intégrité d’un mot de passe secret. Cela inclut les applications mobiles, les applications de bureau et quasiment n'importe quelle application qui s'exécute sur un périphérique et a besoin d'obtenir des jetons d’accès. Si vous souhaitez ajouter la gestion des identités à une application web à l’aide d’Azure AD B2C, vous devez utiliser [OpenID Connect](active-directory-b2c-reference-oidc.md) au lieu d’OAuth 2.0.

Azure AD B2C étend les flux OAuth 2.0 standard pour proposer plus qu’une simple authentification et une simple autorisation. Il introduit le [**paramètre de stratégie**](active-directory-b2c-reference-policies.md), qui vous permet d’utiliser OAuth 2.0 pour ajouter des expériences utilisateur à votre application comme l’inscription, la connexion et la gestion des profils. Ici, nous allons découvrir comment utiliser OAuth 2.0 et des stratégies pour implémenter chacune de ces expériences dans vos applications natives. Nous verrons également comment obtenir des jetons d’accès afin d’accéder à des API web.

Les demandes HTTP d'exemple ci-dessous utilisent notre répertoire B2C d’exemple **fabrikamb2c.onmicrosoft.com**, ainsi que notre applications et nos stratégies d’exemple. Vous êtes libre de tester ces demandes vous-même à l’aide de ces valeurs, ou de les remplacer par les vôtres.
Découvrez comment [obtenir votre propre répertoire B2C, votre application et vos stratégies](#use-your-own-b2c-directory).

## <a name="1.-get-an-authorization-code"></a>1. Obtention d’un code d’autorisation
Le flux de code d’autorisation commence par le client dirigeant l’utilisateur vers le point de terminaison `/authorize`. Il s’agit de la partie interactive du flux, dans laquelle l'utilisateur agira réellement. Dans cette demande, le client indique les autorisations qu’il a besoin d’acquérir de l’utilisateur dans le paramètre `scope` et la stratégie à exécuter dans le paramètre `p`. Trois exemples sont fournis ci-dessous (avec des sauts de ligne pour une meilleure lisibilité), chacun utilisant une stratégie différente.

#### <a name="use-a-sign-in-policy"></a>Utilisation d’une stratégie de connexion

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Utilisation d’une stratégie d’inscription

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Utilisation d’une stratégie de modification de profil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Paramètre | Requis ? | Description |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | Requis | L’ID d’application que le [portail Azure](https://portal.azure.com) a affecté à votre application. |
| response_type | Requis | Le type de réponse, qui doit inclure `code` pour le flux de code d’autorisation. |
| redirect_uri | Requis | L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection que vous avez enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope | Requis | Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique à Azure AD les deux autorisations qui sont demandées. Utiliser l’ID de client comme étendue indique que votre application a besoin d’un **jeton d’accès** qui peut être utilisé avec votre propre service ou API web, représenté par le même ID de client.  L’étendue `offline_access` indique que votre application a besoin d’un **jeton d’actualisation** pour un accès durable aux ressources.  Vous pouvez également utiliser l’étendue `openid` pour demander un jeton d’ID **id_token** à partir d’Azure AD B2C. |
| response_mode | Recommandé | Méthode à utiliser pour envoyer le code d’autorisation résultant à votre application. Il peut s’agir de « query », « form_post » ou « fragment ».
| state | Recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page sur laquelle il était positionné ou la politique en cours d’exécution. |
| p | Requis | Stratégie à exécuter. Il s’agit du nom d’une stratégie qui est créée dans votre répertoire B2C. La valeur du nom de la stratégie doit commencer par « b2c\_1\_ ». Pour plus d’informations sur les stratégies, consultez [Structure de stratégie extensible](active-directory-b2c-reference-policies.md). |
| prompt | Facultatif | Type d’interaction utilisateur requis. La seule valeur valide pour l’instant est « login », ce qui oblige l’utilisateur à saisir ses informations d’identification sur cette requête. L’authentification unique ne prendra pas effet. |

À ce stade, l'utilisateur sera invité à exécuter le flux de travail de la stratégie. Il est possible que l’utilisateur doive entrer son nom d’utilisateur et son mot de passe, se connecter avec une identité sociale, s’inscrire au répertoire, etc., en fonction de la façon dont la stratégie est définie.

Une fois que l’utilisateur a exécuté la stratégie, Azure AD renvoie une réponse à votre application à l’élément `redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`. La réponse sera exactement la même pour chacun des cas ci-dessus, indépendamment de la stratégie qui a été exécutée.

Une réponse correcte utilisant `response_mode=query` se présente ainsi :

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| code | Le code d’autorisation demandé par l’application. L’application peut utiliser ce code d’autorisation afin de demander un jeton d’accès pour une ressource cible. Les codes d’autorisation ont une durée de vie très courte. Généralement, ils expirent au bout de 10 minutes. |
| state | Consultez la description complète dans le tableau précédent. Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri` , de manière que l’application puisse les traiter de manière appropriée :

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error_description | Message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |
| state | Consultez la description complète dans le premier tableau de cette section. Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |


## <a name="2.-get-a-token"></a>2. Obtention d’un jeton
Un code d’autorisation authorization_code étant acquis, vous pouvez échanger `code` contre un jeton sur la ressource souhaitée en envoyant une demande `POST` au point de terminaison `/token`. Dans Azure AD B2C, la seule ressource pour laquelle vous pouvez demander un jeton est l’API web principale de votre application. La convention utilisée pour demander un jeton à vous-même consiste à utiliser votre ID de client d’application en tant qu’étendue :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Paramètre | Requis ? | Description |
| ----------------------- | ------------------------------- | --------------------- |
| p | Requis | La stratégie qui a été utilisée pour obtenir le code d'autorisation. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. Notez que vous ajoutez ce paramètre à la *chaîne de requête*, et non au corps POST. |
| client_id | Requis | L’ID d’application que le [portail Azure](https://portal.azure.com) a affecté à votre application. |
| grant_type | Requis | Le type d’octroi, qui doit être `authorization_code` pour le flux de code d’autorisation. |
| scope | Recommandé | Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique à Azure AD les deux autorisations qui sont demandées. Utiliser l’ID de client comme étendue indique que votre application a besoin d’un **jeton d’accès** qui peut être utilisé avec votre propre service ou API web, représenté par le même ID de client.  L’étendue `offline_access` indique que votre application a besoin d’un **jeton d’actualisation** pour un accès durable aux ressources.  Vous pouvez également utiliser l’étendue `openid` pour demander un jeton d’ID **id_token** à partir d’Azure AD B2C. |
| code | Requis | Le code d’autorisation acquis dans le premier tronçon du flux. |
| redirect_uri | Requis | Le redirect_uri de l'application où vous avez reçu le code d’autorisation. |

Une réponse de jeton réussie se présente ainsi :

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| not_before | Heure à laquelle le jeton est considéré comme valide, en heure epoch. |
| token_type | Valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur. |
| access_token | Jeton JWT (JSON Web Token) signé que vous avez demandé. |
| scope | Les étendues pour lesquelles le jeton est valide, qui peuvent être utilisées dans le but de mettre en cache des jetons pour une utilisation ultérieure. |
| expires_in | La durée de validité du jeton (en secondes). |
| refresh_token | Jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton actuel. Les jetons d’actualisation sont durables, et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. Pour plus d’informations, consultez la page de [référence des jetons B2C](active-directory-b2c-reference-tokens.md). |

Les réponses d’erreur se présentent comme suit :

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error_description | Message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

## <a name="3.-use-the-token"></a>3. Utilisation du jeton
Un jeton `access_token` étant acquis, vous pouvez l’utiliser dans les demandes dirigées vers vos API web principales en l’incluant dans l’en-tête `Authorization` :

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4.-refresh-the-token"></a>4. Actualisation du jeton
Les jetons d’ID et d’accès ont une courte durée de vie. Vous devez les actualiser après leur expiration pour continuer à accéder aux ressources. Pour ce faire, envoyez une nouvelle demande `POST` au point de terminaison `/token`. Cette fois, fournissez l’élément `refresh_token` au lieu de l’élément `code` :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Paramètre | Requis ? | Description |
| ----------------------- | ------------------------------- | -------- |
| p | Requis | Stratégie qui a été utilisée pour obtenir le jeton d’actualisation d’origine. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. Notez que vous ajoutez ce paramètre à la *chaîne de requête*, et non au corps POST. |
| client_id | Recommandé | L’ID d’application que le [portail Azure](https://portal.azure.com) a affecté à votre application. |
| grant_type | Requis | Le type d’octroi, qui doit être `refresh_token` pour ce tronçon du flux de code d’autorisation. |
| scope | Recommandé | Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique à Azure AD les deux autorisations qui sont demandées. Utiliser l’ID de client comme étendue indique que votre application a besoin d’un **jeton d’accès** qui peut être utilisé avec votre propre service ou API web, représenté par le même ID de client.  L’étendue `offline_access` indique que votre application a besoin d’un **jeton d’actualisation** pour un accès durable aux ressources.  Vous pouvez également utiliser l’étendue `openid` pour demander un jeton d’ID **id_token** à partir d’Azure AD B2C. |
| redirect_uri | Facultatif | Le redirect_uri de l'application où vous avez reçu le code d’autorisation. |
| refresh_token | Requis | Le jeton d’actualisation d’origine que vous avez acquis dans le second tronçon du flux. |

Une réponse de jeton réussie se présente ainsi :

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| not_before | Heure à laquelle le jeton est considéré comme valide, en heure epoch. |
| token_type | Valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur. |
| access_token | Jeton JWT (JSON Web Token) signé que vous avez demandé. |
| scope | Les étendues pour lesquelles le jeton est valide, qui peuvent être utilisées dans le but de mettre en cache des jetons pour une utilisation ultérieure. |
| expires_in | La durée de validité du jeton (en secondes). |
| refresh_token | Jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton actuel. Les jetons d’actualisation sont durables, et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. Pour plus d’informations, consultez la page de [référence des jetons B2C](active-directory-b2c-reference-tokens.md). |

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
| error_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

## <a name="use-your-own-b2c-directory"></a>Utilisation de votre propre répertoire B2C

Si vous souhaitez tester ces demandes par vous-même, vous devez suivre ces trois étapes, puis remplacer les valeurs d’exemple ci-dessus par les vôtres :

- [Créez un répertoire B2C](active-directory-b2c-get-started.md) et utilisez le nom de votre répertoire dans les demandes.
- [Créez une application](active-directory-b2c-app-registration.md) pour obtenir un ID d’application et un URI de redirection redirect_uri. Vous pouvez inclure un **client natif** dans votre application.
- [Créez vos stratégies](active-directory-b2c-reference-policies.md) pour obtenir les noms de vos stratégies.



<!--HONumber=Oct16_HO2-->


