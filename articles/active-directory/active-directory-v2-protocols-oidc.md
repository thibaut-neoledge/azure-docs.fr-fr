
---
title: Protocole Azure AD v2.0 OpenID Connect | Microsoft Docs
description: Création d’applications Web à l’aide de l’implémentation v2.0 du protocole d’authentification OpenID Connect d’Azure AD.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: msmbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: dastrock

---
# <a name="v2.0-protocols---openid-connect"></a>Protocoles v2.0 - OpenID Connect
OpenID Connect est un protocole d'authentification basé sur OAuth 2.0, qu’il est possible d’utiliser pour connecter de façon sécurisée des utilisateurs à des applications Web.  En vous appuyant sur l’implémentation du point de terminaison v2.0 d’OpenID Connect, vous pouvez ajouter l’accès à la connexion et aux API à vos applications web.  Ce guide vous explique comme procéder, indépendamment du langage. Il indique comment envoyer et recevoir des messages HTTP sans utiliser l'une de nos bibliothèques open-source.

> [!NOTE]
> Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).
> 
> 

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) étend le protocole *d’autorisation* OAuth 2.0 pour l’utiliser en tant que protocole *d’authentification*, ce qui vous permet de procéder à une authentification unique à l’aide d’OAuth.  Il introduit le concept de jeton `id_token`, qui est un jeton de sécurité permettant au client de vérifier l’identité de l’utilisateur et d’obtenir ses informations de base de profil.  Parce qu’il étend OAuth 2.0, il permet également aux applications d’acquérir de manière sûre les **jetons d’accès** pouvant être utilisés pour accéder à des ressources sécurisées à l’aide d’un [serveur d’autorisation](active-directory-v2-protocols.md#the-basics).  Nous recommandons OpenID Connect si vous concevez une [application web](active-directory-v2-flows.md#web-apps) hébergée sur un serveur et accessible par le biais d’un navigateur.

## <a name="protocol-diagram---sign-in"></a>Schéma de protocole - Connexion
Le flux de connexion le plus simple comprend les étapes suivantes (chacune d’elles est décrite en détail ci-dessous).

![Couloirs OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>Récupérez le document de métadonnées OpenID Connect
OpenID Connect décrit un document de métadonnées qui contient la plupart des informations nécessaires pour qu’une application effectue la connexion.  Cela inclut des informations telles que les URL à utiliser, l’emplacement des clés de signature publiques du service, etc.  Pour le point de terminaison v2.0, le document de métadonnées OpenID Connect que vous utilisez est :

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```

Où le `{tenant}` peut prendre l’une de quatre valeurs différentes :

| Valeur | Description |
| --- | --- |
| `common` |Permet aux utilisateurs avec des comptes Microsoft personnels et des comptes professionnels/scolaires Azure Active Directory de se connecter à l’application. |
| `organizations` |Permet uniquement aux utilisateurs avec des comptes professionnels/scolaires Azure Active Directory de se connecter à l’application. |
| `consumers` |Permet uniquement aux utilisateurs avec des comptes personnels Microsoft (MSA) de se connecter à l’application. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` |Permet uniquement aux utilisateurs avec des comptes professionnels/scolaires d’un client Azure Active Directory spécifique de se connecter à l’application.  Le nom de domaine convivial du client Azure AD ou l’identificateur guid du client peut être utilisé. |

Les métadonnées sont un document json simple, un extrait de code fourni ci-dessous.  Son contenu est décrit en détail dans les [spécifications d’OpenID Connect](https://openid.net).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

En règle générale, vous utilisez ce document de métadonnées pour configurer une bibliothèque OpenID Connect ou un Kit de développement logiciel (SDK). La bibliothèque utilise les métadonnées pour faire son travail.  Toutefois, si vous n’utilisez pas une bibliothèque OpenID Connect pré-build, vous pouvez suivre les étapes décrites dans le reste de cet article pour vous connecter à une application web à l’aide du point de terminaison v2.0. 

## <a name="send-the-sign-in-request"></a>Envoyer la requête de connexion
Lorsque votre application web a besoin d’authentifier l’utilisateur, elle peut le diriger vers le point de terminaison `/authorize`.  Cette requête est similaire au premier tronçon du [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols-oauth-code.md). Notons toutefois quelques distinctions importantes :

* La requête doit inclure l’étendue `openid` dans le paramètre `scope`.
* Le paramètre `response_type` doit inclure `id_token`.
* La requête doit inclure le paramètre `nonce`.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Cliquez sur le lien ci-dessous pour exécuter cette requête ! Une fois que vous êtes connecté, votre navigateur doit être redirigé vers `https://localhost/myapp/` avec une valeur `id_token` dans la barre d’adresse.  Notez que cette requête utilise `response_mode=query` (pour les besoins du didacticiel uniquement).  Il est recommandé d’utiliser `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=query&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Paramètre |  | Description |
| --- | --- | --- |
| locataire |required |La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client.  Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints). |
| client_id |required |L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| response_type |required |Doit inclure `id_token` pour la connexion à OpenID Connect.  Il peut inclure d’autres types de réponses, comme `code`. |
| redirect_uri |recommandé |L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification.  Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope |required |Une liste d’étendues séparées par des espaces.  Pour OpenID Connect, vous devez inclure l’étendue `openid`, qui correspond à l’autorisation de connexion dans l’interface utilisateur de consentement.  Vous pouvez inclure d’autres étendues dans cette requête pour solliciter le consentement. |
| nonce |required |Une valeur incluse dans la requête, générée par l’application, qui sera intégrée dans le jeton id_token résultant en tant que revendication.  L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons.  La valeur est généralement une valeur unique, aléatoire pouvant être utilisé pour identifier l’origine de la requête. |
| response_mode |recommandé |Spécifie la méthode à utiliser pour envoyer le code d’autorisation résultant à votre application.  Il peut s’agir de « query », « form_post » ou de « fragment ».  Pour les applications web, nous vous recommandons d’utiliser `response_mode=form_post` pour garantir le transfert le plus sécurisé des jetons à votre application. |
| state |recommandé |Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton.  Il peut s’agir d’une chaîne du contenu de votre choix.  Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher les falsifications de requête intersite](http://tools.ietf.org/html/rfc6749#section-10.12).  La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| prompt |facultatif |Indique le type d’interaction utilisateur requis.  Les seules valeurs valides pour l’instant sont « login », « none » et « consent ».  `prompt=login` oblige l'utilisateur à saisir ses informations d'identification lors de cette requête, annulant de fait l'authentification unique.  Avec `prompt=none`, c’est le comportement inverse. Cette valeur vous garantit qu'aucune invite interactive d'aucune sorte n'est présentée à l'utilisateur.  Si la demande ne peut pas être exécutée en mode silencieux au moyen d’une authentification unique, le point de terminaison v2.0 renvoie une erreur.  `prompt=consent` déclenche l’affichage de la boîte de dialogue de consentement OAuth après la connexion de l’utilisateur, afin de lui demander d’octroyer des autorisations à l’application. |
| login_hint |facultatif |Peut être utilisé pour remplir au préalable le champ réservé au nom d’utilisateur/à l’adresse électronique de la page de connexion de l’utilisateur si vous connaissez déjà son nom d’utilisateur.  Les applications utilisent souvent ce paramètre au cours de la réauthentification, après avoir extrait le nom d’utilisateur à partir d’une connexion précédente à l’aide de la revendication `preferred_username`. |
| domain_hint |facultatif |Peut être `consumers` ou `organizations`.  S’il est inclus, ce paramètre ignore le processus de découverte par courrier électronique auquel l’utilisateur doit se soumettre sur la page de connexion v2.0, ce qui améliore légèrement l’expérience utilisateur.  Les applications utilisent souvent ce paramètre au cours de la réauthentification, en extrayant la revendication `tid` du jeton id_token.  Si la revendication `tid` est définie sur la valeur `9188040d-6c67-4c5b-b112-36a304b66dad`, vous devez utiliser `domain_hint=consumers`.  Sinon, utilisez `domain_hint=organizations`. |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification.  Le point de terminaison v2.0 garantit également que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope` .  Si l’utilisateur n’a pas accepté l’une ou plusieurs de ces autorisations, le point de terminaison lui demande de corriger ce manquement.  Les détails sur les [autorisations, les consentements et les applications mutualisées sont disponibles ici](active-directory-v2-scopes.md).

Une fois que l’utilisateur a procédé à l’authentification et accordé son consentement, le point de terminaison v2.0 renvoie une réponse à votre application à l’élément `redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

#### <a name="successful-response"></a>Réponse correcte
Une réponse correcte utilisant `response_mode=form_post` se présente ainsi :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paramètre | Description |
| --- | --- |
| id_token |Le jeton id_token que l’application a demandé. Vous pouvez utiliser ce jeton id_token pour vérifier l’identité de l’utilisateur et démarrer une session avec lui.  Pour obtenir plus de détails sur les jetons id_token et leur contenu, consultez la page de [référence des jetons de point de terminaison v2.0](active-directory-v2-tokens.md). |
| state |Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

#### <a name="error-response"></a>Réponse d’erreur
Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri` , de manière à ce que l’application puisse les traiter de manière appropriée :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| . | Description |
| --- | --- |
| error |Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Codes d’erreur pour les erreurs de point de terminaison d’autorisation
Le tableau suivant décrit les différents codes d’erreur qui peuvent être retournés dans le paramètre `error` de la réponse d’erreur.

| Code d'erreur | Description | Action du client |
| --- | --- | --- |
| invalid_request |Erreur de protocole, tel qu’un paramètre obligatoire manquant. |Corrigez l’erreur, puis envoyez à nouveau la demande. Il s’agit d’une erreur de développement généralement détectée lors des tests initiaux. |
| unauthorized_client |L’application cliente n’est pas autorisée à demander un code d’autorisation. |Cela se produit généralement lorsque l’application cliente n’est pas inscrite dans Azure AD ou n’est pas ajoutée au client Azure AD de l’utilisateur. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD. |
| access_denied |Le propriétaire de la ressource n’a pas accordé son consentement. |L’application cliente peut avertir l’utilisateur qu’elle ne peut pas continuer sans son consentement. |
| unsupported_response_type |Le serveur d’autorisation ne prend pas en charge le type de réponse dans la demande. |Corrigez l’erreur, puis envoyez à nouveau la demande. Il s’agit d’une erreur de développement généralement détectée lors des tests initiaux. |
| server_error |Le serveur a rencontré une erreur inattendue. |relancez la requête. Ces erreurs peuvent résulter de conditions temporaires. L’application cliente peut expliquer à l’utilisateur que sa réponse est reportée en raison d’une erreur temporaire. |
| temporarily_unavailable |Le serveur est temporairement trop occupé pour traiter la demande. |relancez la requête. L’application cliente peut expliquer à l’utilisateur que sa réponse est reportée en raison d’une condition temporaire. |
| invalid_resource |La ressource cible n’est pas valide car elle n’existe pas, Azure AD ne la trouve pas ou elle n’est pas configurée correctement. |Cela indique que la ressource, si elle existe, n’a pas été configurée dans le client. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD. |

## <a name="validate-the-id_token"></a>Valider le jeton id_token
La réception du jeton id_token ne suffit pas à authentifier l’utilisateur. Vous devez valider la signature du jeton id_token et vérifier la conformité des revendications du jeton par rapport à la configuration requise de votre application.  Le point de terminaison v2.0 utilise les [jetons web JSON (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et le chiffrement de clés publiques pour signer les jetons et vérifier leur validité.

Vous pouvez décider de valider l’élément `id_token` dans le code du client, mais une pratique courante consiste à envoyer l’élément `id_token` vers un serveur principal, afin d’y appliquer la validation.  Une fois que vous avez validé la signature du jeton id_token, il vous faudra vérifier quelques revendications.  Pour plus d’informations, consultez la page de [référence sur les jetons v2.0](active-directory-v2-tokens.md), notamment les sections [Validation des jetons](active-directory-v2-tokens.md#validating-tokens) et [Informations importantes sur la substitution des clés de signature](active-directory-v2-tokens.md#validating-tokens).  Nous vous recommandons d’utiliser une bibliothèque pour analyser et valider les jetons. Il en existe au moins une pour la plupart des langages et plateformes.
<!--TODO: Improve the information on this-->

En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires.  Voici quelques validations courantes :

* S’assurer que l’utilisateur/l’organisation s’est inscrit pour l’application.
* S’assurer que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
* S’assurer de l’utilisation d’une force certaine d’authentification, comme une authentification multifacteur.

Pour plus d’informations sur les revendications dans un jeton id_token, consultez la page de [référence sur les jetons du point de terminaison v2.0](active-directory-v2-tokens.md).

Une fois que vous avez complètement validé le jeton id_token, vous pouvez démarrer une session avec l’utilisateur et utiliser les revendications du jeton id_token pour récupérer les informations sur l’utilisateur dans votre application.  Ces informations peuvent être utilisées pour l’affichage, les enregistrements, les autorisations, etc.

## <a name="send-a-sign-out-request"></a>Envoi d’une demande de déconnexion
Le `end_session_endpoint` OpenIdConnect n’est pas actuellement pris en charge par le point de terminaison v2.0. Cela signifie que votre application ne peut pas envoyer une requête au point de terminaison v2.0 afin d’arrêter une session utilisateur et d’effacer les cookies définis par le point de terminaison v2.0.
Pour déconnecter un utilisateur, il suffit à votre application de terminer sa propre session avec l’utilisateur, sans affecter aucunement la session de l’utilisateur avec le point de terminaison v2.0.  Lors de la tentative de connexion suivante de l’utilisateur, une page l’invitant à sélectionner un compte s’affiche ; ses comptes de connexion actifs sont répertoriés.
Sur cette page, l’utilisateur peut choisir de se déconnecter d’un compte, et de terminer la session avec le point de terminaison v2.0.

<!--

When you wish to sign the user out of the app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## <a name="protocol-diagram---token-acquisition"></a>Schéma de protocole - Acquisition de jeton
Beaucoup d’applications web nécessitent une connexion de l’utilisateur, puis un accès au service web pour le compte de cet utilisateur à l’aide d’OAuth.  Ce scénario utilise OpenID Connect pour l’authentification de l’utilisateur tout en récupérant un code d’autorisation pouvant être sollicité pour obtenir des jetons d’accès à l’aide du flux de code d’autorisation OAuth.

Le flux complet de connexion OpenID Connect et d’acquisition des jetons se présente ainsi (chaque étape est décrite en détail ci-dessous) :

![Couloirs OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>Obtenir des jetons d’accès
Pour acquérir des jetons d’accès, vous devez modifier légèrement la requête de connexion ci-dessus :

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e      // Your registered Application Id
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes your app needs  
offline_access%20                                        
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                         // Any value, provided by your app
&nonce=678910                                        // Any value, provided by your app
```

> [!TIP]
> Cliquez sur le lien ci-dessous pour exécuter cette requête ! Une fois que vous êtes connecté, votre navigateur doit être redirigé vers `https://localhost/myapp/` avec des valeurs `id_token` et `code` dans la barre d’adresse.  Notez que cette requête utilise `response_mode=query` (pour les besoins du didacticiel uniquement).  Il est recommandé d’utiliser `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

En incluant des étendues d’autorisation dans la requête et en utilisant `response_type=id_token code`, le point de terminaison v2.0 garantit que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope` et renvoie un code d’autorisation à votre application afin de l’échanger contre un jeton d’accès.

#### <a name="successful-response"></a>Réponse correcte
Une réponse correcte utilisant `response_mode=form_post` se présente ainsi :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paramètre | Description |
| --- | --- |
| id_token |Le jeton id_token que l’application a demandé. Vous pouvez utiliser ce jeton id_token pour vérifier l’identité de l’utilisateur et démarrer une session avec lui.  Pour obtenir plus de détails sur les jetons id_token et leur contenu, consultez la page de [référence des jetons de point de terminaison v2.0](active-directory-v2-tokens.md). |
| code |Le code d’autorisation demandé par l’application. L’application peut utiliser ce code d’autorisation pour demander un jeton d’accès pour la ressource cible.  Les codes d’autorisation présentent une durée de vie très courte. Généralement, ils expirent au bout de 10 minutes. |
| state |Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

#### <a name="error-response"></a>Réponse d’erreur
Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri` , de manière à ce que l’application puisse les traiter de manière appropriée :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| . | Description |
| --- | --- |
| error |Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

Pour obtenir une description des codes d’erreur éventuels et connaître l’action client recommandée associée, consultez [Codes d’erreur pour les erreurs de point de terminaison d’autorisation](#error-codes-for-authorization-endpoint-errors).

Une fois que vous avez obtenu une autorisation `code` et un `id_token`, vous pouvez connecter l’utilisateur et obtenir des jetons d’accès pour son compte.  Pour connecter l’utilisateur, vous devez valider le `id_token` exactement comme décrit [ci-dessus](#validating-the-id-token).  Pour obtenir des jetons d’accès, vous pouvez suivre la procédure décrite dans la [documentation du protocole OAuth](active-directory-v2-protocols-oauth-code.md#request-an-access-token).

<!--HONumber=Oct16_HO2-->


