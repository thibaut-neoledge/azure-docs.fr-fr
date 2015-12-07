<properties
	pageTitle="Modèle d’application v2.0 - Protocoles | Microsoft Azure"
	description="Les protocoles pris en charge par la version d’évaluation publique du modèle d’application Azure AD v2.0."
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
	ms.date="11/10/2015"
	ms.author="dastrock"/>

# Version d’évaluation du modèle d’application v2.0 : Protocoles - OAuth 2.0 et OpenID Connect

Le modèle d’application v2.0 fournit l’identité en tant que service pour vos applications en prenant en charge des protocoles standard, OpenID Connect et OAuth 2.0. Bien que ce service soit distribué en standard, vous pouvez constater de subtiles différences entre deux implémentations différentes de ces protocoles. Ces informations vous seront utiles si vous choisissez d’écrire votre code en envoyant ou en traitant directement des requêtes HTTP, non en utilisant l’une de nos bibliothèques open source. <!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]Ces informations s’appliquent à la version préliminaire publique du modèle d’application v2.0. Pour obtenir des instructions sur l’intégration avec le service Azure AD généralement disponible, consultez le [Guide du développeur Azure AD](active-directory-developers-guide.md).

## Concepts de base
Toutes les applications qui utilisent le modèle d'application v2.0 doivent être inscrites à l'adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Le processus d’inscription des applications collecte quelques valeurs et les affecte pour vos applications :

- un **ID d'application** qui identifie de manière unique votre application ;
- un **URI de redirection** ou un **identificateur de package** pouvant être utilisé pour diriger des réponses vers votre application ;
- quelques valeurs spécifiques au scénario. Pour plus de détails, découvrez comment [inscrire une application](active-directory-v2-app-registration.md).

Une fois inscrite, l’application communique avec Azure AD afin de transmettre les requêtes au point de terminaison v2.0 :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Dans presque tous les flux OAuth et OpenID Connect, quatre parties sont concernées par l’échange :

![Rôles OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- Le **serveur d'autorisation** est le point de terminaison v2.0. Il est chargé de garantir l’identité de l’utilisateur, l’octroi et la révocation de l’accès aux ressources et l’émission de jetons. Il est également connu sous le nom du fournisseur d’identité. Il traite de manière sécurisée les informations de l’utilisateur, leur accès et les relations de confiance entre les parties des flux.
- Le **propriétaire de la ressource** est généralement l'utilisateur final. Il s’agit de la partie détentrice des données, qui a le pouvoir d’autoriser les tierces parties à accéder à ces données ou à cette ressource.
- Le **Client OAuth** est votre application, identifiée par son ID d'application. Il s’agit généralement de la partie avec laquelle l’utilisateur final interagit ; elle demande des jetons provenant du serveur d’autorisation. Le client doit se voir octroyer une autorisation d’accès à la ressource par le propriétaire de cette dernière.
- Le **serveur de ressources** héberge la ressource ou les données. Il approuve le serveur d’autorisation pour authentifier et autoriser de manière sûre le client OAuth et utilise les jetons d’accès porteurs pour garantir l’octroi de l’accès à une ressource.


## Jetons
L’implémentation d’OAuth 2.0 et OpenID Connect du modèle d’application v2.0 utilise massivement les jetons porteurs, y compris des jetons porteurs représentés sous forme de JWT. Un jeton porteur est un jeton de sécurité léger qui octroie l’accès à une ressource protégée au « porteur ». En ce sens, le « porteur » désigne toute partie qui peut présenter le jeton. Une partie doit certes d’abord s’authentifier auprès d’Azure AD pour recevoir le jeton porteur, mais si les mécanismes nécessaires à la sécurité du jeton lors de la transmission et du stockage ne sont pas en place, il peut être intercepté et utilisé par une partie non autorisée. Bien que certains jetons de sécurité intègrent un mécanisme de protection contre l’utilisation par des parties non autorisées, les jetons porteurs n’en sont pas dotés et doivent donc être acheminés sur un canal sécurisé, par exemple à l’aide du protocole TLS (HTTPS). Si un jeton porteur est transmis en clair, une partie malveillante peut utiliser une attaque d’intercepteur afin de s’approprier le jeton et de l’utiliser pour accéder sans autorisation à une ressource protégée. Les mêmes principes de sécurité s’appliquent au stockage ou à la mise en cache des jetons porteurs pour une utilisation ultérieure. Veillez systématiquement à ce que votre application transmette et stocke les jetons porteurs de manière sécurisée. Pour en savoir plus sur les aspects de sécurité des jetons porteurs, consultez [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

Pour plus d'informations sur les différents types de jetons utilisés dans le modèle d'application v2.0, consultez la page de [Référence sur les jetons du modèle d'application v2.0](active-directory-v2-tokens.md).

## Protocoles

Si vous êtes prêt à voir des exemples de demandes, entamez l’un des didacticiels ci-dessous. Chacun d’eux correspond à un scénario d’authentification particulier. Si vous avez besoin d'aide pour déterminer le flux qui vous convient, consultez les [types d'applications que vous pouvez créer avec le modèle d'application v2.0](active-directory-v2-flows.md).

- [Génération d’une application mobile et native avec OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
- [Génération d’applications web avec Open ID Connect](active-directory-v2-protocols-oidc.md)
- [Génération d'applications de page unique avec le flux implicite OAuth 2.0](active-directory-v2-protocols-implicit.md)
- Génération de démons ou de processus côté serveur avec le flux d’informations d’identification de Client OAuth 2.0 (prochainement)
- Obtention de jetons dans une API web avec le flux De la part de d’OAuth 2.0 (prochainement)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) -->
<!-- [Call the Azure AD Graph API using the OAuth 2.0 Client Credentials Flow](active-directory-reference-graph.md) -->

<!---HONumber=AcomDC_1125_2015-->