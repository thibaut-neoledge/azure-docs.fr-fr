<properties
	pageTitle="Gestion des erreurs dans OAuth 2.0 | Microsoft Azure"
	description="Cet article décrit les classes d’erreurs courantes dans OAuth 2.0 avec Azure Active Directory et les meilleures pratiques pour les gérer."
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
	ms.date="05/31/2016"
	ms.author="priyamo"/>

# Gestion des erreurs dans OAuth 2.0

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

Dans cet article, vous découvrirez les meilleures pratiques liées à la gestion des classes d’erreurs courantes que vous pouvez rencontrer lors de l’autorisation de votre application avec Azure Active Directory (Azure AD). Pour plus d’informations sur le point de terminaison d’autorisation et le point de terminaison d’émission de jeton, consultez [Autoriser l’accès aux applications web à l’aide d’OAuth 2.0 et Azure Active Directory](active-directory-protocols-oauth-code.md).

## Erreurs de point de terminaison d’autorisation

Dans la première étape du processus d’autorisation, l’application cliente envoie une demande au point de terminaison `/authorize` d’Azure AD, avec la liste des autorisations requises de l’utilisateur.

Les erreurs de point de terminaison d’autorisation proviennent du point de terminaison `/authorize`. Elles sont retournées en tant qu’erreurs HTTP 200 sur une page web ou avec un code de redirection HTTP 302 lorsqu’une application cliente est disponible pour gérer l’erreur.

Voici un exemple de réponse d’erreur HTTP 302 du point de terminaison d’autorisation Azure AD, lorsqu’une demande n’a pas le paramètre `response_type` requis.

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?error=invalid_request&error_description=AADSTS90014%3a+The+request+body+must+contain+the+following+parameter%3a+%27response_type%27.%0d%0aTrace+ID%3a+57f5cb47-2278-4802-a018-d05d9145daad%0d%0aCorrelation+ID%3a+570a9ed3-bf1d-40d1-81ae-63465cc25488%0d%0aTimestamp%3a+2013-12-31+05%3a51%3a35Z&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Paramètre | Description |
|-----------|-------------|
| error | Une valeur de code d’erreur définie dans la section 5.2 du document [OAuth 2.0 Authorization Framework](http://tools.ietf.org/html/rfc6749) (Infrastructure d’autorisation OAuth 2.0). Le tableau suivant décrit les codes d’erreur retournés par Azure AD. |
| error\_description | Une description plus détaillée de l’erreur. Ce message n’est pas destiné à offrir une description claire à l’utilisateur final. |
| state | La valeur d’état est une valeur non réutilisée, générée de manière aléatoire, envoyée dans la demande et retournée dans la réponse pour empêcher les falsifications de requête intersites (CSRF, Cross Site Request Forgery). |

### Codes d’erreur pour les erreurs de point de terminaison d’autorisation

Le tableau suivant décrit les différents codes d’erreur qui peuvent être retournés dans le paramètre `error` de la réponse d’erreur.

| Code d'erreur | Description | Action du client |
|------------|-------------|---------------|
| invalid\_request | Erreur de protocole, tel qu’un paramètre obligatoire manquant. | Corrigez l’erreur, puis envoyez à nouveau la demande. Il s’agit d’une erreur de développement généralement détectée lors des tests initiaux.|
| unauthorized\_client | L’application cliente n’est pas autorisée à demander un code d’autorisation. | Cela se produit généralement lorsque l’application cliente n’est pas inscrite dans Azure AD ou n’est pas ajoutée au client Azure AD de l’utilisateur. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD. |
| access\_denied | Le propriétaire de la ressource n’a pas accordé son consentement. | L’application cliente peut avertir l’utilisateur qu’elle ne peut pas continuer sans son consentement. |
| unsupported\_response\_type | Le serveur d’autorisation ne prend pas en charge le type de réponse dans la demande. | Corrigez l’erreur, puis envoyez à nouveau la demande. Il s’agit d’une erreur de développement généralement détectée lors des tests initiaux.|
|server\_error | Le serveur a rencontré une erreur inattendue. | relancez la requête. Ces erreurs peuvent résulter de conditions temporaires. L’application cliente peut expliquer à l’utilisateur que sa réponse est reportée en raison d’une erreur temporaire. |
| temporarily\_unavailable | Le serveur est temporairement trop occupé pour traiter la demande. | relancez la requête. L’application cliente peut expliquer à l’utilisateur que sa réponse est reportée en raison d’une condition temporaire. |
| invalid\_resource |La ressource cible n’est pas valide car elle n’existe pas, Azure AD ne la trouve pas ou elle n’est pas configurée correctement.| Cela indique que la ressource, si elle existe, n’a pas été configurée dans le client. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD. |

## Erreurs de point de terminaison d’émission de jeton

Une fois que l’application reçoit un code d’autorisation du point de terminaison `/authorize`, elle le transmet au point de terminaison `/token` pour recevoir un jeton d’accès pour la ressource spécifiée.

Les erreurs de point de terminaison d’émission de jeton proviennent du point de terminaison `/token`. Il s’agit de codes d’erreur HTTP, étant donné que le client appelle le point de terminaison d’émission de jeton directement. Outre le code d’état HTTP, le point de terminaison d’émission de jeton Azure AD retourne également un document JSON avec des objets qui décrivent l’erreur.

Par exemple, une erreur liée à la non-validité du paramètre `client_id` de la demande pourrait se présenter comme suit :

```
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8

{"error":"invalid_request","error_description":"AADSTS90011: Request is ambiguous, multiple application identifiers found. Application identifiers: '197451ec-ade4-40e4-b403-02105abd9049, 597451ec-ade4-40e4-b403-02105abd9049'.\r\nTrace ID: 4457d068-2a03-42b2-97f2-d55325289d86\r\nCorrelation ID: 6b3474d8-233e-463f-b0a3-86433d8ba889\r\nTimestamp: 2013-12-31 06:31:41Z","error_codes":[90011],"timestamp":"2013-12-31 06:31:41Z","trace_id":"4457d068-2a03-42b2-97f2-d55325289d86","correlation_id":"6b3474d8-233e-463f-b0a3-86433d8ba889"}
```
### Codes d’état HTTP

Le tableau suivant répertorie les codes d’état HTTP retournés par le point de terminaison d’émission de jeton. Dans certains cas, le code d’erreur est suffisant pour décrire la réponse, mais en cas d’erreurs, vous devez analyser le document JSON joint et examiner son code d’erreur.

| Code HTTP | Description |
|-----------|-------------|
| 400 | Code HTTP par défaut. Il est utilisé dans la plupart des cas et est généralement dû à une demande incorrecte. Corrigez l’erreur, puis envoyez à nouveau la demande. |
| 401 | Échec d’authentification. Par exemple, la demande ne contient pas le paramètre client\_secret.|
| 403 | Échec de l’autorisation. Par exemple, l’utilisateur n’est pas autorisé à accéder à la ressource. |
| 500 | Une erreur interne s’est produite au niveau du service. relancez la requête. |

### Objets de document JSON dans la réponse d’erreur

| Objet JSON | Description |
|-------------|-------------|
| error | Une valeur de code d’erreur définie dans la section 5.2 du document [OAuth 2.0 Authorization Framework](http://tools.ietf.org/html/rfc6749) (Infrastructure d’autorisation OAuth 2.0). Le tableau suivant décrit les codes d’erreur retournés par Azure AD. |
| error\_description | Une description plus détaillée de l’erreur. Ce message n’est pas destiné à offrir une description claire à l’utilisateur final. |
| timestamp | Date et heure auxquelles l’erreur s’est produite en heure UTC (Universal Coordinated Time). |
| trace\_id | Un ID qui identifie la trace de l’erreur. |
| correlation\_id | 	Une valeur générée par le client. Cet ID est inclus dans le document d’erreur JSON lorsque la demande au point de terminaison d’émission de jeton inclut cette valeur dans l’en-tête `client-request-id`. Cet ID peut être utilisé par d’autres appels dans la même session. |
| error\_codes | Contient une liste des codes d’erreur correspondant aux différentes conditions du service. N’utilisez pas ces codes dans votre logique d’application. Toutefois, vous pouvez les utiliser pour diagnostiquer un problème et rechercher des informations en ligne par code d’erreur. |

### Valeurs de code erreur du document JSON

| Code d'erreur | Description | Action du client |
|------------|-------------|---------------|
| invalid\_request | Erreur de protocole, tel qu’un paramètre obligatoire manquant. | Corrigez l’erreur, puis envoyez à nouveau la demande. |
| invalid\_grant | L’octroi d’autorisation (ou ses paramètres) ou le jeton d’actualisation n’est pas valide, a expiré ou est révoqué. | Corrigez l’erreur, puis envoyez à nouveau la demande. |
| unauthorized\_client | Le client authentifié n’est pas autorisé à utiliser ce type d’octroi d’autorisation. | Cela se produit généralement lorsque l’application cliente n’est pas inscrite dans Azure AD ou n’est pas ajoutée au client Azure AD de l’utilisateur. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD. |
| invalid\_client | Échec d’authentification du client. | Les informations d’identification du client ne sont pas valides. Pour résoudre le problème, l’administrateur de l’application met à jour les informations d’identification. |
| unsupported\_grant\_type | Le serveur d’autorisation ne prend pas en charge le type d’octroi d’autorisation. | Modifiez le type d’octroi dans la demande. Ce type d’erreur doit se produire uniquement lors du développement et doit être détecté lors du test initial. |
| invalid\_resource | La ressource cible n’est pas valide car elle n’existe pas, Azure AD ne la trouve pas ou elle n’est pas configurée correctement. | Cela indique que la ressource, si elle existe, n’a pas été configurée dans le client. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD. |
| interaction\_required | La demande nécessite une interaction utilisateur. Par exemple, une étape d’authentification supplémentaire est nécessaire. | Relancez la demande avec la même ressource. |
| temporarily\_unavailable | Le serveur est temporairement trop occupé pour traiter la demande. | relancez la requête. L’application cliente peut expliquer à l’utilisateur que sa réponse est reportée en raison d’une condition temporaire.|

## Erreurs provenant de ressources sécurisées

Voici les erreurs pouvant être retournées par une ressource sécurisée, telle qu’une API web, si elle implémente la spécification [RFC 6750](http://tools.ietf.org/html/rfc6750) de l’infrastructure d’autorisation OAuth 2.0.

Les ressources sécurisées qui implémentent la spécification RFC 6750 émettent des codes d’état HTTP. Si la demande n’inclut pas d’informations d’authentification ou ne contient pas le jeton, la réponse inclut un en-tête `WWW-Authenticate`. Lorsqu’une demande échoue, le serveur de ressources répond avec un code d’état HTTP et un code d’erreur.

Voici un exemple de réponse qui échoue lorsque la demande du client n’inclut pas le jeton du porteur :

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

## Paramètres d’erreur

| Paramètre | Description |
|-----------|-------------|
| authorization\_uri | L’URI (point de terminaison physique) du serveur d’autorisation. Cette valeur est également utilisée comme clé de recherche pour obtenir plus d’informations sur le serveur à partir d’un point de terminaison de détection. <p><p> Le client doit valider l’approbation du serveur d’autorisation. Lorsque la ressource est protégée par Azure AD, il suffit de vérifier que l’URL commence par https://login.windows.net ou un autre nom d’hôte pris en charge par Azure AD. Une ressource spécifique au client doit toujours retourner un URI d’autorisation spécifique au client. |
| error | Une valeur de code d’erreur définie dans la section 5.2 du document [OAuth 2.0 Authorization Framework](http://tools.ietf.org/html/rfc6749) (Infrastructure d’autorisation OAuth 2.0).|
| error\_description | Une description plus détaillée de l’erreur. Ce message n’est pas destiné à offrir une description claire à l’utilisateur final.|
| resource\_id | Retourne l’identificateur unique de la ressource. L’application cliente peut utiliser cet identificateur en tant que valeur du paramètre `resource` lorsqu’elle demande un jeton pour la ressource. <p><p> Il est très important pour l’application cliente de vérifier cette valeur. Sinon, un service malveillant peut être en mesure de provoquer une attaque **d’élévation de privilèges**. <p><p> La stratégie recommandée pour empêcher une attaque consiste à vérifier que le `resource_id` correspond à la base de l’URL de l’API web faisant l’objet d’un accès. Par exemple, si https://service.contoso.com/data fait l’objet d’un accès, le `resource_id` peut être htttps://service.contoso.com/. L’application cliente doit rejeter un `resource_id` qui ne commence pas par l’URL de base sauf s’il existe une autre façon fiable de vérifier l’ID. |

## Codes d’erreur du schéma de porteur

La spécification RFC 6750 définit les erreurs suivantes pour les ressources qui utilisent l’en-tête WWW-Authenticate et le schéma de porteur dans la réponse.

| Code d’état HTTP | Code d'erreur | Description | Action du client |
|------------------|------------|-------------|---------------|
| 400 | invalid\_request | La demande n’est pas correcte. Par exemple, un paramètre est manquant ou elle utilise deux fois le même paramètre. | Corrigez l’erreur et relancez la demande. Ce type d’erreur doit se produire uniquement lors du développement et doit être détecté lors du test initial. |
| 401 | invalid\_token | Le jeton d’accès est manquant, non valide ou révoqué. La valeur du paramètre error\_description fournit des détails supplémentaires. | Demandez un nouveau jeton auprès du serveur d’autorisation. Si le nouveau jeton échoue, une erreur inattendue s’est produite. Envoyez un message d’erreur à l’utilisateur et effectuez une nouvelle tentative après un délai aléatoire. |
| 403 | insufficient\_scope | Le jeton d’accès ne contient pas les autorisations d’emprunt d’identité requises pour accéder à la ressource. | Envoyez une nouvelle demande d’autorisation au point de terminaison d’autorisation. Si la réponse contient le paramètre d’étendue, utilisez la valeur d’étendue dans la demande à la ressource. |
| 403 | insufficient\_access | Le sujet du jeton n’a pas les autorisations requises pour accéder à la ressource. | Invitez l’utilisateur à utiliser un compte différent ou à demander des autorisations pour la ressource spécifiée. |

<!---HONumber=AcomDC_0608_2016-->