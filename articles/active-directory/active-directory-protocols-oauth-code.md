<properties
	pageTitle="Vue d’ensemble du protocole Azure AD .NET | Microsoft Azure"
	description="Cet article explique comment utiliser des messages HTTP pour autoriser l’accès aux applications web et API web dans votre client à l’aide d’Azure Active Directory et OAuth 2.0."
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


# Autoriser l’accès aux applications web à l’aide d’OAuth 2.0 et Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

Azure Active Directory (Azure AD) utilise OAuth 2.0 pour vous permettre d’autoriser l’accès aux applications web et aux API web dans votre client Azure AD. Ce guide est indépendant du langage. Il explique comment envoyer et recevoir des messages HTTP sans utiliser l’une de nos bibliothèques open source.

Le flux de code d’autorisation OAuth 2.0 est décrit dans la [section 4.1 de la spécification OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Il est utilisé pour exécuter des activités d’authentification et d’autorisation dans la majorité des types d’applications, notamment les applications web et les applications installées de façon native.

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## Flux d’autorisation OAuth 2.0

À un niveau élevé, le flux d’autorisation complet pour une application est semblable à l’illustration suivante :

![Flux de code d’authentification OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## Demander un code d’autorisation

Le flux de code d'autorisation commence par le client dirigeant l'utilisateur vers le point de terminaison `/authorize`. Dans cette requête, le client indique les autorisations qu’il doit obtenir auprès de l’utilisateur : Vous pouvez obtenir les points de terminaison OAuth 2.0 à partir de la page de votre application dans le Portail Azure Classic, à l’aide du bouton **Points de terminaison** du panneau inférieur.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| locataire | required | La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application. Les valeurs autorisées sont les identificateurs du client, par exemple `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` ou `common` pour les jetons indépendants du client. |
| client\_id | required | L’ID d’application attribué à votre application lorsque vous l’avez inscrite auprès d’Azure AD. Il est disponible sur le Portail de gestion Azure. Cliquez sur **Active Directory**, sur le répertoire, sur l’application, puis sur **Configurer**. |
| response\_type | requis | Doit inclure `code` pour le flux de code d’autorisation. |
| redirect\_uri | recommandé | L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. Pour les applications natives et mobiles, vous devez utiliser la valeur par défaut `urn:ietf:wg:oauth:2.0:oob`. |
| scope | required | Liste séparée par des espaces [d’étendues](active-directory-v2-scopes.md) pour lesquelles vous souhaitez que l’utilisateur donne son consentement. |
| response\_mode | recommandé | Spécifie la méthode à utiliser pour envoyer le jeton résultant à votre application. Peut être `query` ou `form_post`. |
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher les falsifications de requête intersites](http://tools.ietf.org/html/rfc6749#section-10.12). La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| prompt | facultatif | Indique le type d’interaction utilisateur requis. Les seules valeurs valides à ce stade sont login, none et consent. `prompt=login` oblige l'utilisateur à saisir ses informations d'identification lors de cette requête, annulant de fait l'authentification unique. Avec `prompt=none`, c’est le comportement inverse. Cette valeur vous garantit qu'aucune invite interactive d'aucune sorte n'est présentée à l'utilisateur. Si la requête ne peut pas être exécutée en mode silencieux au moyen d'une authentification unique, le point de terminaison v2.0 renvoie une erreur. `prompt=consent` déclenche l'affichage de la boîte de dialogue de consentement OAuth après la connexion de l'utilisateur, afin de lui demander d'octroyer des autorisations à l'application. |
| login\_hint | facultatif | Peut être utilisé pour remplir au préalable le champ réservé au nom d’utilisateur/à l’adresse électronique de la page de connexion de l’utilisateur si vous connaissez déjà son nom d’utilisateur. Les applications utilisent souvent ce paramètre au cours de la réauthentification, après avoir extrait le nom d’utilisateur à partir d’une connexion précédente à l’aide de la revendication `preferred_username`. |
| domain\_hint | facultatif | Peut être `consumers` ou `organizations`. S’il est inclus, ce paramètre ignore le processus de découverte par courrier électronique auquel l’utilisateur doit se soumettre sur la page de connexion v2.0, ce qui améliore légèrement l’expérience utilisateur. Les applications utilisent souvent ce paramètre au cours de la réauthentification, en extrayant la revendication `tid` à partir d’une connexion précédente. Si la revendication `tid` est définie sur la valeur `9188040d-6c67-4c5b-b112-36a304b66dad`, vous devez utiliser `domain_hint=consumers`. Sinon, utilisez `domain_hint=organizations`. |

> [AZURE.NOTE] Si l’utilisateur fait partie d’une organisation, un administrateur de l’organisation peut donner son consentement ou refuser pour le compte de l’utilisateur, ou autoriser l’utilisateur à donner son consentement. L’utilisateur a la possibilité de donner son consentement uniquement lorsque l’administrateur le lui permet.

À ce stade, l’utilisateur est invité à entrer ses informations d’identification et à donner son consentement vis-à-vis des autorisations indiquées dans le paramètre de requête `scope`. Une fois que l’utilisateur s’authentifie et donne son consentement, Azure AD envoie une réponse à votre application à l’adresse `redirect_uri` dans votre demande.

### Réponse correcte

Une réponse réussie se présenterait ainsi :

```
http://localhost:12345/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&state=12345
&session_state=733ad279-b681-49c3-9215-951abf94d2c5
```

| Paramètre | Description |
| -----------------------| --------------- |
| admin\_consent | La valeur est True si un administrateur a donné son consentement lorsqu’il y a été invité.|
| code | Le code d’autorisation demandé par l’application. L’application peut utiliser ce code d’autorisation pour demander un jeton d’accès pour la ressource cible. |
| session\_state | Une valeur unique identifiant la session utilisateur actuelle. Cette valeur est un GUID, mais doit être traitée comme une valeur opaque n’ayant fait l’objet d’aucune analyse. |
| state | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques.
Si un paramètre d’état est inclus dans la demande, la même valeur apparaît dans la réponse. Il est recommandé que l’application vérifie que les valeurs d’état de la demande et de la réponse sont identiques.

### Réponse d’erreur

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri`, de manière à ce que l’application puisse les traiter de manière appropriée.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

## Utiliser le code d’autorisation pour demander un jeton d’accès

Maintenant que vous avez acquis un code d’autorisation et que l’utilisateur vous a octroyé une autorisation, vous pouvez échanger le code contre un jeton d’accès à la ressource souhaitée, en envoyant une demande POST au point de terminaison `/token` :

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: client_secret only required for web apps
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------------- |
| locataire | required | La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application. Les valeurs autorisées sont les identificateurs du client, par exemple `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` ou `common` pour les jetons indépendants du client. |
| client\_id | required | L’ID d’application attribué à votre application lorsque vous l’avez inscrite auprès d’Azure AD. Il est disponible sur le Portail Azure Classic. Cliquez sur **Active Directory**, sur le répertoire, sur l’application, puis sur **Configurer**. |
| grant\_type | required | Doit être `authorization_code` pour le flux de code d'autorisation. |
| code | required | Le `authorization_code` que vous avez obtenu dans la section précédente |
| redirect\_uri | required | La valeur `redirect_uri` qui a été utilisée pour acquérir le `authorization_code`. |
| client\_secret | requis pour les applications Web | Le secret d’application que vous avez créé dans le portail d’inscription des applications pour votre application. Il ne doit pas être utilisé dans une application native, car les clés secrètes client ne peuvent pas être stockées de manière sûre sur les appareils. Il est requis pour les applications web et les API web, qui présentent la capacité de stocker de manière sûre le `client_secret` sur le côté serveur. |


### Réponse correcte

Une réponse réussie se présenterait ainsi :

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access\_token | Le jeton d’accès demandé. L’application peut utiliser ce jeton pour procéder à l’authentification sur la ressource sécurisée, comme une API Web. |
| token\_type | Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le jeton porteur. Pour plus d’informations sur les jetons du porteur, consultez le document [OAuth 2.0 Authorization Framework: Bearer Token Usage](http://www.rfc-editor.org/rfc/rfc6750.txt) (Infrastructure d’autorisation OAuth 2.0 : utilisation des jetons du porteur) (RFC 6750). |
| expires\_in | La durée de validité (en secondes) du jeton d’accès. |
| scope | Autorisations d’emprunt d’identité accordées à l’application cliente. L’autorisation par défaut est `user_impersonation`. Le propriétaire de la ressource sécurisée peut enregistrer des valeurs supplémentaires dans Azure AD.|
| refresh\_token | Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons d’accès supplémentaires après l’expiration du jeton d’accès actuel. Les jetons d’actualisation sont durables, et peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. |
| id\_token | Un jeton Web JSON non signé (JWT). L’application peut décoder les segments de ce jeton à l’aide d’un décodeur base64Url afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais ne peut aucunement les utiliser pour les limites d’autorisation ou de sécurité. |

### Demandes de jeton JWT
Le jeton JWT dans la valeur du paramètre `id_token` peut être décodé dans les revendications suivantes :

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

Le paramètre `id_token` inclut les types de revendication suivants. Pour plus d’informations sur les jetons web JSON, consultez le [projet de spécification JWT de l’IETF](http://go.microsoft.com/fwlink/?LinkId=392344) (en anglais). Pour plus d’informations sur les types de jeton et les revendications, consultez la page [Types de jeton et de revendication pris en charge](active-directory-token-and-claims.md).

| Type de revendication | Description |
|------------|-------------|
| aud | Audience du jeton. Lorsque le jeton est émis à une application cliente, l’audience est le `client_id` du client.
| exp | Heure d’expiration. L’heure d’expiration du jeton. Pour que le jeton soit valide, la date et l’heure actuelles doivent être antérieures ou identiques à la valeur `exp`. L’heure est représentée en nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’au moment de l’émission du jeton. |
| family\_name | Le nom de famille de l’utilisateur. L’application peut afficher cette valeur. |
| given\_name | Le prénom de l’utilisateur. L’application peut afficher cette valeur. |
| iat | Heure d’émission. L’heure d’émission du jeton JWT. L’heure est représentée en nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’au moment de l’émission du jeton. |
| iss | Identifie l’émetteur du jeton. |
| nbf | Heure à laquelle le jeton entre en vigueur. Pour que le jeton soit valide, la date et l’heure actuelles doivent être postérieures ou identiques à la valeur Nbf. L’heure est représentée en nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’au moment de l’émission du jeton. |
| oid | Identificateur (ID) de l’objet utilisateur dans Azure AD. |
| sub | Identificateur du sujet du jeton. Il s’agit d’un identificateur persistant et immuable de l’utilisateur décrit par le jeton. Utilisez cette valeur dans la logique de mise en cache. |
| tid | Identificateur (ID) du client Azure AD qui a émis le jeton. |
| unique\_name | Identificateur unique pouvant être affiché pour l’utilisateur. Il s’agit généralement d’un nom d’utilisateur principal (UPN, user principal name). |
| upn | Nom d’utilisateur principal. |
| ver | Version. La version du jeton JWT, généralement 1.0. |

### Réponse d’erreur

Une réponse d’erreur se présenterait ainsi :

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error\_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |
| error\_codes | Liste des codes d’erreur STS spécifiques pouvant être utiles dans les tests de diagnostic. |
| timestamp | Heure à laquelle l’erreur s’est produite. |
| trace\_id | Identifiant unique de la demande pouvant être utile dans les tests de diagnostic. |
| correlation\_id | Identifiant unique de la demande pouvant être utile dans les tests de diagnostic sur les divers composants.|

## Utiliser le jeton d’accès pour accéder à la ressource

Maintenant que vous avez acquis un jeton `access_token`, vous pouvez l’utiliser dans des requêtes dirigées vers des API web en l’incluant dans l’en-tête `Authorization` : La spécification [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) explique comment utiliser des jetons du porteur dans les requêtes HTTP pour accéder aux ressources protégées.

### Exemple de requête

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

## Actualisation des jetons d’accès

Les jetons d’accès présentent une durée de vie courte. Après leur expiration, vous devez les actualiser afin de pouvoir continuer à accéder aux ressources. Pour actualiser le `access_token`, envoyez une nouvelle requête `POST` au point de terminaison `/token` en fournissant l’élément `refresh_token` au lieu de l’élément `code`.

La durée de vie d’un jeton d’actualisation n’est pas fournie. Elle varie en fonction des paramètres de stratégie et de l’heure à laquelle le code d’autorisation est révoqué par Azure AD. Votre application doit envisager et gérer les situations d’échec des demandes de nouveau jeton d’accès. Elle doit alors revenir au code qui demande un nouveau jeton d’accès.

Voici un exemple de requête au point de terminaison **spécifique au client** (vous pouvez également utiliser le point de terminaison **commun**) pour obtenir un nouveau jeton d’accès à l’aide d’un jeton d’actualisation :

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| Paramètre | Description |
|-----------|-------------|
| access\_token | Le nouveau jeton d’accès qui a été demandé.|
| expires\_in | La durée de vie restante du jeton en secondes. 3600 (une heure) est une valeur courante. |
| expires\_on | La date et l’heure auxquelles le jeton expire. La date est représentée en nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’au moment de l’expiration. |
| refresh\_token | Un nouveau jeton d’actualisation OAuth 2.0 pouvant être utilisé pour demander de nouveaux jetons d’accès lorsque celui de cette réponse expire. |
| resource | Identifie la ressource sécurisée accessible à l’aide du jeton d’accès. |
| scope | Autorisations d’emprunt d’identité accordées à l’application cliente native. L’autorisation par défaut est **user\_impersonation**. Le propriétaire de la ressource cible peut enregistrer des valeurs alternatives dans Azure AD. |
| token\_type | Le type de jeton. La seule valeur possible est **Bearer**. |

### Réponse correcte

Une réponse de jeton réussie se présente ainsi :

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### Réponse d’erreur

Une réponse d’erreur se présenterait ainsi :

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

<!---HONumber=AcomDC_0601_2016-->