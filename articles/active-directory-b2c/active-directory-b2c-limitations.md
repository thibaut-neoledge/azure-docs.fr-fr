<properties
	pageTitle="Version préliminaire d'Azure Active Directory B2C : limites et restrictions | Microsoft Azure"
	description="Une liste des limites et restrictions pour Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="swkrish"/>

# Version préliminaire d'Azure Active Directory B2C : limites et restrictions

Il existe plusieurs fonctions et fonctionnalités d'Azure Active Directory (AD) B2C qui ne sont pas encore prises en charge dans la version préliminaire. Plusieurs de ces limites seront supprimées avant qu'Azure AD B2C ne bénéficie d'une disponibilité générale, mais vous devez les garder à l'esprit si vous concevez des applications grand public à l'aide de la version préliminaire d'Azure AD B2C.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Problèmes lors de la création de répertoires Azure AD B2C

Il existe des problèmes connus que vous pourriez rencontrer lors de la [création d’un client Azure AD B2C](active-directory-b2c-get-started). Consultez cet [article](active-directory-b2c-support-create-directory.md) pour obtenir des instructions.

## Problèmes de personnalisation sur des messages électroniques de vérification et les pages de réinitialisation du mot de passe libre-service

Par défaut, les messages électroniques de vérification et les pages de réinitialisation du mot de passe libre-service contiennent les éléments de personnalisation « Microsoft » et « Azure ». Nous allons les supprimer par la suite. Vous pouvez modifier la personnalisation de ces pages à l’aide de la [fonctionnalité de personnalisation de la société](./active-directory/active-directory-add-company-branding.md), sur laquelle ces éléments de personnalisation n’apparaîtront pas.

## Prise en charge des applications de production

Les applications qui s'intègrent avec Azure AD B2C ne doivent pas être mises à disposition du public en tant qu'applications de niveau production. Azure AD B2C est en version préliminaire publique pour l'instant. Les modifications avec rupture peuvent être introduites à tout moment, et ce service ne garantit aucun contrat de niveau de service. Aucune prise en charge n’est assurée pour les incidents pouvant survenir. Si vous êtes prêt à accepter les risques de prendre une dépendance sur un service qui est en cours de développement, vous devez nous contacter par tweet à @AzureAD afin que nous évoquions l’étendue de votre application ou service.

## Restrictions sur les applications

Les types d'applications suivants ne sont actuellement pas pris en charge dans la version préliminaire d'Azure AD B2C. Pour obtenir une description des types d’applications pris en charge, voir [cet article](active-directory-b2c-apps).

### Applications à page unique (Javascript)

Plusieurs applications modernes présentent une application frontale à page unique (SPA) écrite principalement en Javascript, utilisant bien souvent des infrastructures d’application à page unique comme AngularJS, Ember.js, Durantal, etc. Ce flux n'est pas encore disponible dans la version préliminaire d'Azure AD B2C.

### Démons / applications côté serveur

Les applications qui contiennent des processus de longue durée ou qui fonctionnent sans la présence d'un utilisateur doivent également disposer d'un moyen d'accès aux ressources sécurisées, comme les API Web. Ces applications peuvent s’authentifier et récupérer des jetons à l’aide de l’identité d’application (plutôt qu’avec l’identité déléguée d’un client), avec le [flux des informations d’identification du client OAuth 2.0](active-directory-b2c-protocols.md#oauth2-client-credentials-grant-flow). Ce flux n'est pas encore disponible dans la version préliminaire d'Azure AD B2C. Cela signifie que les applications peuvent uniquement obtenir les jetons après qu'un flux de connexion interactif de client s'est produit.

### API Web autonome

Dans la version préliminaire d’Azure AD B2C, vous avez la possibilité de [concevoir une API web sécurisée à l’aide de jetons OAuth 2.0](active-directory-b2c-apps.md#web-apis). Toutefois, cette API Web pourra recevoir uniquement les jetons d'un client qui partage le même ID d'application. La création d'une API Web accessible par différents clients n'est pas prise en charge.

### Chaînes d'API Web (On-Behalf-Of)

De nombreuses architectures incluent une API Web qui doit appeler une autre API Web en aval, toutes deux sécurisées par Azure AD B2C. Ce scénario est courant dans les clients natifs qui disposent d'une API Web principale, qui à son tour appelle un service Microsoft Online, comme l'API Graph Azure AD.

Ce scénario d'API Web chaînée peut être pris en charge à l'aide de la concession des informations d'identification du porteur OAuth 2.0 Jwt, également appelé flux On-Behalf-Of. Toutefois, le flux On-Behalf-Of n'est pas actuellement implémenté dans la version préliminaire d'Azure AD B2C.

## Restriction sur les bibliothèques et les kits de développement logiciel

Toutes les langues et plateformes ne possèdent pas de bibliothèques prenant en charge la version préliminaire d'Azure AD B2C. L'ensemble de bibliothèques d'authentification est actuellement limité à .NET, iOS, Android et NodeJS. Des didacticiels de démarrage rapide correspondant à chacun des éléments sont disponibles dans la section [Prise en main](active-directory-b2c-overview.md#getting-started).

Si vous souhaitez intégrer une application avec la version préliminaire d’Azure AD B2C en utilisant une langue ou plateforme différente, consultez [Référence sur le protocole OAuth 2.0 et OpenID Connect](active-directory-b2c-protocols.md), qui vous expliquera comment rédiger les messages HTTP nécessaires à la communication avec le service Azure AD B2C.

## Restriction sur les protocoles

La version préliminaire d'Azure AD B2C prend en charge OpenID Connect et OAuth 2.0. Toutefois, certaines des fonctionnalités de ces protocoles n'ont pas été intégrées. Pour mieux comprendre l’étendue de la fonctionnalité de protocole prise en charge dans la version préliminaire d’Azure AD B2C, consultez notre page [Référence sur le protocole OAuth 2.0 et OpenID Connect](active-directory-b2c-protocols.md).

## Restriction sur les jetons

La plupart des jetons émis par la version préliminaire d'Azure AD B2C sont implémentés en tant que jetons Web JSON (JWT). Toutefois, toutes les informations contenues dans les jetons Web JSON (appelées « revendications ») ne sont pas tout à fait correctes ou elles sont manquantes. Certains exemples incluent les revendications « sub » et « preferred\_username ». Attendez-vous à de grandes modifications par rapport à la version préliminaire. Pour mieux comprendre les jetons émis actuellement par le service Azure AD B2C, lisez la page de [référence sur les jetons](active-directory-b2c-tokens.md).

## Problèmes de gestion des utilisateurs sur le portail Azure

Les fonctionnalités B2C sont accessibles sur le portail Azure en version préliminaire. Toutefois, vous pouvez utiliser le portail Azure pour accéder aux autres fonctionnalités du client, y compris la gestion des utilisateurs. La gestion des utilisateurs (onglet **Utilisateurs**) sur le portail Azure pose actuellement quelques problèmes.

- Pour un utilisateur de compte local (c’est-à-dire, un client qui s’inscrit avec une adresse de messagerie et un mot de passe ou un nom d’utilisateur et un mot de passe), le champ **Nom d’utilisateur** ne correspond pas à l’identificateur (adresse de messagerie ou nom d’utilisateur) utilisé pendant l’inscription. Ceci est dû au fait que le champ affiché dans le portail Azure est en fait le nom principal de l'utilisateur (UPN), qui n'est pas utilisé dans les scénarios B2C. Pour afficher l’identificateur du compte local utilisé pour l’inscription, recherchez l’objet utilisateur dans l’[Explorateur graphique](https://graphexplorer.cloudapp.net/). Vous rencontrerez le même problème avec un utilisateur de compte social (c'est-à-dire un client qui s'inscrit avec Facebook, Google+, etc.), mais dans ce cas, il n'existe aucun identificateur d'utilisateur à proprement parler.

    ![Compte local - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Pour un utilisateur de compte local, vous ne pouvez pas modifier les champs et enregistrer des modifications sous l’onglet **Profil**. Nous résoudrons cela bientôt.

## Problèmes de réinitialisation de mot de passe initiée par l’administrateur sur le portail Azure

Si vous réinitialisez le mot de passe pour un consommateur basé sur un compte local sur le portail Azure (commande **Réinitialiser le mot de passe** de l’onglet **Utilisateurs**), ce consommateur ne sera pas en mesure de modifier son mot de passe à la prochaine connexion et il sera exclu de vos applications. Nous travaillons actuellement à la correction de ce problème. Pour résoudre ce problème, utilisez l’[API Graph d’Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md) pour réinitialiser le mot de passe du client.

## Restriction sur la suppression des répertoires Azure AD B2C

Vous ne pourrez pas supprimer un client Azure AD B2C dans le portail Azure.

<!---HONumber=Oct15_HO1-->