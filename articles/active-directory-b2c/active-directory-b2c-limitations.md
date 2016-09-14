<properties
	pageTitle="Azure Active Directory B2C : limites et restrictions | Microsoft Azure"
	description="Une liste des limites et restrictions pour Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C : limites et restrictions

Il existe plusieurs fonctions et fonctionnalités d’Azure Active Directory (Azure AD) B2C qui ne sont pas encore prises en charge. La plupart de ces limites et problèmes connus seront résolus à l’avenir, mais vous devez les garder à l’esprit si vous concevez des applications accessibles aux consommateurs à l’aide de la version d’Azure AD B2C.

## Problèmes lors de la création de clients Azure AD B2C

Si vous rencontrez des problèmes lors de la [création d’un client Azure AD B2C](active-directory-b2c-get-started.md), consultez [Création d’un client Azure Active Directory (Azure AD) ou d’un client Azure AD B2C : problèmes et résolutions](active-directory-b2c-support-create-directory.md) pour obtenir des instructions.

Notez qu’il existe des problèmes connus liés à la suppression d’un client B2C existant et à sa recréation sous le même nom de domaine. Vous devez créer un client B2C portant un nom de domaine différent.

## Remarque sur les quotas de client B2C

Par défaut, le nombre d’utilisateurs dans un client B2C est limité à 50 000 utilisateurs. Si vous avez besoin d’augmenter le quota de votre client B2C, vous devez contacter le support technique.

## Problèmes de marque sur le courrier électronique de vérification

Le message de vérification par défaut contient la marque « Microsoft ». Nous allons la supprimer dans un futur proche. Pour le moment, vous pouvez la supprimer en utilisant la [fonctionnalité de personnalisation de la société](../active-directory/active-directory-add-company-branding.md).

## Restrictions sur les applications

Les types d’applications suivants ne sont actuellement pas pris en charge dans Azure AD B2C. Pour obtenir une description des types d’applications pris en charge, consultez [Azure AD B2C : types d’applications](active-directory-b2c-apps.md).

### Applications à page unique (JavaScript)

Plusieurs applications modernes présentent une application frontale à page unique (SPA) écrite principalement en JavaScript, utilisant bien souvent une infrastructure d’application à page unique comme AngularJS, Ember.js, Durandal, etc. Ce flux n’est pas encore disponible dans Azure AD B2C.

### Démons / applications côté serveur

Les applications qui contiennent des processus de longue durée ou qui fonctionnent sans la présence d’un utilisateur doivent également disposer d’un moyen d’accès aux ressources sécurisées, comme les API Web. Ces applications peuvent authentifier et obtenir des jetons à l’aide de l’identité d’application (plutôt qu’avec l’identité déléguée d’un consommateur), avec le [flux des informations d’identification du client OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow). Ce flux n’est pas encore disponible dans Azure AD B2C. Cela signifie que les applications peuvent uniquement obtenir les jetons après qu’un flux de connexion interactif de consommateur s’est produit.

### API Web autonome

Dans Azure AD B2C, vous avez la possibilité de [concevoir une API web sécurisée à l’aide de jetons OAuth 2.0](active-directory-b2c-apps.md#web-apis). Toutefois, cette API Web pourra recevoir uniquement les jetons d'un client qui partage le même ID d'application. La création d'une API Web accessible par différents clients n'est pas prise en charge.

### Chaînes d’API Web (On-Behalf-Of)

De nombreuses architectures incluent une API Web qui doit appeler une autre API Web en aval, toutes deux sécurisées par Azure AD B2C. Ce scénario est courant dans les clients natifs qui disposent d’une API Web principale, qui à son tour appelle un service Microsoft Online, comme l’API Graph Azure AD.

Ce scénario d’API Web chaînée peut être pris en charge à l’aide de la concession des informations d’identification du porteur OAuth 2.0 Jwt, également appelé flux On-Behalf-Of. Toutefois, le flux On-Behalf-Of n’est pas implémenté dans Azure AD B2C pour l’instant.

## Restriction sur les bibliothèques et les kits de développement logiciel

L’ensemble de bibliothèques prises en charge Microsoft qui fonctionnent avec Azure AD B2C est très limité pour l’instant. Nous prenons en charge les applications web .NET et les services et applications web Node.js. Nous avons également une version préliminaire de la bibliothèque cliente .NET appelée bibliothèque MSAL qui peut être utilisée avec Azure AD B2C dans Windows et d’autres applications .NET.

Actuellement, nous ne prenons pas en charge d’autres langages ou plateformes de bibliothèque, notamment iOS et Android. Si vous souhaitez travailler sur une autre plateforme que celles mentionnées ci-dessus, nous vous recommandons d’utiliser un kit de développement logiciel (SDK) open source qui fait référence à notre [référence sur le protocole OAuth 2.0 et OpenID Connect](active-directory-b2c-reference-protocols.md) si nécessaire. Azure AD B2C implémente OAuth et OpenID Connect, ce qui permet d’utiliser une bibliothèque OAuth ou OpenID Connect générique pour l’intégration.

Nos didacticiels de démarrage rapide iOS et Android utilisent les bibliothèques open source que nous avons testées pour la compatibilité avec Azure AD B2C. Tous nos didacticiels de démarrage rapide sont disponibles dans notre section [Prise en main](active-directory-b2c-overview.md#getting-started).

## Restriction sur les protocoles

Azure AD B2C prend en charge OpenID Connect et OAuth 2.0. Toutefois, certaines des fonctionnalités de ces protocoles n'ont pas été intégrées. Pour mieux comprendre l’étendue de la fonctionnalité de protocole prise en charge dans Azure AD B2C, consultez notre [référence sur le protocole OAuth 2.0 et OpenID Connect](active-directory-b2c-reference-protocols.md). La prise en charge de SAML et WS-Fed n’est pas disponible.

## Restriction sur les jetons

La plupart des jetons émis par Azure AD B2C sont implémentés en tant que jetons JSON Web Tokens (JWT). Toutefois, toutes les informations contenues dans les jetons Web JSON (appelées « revendications ») ne sont pas tout à fait correctes ou elles sont manquantes. Certains exemples incluent les revendications « sub » et « preferred\_username ». À mesure que les valeurs, le format ou la signification des revendications évoluent, les jetons de vos stratégies existantes ne sont pas affectés et vous pouvez compter sur leurs valeurs dans les applications de production. À mesure que les valeurs évoluent, nous allons vous donner la possibilité de configurer ces modifications pour chacune de vos stratégies. Pour mieux comprendre les jetons émis actuellement par le service Azure AD B2C, lisez la page de [référence sur les jetons](active-directory-b2c-reference-tokens.md).

## Restriction sur les groupes imbriqués

Les abonnements aux groupes imbriqués ne sont pas pris en charge dans les clients Azure AD B2C. Nous ne prévoyons pas d’ajouter cette fonctionnalité.

## Restriction sur la fonctionnalité de requête différentielle sur l’API Azure AD Graph

La [fonctionnalité de requête différentielle sur l’API Graph Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) n’est pas prise en charge dans les clients Azure AD B2C. Nous l’avons incluse à notre programme sur le long terme.

## Problèmes de gestion des utilisateurs sur le portail Azure Classic

Les fonctionnalités B2C sont accessibles sur le portail Azure. Toutefois, vous pouvez utiliser le portail Azure Classic pour accéder aux autres fonctionnalités client, notamment la gestion des utilisateurs. La gestion des utilisateurs (onglet **Utilisateurs**) sur le portail Azure Classic pose actuellement quelques problèmes :

- Pour un utilisateur de compte local (c’est-à-dire un consommateur qui s’inscrit avec une adresse e-mail et un mot de passe ou un nom d’utilisateur et un mot de passe), le champ **Nom d’utilisateur** ne correspond pas à l’identificateur (adresse e-mail ou nom d’utilisateur) utilisé pendant l’inscription. Ceci est dû au fait que le champ affiché dans le portail Azure Classic est en fait le nom principal de l’utilisateur (UPN), qui n’est pas utilisé dans les scénarios B2C. Pour afficher l’identificateur du compte local utilisé pour l’inscription, recherchez l’objet utilisateur dans l’[Explorateur graphique](https://graphexplorer.cloudapp.net/). Vous rencontrerez le même problème avec un utilisateur de compte social (c'est-à-dire un client qui s'inscrit avec Facebook, Google+, etc.), mais dans ce cas, il n'existe aucun identificateur d'utilisateur à proprement parler.

    ![Compte local - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Pour un utilisateur de compte local, vous ne pouvez pas modifier les champs et enregistrer des modifications sous l’onglet **Profil**.

## Problèmes de réinitialisation de mot de passe initiée par l’administrateur sur le portail Azure Classic

Si vous réinitialisez le mot de passe d’un consommateur basé sur un compte local sur le portail Azure Classic (commande **Réinitialiser le mot de passe** de l’onglet **Utilisateurs**), ce consommateur ne sera pas en mesure de modifier son mot de passe à la prochaine connexion, si vous utilisez la stratégie d’inscription ou de connexion, et il sera exclu de vos applications. Pour résoudre ce problème, utilisez [l’API Graph Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md) pour réinitialiser le mot de passe du consommateur (sans expiration de mot de passe), ou utilisez une stratégie de connexion au lieu d’une stratégie d’inscription ou de connexion.

## Problèmes liés à la création d’un attribut personnalisé

Un [attribut personnalisé ajouté sur le portail Azure](active-directory-b2c-reference-custom-attr.md) n’est pas créé immédiatement dans votre client B2C. Vous devrez utiliser l’attribut personnalisé dans au moins l’une de vos stratégies avant qu’il ne soit créé dans votre client B2C et ne devienne disponible via l’API Graph.

## Problèmes liés à la vérification d’un domaine sur le portail Azure Classic

Actuellement, vous ne pouvez pas vérifier un domaine avec succès sur le [portail Azure Classic](https://manage.windowsazure.com/).

## Problèmes de connexion avec la stratégie d’authentification multifacteur sur les navigateurs Safari

Les requêtes envoyées aux stratégies de connexion (avec l’authentification multifacteur activée) échouent par intermittence sur les navigateurs Safari avec des erreurs HTTP 400 (demande incorrecte). Cela est dû aux faibles limites de taille des cookies de Safari. Il existe deux solutions pour contourner ce problème :

- Utiliser la « stratégie de connexion ou d’inscription » au lieu de la « stratégie de connexion ».
- Réduisez le nombre de demandes de **Revendications d’application** dans votre stratégie.

<!---HONumber=AcomDC_0831_2016-->