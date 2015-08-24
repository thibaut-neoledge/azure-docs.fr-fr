<properties
	pageTitle="Modèle d’application v2 | Microsoft Azure"
	description="Types de jetons et de revendications émis par le point de terminaison v2.0"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Version préliminaire du modèle d’application v2.0 : informations de référence sur les jetons

Le point de terminaison v2.0 émet plusieurs types de jetons de sécurité lors du traitement de chaque [flux d’authentification](active-directory-v2-flows.md). Ce document décrit le format, les caractéristiques en matière de sécurité et le contenu de chaque type de jeton.

> [AZURE.NOTE]Ces informations s’appliquent à la version préliminaire publique du modèle d’application v2.0. Pour obtenir des instructions sur l’intégration au service Azure AD disponible au grand public, consultez le [Guide du développeur Azure Active Directory](active-directory-developers-guide.md).

## Types de jetons

Le point de terminaison v2.0 prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-v2-protocols.md) qui utilise à la fois les jetons access\_token et refresh\_token. Il prend également en charge l’authentification et la connexion via [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow), ce qui introduit un troisième type de jeton : id\_token. Chacun de ces jetons est représenté en tant que « jeton porteur ».

Un jeton porteur est un jeton de sécurité léger qui octroie l’accès à une ressource protégée au « porteur ». En ce sens, le « porteur » désigne toute partie qui peut présenter le jeton. Une partie doit certes d’abord s’authentifier auprès d’Azure AD pour recevoir le jeton porteur, mais si les mécanismes nécessaires à la sécurité du jeton lors de la transmission et du stockage ne sont pas en place, il peut être intercepté et utilisé par une partie non autorisée. Bien que certains jetons de sécurité intègrent un mécanisme de protection contre l’utilisation par des parties non autorisées, les jetons porteurs n’en sont pas dotés et doivent donc être acheminés sur un canal sécurisé, par exemple à l’aide du protocole TLS (HTTPS). Si un jeton porteur est transmis en clair, une partie malveillante peut utiliser une attaque d’intercepteur afin de s’approprier le jeton et de l’utiliser pour accéder sans autorisation à une ressource protégée. Les mêmes principes de sécurité s’appliquent au stockage ou à la mise en cache des jetons porteurs pour une utilisation ultérieure. Veillez systématiquement à ce que votre application transmette et stocke les jetons porteurs de manière sécurisée. Pour en savoir plus sur les aspects de sécurité des jetons porteurs, consultez [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

La plupart des jetons émis par le point de terminaison v2.0 sont implémentés en tant que jetons Web JSON (JWT). Un jeton JWT constitue un moyen compact et sécurisé pour les URL de transférer des informations entre deux parties. Les informations contenues dans les jetons JWT sont appelées « revendications », c’est-à-dire des assertions d’informations sur le porteur et le sujet du jeton. Les revendications dans les jetons JWT sont des objets JSON codés et sérialisés pour la transmission. Étant donné que les jetons JWT émis par le point de terminaison v2.0 sont signés, mais pas chiffrés, vous pouvez facilement inspecter le contenu d’un jeton JWT à des fins de débogage. Il existe plusieurs outils disponibles pour y parvenir, tels que [calebb.net](https://calebb.net). Pour plus d’informations sur les jetons JWT, consultez la [spécification JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## Jetons id\_token

Les jetons id\_token constituent une forme de jeton de sécurité de connexion. Votre application les reçoit quand elle procède à l’authentification à l’aide d’[OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). Ils sont représentés en tant que [jetons JWT](#types-of-tokens) et contiennent des revendications que vous pouvez utiliser pour connecter l’utilisateur à votre application. Vous pouvez utiliser les revendications dans un jeton id\_token comme bon vous semble. En général, elles permettent d’afficher des informations de compte ou de prendre des décisions de contrôle d’accès dans une application. Le point de terminaison v2.0 émet un seul type de jeton id\_token. Son jeu de revendications est cohérent quel que soit le type d’utilisateur connecté. Cela signifie que le format et le contenu des jetons id\_token sont identiques pour les utilisateurs titulaires d’un compte Microsoft personnel et ceux titulaires d’un compte professionnel ou scolaire.

Les jetons id\_token sont signés, mais ils ne sont pas chiffrés pour l’instant. Quand votre application reçoit un jeton id\_token, elle doit [valider la signature](#validating-tokens) pour prouver l’authenticité du jeton et valider certaines revendications du jeton pour prouver sa validité. Les revendications validées par une application varient selon les spécifications du scénario, mais il existe certaines [validations de revendication communes](#validating-tokens) auxquelles votre application doit procéder dans chaque scénario.

Vous trouverez ci-dessous tous les détails sur les revendications dans les jetons id\_token, ainsi qu’un exemple de jeton id\_token. Notez que les revendications dans les jetons id\_token ne sont pas retournées dans un ordre particulier. Par ailleurs, de nouvelles revendications peuvent être introduites dans des jetons id\_token à n’importe quel moment. L’introduction de nouvelles déclarations ne doit pas altérer votre application. La liste ci-dessous répertorie les revendications qui sont correctement interprétées par votre application au moment de la rédaction de cet article. Pour vous entraîner, essayez d’inspecter les revendications dans l’exemple de jeton id\_token en le collant dans [calebb.net](https://calebb.net). En cas de besoin, vous trouverez des informations supplémentaires dans la [spécification OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### Exemple de jeton id\_token
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ
19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI0OTIxMDI1My0wYmExLTRhOWEtYTQyNC02MTY5OTlmYWI2MjAiL
CJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTAzMTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMW
YzNzZlL3YyLjAvIiwiaWF0IjoxNDM4NTM1NTQzLCJuYmYiOjE0Mzg1MzU1NDMsImV4cCI6MTQzODUzOTQ0MywidmVyIjoiMi4
wIiwidGlkIjoiYjk0MTAzMTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlIiwib2lkIjoiYTFlYmRkZTgtZTRmOS00NTcx
LWFkOTMtMzA1OWUzNzUwZDIzIiwicHJlZmVycmVkX3VzZXJuYW1lIjoic2FtcGxlLmFkbWluQHN0cm9ja2lzZGV2Lm9ubWljc
m9zb2Z0LmNvbSIsInN1YiI6IjJvMmQ5SVBGVzI5MGo0RVkySXg0RUdoaEtlWnVGaC1LcFhHS2tuZkNxRWMiLCJuYW1lIjoiU2
FtcGxlIEFkbWluIiwibm9uY2UiOiIxMjM0NSIsImNfaGFzaCI6IngxeU92VTZRaXE0Y1lVcVIxeDBvM2cifQ.Qk9exyv04I6a
P6Sju2xNG9O2sj8dG-aEoJeS5dmnjdLo8k1ZzgZd7w-6yCrKXgPh4FJ1YY-08DZnHNmP3oxm3zmEv3RUIBEyTmo3598PRYLWl
vttis1KD5PoNgAyKfHqiOCL5q_Owd0m9oAKDagbJhRVZOS89phllA0AQnaI6hJOKvMsbOYJt-w00y6TXf1Nkzp_Yey8EmRiwN
7gqvudL1UfZ7_UbST2DBjPIyZsv0gT8gpApz6CecCOyX1NNWpUg8ZRkNnOjGL-IMhq4okPCTTfYriOo93z9Y9v6NmaJxV5bBN
V-DIguXSzLVKnnflfSLyvhinsjLKCnu9L3oXHxw
```

#### Revendications dans les jetons id\_token
| Nom | Revendication | Exemple de valeur | Description |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Audience | `aud` | `49210253-0ba1-4a9a-a424-616999fab620` | Identifie le destinataire du jeton. Dans les jetons id\_token, l’audience est l’ID attribué à votre application dans le portail d’inscription de l’application. Votre application doit valider cette valeur et rejeter le jeton s’il ne correspond pas. |
| Émetteur | `iss` | `https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0/` | Identifie le service d’émission de jeton de sécurité (STS) qui construit et retourne le jeton, ainsi que le client Azure AD dans lequel l’utilisateur a été authentifié. Votre application doit valider la revendication de l’émetteur de manière à s’assurer que le jeton provient bien du point de terminaison v2.0. Il peut également utiliser la partie GUID de la revendication pour restreindre l’ensemble des clients qui sont autorisés à se connecter à l’application. |
| Émis à | `iat` | `1438535543` | Heure à laquelle le jeton a été émis, représentée en heure epoch. |
| Heure d’expiration | `exp` | `1438539443` | Heure à laquelle le jeton n’est plus valide, représentée en heure epoch. Votre application doit utiliser cette revendication pour vérifier la validité de la durée de vie du jeton. |
| Version | `ver` | `2.0` | Version du jeton id\_token, telle que définie par Azure AD. Pour le modèle d’application v2.0, la valeur est `2.0`. |
| ID client | `tid` | `b9410318-09af-49c2-b0c3-653adc1f376e` | GUID représentant le client Azure AD d’où provient l’utilisateur. Pour les comptes professionnels et scolaires, le GUID correspond à l’ID de client immuable de l’organisation à laquelle appartient l’utilisateur. Pour les comptes personnels, la valeur est `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Hachage de code | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Le hachage de code n’est inclus dans un jeton id\_token que si ce dernier est émis en même temps qu’un code d’autorisation OAuth 2.0. Il peut servir à valider l’authenticité d’un code d’autorisation. Pour plus d’informations sur l’exécution de cette validation, consultez la [spécification OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| Hachage de jeton d’accès | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Le hachage de jeton d’accès n’est inclus dans un jeton id\_token que si ce dernier est émis en même temps qu’un jeton d’accès OAuth 2.0. Il peut servir à valider l’authenticité d’un jeton d’accès. Pour plus d’informations sur l’exécution de cette validation, consultez la [spécification OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| Valeur à usage unique | `nonce` | `12345` | La valeur à usage unique est une stratégie d’atténuation des attaques par relecture de jetons. Votre application peut spécifier une valeur à usage unique dans une demande d’autorisation à l’aide du paramètre de requête `nonce`. La valeur que vous fournissez dans la demande est émise dans la revendication `nonce` du jeton id\_token, sans aucune modification. Cela permet à votre application de comparer la valeur à celle spécifiée dans la demande et d’associer la session de l’application à un jeton id\_token donné. Votre application doit effectuer cette validation au cours du processus de validation du jeton id\_token. |
| Nom | `name` | `Leonardo DaVinci` | La revendication de nom fournit une valeur humaine lisible qui identifie le sujet du jeton. Il n’est pas certain que cette valeur soit unique. Elle est mutable et conçue pour être utilisée uniquement à des fins d’affichage. |
| Nom d’utilisateur par défaut | `preferred_username` | `leo@outlook.com` | Nom d’utilisateur principal utilisé pour représenter l’utilisateur dans le point de terminaison v2.0. Il peut s’agir d’une adresse e-mail, d’un numéro de téléphone ou d’un nom d’utilisateur générique sans format spécifié. Sa valeur est mutable et peut changer pour un utilisateur donné au fil du temps. |
| Objet | `sub` | `AAAAAAAAAAAAAAAAAAAAAOUtxUJsxQtHuMcFCIA1NC0` | Principal sur lequel portent les assertions d’informations du jeton, comme l’utilisateur d’une application. Cette valeur n’est pas modifiable et ne peut pas être réassignée ou réutilisée. Vous pouvez donc l’utiliser pour effectuer des vérifications d'autorisation en toute sécurité, comme lorsque le jeton est utilisé pour accéder à une ressource. Étant donné que le sujet est toujours présent dans les jetons émis par Azure AD, vous êtes invité à utiliser cette valeur dans un but général de système d'autorisation. |
| ID d’objet | `oid` | `27cb5cec-7c0c-40b4-a69a-22500b6ea853` | ID d’objet du compte professionnel ou scolaire dans le système Azure AD. Cette revendication n’est pas émise pour les comptes Microsoft personnels. |

<!---
| Not Before | `nbf` | `1438535543` |  The time at which the token becomes valid, represented in epoch time. It is usually the same as the issuance time.  Your app should use this claim to verify the validity of the token lifetime.  |
-->



## Jetons d’accès

Les jetons d’accès émis par le point de terminaison v2.0 acceptent deux formats. Les jetons d’accès émis au nom d’un compte professionnel ou scolaire sont des jetons JWT (similaires aux jetons id\_token). Les jetons d’accès émis au nom d’un compte Microsoft personnel sont au format « compact tickets ». Ainsi, lors du développement, vous remarquerez peut-être que les jetons d’accès émis par le point de terminaison v2.0 ont un format de chaîne différent. Cette différence au niveau des jetons d’accès sera progressivement éliminée du point de terminaison v2.0.

Cela dit, les jetons d’accès émis par le point de terminaison v2.0 sont pour l’instant uniquement consommables par les services Microsoft. Vos applications n’ont pas besoin de valider ou d’inspecter les jetons d’accès dans les scénarios actuellement pris en charge. Vous pouvez traiter les jetons d’accès comme étant totalement opaques ; il s’agit simplement de chaînes que votre application peut passer à Microsoft dans des requêtes HTTP.

Dans un avenir proche, le point de terminaison v2.0 permettra à votre application de recevoir des jetons d’accès à partir d’autres clients. À ce stade, nous mettrons à jour ces informations pour refléter les données requises par votre application pour valider les jetons d’accès et effectuer d’autres tâches similaires.

Quand vous demandez un jeton d’accès auprès du point de terminaison v2.0, ce dernier retourne également des métadonnées sur le jeton d’accès que votre application peut consommer. Ces informations incluent le délai d’expiration du jeton d’accès et les étendues dans lesquelles il est valide. Votre application peut ainsi effectuer une mise en cache intelligente des jetons d’accès sans avoir à les analyser.

## Jetons d’actualisation

Les jetons d’actualisation sont des jetons de sécurité que votre application peut utiliser pour acquérir de nouveaux jetons d’accès dans un flux OAuth 2.0. Ils permettent à votre application d’obtenir un accès à long terme à des ressources au nom d’un utilisateur, et ce sans nécessiter l’intervention de l’utilisateur.

Les jetons d’actualisation prennent en charge plusieurs ressources. Cela signifie qu’un jeton d’actualisation reçu au cours d’une demande de jeton pour une ressource peut être échangé contre des jetons d’accès à une ressource complètement différente.

Pour recevoir une actualisation dans une réponse de jeton, votre application doit demander et se voir accorder l’étendue `offline_acesss`. Pour en savoir plus sur l’étendue `offline_access`, consultez [l’article sur le consentement et les étendues](active-directory-v2-scopes.md).

Les jetons d’actualisation sont, et seront toujours, entièrement opaques pour votre application. Émis par le point de terminaison v2.0 Azure AD, ils ne peuvent être inspectés et interprétés que par le point de terminaison v2.0. Les jetons d’actualisation sont de longue durée. Toutefois, quand vous écrivez votre application, faites en sorte qu’elle n’attende pas un jeton d’actualisation d’une durée particulière. Les jetons d’actualisation peuvent être rendus non valides à tout moment, et ce pour diverses raisons. Pour savoir si un jeton d’actualisation est valide, votre application doit tenter de l’échanger en faisant une demande de jeton auprès du point de terminaison v2.0. C'est la seule façon de faire.

Quand vous échangez un jeton d’actualisation contre un nouveau jeton d’accès (et si l’étendue `offline_access` a été accordée à votre application), vous recevez un nouveau jeton d’actualisation dans la réponse du jeton. Vous devez enregistrer le jeton d’actualisation nouvellement émis, ce qui a pour effet de remplacer celui que vous avez utilisé dans la demande. Vous avez ainsi la garantie que vos jetons d’actualisation resteront valides le plus longtemps possible.

## Validation des jetons

À ce stade, la seule opération de validation que vos applications doivent effectuer est la validation des jetons id\_token. Pour valider un jeton id\_token, votre application doit valider la signature du jeton id\_token et les revendications dans le jeton id\_token.

<!-- TODO: Link -->
Nous fournissons des bibliothèques et des exemples de code qui montrent comment gérer facilement la validation des jetons. Les informations ci-dessous sont fournies simplement pour ceux qui souhaitent comprendre le processus sous-jacent. Il existe également de nombreuses bibliothèques open source tierces qui permettent de valider les jetons JWT. Quels que soient la plateforme et le langage que vous utilisez, vous avez la quasi-certitude de trouver au moins une option.

#### Validation de la signature
Un jeton JWT contient trois segments séparés par le caractère `.`. Le premier segment est appelé l’**en-tête**, le second le **corps** et le troisième la **signature**. Le segment de signature peut être utilisé pour valider l’authenticité du jeton id\_token afin qu’il soit approuvé par votre application.

Les jetons id\_token sont signés à l’aide d’algorithmes de chiffrement asymétrique standard, tels que RSA 256. L’en-tête du jeton id\_token contient des informations sur la clé et la méthode de chiffrement utilisées pour signer le jeton :

```
{
		typ: "JWT",
		alg: "RS256",
		x5t: "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

La revendication `alg` indique l’algorithme utilisé pour signer le jeton, tandis que la revendication `kid` ou `x5t` indique la clé publique utilisée pour signer le jeton.

À tout moment, le point de terminaison v2.0 peut signer un jeton id\_token à l’aide de l’un des ensembles de paires de clés publique-privée. Étant donné que le point de terminaison v2.0 alterne le jeu de clés possible de façon périodique, votre application doit être écrite de manière à gérer automatiquement ces changements de clés. Pour vérifier les mises à jour apportées aux clés publiques utilisées par le point de terminaison v2.0, spécifiez une fréquence raisonnable d’environ 24 heures.

Pour acquérir les données de la clé de signature nécessaires pour valider la signature, utilisez le document de métadonnées OpenID Connect à l’emplacement suivant :

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`

Ce document de métadonnées est un objet JSON qui contient diverses informations utiles, comme l’emplacement des différents points de terminaison nécessaires pour effectuer l’authentification OpenID Connect. Il comprend également un `jwks_uri` qui indique l’emplacement de l’ensemble des clés publiques utilisées pour signer les jetons. Cet emplacement est fourni ci-dessous, mais il est préférable d’extraire cet emplacement de manière dynamique à l’aide du document de métadonnées et d’analyser le `jwks_uri` :

`https://login.microsoftonline.com/common/discovery/v2.0/keys`

Le document JSON situé à cette URL contient toutes les informations sur les clés publiques utilisées à cet instant donné. Votre application peut utiliser les revendications `kid` ou `x5t` dans l’en-tête JWT pour sélectionner la clé publique utilisée dans ce document pour signer un jeton donné. Elle peut ensuite procéder à la validation des signatures à l’aide de la clé publique correcte et de l’algorithme indiqué.

La validation des signatures dépasse le cadre de ce document. Si vous avez besoin d’aide, de nombreuses bibliothèques open source sont disponibles.

#### Validation des revendications
Quand votre application reçoit un jeton id\_token au moment de la connexion de l’utilisateur, elle doit également procéder à quelques vérifications sur les revendications dans le jeton id\_token. Il s’agit des revendications suivantes :

- Revendication **Audience** : il s’agit de vérifier que le jeton id\_token était bien destiné à votre application.
- Revendications **Émis à** et **Heure d’expiration** : il s’agit de vérifier que le jeton id\_token n’est pas arrivé à expiration.
- Revendication **Émetteur** : il s’agit de vérifier que le jeton a été effectivement émis à votre application par le point de terminaison v2.0.
- **Valeur à usage unique** : il s’agit d’atténuer les attaques par relecture de jetons.

Les valeurs attendues pour ces revendications sont détaillées plus haut dans la [section Jeton id\_token](#id_tokens).


## Durées de vie de jeton

Les durées de vie de jeton suivantes sont fournies uniquement à titre indicatif dans le cadre du développement et du débogage d’applications. Quand vous écrivez vos applications, faites en sorte qu’elles n’attendent pas des durées de vie constantes, car celles-ci sont amenées à changer à un moment donné.

| Jeton | Durée de vie | Description |
| ----------------------- | ------------------------------- | ------------ |
| id\_token (comptes professionnels ou scolaires) | 1 heure | Les jetons id\_token sont généralement valides une heure. Votre application web peut utiliser cette même durée de vie pour conserver sa propre session avec l’utilisateur (recommandé) ou vous pouvez choisir une durée de vie de session différente. Si votre application a besoin d’obtenir un nouveau jeton id\_token, elle doit simplement faire une nouvelle demande de connexion auprès du point de terminaison d’autorisation v2.0. Si l’utilisateur dispose d’une session de navigateur valide avec le point de terminaison v2.0, il se peut qu’il ne soit pas obligé de retaper ses informations d’identification. |
| id\_token (comptes personnels) | 24 heures | Les jetons id\_token pour les comptes personnels sont généralement valides 24 heures. Votre application web peut utiliser cette même durée de vie pour conserver sa propre session avec l’utilisateur (recommandé) ou vous pouvez choisir une durée de vie de session différente. Si votre application a besoin d’obtenir un nouveau jeton id\_token, elle doit simplement faire une nouvelle demande de connexion auprès du point de terminaison d’autorisation v2.0. Si l’utilisateur dispose d’une session de navigateur valide avec le point de terminaison v2.0, il se peut qu’il ne soit pas obligé de retaper ses informations d’identification. |
| Jetons d’accès (comptes professionnels ou scolaires) | 1 heure | Indiqués dans les réponses de jeton dans le cadre des métadonnées du jeton. |
| Jetons d’accès (comptes personnels) | 1 heure | Indiqués dans les réponses de jeton dans le cadre des métadonnées du jeton. Les jetons d’accès émis au nom de comptes personnels peuvent être configurés pour une durée de vie différente, mais ils sont généralement valides une heure. |
| Jetons d’actualisation (comptes professionnels ou scolaires) | Jusqu’à 14 jours | Un jeton d’actualisation est valide pendant 14 jours au maximum. Toutefois, le jeton d’actualisation peut devenir non valide à tout moment pour différentes raisons. Votre application doit donc continuer d’utiliser un jeton d’actualisation jusqu’à ce qu’il échoue ou jusqu’à ce que votre application le remplace par un nouveau. Un jeton d’actualisation devient également non valide si l’utilisateur a entré ses informations d’identification il y a plus de 90 jours. |
| Jetons d’actualisation (comptes personnels) | Jusqu’à 1 an | Un jeton d’actualisation est valide pendant 1 an au maximum. Toutefois, le jeton d’actualisation peut devenir non valide à tout moment pour différentes raisons. Votre application doit donc continuer d’utiliser un jeton d’actualisation jusqu’à ce qu’il échoue. |
| Codes d’autorisation (comptes professionnels ou scolaires) | 10 minutes | Les codes d’autorisation, qui sont volontairement de courte durée, doivent être échangés immédiatement contre des jetons d’accès et des jetons d’actualisation quand ils sont reçus. |
| Codes d’autorisation (comptes personnels) | 5 minutes | Les codes d’autorisation, qui sont volontairement de courte durée, doivent être échangés immédiatement contre des jetons d’accès et des jetons d’actualisation quand ils sont reçus. Les codes d’autorisation émis au nom de comptes personnels sont également à usage unique. |

<!---HONumber=August15_HO7-->