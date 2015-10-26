<properties
	pageTitle="Version préliminaire d'Azure AD B2C | Microsoft Azure"
	description="Les types de jetons émis dans la version préliminaire d'Azure AD B2C."
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
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Version préliminaire d'Azure AD B2C : référence de jeton

Azure AD B2C émet plusieurs types de jetons de sécurité lors du traitement de chaque [flux d'authentification](active-directory-b2c-apps.md). Ce document décrit le format, les caractéristiques en matière de sécurité et le contenu de chaque type de jeton.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Types de jetons

Azure AD B2C prend en charge le [protocole d'autorisation OAuth 2.0](active-directory-b2c-reference-protocols.md) qui utilise à la fois les jetons access\_token et refresh\_token. Il prend également en charge l'authentification et la connexion via [OpenID Connect](active-directory-b2c-reference-protocols.md), ce qui introduit un troisième type de jeton : id\_token. Chacun de ces jetons est représenté en tant que « jeton porteur ».

Un jeton porteur est un jeton de sécurité léger qui octroie l’accès à une ressource protégée au « porteur ». En ce sens, le « porteur » désigne toute partie qui peut présenter le jeton. Une partie doit certes d’abord s’authentifier auprès d’Azure AD pour recevoir le jeton porteur, mais si les mécanismes nécessaires à la sécurité du jeton lors de la transmission et du stockage ne sont pas en place, il peut être intercepté et utilisé par une partie non autorisée. Bien que certains jetons de sécurité intègrent un mécanisme de protection contre l’utilisation par des parties non autorisées, les jetons porteurs n’en sont pas dotés et doivent donc être acheminés sur un canal sécurisé, par exemple à l’aide du protocole TLS (HTTPS). Si un jeton porteur est transmis en clair, une partie malveillante peut utiliser une attaque d’intercepteur afin de s’approprier le jeton et de l’utiliser pour accéder sans autorisation à une ressource protégée. Les mêmes principes de sécurité s’appliquent au stockage ou à la mise en cache des jetons porteurs pour une utilisation ultérieure. Veillez systématiquement à ce que votre application transmette et stocke les jetons porteurs de manière sécurisée. Pour en savoir plus sur les aspects de sécurité des jetons porteurs, consultez [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

La plupart des jetons émis par Azure AD B2C sont implémentés en tant que jetons Web JSON (JWT). Un jeton JWT constitue un moyen compact et sécurisé pour les URL de transférer des informations entre deux parties. Les informations contenues dans les jetons JWT sont appelées « revendications », c’est-à-dire des assertions d’informations sur le porteur et le sujet du jeton. Les revendications dans les jetons JWT sont des objets JSON codés et sérialisés pour la transmission. Étant donné que les jetons JWT émis par Azure AD B2C sont signés, mais pas chiffrés, vous pouvez facilement inspecter le contenu d'un jeton JWT à des fins de débogage. Il existe plusieurs outils disponibles pour y parvenir, tels que [calebb.net](https://calebb.net). Pour plus d'informations sur les jetons JWT, consultez la [spécification JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## Jetons id\_token

Les jetons Id\_tokens constituent une forme de jeton de sécurité que votre application reçoit des points de terminaison `authorize` et `token` d'Azure AD B2C. Ils sont représentés en tant que [jetons JWT](#types-of-tokens) et contiennent des revendications que vous pouvez utiliser pour identifier l'utilisateur dans votre application. Lorsqu'ils sont acquis à partir du point de terminaison `authorize`, les jetons id\_tokens sont souvent utilisés pour connecter l'utilisateur à une application Web. Lorsqu'ils sont acquis à partir du point de terminaison `token`, les jetons id\_tokens peuvent être envoyés dans les requêtes HTTP lors de la communication entre deux composants de la même application ou du même service. Vous pouvez utiliser les revendications dans un jeton id\_token comme bon vous semble. En général, elles permettent d’afficher des informations de compte ou de prendre des décisions de contrôle d’accès dans une application.

Les jetons id\_token sont signés, mais ils ne sont pas chiffrés pour l’instant. Quand votre application ou API reçoit un jeton id\_token, elle doit [valider la signature](#validating-tokens) pour prouver l'authenticité du jeton et valider certaines revendications du jeton pour prouver sa validité. Les revendications validées par une application varient selon les spécifications du scénario, mais il existe certaines [validations de revendication communes](#validating-tokens) auxquelles votre application doit procéder dans chaque scénario.

Vous trouverez ci-dessous tous les détails sur les revendications dans les jetons id\_token, ainsi qu’un exemple de jeton id\_token. Notez que les revendications dans les jetons id\_token ne sont pas retournées dans un ordre particulier. Par ailleurs, de nouvelles revendications peuvent être introduites dans des jetons id\_token à n’importe quel moment. L’introduction de nouvelles déclarations ne doit pas altérer votre application. La liste ci-dessous répertorie les revendications qui sont correctement interprétées par votre application au moment de la rédaction de cet article. Pour vous entraîner, essayez d'inspecter les revendications dans l'exemple de jeton id\_token en le collant dans [calebb.net](https://calebb.net). En cas de besoin, vous trouverez des informations supplémentaires dans la [spécification OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### Exemple de jeton id\_token
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

#### Revendications dans les jetons id\_token

Avec Azure AD B2C, vous disposez d'un contrôle affiné du contenu de vos jetons. Vous pouvez configurer des [stratégies](active-directory-b2c-reference-policies.md) pour envoyer un certain ensemble de données utilisateur dans les revendications dont votre application a besoin pour ses opérations. Ces revendications peuvent inclure des propriétés standard, comme `displayName` et `emailAddress` de l'utilisateur, ainsi que des [attributs d'utilisateur personnalisés](active-directory-b2c-reference-custom-attr) que vous pouvez définir dans votre répertoire B2C. Toutefois, chaque jeton id\_token que vous recevez contient un ensemble spécifique de revendications relatives à la sécurité que vos applications peuvent utiliser pour authentifier les utilisateurs et les requêtes de manière sécurisée. Vous trouverez ci-dessous les revendications qui existent dans chaque jeton id\_token émis par Azure AD B2C. Toute autre revendication sera déterminée par la stratégie.

| Nom | Revendication | Exemple de valeur | Description |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Audience | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifie le destinataire du jeton. Dans les jetons id\_token, l’audience est l’ID attribué à votre application dans le portail d’inscription de l’application. Votre application doit valider cette valeur et rejeter le jeton s’il ne correspond pas. |
| Émetteur | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifie le service d'émission de jeton de sécurité (STS) qui construit et retourne le jeton, ainsi que le répertoire Azure AD dans lequel l'utilisateur a été authentifié. Votre application doit valider la revendication de l’émetteur de manière à s’assurer que le jeton provient bien du point de terminaison v2.0. |
| Émis à | `iat` | `1438535543` | Heure à laquelle le jeton a été émis, représentée en heure epoch. |
| Heure d’expiration | `exp` | `1438539443` | Heure à laquelle le jeton n’est plus valide, représentée en heure epoch. Votre application doit utiliser cette revendication pour vérifier la validité de la durée de vie du jeton. |
| Pas avant | `nbf` | `1438535543` | Heure à laquelle le jeton est valide, représentée en heure epoch. Elle est généralement identique à l'heure d'émission. Votre application doit utiliser cette revendication pour vérifier la validité de la durée de vie du jeton. |
| Version | `ver` | `1.0` | Version du jeton id\_token, telle que définie par Azure AD. |
| Hachage de code | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Le hachage de code n’est inclus dans un jeton id\_token que si ce dernier est émis en même temps qu’un code d’autorisation OAuth 2.0. Il peut servir à valider l’authenticité d’un code d’autorisation. Pour plus d'informations sur l'exécution de cette validation, consultez la [spécification OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| Hachage de jeton d’accès | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Le hachage de jeton d’accès n’est inclus dans un jeton id\_token que si ce dernier est émis en même temps qu’un jeton d’accès OAuth 2.0. Il peut servir à valider l’authenticité d’un jeton d’accès. Pour plus d'informations sur l'exécution de cette validation, consultez la [spécification OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| Valeur à usage unique | `nonce` | `12345` | La valeur à usage unique est une stratégie d’atténuation des attaques par relecture de jetons. Votre application peut spécifier une valeur à usage unique dans une demande d'autorisation à l'aide du paramètre de requête `nonce`. La valeur que vous fournissez dans la demande est émise dans la revendication `nonce` du jeton id\_token, sans aucune modification. Cela permet à votre application de comparer la valeur à celle spécifiée dans la demande et d’associer la session de l’application à un jeton id\_token donné. Votre application doit effectuer cette validation au cours du processus de validation du jeton id\_token. |
| Objet | `sub` | `Not supported currently. Use oid claim.` | Principal sur lequel portent les assertions d’informations du jeton, comme l’utilisateur d’une application. Cette valeur n’est pas modifiable et ne peut pas être réassignée ou réutilisée. Vous pouvez donc l’utiliser pour effectuer des vérifications d'autorisation en toute sécurité, comme lorsque le jeton est utilisé pour accéder à une ressource. Toutefois, la revendication de l'objet n'est pas encore implémentée dans la version préliminaire d'Azure AD B2C. Au lieu d'utiliser la revendication de l'objet pour l'autorisation, configurez les stratégies pour inclure la revendication `oid` d'ID d'objet et utiliser sa valeur pour identifier les utilisateurs. |
| Référence de classe du contexte d'authentification | `acr` | `b2c_1_sign_in` | Nom de la stratégie utilisée pour acquérir le jeton id\_token. |
| Moment de l'authentification | `auth_time | `1438535543` | Heure à laquelle l'utilisateur a entré ses informations d'identification pour la dernière fois, en heure epoch. |



## Jetons d’accès

Pour le moment, Azure AD B2C n'émet pas de jetons d'accès. L'authentification entre deux parties différentes n'est pas prise en charge à ce stade précoce de la version préliminaire. Toutefois, vous pouvez utiliser des jetons id\_tokens pour communiquer entre les composants de la même application. Pour en savoir plus sur les applications et les scénarios d'authentification pris en charge par la version préliminaire d'Azure AD B2C, reportez-vous à [l'article sur les applications B2C](active-directory-b2c-apps.md).

## Jetons d’actualisation

Les jetons d'actualisation sont des jetons de sécurité que votre application peut utiliser pour acquérir de nouveaux jetons id\_tokens et access\_tokens dans un flux OAuth 2.0. Ils permettent à votre application d’obtenir un accès à long terme à des ressources au nom d’un utilisateur, et ce sans nécessiter l’intervention de l’utilisateur.

Pour recevoir une actualisation dans une réponse de jeton, votre application doit demander l'étendue `offline_acesss`. Pour en savoir plus sur les étendues `offline_access`, reportez-vous à la [référence du protocole Azure AD B2C](active-directory-b2c-reference-protocols.md).

Les jetons d’actualisation sont, et seront toujours, entièrement opaques pour votre application. Ils sont émis par Azure AD et ils ne peuvent être inspectés et interprétés que par Azure AD. Les jetons d’actualisation sont de longue durée. Toutefois, quand vous écrivez votre application, faites en sorte qu’elle n’attende pas un jeton d’actualisation d’une durée particulière. Les jetons d’actualisation peuvent être rendus non valides à tout moment, et ce pour diverses raisons. Pour savoir si un jeton d'actualisation est valide, votre application doit tenter de l'échanger en faisant une demande de jeton auprès d'Azure AD. C'est la seule façon de faire.

Quand vous échangez un jeton d'actualisation contre un nouveau jeton (et si l'étendue `offline_access` a été accordée à votre application), vous recevez un nouveau jeton d'actualisation dans la réponse du jeton. Vous devez enregistrer le jeton d'actualisation nouvellement émis, ce qui a pour effet de remplacer celui que vous avez utilisé précédemment dans la demande. Vous avez ainsi la garantie que vos jetons d’actualisation resteront valides le plus longtemps possible.

## Validation des jetons

À ce stade, la seule opération de validation que vos applications doivent effectuer est la validation des jetons id\_token. Pour valider un jeton id\_token, votre application doit valider la signature du jeton id\_token et les revendications dans le jeton id\_token.

<!-- TODO: Link -->
Il existe de nombreuses bibliothèques open source pour valider les jetons JWT en fonction de votre langage préféré. Nous vous recommandons d'explorer ces options plutôt que d'implémenter votre propre logique de validation. Ces informations seront utiles pour savoir comment utiliser correctement ces bibliothèques.

#### Validation de la signature
Un jeton JWT contient trois segments séparés par le caractère `.`. Le premier segment est appelé l'**en-tête**, le second le **corps** et le troisième la **signature**. Le segment de signature peut être utilisé pour valider l’authenticité du jeton id\_token afin qu’il soit approuvé par votre application.

Les jetons id\_token sont signés à l’aide d’algorithmes de chiffrement asymétrique standard, tels que RSA 256. L’en-tête du jeton id\_token contient des informations sur la clé et la méthode de chiffrement utilisées pour signer le jeton :

```
{
		"typ": "JWT",
		"alg": "RS256",
		"kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

La revendication `alg` indique l'algorithme utilisé pour signer le jeton, tandis que la revendication `kid` ou `x5t` indique la clé publique utilisée pour signer le jeton.

À tout moment, Azure AD peut signer un jeton id\_token à l'aide de l'un des ensembles de paires de clés publique-privée. Étant donné qu'Azure AD alterne le jeu de clés possible de façon périodique, votre application doit être écrite de manière à gérer automatiquement ces changements de clés. Pour vérifier les mises à jour apportées aux clés publiques utilisées par Azure AD, spécifiez une fréquence raisonnable d'environ 24 heures.

Azure AD B2C présente un point de terminaison de métadonnées OpenID Connect, qui permet à une application de récupérer les informations relatives à Azure AD B2C lors de l'exécution. Ces informations incluent les points de terminaison, le contenu des jetons et les clés de signature de jetons. Il existe un document de métadonnées JSON pour chaque stratégie dans votre répertoire B2C. Par exemple, le document de métadonnées pour la stratégie `b2c_1_sign_in` dans `fabrikamb2c.onmicrosoft.com` se trouve à l'emplacement suivant :

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Pour acquérir les données de la clé de signature nécessaires pour valider la signature, utilisez le document de métadonnées OpenID Connect à l’emplacement suivant :

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

où `fabrikamb2c.onmicrosoft.com` est le répertoire B2C utilisé pour authentifier l'utilisateur et `b2c_1_sign_in` est la stratégie utilisée pour acquérir le jeton id\_token. Afin de déterminer la stratégie utilisée pour la connexion d'un jeton id\_token (et l'emplacement à partir duquel extraire les métadonnées), deux options sont possibles. Tout d'abord, le nom de la stratégie est inclus dans la revendication `acr` du jeton id\_token. Vous pouvez analyser les revendications contenues dans le corps du jeton JWT par le biais d'un décodage base-64 du corps et la désérialisation de la chaîne JSON résultante. La revendication `acr` sera le nom de la stratégie qui a été utilisée pour émettre le jeton id\_token. L'autre option consiste à coder la stratégie dans la valeur du paramètre `state` lors de l'émission de la requête, puis à la décoder pour déterminer la stratégie qui a été utilisée. Les deux méthodes sont parfaitement valides.

Le document de métadonnées est un objet JSON qui contient diverses informations utiles, comme l'emplacement des différents points de terminaison nécessaires pour effectuer l'authentification OpenID Connect. Il comprend également un `jwks_uri` qui indique l'emplacement de l'ensemble des clés publiques utilisées pour signer les jetons. Cet emplacement est fourni ci-dessous, mais il est préférable d'extraire cet emplacement de manière dynamique à l'aide du document de métadonnées et d'analyser le `jwks_uri` :

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Le document JSON situé à cette URL contient toutes les informations sur les clés publiques utilisées à cet instant donné. Votre application peut utiliser les revendications `kid` ou `x5t` dans l'en-tête JWT pour sélectionner la clé publique utilisée dans ce document pour signer un jeton donné. Elle peut ensuite procéder à la validation des signatures à l’aide de la clé publique correcte et de l’algorithme indiqué.

La validation des signatures dépasse le cadre de ce document. Si vous avez besoin d’aide, de nombreuses bibliothèques open source sont disponibles.

#### Validation des revendications
Quand votre application ou API reçoit un jeton id\_token, elle doit également procéder à quelques vérifications sur les revendications dans le jeton id\_token. Il s’agit des actions suivantes :

- Revendication **Audience** : il s'agit de vérifier que le jeton id\_token était bien destiné à votre application.
- Revendications **Pas avant** et **Heure d'expiration** : il s'agit de vérifier que le jeton id\_token n'est pas arrivé à expiration.
- Revendication **Émetteur** : il s'agit de vérifier que le jeton a été effectivement émis à votre application par Azure AD.
- **Valeur à usage unique** : il s'agit d'atténuer les attaques par relecture de jetons.

Les valeurs attendues pour ces revendications sont détaillées plus haut dans la [section Jeton id\_token](#id_tokens).

## Durées de vie de jeton

Les durées de vie de jeton suivantes sont fournies uniquement à titre indicatif dans le cadre du développement et du débogage d’applications. Quand vous écrivez vos applications, faites en sorte qu’elles n’attendent pas des durées de vie constantes, car celles-ci sont amenées à changer à un moment donné.

| Jeton | Durée de vie | Description |
| ----------------------- | ------------------------------- | ------------ |
| Jetons id\_token | 1 heure | Les jetons id\_token sont généralement valides une heure. Votre application web peut utiliser cette même durée de vie pour conserver sa propre session avec l’utilisateur (recommandé) ou vous pouvez choisir une durée de vie de session différente. Si votre application a besoin d'obtenir un nouveau jeton id\_token, elle doit simplement faire une nouvelle demande de connexion auprès d'Azure AD. Si l'utilisateur dispose d'une session de navigateur valide avec Azure AD, il se peut qu'il ne soit pas obligé de retaper ses informations d'identification. |
| Jetons d’actualisation | Jusqu’à 14 jours | Un jeton d’actualisation est valide pendant 14 jours au maximum. Toutefois, le jeton d’actualisation peut devenir non valide à tout moment pour différentes raisons. Votre application doit donc continuer d’utiliser un jeton d’actualisation jusqu’à ce qu’il échoue ou jusqu’à ce que votre application le remplace par un nouveau. Un jeton d’actualisation devient également non valide si l’utilisateur a entré ses informations d’identification il y a plus de 90 jours. |
| Codes d'autorisation | 5 minutes | Les codes d'autorisation, qui sont volontairement de courte durée, doivent être échangés immédiatement contre des jetons d'accès, des jetons d'ID et des jetons d'actualisation quand ils sont reçus. |

<!---HONumber=Oct15_HO3-->