<properties
	pageTitle="Modèle d'application v2.0 - Limitations et restrictions | Microsoft Azure"
	description="Une liste des limites et restrictions associées au modèle d’application v2.0 Azure AD."
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
	ms.date="09/11/2015"
	ms.author="dastrock"/>

# Version d’évaluation modèle d’application v2.0 : Limites et restrictions

Il existe plusieurs fonctions et fonctionnalités du modèle d’application v2.0 qui ne sont pas encore prises en charge dans la période préliminaire publique. Chacune de ces limites est supprimée avant que le modèle d’application v2.0 bénéficie d’une disponibilité générale, mais vous devez les garder à l’esprit si vous concevez des applications durant la période préliminaire publique.

> [AZURE.NOTE]Ces informations s’appliquent à la version préliminaire publique du modèle d’application v2.0. Pour obtenir des instructions sur l’intégration au service Azure AD mis à la disposition générale, consultez le [Guide du développeur Azure AD](active-directory-developers-guide.md).

## Prise en charge pour les applications de production
Les applications qui s’intègrent avec le modèle d’application v2.0 ne doivent pas être mises à disposition du public en tant qu’applications de niveau production. Le modèle d’application v2.0 est en version préliminaire publique pour l’instant. Les modifications avec rupture peuvent être introduites à tout moment, et ce service ne garantit aucun contrat de niveau de service. Aucune prise en charge n’est assurée pour les incidents pouvant survenir. Si vous êtes prêt à accepter les risques de prendre une dépendance sur un service qui est en cours de développement, vous devez nous contacter (auprès d’Azure AD) afin que nous évoquions l’étendue de votre application ou service.

## Restrictions sur les applications
Les types d’applications suivants ne sont actuellement pas pris en charge dans la version préliminaire publique du modèle d’application v2.0. Pour obtenir une description des types d’applications pris en charge, consultez [cet article](active-directory-v2-flows.md).

##### Applications à page unique (Javascript)
Plusieurs applications modernes présentent une application frontale à page unique écrite principalement en Javascript, utilisant bien souvent des infrastructures d’application à page unique comme AngularJS, Ember.js, Durantal, etc. Le service Azure AD disponible généralement prend en charge ces applications à l’aide du [flux implicite OAuth 2.0](active-directory-v2-protocols.md#oauth2-implicit-flow). Néanmoins, ce flux n’est pas encore disponible dans le modèle d’application v2.0. Il le sera très prochainement.

Si vous vous inquiétez à propos de l’utilisation d’une application à page unique avec le modèle d’application v2.0, vous pouvez implémenter l’authentification à l’aide du [flux d’application Web](active-directory-v2-flows.md#web-apps). Néanmoins, cette approche n’est pas recommandée, et vous disposerez d’une documentation limitée pour ce scénario. Si vous souhaitez bénéficier d’une présentation plus détaillée du scénario d’application à page unique, nous vous invitons à consulter [l’exemple de code d’application à page unique du service Azure AD généralement disponible](active-directory-devquickstarts-angular.md).

##### Applications côté démons/serveur
Les applications qui contiennent des processus de longue durée ou qui fonctionnent sans la présence d’un utilisateur doivent également disposer d’un moyen d’accès aux ressources sécurisées, comme les API Web. Ces applications peuvent s’authentifier et récupérer des jetons à l’aide de l’identité d’application (plutôt qu’avec l’identité déléguée d’un utilisateur), avec le [flux des informations d’identification du client OAuth 2.0](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow).

Ce flux n’est pas actuellement pas pris en charge par le modèle d’application v2.0. Concrètement, cela signifie que les applications peuvent récupérer des jetons uniquement après l’exécution d’un flux interactif de connexion utilisateur. Le flux des informations d’identification du client sera ajouté très prochainement. Si vous souhaitez consulter le flux des informations d’identification du client dans le modèle d’application du service Azure AD disponible généralement, consultez l’[exemple de démon sur GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

##### API Web chaînées (On-Behalf-Of)
De nombreuses architectures incluent une API Web qui doit appeler une autre API Web en aval, toutes deux sécurisées par le modèle d’application v2.0. Ce scénario est courant dans les clients natifs qui disposent d’une API Web principale, qui à son tour appelle un service Microsoft Online, comme Office 365 ou l’API Graph.

Ce scénario d’API Web chaînée peut être pris en charge à l’aide de la concession des informations d’identification du porteur OAuth 2.0 Jwt, également appelé [flux On-Behalf-Of](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow). Toutefois, le flux On-Behalf-Of n’est pas actuellement implémenté dans la version d’évaluation du modèle d’application v2.0. Pour observer le fonctionnement de ce flux dans le service Azure AD disponible généralement, consultez l’[’exemple de code On-Behalf-Of sur GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

##### API Web autonome
Dans la version d’évaluation du modèle d’application v2.0, vous avez la possibilité de [concevoir une API Web sécurisée à l’aide de jetons OAuth](active-directory-v2-flows.md#web-apis) à partir du point de terminaison v2.0. Toutefois, cette API Web pourra recevoir uniquement les jetons d’un client qui partage le même ID d’application. La création d’un service Web tiers accessible par différents clients n’est pas prise en charge.

Pour voir comment créer une API Web qui accepte des jetons d’un client reconnu présentant un ID d’application identique, consultez les exemples d’API Web de modèle d’application v2.0 de la section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).

## Restrictions sur les utilisateurs
Actuellement, toutes les applications créées avec le modèle d’application v2.0 seront exposées publiquement à l’ensemble des utilisateurs disposant d’un compte Microsoft ou d’un compte Microsoft Azure AD. Les utilisateurs possédant l’un de ces comptes peuvent accéder à votre application ou l’installer, saisir leurs informations d’identification dans le modèle d’application v2.0 et accepter vos autorisations d’application. Vous pouvez écrire votre code d’application de manière à bloquer les connexions de certains utilisateurs. Le cas échéant, vous ne les empêchez pas d’accepter votre application.

En effet, vos applications ne peuvent pas limiter les types d’utilisateurs pouvant s’y connecter. Vous ne serez pas en mesure de concevoir des applications métier (restreintes aux utilisateurs d’une organisation), des applications disponibles uniquement aux utilisateurs de l’entreprise (disposant d’un compte Azure AD), ni des applications disponibles uniquement aux consommateurs (disposant d’un compte Microsoft).

## Restrictions sur les inscriptions d’application
À ce stade, l’ensemble des applications dont l’intégration avec le modèle d’application v2.0 est envisagée doivent créer une nouvelle inscription d’application à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Aucune application existante Azure AD ou de compte Microsoft, ou application inscrite dans un portail différent du nouveau portail d’inscription d’application n’est compatible avec le modèle d’application v2.0. Il n’existe aucun chemin de migration vers le modèle d’application v2.0 dédié à une application du service Azure AD disponible généralement.

De la même manière, les applications inscrites dans le nouveau portail d’inscription des applications fonctionnent exclusivement avec le modèle d’application v2.0. Vous ne pouvez pas utiliser le portail d’inscription des applications pour créer des applications s’intégrant avec les services Azure AD ou de compte Microsoft.

Les applications inscrites dans le nouveau portail d’inscription des applications sont actuellement limitées à un jeu limité de valeurs redirect\_uri. Les valeurs redirect\_uri pour les services ou applications Web doivent démarrer par le schéma ou `https`, tandis que les valeurs redirect\_uri pour toutes les autres plateformes doivent utiliser les valeurs codées en dur de `urn:ietf:oauth:2.0:oob`.

Pour savoir comment inscrire une application dans le nouveau portail d’inscription des applications, consultez [cet article](active-directory-v2-app-registration.md).

## Restrictions sur les services et API
Le modèle d’application v2.0 prend actuellement en charge la connexion des applications inscrites dans le nouveau portail d’inscription des applications, à condition qu’elles appartiennent à la liste des [flux d’authentification pris en charge](active-directory-v2-flows.md). Toutefois, ces applications pourront obtenir des jetons d’accès OAuth 2.0 uniquement pour un ensemble très limité de ressources. Le point de terminaison v2.0 émet des valeurs access-token uniquement pour :

- L’application qui a demandé le jeton. Une application peut acquérir une valeur access-token pour son propre compte, si l’application logique est composée de plusieurs composants ou niveaux. Pour voir ce scénario en action, consultez nos didacticiels [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).
- La messagerie Outlook, le calendrier et API REST de Contacts, qui se trouvent àhttps://outlook.office.com. Pour savoir comment écrire une application qui accède à ces API, consultez ces didacticiels [Prise en main Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

Davantage de services Microsoft Online seront ajoutés très prochainement, tout comme la prise en charge de vos propres services et API Web.

## Restrictions sur les bibliothèques et les kits de développement logiciel
Toutes les langues et plateformes ne possèdent pas de bibliothèques prenant en charge la version d’évaluation du modèle d’application v2.0. L’ensemble de bibliothèques d’authentification est actuellement limité à .NET, iOS, Android, NodeJS et Javascript. Les exemples de code et les didacticiels correspondant à chacun des éléments sont disponibles dans la section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).

Si vous souhaitez intégrer une application avec le modèle d’application v2.0 en utilisant une langue ou plateforme différente, consultez la page de [Référence sur le protocole OAuth 2.0 et OpenID Connect](active-directory-v2-protocols.md), qui vous expliquera comment rédiger les messages HTTP nécessaires à la communication avec le point de terminaison v2.0.

## Restrictions sur les protocoles
Le modèle d’application v2.0 prend en charge Open ID Connect et OAuth 2.0. Toutefois, certaines des fonctionnalités de ces protocoles ont été intégrées dans le modèle d’application v2.0. Voici quelques exemples :

- Prise en charge totale du paramètre `prompt` OpenID Connect
- Le paramètre `login_hint` OpenID Connect
- Le paramètre `domain_hint` OpenID Connect
- Le paramètre `end_sesssion_endpoint` OpenID Connect

Pour mieux comprendre l’étendue de la fonctionnalité de protocole prise en charge dans le modèle d’application v2.0, consultez notre page de [Référence sur le protocole OAuth 2.0 et OpenID Connect](active-directory-v2-protocols.md).

<!---HONumber=Oct15_HO3-->