<properties
	pageTitle="Azure AD B2C en version préliminaire | Microsoft Azure"
	description="Création d’applications directement à l’aide des protocoles pris en charge par Azure AD B2C en version préliminaire."
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

# Azure AD B2C en version préliminaire : protocoles d’authentification

Azure AD B2C fournit l’identité en tant que service pour vos applications en prenant en charge deux protocoles standard, OpenID Connect et OAuth 2.0. Bien que ce service soit conforme aux normes, vous pouvez constater de subtiles différences entre deux implémentations différentes de ces protocoles. Les informations fournies ici vous seront utiles si vous choisissez d’écrire votre code en envoyant ou en traitant directement des requêtes HTTP, plutôt qu’en utilisant l’une de nos bibliothèques open source. Nous vous recommandons de lire les informations succinctes de cette page avant d’entrer dans les détails de chaque protocole. En revanche, si vous êtes déjà familiarisé avec Azure AD B2C, vous pouvez accéder directement aux [guides de référence du protocole](#protocols).

<!-- TODO: Need link to libraries above -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
## Concepts de base
Chaque application qui utilise Azure AD B2C doit être inscrite dans votre annuaire B2C sur le [portail Azure](https://portal.azure.com). Le processus d’inscription des applications collecte quelques valeurs et les affecte à votre application :

- un **ID d’application** identifiant de manière unique votre application ;
- un **URI de redirection** ou un **identificateur de package** pouvant être utilisés pour diriger des réponses vers votre application ;
- quelques valeurs spécifiques au scénario. Pour plus de détails, découvrez comment [inscrire une application](active-directory-b2c-app-registration.md).

Une fois inscrite, l’application communique avec Azure AD en transmettant les requêtes au point de terminaison v2.0 :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Dans presque tous les flux OAuth et OpenID Connect, quatre parties sont concernées par l’échange :

![Rôles OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- Le **serveur d’autorisation** est le point de terminaison Azure AD v2.0. Il est chargé de garantir l’identité de l’utilisateur, l’octroi et la révocation de l’accès aux ressources et l’émission de jetons. Il est également connu sous le nom du fournisseur d’identité. Il traite de manière sécurisée les informations de l’utilisateur, leur accès et les relations de confiance entre les parties des flux.
- Le **propriétaire de la ressource** est généralement l’utilisateur final. Il s’agit de la partie détentrice des données, qui a le pouvoir d’autoriser les tierces parties à accéder à ces données ou à cette ressource.
- Le **Client OAuth** est votre application, identifiée par son ID d’application. Il s’agit généralement de la partie avec laquelle l’utilisateur final interagit ; elle demande des jetons provenant du serveur d’autorisation. Le client doit se voir octroyer une autorisation d’accès à la ressource par le propriétaire de cette dernière.
- Le **serveur de ressources** héberge la ressource ou les données. Il approuve le serveur d’autorisation pour authentifier et autoriser de manière sûre le client OAuth et utilise les jetons d’accès porteurs pour garantir l’octroi de l’accès à une ressource.

## Stratégies

En fait, les **stratégies** Azure AD B2C constituent la fonctionnalité la plus importante du service. Azure AD B2C étend les protocoles OAuth 2.0 et OpenID Connect standard en introduisant des stratégies qui permettent à Azure AD B2C d’effectuer des opérations d’authentification et d’autorisation bien plus simples. Les stratégies décrivent entièrement les expériences liées à l’identité du consommateur, telles que l’inscription, la connexion ou la modification de profil. Elles peuvent être définies dans une interface utilisateur d’administration et exécutées à l’aide d’un paramètre de requête spécial dans les requêtes d’authentification HTTP. Les stratégies ne constituant pas une fonctionnalité standard d’OAuth 2.0 et d’OpenID Connect, vous devez prendre le temps de les comprendre. Pour plus d’informations, voir le [Guide de référence de stratégie d’Azure AD B2C](active-directory-b2c-reference-policies).


## Jetons
L’implémentation d’OAuth 2.0 et d’OpenID Connect par Azure AD B2C utilise massivement les jetons du porteur, y compris ceux représentés sous forme de JWT. Un jeton porteur est un jeton de sécurité léger qui octroie l’accès à une ressource protégée au « porteur ». En ce sens, le « porteur » désigne toute partie qui peut présenter le jeton. Une partie doit certes d’abord s’authentifier auprès d’Azure AD pour recevoir le jeton porteur, mais si les mécanismes nécessaires à la sécurité du jeton lors de la transmission et du stockage ne sont pas en place, il peut être intercepté et utilisé par une partie non autorisée. Bien que certains jetons de sécurité intègrent un mécanisme de protection contre l’utilisation par des parties non autorisées, les jetons porteurs n’en sont pas dotés et doivent donc être acheminés sur un canal sécurisé, par exemple à l’aide du protocole TLS (HTTPS). Si un jeton porteur est transmis en clair, une partie malveillante peut utiliser une attaque d’intercepteur afin de s’approprier le jeton et de l’utiliser pour accéder sans autorisation à une ressource protégée. Les mêmes principes de sécurité s’appliquent au stockage ou à la mise en cache des jetons porteurs pour une utilisation ultérieure. Veillez systématiquement à ce que votre application transmette et stocke les jetons porteurs de manière sécurisée. Pour en savoir plus sur les aspects de sécurité des jetons porteurs, consultez [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

Pour plus d’informations sur les différents types de jetons utilisés dans Azure AD B2C, voir la [référence sur les jetons Azure AD](active-directory-b2c-reference-tokens.md).

## Protocoles

Si vous êtes prêt à voir des exemples de demandes, entamez l’un des didacticiels ci-dessous. Chacun d’eux correspond à un scénario d’authentification particulier. Si vous avez besoin d’aide pour déterminer le flux qui vous convient, voir les [types d’applications que vous pouvez créer avec Azure AD B2C](active-directory-b2c-apps).

- [Génération d’une application mobile et native avec OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
- [Génération d’applications web avec Open ID Connect](active-directory-b2c-reference-oidc.md)
- Génération d’applications de page unique avec le flux implicite OAuth 2.0 (prochainement)
- Génération de démons ou de processus côté serveur avec le flux d’informations d’identification de Client OAuth 2.0 (prochainement)
- Obtention de jetons à l’aide d’un nom d’utilisateur et d’un mot de passe avec le flux d’informations d’identification de mot de passe de propriétaire de ressource OAuth 2.0 (prochainement)
- Obtention de jetons dans une API web avec le flux De la part de d’OAuth 2.0 (prochainement)

<!-- [Call the Azure AD Graph API using the OAuth 2.0 Client Credentials Flow](active-directory-reference-graph.md) -->

<!---HONumber=Sept15_HO3-->