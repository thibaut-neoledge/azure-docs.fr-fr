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


# <a name="azure-active-directory-b2c:-web-sign-in-with-openid-connect"></a>Azure Active Directory B2C : connexion web avec OpenID Connect

OpenID Connect est un protocole d’authentification basé sur OAuth 2.0, qu’il est possible d’utiliser pour connecter de façon sécurisée des utilisateurs à des applications Web.  À l’aide de l’implémentation d’OpenID Connect d’Azure Active Directory (Azure AD) B2C, vous pouvez sous-traiter l’inscription, la connexion et d’autres tâches de gestion des identités dans vos applications web à Azure AD. Ce guide explique comment procéder, indépendamment du langage. Il explique comment envoyer et recevoir des messages HTTP sans utiliser l’une de nos bibliothèques open source.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) étend le protocole *d’autorisation* OAuth 2.0 pour l’utiliser en tant que protocole *d’authentification*. Vous pouvez ainsi procéder à une authentification unique à l’aide d’OAuth. Il introduit le concept de jeton `id_token`, qui est un jeton de sécurité permettant au client de vérifier l’identité de l’utilisateur et d’obtenir ses informations de base de profil.

Comme il étend OAuth 2.0, les applications peuvent acquérir en toute sécurité des jetons d’accès **access_tokens**. Vous pouvez utiliser des jetons d’accès access_tokens pour accéder aux ressources qui sont sécurisées par un [serveur d’autorisation](active-directory-b2c-reference-protocols.md#the-basics). Nous recommandons OpenID Connect si vous concevez une application web hébergée sur un serveur et accessible par le biais d’un navigateur. Si vous souhaitez ajouter la gestion des identités à votre application mobile ou à votre application de bureau à l’aide d’Azure AD B2C, vous devez utiliser [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) au lieu d’OpenID Connect.

Azure AD B2C étend le protocole OpenID Connect standard pour proposer plus qu’une simple authentification et une simple autorisation. Il introduit le [**paramètre de stratégie**](active-directory-b2c-reference-policies.md), grâce auquel vous pouvez utiliser OpenID Connect pour ajouter des expériences utilisateur à votre application comme l’inscription, la connexion et la gestion des profils. Ici, vous allez découvrir comment utiliser OpenID Connect et des stratégies pour implémenter chacune de ces expériences dans vos applications web. Vous verrez également comment obtenir des jetons d’accès afin d’accéder à des API web.

Les demandes HTTP d'exemple ci-dessous utilisent notre répertoire B2C d’exemple **fabrikamb2c.onmicrosoft.com** **https://aadb2cplayground.azurewebsites.net**, ainsi que notre application et nos stratégies d’exemple. Vous êtes libre de tester ces demandes vous-même à l’aide de ces valeurs, ou de les remplacer par les vôtres.
Découvrez comment [obtenir votre propre client B2C, votre application et vos stratégies](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Envoi de demandes d’authentification
Lorsque votre application Web a besoin d’authentifier l’utilisateur et d’exécuter une stratégie, elle peut le diriger vers le point de terminaison `/authorize` . Il s’agit de la partie interactive du flux, dans laquelle l’utilisateur agit réellement en fonction de la stratégie.

Dans cette demande, le client indique les autorisations qu’il a besoin d’acquérir de l’utilisateur dans le paramètre `scope` et la stratégie à exécuter dans le paramètre `p`. Trois exemples sont fournis ci-dessous (avec des sauts de ligne pour une meilleure lisibilité), chacun utilisant une stratégie différente. Pour avoir une idée du fonctionnement de chaque demande, essayez de coller la demande dans un navigateur et exécutez-la.

#### <a name="use-a-sign-in-policy"></a>Utilisation d’une stratégie de connexion

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

#### <a name="use-a-sign-up-policy"></a>Utilisation d’une stratégie d’inscription

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

#### <a name="use-an-edit-profile-policy"></a>Utilisation d’une stratégie de modification de profil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Paramètre | Requis ? | Description |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | Requis | L’ID d’application que le [portail Azure](https://portal.azure.com/) a affecté à votre application. |
| response_type | Requis | Le type de réponse, qui doit inclure `id_token` pour OpenID Connect. Si votre application Web a également besoin de jetons pour appeler une API Web, vous pouvez utiliser `code+id_token`, comme nous l'avons fait ici. |
| redirect_uri | Recommandé | L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection que vous avez enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope | Requis | Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique à Azure AD les deux autorisations qui sont demandées. L’étendue `openid` indique une autorisation pour connecter l’utilisateur et obtenir des données relatives à l’utilisateur sous la forme de jetons d’ID **id_tokens** (plus d’informations à ce sujet plus loin dans l’article). L’étendue `offline_access` est facultative pour les applications Web. Elle indique que votre application a besoin d’un **jeton d’actualisation** pour un accès durable aux ressources. |
| response_mode | Recommandé | Méthode à utiliser pour envoyer le code d’autorisation résultant à votre application. Il peut s’agir de « query », « form_post » ou « fragment ».  « form_post » est recommandé pour une sécurité optimale. |
| state | Recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page sur laquelle il était positionné. |
| nonce | Requis | Une valeur incluse dans la requête (générée par l’application) qui est intégrée au jeton id_token résultant en tant que revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique, aléatoire pouvant être utilisé pour identifier l’origine de la requête. |
| p | Requis | Stratégie à exécuter. Il s’agit du nom d’une stratégie qui est créée dans votre client B2C. La valeur du nom de la stratégie doit commencer par « b2c\_1\_ ». Pour plus d’informations sur les stratégies, consultez [Structure de stratégie extensible](active-directory-b2c-reference-policies.md). |
| prompt | Facultatif | Type d’interaction utilisateur requis. La seule valeur valide pour l’instant est « login », ce qui oblige l’utilisateur à saisir ses informations d’identification sur cette requête. L’authentification unique ne prendra pas effet. |

À ce stade, l'utilisateur sera invité à exécuter le flux de travail de la stratégie. Il est possible que l’utilisateur doive entrer son nom d’utilisateur et son mot de passe, se connecter avec une identité sociale, s’inscrire au répertoire, etc., en fonction de la façon dont la stratégie est définie.

Une fois que l’utilisateur a exécuté la stratégie, Azure AD renvoie une réponse à votre application à l’élément `redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`. La réponse sera exactement la même pour chacun des cas ci-dessus, indépendamment de la stratégie qui a été exécutée.

Une réponse réussie utilisant `response_mode=fragment` se présenterait ainsi :

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id_token | Le jeton id-token que l’application a demandé. Vous pouvez utiliser ce jeton id_token pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. Pour obtenir plus de détails sur les jetons d’ID id_tokens et leur contenu, consultez les [Informations de référence sur les jetons v2.0](active-directory-b2c-reference-tokens.md). |
| code | Le code d’autorisation demandé par l’application, si vous avez utilisé `response_type=code+id_token`. L’application peut utiliser ce code d’autorisation afin de demander un jeton d’accès pour une ressource cible. Les codes d’autorisation ont une durée de vie très courte. Généralement, ils expirent au bout de 10 minutes. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri` , de manière que l’application puisse les traiter de manière appropriée :

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |
| state | Consultez la description complète dans le tableau précédent. Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |


## <a name="validate-the-id_token"></a>Valider le jeton id_token
La réception du jeton id_token ne suffit pas à authentifier l’utilisateur ; vous devez valider la signature du jeton id_token et vérifier la conformité des revendications du jeton par rapport à la configuration requise de votre application. Azure AD B2C utilise les [jetons Web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et le chiffrement de clés publiques pour signer les jetons et vérifier leur validité.

Il existe de nombreuses bibliothèques open source pour valider les jetons JWT en fonction de votre langage préféré. Nous vous recommandons d’explorer ces options plutôt que d’implémenter votre propre logique de validation. Ces informations sont utiles pour savoir comment utiliser correctement ces bibliothèques.

Azure AD B2C présente un point de terminaison de métadonnées OpenID Connect, qui permet à une application de récupérer les informations relatives à Azure AD B2C lors de l’exécution. Ces informations incluent les points de terminaison, le contenu des jetons et les clés de signature de jetons. Il existe un document de métadonnées JSON pour chaque stratégie dans votre client B2C. Par exemple, le document de métadonnées pour la stratégie `b2c_1_sign_in` dans `fabrikamb2c.onmicrosoft.com` se trouve à l’emplacement suivant :

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

L’une des propriétés de ce document de configuration est l’élément `jwks_uri`, dont la valeur pour la même stratégie serait la suivante :

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Afin de déterminer la stratégie utilisée pour la connexion d’un jeton id_token (et l’emplacement à partir duquel extraire les métadonnées), deux options sont possibles. Tout d'abord, le nom de la stratégie est inclus dans la revendication `acr` du jeton id_token. Pour plus d'informations sur la façon d'analyser les revendications à partir d'un jeton id_token, consultez la [référence des jetons Azure AD B2C](active-directory-b2c-reference-tokens.md). L'autre option consiste à coder la stratégie dans la valeur du paramètre `state` lors de l'émission de la requête, puis à la décoder pour déterminer la stratégie qui a été utilisée. Les deux méthodes sont parfaitement valides.

Après avoir acquis le document de métadonnées à partir du point de terminaison de métadonnées OpenID Connect, vous pouvez utiliser les clés publiques RSA 256 (qui se trouvent à ce point de terminaison) pour valider la signature du jeton id_token. En permanence, ce point de terminaison peut comporter plusieurs clés, chacune étant identifiée par un élément `kid`. L’en-tête de ce jeton id_token contient également une revendication `kid`, qui identifie la clé utilisée pour la signature du jeton id_token. Pour plus d’informations, consultez [AD B2C : référence de jeton](active-directory-b2c-reference-tokens.md), notamment les sections relatives aux [jetons de validation](active-directory-b2c-reference-tokens.md#validating-tokens) et aux [informations importantes sur la substitution des clés de signature](active-directory-b2c-reference-tokens.md#validating-tokens).
<!--TODO: Improve the information on this-->

Après avoir validé la signature du jeton d’ID id_token, vous devez vérifier plusieurs revendications, par exemple :

- Vous devez valider la revendication `nonce` afin d’empêcher les attaques par relecture de jetons. Sa valeur doit correspondre à ce que vous avez spécifié dans la requête de connexion.
- Vous devez valider la revendication `aud` afin de vérifier que le jeton d’ID id_token a été émis pour votre application. Sa valeur doit correspondre à l’ID d’application de votre application.
- Vous devez valider les revendications `iat` et `exp` afin de vérifier que le jeton d’ID id_token n’a pas expiré.

Il existe également plusieurs validations supplémentaires à effectuer, décrites en détail dans les [spécifications principales d’OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).  En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires. Voici quelques validations courantes :

- Vérifier que l’utilisateur/l’organisation s’est inscrit pour l’application.
- Vérifier que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
- S’assurer de l’utilisation d’une force certaine d’authentification, comme Azure Multi-Factor Authentication.

Pour plus d’informations sur les revendications dans un jeton id_token, consultez la page de [référence des jetons Azure AD B2C](active-directory-b2c-reference-tokens.md).

Après avoir complètement validé le jeton id_token, vous pouvez démarrer une session avec l’utilisateur et utiliser les revendications du jeton id_token pour récupérer les informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour l’affichage, les enregistrements, les autorisations, etc.

## <a name="get-a-token"></a>Obtention d’un jeton
Si votre application web doit uniquement exécuter des stratégies, vous pouvez ignorer les sections suivantes. Ces sections s’appliquent uniquement aux applications web qui doivent effectuer des appels authentifiés à une API web et qui sont également protégées par Azure AD B2C.

Vous pouvez échanger le code d’autorisation authorization_code que vous avez acquis (en utilisant `response_type=code+id_token`) contre un jeton sur la ressource souhaitée en envoyant une demande `POST` au point de terminaison `/token`. Actuellement, la seule ressource pour laquelle vous pouvez demander un jeton est l’API web principale de votre application. La convention pour demander un jeton à vous-même consiste à utiliser votre ID de client d’application en tant qu’étendue :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Paramètre | Requis ? | Description |
| ----------------------- | ------------------------------- | --------------------- |
| p | Requis | La stratégie qui a été utilisée pour obtenir le code d'autorisation. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. Notez que vous ajoutez ce paramètre à la **chaîne de requête**, et non au corps POST. |
| client_id | Requis | L’ID d’application que le [portail Azure](https://portal.azure.com/) a affecté à votre application. |
| grant_type | Requis | Le type d’octroi, qui doit être `authorization_code` pour le flux de code d’autorisation. |
| scope | Recommandé | Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique à Azure AD les deux autorisations qui sont demandées. L’étendue `openid` indique une autorisation pour connecter l’utilisateur et obtenir des données relatives à l’utilisateur sous la forme de jetons d’ID **id_tokens**. Elle peut être utilisée afin d’obtenir des jetons pour l’API web principale de votre application, qui est représentée par le même ID d’application que le client. L’étendue `offline_access` indique que votre application a besoin d’un **jeton d’actualisation** pour un accès durable aux ressources. |
| code | Requis | Le code d’autorisation acquis dans le premier tronçon du flux. |
| redirect_uri | Requis | Le redirect_uri de l'application où vous avez reçu le code d’autorisation. |
| client_secret | Requis | Le secret d'application que vous avez généré dans le [portail Azure](https://portal.azure.com/). Ce secret d’application est un artefact de sécurité important. Vous devez le stocker sur votre serveur de manière sécurisée. Vous devez également veiller à renouveler cette clé secrète client régulièrement. |

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
| access_token | Le jeton JWT signé que vous avez demandé. |
| scope | Les étendues pour lesquelles le jeton est valide, qui peuvent être utilisées dans le but de mettre en cache des jetons pour une utilisation ultérieure. |
| expires_in | La durée de validité du jeton d’accès access_token (en secondes). |
| refresh_token | Jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton actuel.  Les jetons d’actualisation sont durables, et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. Pour plus d’informations, consultez la page de [référence des jetons B2C](active-directory-b2c-reference-tokens.md). Notez que vous devez avoir utilisé l’étendue `offline_access` dans les demandes d’autorisation et de jeton pour recevoir un jeton d’actualisation refresh_token. |

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

## <a name="use-the-token"></a>Utilisation du jeton
Un jeton `access_token` étant acquis, vous pouvez l’utiliser dans les demandes dirigées vers vos API web principales en l’incluant dans l’en-tête `Authorization` :

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Actualisation du jeton
Les jetons id_token sont de courte durée. Vous devez les actualiser après leur expiration pour continuer à accéder aux ressources. Pour ce faire, envoyez une nouvelle demande `POST` au point de terminaison `/token`. Cette fois, fournissez l’élément `refresh_token` au lieu de l’élément `code` :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Paramètre | Requis | Description |
| ----------------------- | ------------------------------- | -------- |
| p | Requis | Stratégie qui a été utilisée pour obtenir le jeton d’actualisation d’origine. Vous ne pouvez pas utiliser une autre stratégie dans cette demande. Notez que vous ajoutez ce paramètre à la **chaîne de requête**, et non au corps POST. |
| client_id | Requis | L’ID d’application que le [portail Azure](https://portal.azure.com/) a affecté à votre application. |
| grant_type | Requis | Le type d’octroi, qui doit être `refresh_token` pour ce tronçon du flux de code d’autorisation. |
| scope | Recommandé | Une liste d’étendues séparées par des espaces. Une valeur d’étendue unique indique à Azure AD les deux autorisations qui sont demandées. L’étendue `openid` indique une autorisation pour connecter l’utilisateur et obtenir des données relatives à l’utilisateur sous la forme de jetons d’ID **id_tokens**. Elle peut être utilisée afin d’obtenir des jetons pour l’API web principale de votre application, qui est représentée par le même ID d’application que le client. L’étendue `offline_access` indique que votre application a besoin d’un **jeton d’actualisation** pour un accès durable aux ressources. |
| redirect_uri | Recommandé | Le redirect_uri de l'application où vous avez reçu le code d’autorisation. |
| refresh_token | Requis | Le jeton d’actualisation d’origine que vous avez acquis dans le second tronçon du flux. Notez que vous devez avoir utilisé l’étendue `offline_access` dans les demandes d’autorisation et de jeton pour recevoir un jeton d’actualisation refresh_token. |
| client_secret | Requis | Le secret d'application que vous avez généré dans le [portail Azure](https://portal.azure.com/). Ce secret d’application est un artefact de sécurité important. Vous devez le stocker sur votre serveur de manière sécurisée. Vous devez également veiller à renouveler cette clé secrète client régulièrement. |

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
| access_token | Le jeton JWT signé que vous avez demandé. |
| scope | Les étendues pour lesquelles le jeton est valide, qui peuvent être utilisées dans le but de mettre en cache des jetons pour une utilisation ultérieure. |
| expires_in | La durée de validité du jeton d’accès access_token (en secondes). |
| refresh_token | Jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton actuel.  Les jetons d’actualisation sont durables, et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. Pour plus d’informations, consultez la page de [référence des jetons B2C](active-directory-b2c-reference-tokens.md). |

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


## <a name="send-a-sign-out-request"></a>Envoi d’une demande de déconnexion

Quand vous souhaitez déconnecter l’utilisateur de l’application, la suppression des cookies de votre application ou l’arrêt de la session de l’utilisateur ne suffisent pas. Vous devez également rediriger l’utilisateur vers Azure AD. Si vous n’y parvenez pas, l’utilisateur peut être en mesure de se ré-authentifier auprès de votre application, sans entrer ses informations d’identification à nouveau. En effet, il dispose d’une session d’authentification unique valide avec Azure AD.

Vous pouvez simplement rediriger l’utilisateur vers le `end_session_endpoint` qui est répertorié dans le même document de métadonnées OpenID Connect décrit dans la section précédente « Valider le jeton d’ID id_token » :

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paramètre | Requis ? | Description |
| ----------------------- | ------------------------------- | ------------ |
| p | Requis | La stratégie que vous souhaitez utiliser pour déconnecter l’utilisateur de votre application. |
| post_logout_redirect_uri | Recommandé | URL vers laquelle l’utilisateur doit être redirigé après la déconnexion. Si elle n’est pas incluse, l’utilisateur voit un message générique d’Azure AD B2C.  |

> [AZURE.NOTE]
    Bien que la redirection de l’utilisateur vers le `end_session_endpoint` efface l’état d’authentification unique de certains utilisateurs avec Azure AD B2C, elle ne déconnecte pas l’utilisateur de la session du fournisseur d’identité sur le réseau social. Si l’utilisateur sélectionne le même IDP pendant une connexion ultérieure, il est de nouveau authentifié sans entrer ses informations d’identification. Si un utilisateur souhaite se déconnecter de votre application B2C, cela ne signifie pas nécessairement qu’il souhaite se déconnecter complètement de son compte Facebook. Toutefois, dans le cas de comptes locaux, la session de l’utilisateur sera terminée correctement.

## <a name="use-your-own-b2c-tenant"></a>Utilisation de votre propre client B2C

Si vous souhaitez tester ces demandes par vous-même, vous devez suivre ces trois étapes, puis remplacer les valeurs d’exemple ci-dessus par les vôtres :

- [Créez un client B2C](active-directory-b2c-get-started.md)et utilisez le nom de votre client dans les demandes.
- [Créez une application](active-directory-b2c-app-registration.md) pour obtenir un ID d’application et un URI de redirection redirect_uri. Vous pouvez inclure une **application Web/API Web** dans votre application et éventuellement créer un **secret d'application**.
- [Créez vos stratégies](active-directory-b2c-reference-policies.md) pour obtenir les noms de vos stratégies.



<!--HONumber=Oct16_HO2-->


