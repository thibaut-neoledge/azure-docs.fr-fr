 <properties
   pageTitle="Informations de référence sur les jetons Azure AD | azure.microsoft.com/ Azure"
   description="Un guide pour la compréhension et l'évaluation des revendications dans les jetons SAML 2.0 et les jetons web JSON (JWT) émis par Azure Active Directory (AAD)"
   documentationCenter="na"
   authors="msmbaldwin"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/19/2016"
   ms.author="mbaldwin"/>

# Référence de jeton Azure AD

Azure Active Directory (Azure AD) émet plusieurs types de jetons de sécurité lors du traitement de chaque flux d’authentification. Ce document décrit le format, les caractéristiques en matière de sécurité et le contenu de chaque type de jeton.

## Types de jetons

Azure AD prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-protocols-oauth-code.md), qui utilise aussi bien les jetons « access\_token » que « refresh\_token ». Il prend également en charge l’authentification et la connexion via [OpenID Connect](active-directory-protocols-openid-connect-code.md), ce qui introduit un troisième type de jeton : id\_token. Chacun de ces jetons est représenté en tant que « jeton porteur ».

Un jeton porteur est un jeton de sécurité léger qui octroie l’accès à une ressource protégée au « porteur ». En ce sens, le « porteur » désigne toute partie qui peut présenter le jeton. Une partie doit certes d’abord s’authentifier auprès d’Azure AD pour recevoir le jeton porteur, mais si les mécanismes nécessaires à la sécurité du jeton lors de la transmission et du stockage ne sont pas en place, il peut être intercepté et utilisé par une partie non autorisée. Bien que certains jetons de sécurité intègrent un mécanisme de protection contre l’utilisation par des parties non autorisées, les jetons porteurs n’en sont pas dotés et doivent donc être acheminés sur un canal sécurisé, par exemple à l’aide du protocole TLS (HTTPS). Si un jeton porteur est transmis en clair, une partie malveillante peut utiliser une attaque d’intercepteur afin de s’approprier le jeton et de l’utiliser pour accéder sans autorisation à une ressource protégée. Les mêmes principes de sécurité s’appliquent au stockage ou à la mise en cache des jetons porteurs pour une utilisation ultérieure. Veillez systématiquement à ce que votre application transmette et stocke les jetons porteurs de manière sécurisée. Pour en savoir plus sur les aspects de sécurité des jetons porteurs, consultez [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

La plupart des jetons émis par Azure AD sont implémentés en tant que jetons Web JSON (JWT). Un jeton JWT constitue un moyen compact et sécurisé pour les URL de transférer des informations entre deux parties. Les informations contenues dans les jetons JWT sont appelées « revendications », c’est-à-dire des assertions d’informations sur le porteur et le sujet du jeton. Les revendications dans les jetons JWT sont des objets JSON codés et sérialisés pour la transmission. Étant donné que les jetons JWT émis par Azure AD sont signés, mais pas chiffrés, vous pouvez facilement inspecter le contenu d’un jeton JWT à des fins de débogage. Il existe plusieurs outils disponibles pour y parvenir, tels que [jwt.calebb.net](http://jwt.calebb.net). Pour plus d’informations sur les jetons JWT, consultez la [spécification JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## Jetons id\_token

Les jetons id\_token constituent une forme de jeton de sécurité de connexion. Votre application les reçoit quand elle procède à l’authentification à l’aide d’[OpenID Connect](active-directory-protocols-openid-connect-code.md). Ils sont représentés en tant que [jetons JWT](#types-of-tokens) et contiennent des revendications que vous pouvez utiliser pour connecter l’utilisateur à votre application. Vous pouvez utiliser les revendications dans un jeton id\_token comme bon vous semble. En général, elles permettent d’afficher des informations de compte ou de prendre des décisions de contrôle d’accès dans une application.

Les jetons id\_token sont signés, mais ils ne sont pas chiffrés pour l’instant. Quand votre application reçoit un jeton id\_token, elle doit [valider la signature](#validating-tokens) pour prouver l’authenticité du jeton et valider certaines revendications du jeton pour prouver sa validité. Les revendications validées par une application varient selon les spécifications du scénario, mais il existe certaines [validations de revendication communes](#validating-tokens) auxquelles votre application doit procéder dans chaque scénario.

Vous trouverez ci-dessous tous les détails sur les revendications dans les jetons id\_token, ainsi qu’un exemple de jeton id\_token. Notez que les revendications dans les jetons id\_token ne sont pas retournées dans un ordre particulier. Par ailleurs, de nouvelles revendications peuvent être introduites dans des jetons id\_token à n’importe quel moment. L’introduction de nouvelles déclarations ne doit pas altérer votre application. La liste ci-dessous répertorie les revendications qui sont correctement interprétées par votre application au moment de la rédaction de cet article. En cas de besoin, vous trouverez des informations supplémentaires dans la [spécification OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### Exemple de jeton id\_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [AZURE.TIP] Pour vous entraîner, essayez d’inspecter les revendications dans l’exemple de jeton id\_token en le collant dans [calebb.net](http://jwt.calebb.net).

#### Revendications dans les jetons id\_token

| Revendication JWT | Name | Description |
|-----------|------|-------------|
| `appid`| ID de l'application | Identifie l’application qui utilise le jeton pour accéder à une ressource. L'application peut agir pour elle-même ou pour le compte d'un utilisateur. L'ID d'application représente généralement un objet d’application, mais elle peut également représenter un objet du principal du service dans Azure AD. <br><br> **Exemple de valeur JWT**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud`| Public ciblé | Destinataire du jeton. L'application qui reçoit le jeton doit vérifier que la valeur de l'audience est correcte et rejeter les jetons destinés à une autre audience. <br><br> **Exemple de valeur SAML **: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Exemple de valeur JWT **: <br> `"aud":"https://contoso.com"` |
| `appidacr`| Référence de classe du contexte d’authentification de l’application | Indique comment le client a été authentifié. Pour un client public, la valeur est 0. Si l'ID client et la clé secrète client sont utilisés, la valeur est 1. <br><br> **Exemple de valeur JWT **: <br> `"appidacr": "0"`|
| `acr`| Référence de classe du contexte d'authentification | Indique comment le sujet a été authentifié, et non pas le client comme dans la revendication de référence de classe du contexte de l’authentification de l’application. La valeur « 0 » indique que l'authentification de l'utilisateur final ne répondait pas aux exigences de la norme ISO/IEC 29115. <br><br> **Exemple de valeur JWT **: <br> `"acr": "0"`|
| | Moment d’authentification | Enregistre la date et l’heure de l’authentification. <br><br> **Exemple de valeur SAML **: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
| `amr`| Méthode d'authentification | Identifie comment le sujet du jeton a été authentifié. <br><br> **Exemple de valeur SAML **: <br> `<AuthnContextClassRef>`<br>`http://schemas.azure.microsoft.com/.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Exemple de valeur JWT **: `“amr”: ["pwd"]` |
| `given_name`| Prénom | Fournit le prénom de l’utilisateur tel que défini dans l’objet utilisateur Azure AD. <br><br> **Exemple de valeur SAML **: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Exemple de valeur JWT **: <br> `"given_name": "Frank"` |
| `groups`| Groupes | Fournit les ID d’objet qui représentent les appartenances aux groupes du sujet. Ces valeurs sont uniques (voir l'ID objet) et peuvent être utilisées en toute sécurité pour la gestion des accès, telle que l'autorisation d'accéder à une ressource. Les groupes inclus dans la revendication des groupes sont configurés pour chaque application, via la propriété « groupMembershipClaims » du manifeste d'application. Une valeur Null exclut tous les groupes, une valeur « SecurityGroup » inclut uniquement les appartenances aux groupes de sécurité Active Directory et une valeur « All » inclut les groupes de sécurité et les listes de Distribution Office 365. <br><br> **Exemple de valeur SAML **: <br> `<Attribute Name="http://schemas.azure.microsoft.com/.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Exemple de valeur JWT **: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` | Fournisseur d’identité | Enregistre le fournisseur d’identité qui a authentifié le sujet du jeton. Cette valeur est identique à la valeur de la revendication de l’émetteur sauf si le compte d'utilisateur est dans un autre client que l'émetteur. <br><br> **Exemple de valeur SAML **: <br> `<Attribute Name=” http://schemas.azure.microsoft.com/.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Exemple de valeur JWT **: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` | IssuedAt | Enregistre l’heure à laquelle le jeton a été émis. Il est souvent utilisé pour mesurer l’actualisation du jeton. <br><br> **Exemple de valeur SAML **: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Exemple de valeur JWT **: <br> `"iat": 1390234181` |
| `iss` | Émetteur | Identifie le service d’émission de jeton de sécurité (STS) qui construit et retourne le jeton. Dans les jetons retournés par Azure AD, l'émetteur est sts.windows.net. Le GUID dans la valeur de revendication de l'émetteur est l'ID client de Azure AD Directory. L'ID client est un identificateur non modifiable et fiable du répertoire. <br><br> **Exemple de valeur SAML **: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Exemple de valeur JWT **: <br> `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` | Nom | Fournit le nom de famille de lutilisateur tel que défini dans l’objet utilisateur Azure AD. <br><br> **Exemple de valeur SAML **: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Exemple de valeur JWT **: <br> `"family_name": "Miller"` |
| `unique_name`| Name | Fournit une valeur contrôlable de visu qui identifie le sujet du jeton. Il n’est pas certain que cette valeur soit unique au sein d'un client. Elle est conçue pour être utilisée uniquement à des fins d'affichage. <br><br> **Exemple de valeur SAML **: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Exemple de valeur JWT **: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` | ID objet | Contient un identificateur unique d’un objet dans Azure AD. Cette valeur est immuable et ne peut pas être réattribuée ou réutilisée. Utilisez l'ID objet pour identifier un objet dans les requêtes à Azure AD. <br><br> **Exemple de valeur SAML **: <br> `<Attribute Name="http://schemas.azure.microsoft.com/.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Exemple de valeur JWT **: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` | contrôleur | Représente tous les rôles d’application qui ont été accordés au sujet directement et indirectement via l’appartenance au groupe, et peut être utilisée pour appliquer un contrôle d’accès basé sur les rôles. Les rôles d’application sont définis pour chaque application, via la propriété `appRoles` du manifeste d’application. La propriété `value` de chaque rôle d’application est la valeur qui s’affiche dans la revendication des rôles. <br><br> **Exemple de valeur SAML **: <br> `<Attribute Name="http://schemas.azure.microsoft.com/.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Exemple de valeur JWT **: <br> `“roles”: ["Admin", … ]` |
| `scp` | Scope | Indique les autorisations d’emprunt d’identité accordées à l’application cliente. L’autorisation par défaut est `user_impersonation`. Le propriétaire de la ressource sécurisée peut enregistrer des valeurs supplémentaires dans Azure AD. <br><br> **Exemple de valeur JWT **: <br> `"scp": "user_impersonation"`|
| `sub` |Objet| Identifie l’objet principal sur lequel portent les assertions d’informations du jeton, comme l’utilisateur d’une application. Cette valeur n’est pas modifiable et ne peut pas être réassignée ou réutilisée. Vous pouvez donc l’utiliser pour effectuer des vérifications d’autorisation en toute sécurité. Étant donné que le sujet est toujours présent dans les jetons émis par Azure AD, vous êtes invité à utiliser cette valeur dans un but général de système d’autorisation. <br> `SubjectConfirmation` n’est pas une revendication. Cette valeur décrit comment le sujet du jeton est vérifié. `Bearer` indique que le sujet est confirmé par sa possession du jeton. <br><br> **Exemple de valeur SAML **: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Exemple de valeur JWT **: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"`|
| `tid` | ID client | Identificateur non modifiable et non réutilisable qui identifie le client du répertoire qui a émis le jeton. Vous pouvez utiliser cette valeur pour accéder à des ressources de répertoire spécifiques au client dans une application mutualisée. Par exemple, vous pouvez utiliser cette valeur pour identifier le client dans un appel à l'API Graph. <br><br> **Exemple de valeur SAML **: <br> `<Attribute Name=”http://schemas.azure.microsoft.com/.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Exemple de valeur JWT **: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"`|
| `nbf`, `exp`|Durée de vie du jeton | Définit l’intervalle de temps pendant lequel un jeton est valide. Le service qui valide le jeton doit vérifier que la date actuelle est comprise dans sa durée de vie, sans quoi le jeton doit être rejeté. Le service peut accorder une marge de cinq minutes au-delà de la plage de durée de vie du jeton pour prendre en compte tout écart de temps (« décalage horaire ») entre Azure AD et le service. <br><br> **Exemple de valeur SAML **: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Exemple de valeur JWT **: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn`| Nom d’utilisateur principal | Enregistre le nom d’utilisateur de l’utilisateur principal.<br><br> **Exemple de valeur JWT **: <br> `"upn": frankm@contoso.com`|
| `ver`| Version | Enregistre le numéro de version du jeton. <br><br> **Exemple de valeur JWT **: <br> `"ver": "1.0"`|

## Jetons d’accès

Les jetons d’accès sont pour l’instant uniquement consommables par les services azure.microsoft.com/. Vos applications n’ont pas besoin de valider ou d’inspecter les jetons d’accès dans les scénarios actuellement pris en charge. Vous pouvez traiter les jetons d’accès comme étant totalement opaques ; il s’agit simplement de chaînes que votre application peut passer à azure.microsoft.com/ dans des requêtes HTTP.

Quand vous demandez un jeton d’accès, Azure AD retourne également des métadonnées sur le jeton d’accès que votre application peut consommer. Ces informations incluent le délai d’expiration du jeton d’accès et les étendues dans lesquelles il est valide. Votre application peut ainsi effectuer une mise en cache intelligente des jetons d’accès sans avoir à les analyser.

## Jetons d’actualisation

Les jetons d’actualisation sont des jetons de sécurité que votre application peut utiliser pour acquérir de nouveaux jetons d’accès dans un flux OAuth 2.0. Ils permettent à votre application d’obtenir un accès à long terme à des ressources au nom d’un utilisateur, et ce sans nécessiter l’intervention de l’utilisateur.

Les jetons d’actualisation prennent en charge plusieurs ressources. Cela signifie qu’un jeton d’actualisation reçu au cours d’une demande de jeton pour une ressource peut être échangé contre des jetons d’accès à une ressource complètement différente. Pour ce faire, définissez le paramètre `resource` sur la ressource cible dans la requête.

Les jetons d’actualisation sont entièrement opaques pour votre application. Les jetons d’actualisation sont de longue durée. Toutefois, quand vous écrivez votre application, faites en sorte qu’elle n’attende pas un jeton d’actualisation d’une durée particulière. Les jetons d’actualisation peuvent être rendus non valides à tout moment, et ce pour diverses raisons. Pour savoir si un jeton d’actualisation est valide, votre application doit tenter de l’échanger en faisant une demande de jeton auprès du point de terminaison de jeton d’Azure AD. C’est la seule façon de faire.

Quand vous échangez un jeton d’actualisation contre un nouveau jeton d’accès, vous recevez un nouveau jeton d’actualisation dans la réponse du jeton. Vous devez enregistrer le jeton d’actualisation nouvellement émis, ce qui a pour effet de remplacer celui que vous avez utilisé dans la demande. Vous avez ainsi la garantie que vos jetons d’actualisation resteront valides le plus longtemps possible.

## Validation des jetons

À ce stade, la seule opération de validation que votre application client doive effectuer est la validation des jetons id\_token. Pour valider un jeton id\_token, votre application doit valider la signature du jeton id\_token et les revendications dans le jeton id\_token.

Nous fournissons des bibliothèques et des exemples de code qui montrent comment gérer facilement la validation des jetons. Les informations ci-dessous sont fournies simplement pour ceux qui souhaitent comprendre le processus sous-jacent. Il existe également de nombreuses bibliothèques open source tierces qui permettent de valider les jetons JWT. Quels que soient la plateforme et le langage que vous utilisez, vous avez la quasi-certitude de trouver au moins une option. Pour plus d’informations sur les exemples de code et les bibliothèques d’authentification Azure AD, reportez-vous à la section [Bibliothèques d’authentification Azure AD](active-directory-authentication-libraries.md).

#### Validation de la signature

Un jeton JWT contient trois segments séparés par le caractère `.`. Le premier segment est appelé l’**en-tête**, le second le **corps** et le troisième la **signature**. Le segment de signature peut être utilisé pour valider l’authenticité du jeton id\_token afin qu’il soit approuvé par votre application.

Les jetons id\_token sont signés à l’aide d’algorithmes de chiffrement asymétrique standard, tels que RSA 256. L’en-tête du jeton id\_token contient des informations sur la clé et la méthode de chiffrement utilisées pour signer le jeton :

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

La revendication `alg` indique l’algorithme utilisé pour signer le jeton, tandis que la revendication `x5t` indique la clé publique utilisée pour signer le jeton.

À tout moment, Azure AD peut signer un jeton id\_token à l'aide de l'un des ensembles de paires de clés publique-privée. Étant donné qu'Azure AD alterne le jeu de clés possible de façon périodique, votre application doit être écrite de manière à gérer automatiquement ces changements de clés. Pour vérifier les mises à jour apportées aux clés publiques utilisées par Azure AD, spécifiez une fréquence raisonnable d’environ 24 heures.

Pour acquérir les données de la clé de signature nécessaires pour valider la signature, utilisez le document de métadonnées OpenID Connect à l’emplacement suivant :

```
https://login.azure.microsoft.com/online.com/common/.well-known/openid-configuration
```

> [AZURE.TIP] Testez cette URL dans un navigateur !

Ce document de métadonnées est un objet JSON qui contient diverses informations utiles, comme l’emplacement des différents points de terminaison nécessaires pour effectuer l’authentification OpenID Connect.

Il comprend également un `jwks_uri` qui indique l’emplacement de l’ensemble des clés publiques utilisées pour signer les jetons. Le document JSON situé sous `jwks_uri` contient toutes les informations sur les clés publiques utilisées à cet instant donné. Votre application peut utiliser la revendication `kid` dans l’en-tête JWT pour sélectionner la clé publique utilisée dans ce document pour signer un jeton donné. Elle peut ensuite procéder à la validation des signatures à l’aide de la clé publique correcte et de l’algorithme indiqué.

La validation des signatures dépasse le cadre de ce document. Si vous avez besoin d’aide, de nombreuses bibliothèques open source sont disponibles.

#### Validation des revendications

Quand votre application reçoit un jeton id\_token au moment de la connexion de l’utilisateur, elle doit également procéder à quelques vérifications sur les revendications dans le jeton id\_token. Ces vérifications portent notamment sur les revendications suivantes :

  - Revendication **Audience** : il s’agit de vérifier que le jeton id\_token était bien destiné à votre application.
  - Revendications **Pas avant** et **Heure d'expiration** : il s'agit de vérifier que le jeton id\_token n'est pas arrivé à expiration.
  - Revendication **Émetteur** : il s'agit de vérifier que le jeton a été effectivement émis à votre application par Azure AD.
  - **Valeur à usage unique** : il s’agit d’atténuer les attaques par relecture de jetons.
  - et bien plus...

Pour obtenir une liste complète des revendications que votre application doit valider, reportez-vous à la [spécification OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Les valeurs attendues pour ces revendications sont détaillées plus haut dans la [section Jeton id\_token](#id-tokens).

## Exemples de jeton

Cette section affiche des exemples de jetons SAML et JWT retournés par Azure AD. Ces exemples vous permettent de voir les revendications en contexte. Jeton SAML

Il s'agit d'un exemple de jeton SAML classique.

	<?xml version="1.0" encoding="UTF-8"?>
	<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
	  <t:Lifetime>
		<wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
		<wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
	  </t:Lifetime>
	  <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
		<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
		  <Address>https://contoso.onazure.microsoft.com/.com/MyWebApp</Address>
		</EndpointReference>
	  </wsp:AppliesTo>
	  <t:RequestedSecurityToken>
		<Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
		  <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
		  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
			<ds:SignedInfo>
			  <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
			  <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
			  <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
				<ds:Transforms>
				  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
				  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
				</ds:Transforms>
				<ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
				<ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
			  </ds:Reference>
			</ds:SignedInfo>
			<ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
			<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
			  <X509Data>
				<X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
			  </X509Data>
			</KeyInfo>
		  </ds:Signature>
		  <Subject>
			<NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
			<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
		  </Subject>
		  <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
			<AudienceRestriction>
			  <Audience>https://contoso.onazure.microsoft.com/.com/MyWebApp</Audience>
			</AudienceRestriction>
		  </Conditions>
		  <AttributeStatement>
			<Attribute Name="http://schemas.azure.microsoft.com/.com/identity/claims/objectidentifier">
			  <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.azure.microsoft.com/.com/identity/claims/tenantid">
			  <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
			  <AttributeValue>sample.admin@contoso.onazure.microsoft.com/.com</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
			  <AttributeValue>Admin</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
			  <AttributeValue>Sample</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.azure.microsoft.com/.com/ws/2008/06/identity/claims/groups">
			  <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
			  <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
			  <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
			  <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
			  <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
			  <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
			  <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
			  <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
			  <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
			  <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
			  <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
			  <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
			  <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
			</Attribute>
			<Attribute Name="http://schemas.azure.microsoft.com/.com/identity/claims/identityprovider">
			  <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
			</Attribute>
		  </AttributeStatement>
		  <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
			<AuthnContext>
			  <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
			</AuthnContext>
		  </AuthnStatement>
		</Assertion>
	  </t:RequestedSecurityToken>
	  <t:RequestedAttachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedAttachedReference>
	  <t:RequestedUnattachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedUnattachedReference>
	  <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
	  <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
	  <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
	</t:RequestSecurityTokenResponse>

### Jeton JWT - emprunt d'identité de l'utilisateur

Il s’agit d’un exemple de jeton web JSON (JWT) classique utilisé dans un flux d’octroi d’un code d’autorisation. Outre les revendications, le jeton inclut un numéro de version dans **ver** et **appidacr** et la référence de classe du contexte de l'authentification qui indique comment le client a été authentifié. Pour un client public, la valeur est 0. Si un ID client ou une clé secrète client est utilisé, la valeur est 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onazure.microsoft.com/.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onazure.microsoft.com/.com",
     unique_name: "sample.user@contoso.onazure.microsoft.com/.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

<!---HONumber=AcomDC_0817_2016-->