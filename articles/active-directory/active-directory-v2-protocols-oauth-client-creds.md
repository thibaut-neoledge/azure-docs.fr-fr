
<properties
    pageTitle="Flux des informations d’identification du client OAuth v2.0 Azure AD | Microsoft Azure"
    description="Création d'applications Web à l'aide de la mise en œuvre du protocole d'authentification OAuth 2.0 d'Azure AD."
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
    ms.date="09/26/2016"
    ms.author="dastrock"/>


# <a name="v2.0-protocols---oauth-2.0-client-credentials-flow"></a>Protocoles v2.0 - Flux des informations d’identification du client OAuth 2.0

[L’octroi des informations d’identification du client OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4), parfois appelé « OAuth à deux branches », peut être utilisé pour accéder à des ressources hébergées sur le web à l’aide de l’identité d’une application.  Il est couramment utilisé pour les interactions de serveur à serveur qui doivent s’exécuter en arrière-plan sans la présence immédiate d’un utilisateur final.  Ces types d’application sont souvent appelés **démons** (daemons) ou **comptes de service**.

> [AZURE.NOTE]
    Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).

Dans l’octroi « OAuth à trois branches » plus courant, une application cliente est autorisée à accéder à une ressource pour le compte d’un utilisateur particulier.  L’autorisation est **déléguée** de l’utilisateur à l’application, en général, durant le processus de [consentement](active-directory-v2-scopes.md) .  Toutefois, dans le flux des informations d’identification du client, les autorisations sont accordées **directement** à l’application elle-même.  Lorsque l’application présente un jeton à une ressource, la ressource impose que l’application elle-même, pas un utilisateur, ait l’autorisation d’effectuer une action.

## <a name="protocol-diagram"></a>Schéma de protocole
Le flux des informations d’identification du client complet ressemble à ceci : chacune des étapes est décrite en détail ci-dessous.

![Flux des informations d’identification du client](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Obtenir l’autorisation directe 
Il existe deux façons dont une application reçoit l’autorisation directe d’accéder à une ressource : via une liste de contrôle d’accès sur la ressource ou via l’attribution de l’autorisation d’application dans Azure AD.  Il existe plusieurs autres façons dont une ressource peut choisir d’autoriser ses clients, et chaque serveur de ressources peut choisir la méthode la plus logique pour son application.  Ces deux méthodes sont les plus courantes dans Azure AD et sont recommandées pour les clients et les ressources qui souhaitent exécuter le flux des informations d’identification du client.

### <a name="access-control-lists"></a>Listes de contrôle d'accès
Un fournisseur de ressources donné peut appliquer une vérification d’autorisation basée sur une liste d’ID d’application qu’il connaît et octroyer un certain niveau d’accès.  Lorsque la ressource reçoit un jeton à partir du point de terminaison v2.0, elle peut décoder le jeton et extraire l’ID d’application du client à partir des revendications `appid` et `iss`.  Elle peut ensuite comparer l’application à une liste de contrôle d’accès (ACL) qu’elle gère.  La granularité et la méthode de la liste de contrôle d’accès peuvent varier considérablement d’une ressource à l’autre.

Les exécuteurs de test pour une application web ou une API web constituent un cas d’utilisation courant pour ces listes de contrôle d’accès.  L’API web peut n’accorder qu’un sous-ensemble de ses autorisations complètes à ses clients divers.  Mais pour exécuter des tests de bout en bout sur l’API, un client de test est créé qui acquiert des jetons à partir du point de terminaison v2.0 et envoie ceux-ci à l’API.  L’API peut ensuite appliquer la liste de contrôle d’accès à l’ID d’application du client de test pour lui accorder un accès complet à toutes les fonctionnalités de l’API.  Notez que si vous disposez d’une telle liste sur votre service, vous devez être sûr de ne pas seulement valider l’`appid` de l’appelant, mais vous devez également vérifier que l’`iss` du jeton est approuvé.

Ce type d’autorisation est courant pour les démons et les comptes de service qui doivent accéder à des données qui appartiennent à des utilisateurs avec des comptes Microsoft personnels.  Pour les données appartenant à des organisations, nous vous recommandons d’acquérir l’autorisation requise via les autorisations de l’application.

### <a name="application-permissions"></a>Autorisations de l’application
Au lieu d’utiliser des listes de contrôle d’accès, les API peuvent exposer un ensemble **d’autorisations de l’application** qui peuvent être accordées à une application.  Une autorisation de l’application est accordée à une application par un administrateur d’une organisation et peut uniquement être utilisée pour accéder aux données appartenant à cette organisation et ses employés.  Par exemple, Microsoft Graph expose plusieurs autorisations d’application :

- Lecture des messages dans toutes les boîtes aux lettres
- Lecture et écriture de messages dans toutes les boîtes aux lettres
- Envoi de courrier électronique en tant que n’importe quel utilisateur
- Lecture des données de l’annuaire
- [+ plus](https://graph.microsoft.io)

Afin d’acquérir ces autorisations dans votre application, vous pouvez effectuer les étapes suivantes.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Demander les autorisations dans le portail d’inscription de l’application

- Accédez à votre application dans [apps.dev.microsoft.com](https://apps.dev.microsoft.com) ou [créez une application](active-directory-v2-app-registration.md) si ce n’est pas déjà fait.  Vous devez vous assurer que votre application a créé au moins un secret d’application.
- Recherchez la section **Autorisations directes pour les applications** et ajoutez les autorisations dont votre application a besoin.
- Veillez à **enregistrer** l’inscription de l’application

#### <a name="recommended:-sign-the-user-into-your-app"></a>Recommandé : connectez l’utilisateur à votre application

En général, lors de la création d’une application qui utilise les autorisations de l’application, l’application doit disposer d’une page/vue qui permet à l’administrateur d’approuver les autorisations de l’application.  Cette page peut faire partie du flux d’inscription de l’application, des paramètres de l’application ou d’un flux de connexion dédié.  Dans de nombreux cas, il est judicieux pour l’application d’afficher la vue de « connexion » uniquement après qu’un utilisateur se soit connecté avec un compte Microsoft professionnel ou scolaire.

La connexion de l’utilisateur à l’application vous permet d’identifier l’organisation à laquelle l’utilisateur appartient, avant de lui demander d’approuver les autorisations de l’application.  Bien que cela ne soit pas strictement nécessaire, cela peut vous aider à créer une expérience plus intuitive pour les utilisateurs professionnels.  Pour connecter l’utilisateur, suivez nos [didacticiels sur le protocole v2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Demander les autorisations à un administrateur d’annuaire

Lorsque vous êtes prêt à demander les autorisations à l’administrateur de l’entreprise, vous pouvez rediriger l’utilisateur vers le **point de terminaison de consentement de l’administrateur**v2.0.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| locataire | required | Le client d’annuaire auquel vous souhaitez demander l’autorisation.  Peut être fourni au format GUID ou sous forme de nom convivial.  Si vous ne savez pas à quel client appartient l’utilisateur et si vous souhaitez lui permettre de se connecter avec n’importe quel client, utilisez `common`. |
| client_id | required | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| redirect_uri | required | Le redirect_uri où vous souhaitez que la réponse soit envoyée pour être gérée par votre application.  Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL, et peut avoir des segments de chemin d’accès supplémentaires. |
| state | recommandé | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton.  Il peut s’agir d’une chaîne du contenu de votre choix.  La valeur d’état est utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |

À ce stade, Azure AD impose que seul un administrateur de client peut se connecter pour terminer la demande.  L’administrateur est invité à approuver toutes les autorisations directes d’application demandées pour votre application dans le portail d’inscription. 

##### <a name="successful-response"></a>Réponse correcte
Si l’administrateur approuve les autorisations pour votre application, la réponse correcte sera :

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paramètre | Description |
| ----------------------- | ------------------------------- | --------------- |
| locataire | Le client d’annuaire ayant accordé à votre application les autorisations demandées, au format GUID. |
| state | Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton.  Il peut s’agir d’une chaîne du contenu de votre choix.  La valeur d’état est utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| admin_consent | Sera défini sur `True`. |


##### <a name="error-response"></a>Réponse d’erreur
Si l’administrateur n’approuve pas les autorisations pour votre application, la réponse d’échec sera :

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paramètre | Description |
| ----------------------- | ------------------------------- | --------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur.  |

Une fois que vous avez reçu une réponse correcte du point de terminaison de mise en service de l’application, votre application a acquis les autorisations directes d’application qu’elle avait demandées.  Vous pouvez maintenant passer à la demande d’un jeton pour la ressource souhaitée.

## <a name="get-a-token"></a>Obtention d’un jeton
Une fois que vous avez acquis l’autorisation nécessaire pour votre application, vous pouvez passer à l’acquisition des jetons d’accès pour les API.  Pour obtenir un jeton à l’aide de l’octroi des informations d’identification du client, envoyez une demande POST au point de terminaison v2.0 `/token` :

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| client_id | required | L’ID d’application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) a affecté à votre application. |
| scope | required | La valeur transmise pour le paramètre `scope` dans cette demande doit être l’identificateur de ressource (URI ID d’application) de la ressource souhaitée, avec le suffixe `.default`.  Dans l’exemple Microsoft Graph donné, la valeur doit donc être `https://graph.microsoft.com/.default`.  Cette valeur indique au point de terminaison v2.0 que parmi toutes les autorisations directes d’application que vous avez configurées pour votre application, il doit émettre un jeton pour celles se rapportant à la ressource souhaitée. |
| client_secret | required | Le secret d’application que vous avez créé dans le portail d’inscription pour votre application. |
| grant_type | required | Doit être `client_credentials`. | 

#### <a name="successful-response"></a>Réponse correcte
Une réponse correcte prendra la forme :

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access_token | Le jeton d’accès demandé. L’application peut utiliser ce jeton pour procéder à l’authentification sur la ressource sécurisée, par exemple une API Web. |
| token_type | Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est `Bearer`.  |
| expires_in | La durée de validité (en secondes) du jeton d’accès. |

#### <a name="error-response"></a>Réponse d’erreur
Une réponse incorrecte prendra la forme :

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| error | Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification.  |
| error_codes | Liste des codes d’erreur STS spécifiques pouvant être utiles dans les tests de diagnostic.  |
| timestamp | Heure à laquelle l’erreur s’est produite. |
| trace_id | Identifiant unique de la demande pouvant être utile dans les tests de diagnostic.  |
| correlation_id | Identifiant unique de la demande pouvant être utile dans les tests de diagnostic sur les divers composants. |

## <a name="use-a-token"></a>Utilisation d’un jeton
Maintenant que vous avez acquis un jeton, vous pouvez l’utiliser pour effectuer des demandes auprès de la ressource.  Lorsque le jeton expire, il suffit de répéter la demande auprès du point de terminaison `/token` pour acquérir un nouveau jeton d’accès.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Exemple de code
Pour voir un exemple d’application qui implémente l’octroi client_credentials à l’aide du point de terminaison de consentement de l’administrateur, consultez notre [exemple de code de démon v2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).


<!--HONumber=Oct16_HO2-->


