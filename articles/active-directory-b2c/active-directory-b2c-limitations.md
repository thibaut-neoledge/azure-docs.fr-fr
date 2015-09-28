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
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Version préliminaire d'Azure Active Directory B2C : limites et restrictions

Il existe plusieurs fonctions et fonctionnalités d'Azure Active Directory (AD) B2C qui ne sont pas encore prises en charge dans la version préliminaire. Plusieurs de ces limites seront supprimées avant qu'Azure AD B2C ne bénéficie d'une disponibilité générale, mais vous devez les garder à l'esprit si vous concevez des applications grand public à l'aide de la version préliminaire d'Azure AD B2C.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Problèmes lors de la création de répertoires Azure AD B2C

Il existe des problèmes connus que vous pourriez rencontrer lors de la [création d'un répertoire Azure AD B2C](active-directory-b2c-get-started). Consultez cet [article](active-directory-b2c-support-create-directory.md) pour obtenir des instructions.

## Problème de personnalisation de la page de connexion au compte local

Le modèle par défaut sur la page de connexion au compte local contient des éléments de personnalisation « Microsoft Azure ». Nous travaillons en ce moment à la résolution de ce problème. Vous pouvez modifier la personnalisation sur cette page à l'aide de la [fonctionnalité de marque de société](./active-directory/active-directory-add-company-branding.md).

## Problèmes avec la déconnexion du compte local

Actuellement, la déconnexion du compte local ne fonctionne pas. Nous travaillons en ce moment à la résolution de ce problème. La solution de contournement consiste à fermer le navigateur ou à effacer les cookies.

## Prise en charge des applications de production

Les applications qui s'intègrent avec Azure AD B2C ne doivent pas être mises à disposition du public en tant qu'applications de niveau production. Azure AD B2C est en version préliminaire publique pour l'instant. Les modifications avec rupture peuvent être introduites à tout moment, et ce service ne garantit aucun contrat de niveau de service. Aucune prise en charge n’est assurée pour les incidents pouvant survenir. Si vous êtes prêt à accepter les risques de prendre une dépendance sur un service qui est en cours de développement, vous devez nous contacter par tweet à @AzureAD afin que nous évoquions l’étendue de votre application ou service.

## Restrictions sur les applications

Les types d'applications suivants ne sont actuellement pas pris en charge dans la version préliminaire d'Azure AD B2C. Pour obtenir une description des types d'applications pris en charge, consultez [cet article](active-directory-b2c-apps).

### Applications à page unique (Javascript)

Plusieurs applications modernes présentent une application frontale à page unique (SPA) écrite principalement en Javascript, utilisant bien souvent des infrastructures d’application à page unique comme AngularJS, Ember.js, Durantal, etc. Ce flux n'est pas encore disponible dans la version préliminaire d'Azure AD B2C.

### Démons / applications côté serveur

Les applications qui contiennent des processus de longue durée ou qui fonctionnent sans la présence d'un utilisateur doivent également disposer d'un moyen d'accès aux ressources sécurisées, comme les API Web. Ces applications peuvent s'authentifier et récupérer des jetons à l'aide de l'identité d'application (plutôt qu'avec l'identité déléguée d'un client), avec le [flux des informations d'identification du client OAuth 2.0](active-directory-b2c-protocols.md#oauth2-client-credentials-grant-flow). Ce flux n'est pas encore disponible dans la version préliminaire d'Azure AD B2C. Cela signifie que les applications peuvent uniquement obtenir les jetons après qu'un flux de connexion interactif de client s'est produit.

### API Web autonome

Dans la version préliminaire d'Azure AD B2C, vous avez la possibilité de [concevoir une API Web sécurisée à l'aide de jetons OAuth 2.0](active-directory-b2c-apps.md#web-apis). Toutefois, cette API Web pourra recevoir uniquement les jetons d'un client qui partage le même ID d'application. La création d'une API Web accessible par différents clients n'est pas prise en charge.

## Restriction sur les bibliothèques et les kits de développement logiciel

Toutes les langues et plateformes ne possèdent pas de bibliothèques prenant en charge la version préliminaire d'Azure AD B2C. L'ensemble de bibliothèques d'authentification est actuellement limité à .NET, iOS, Android et NodeJS. Les didacticiels de prise en main correspondant à chacun des éléments sont disponibles dans la section [Prise en main](active-directory-b2c-overview.md#getting-started).

Si vous souhaitez intégrer une application avec la version préliminaire d'Azure AD B2C en utilisant une langue ou plateforme différente, consultez la page de [Référence sur le protocole OAuth 2.0 et OpenID Connect](active-directory-b2c-protocols.md), qui vous expliquera comment rédiger les messages HTTP nécessaires à la communication avec le service Azure AD B2C.

## Restriction sur les protocoles

La version préliminaire d'Azure AD B2C prend en charge OpenID Connect et OAuth 2.0. Toutefois, certaines des fonctionnalités de ces protocoles n'ont pas été intégrées. Pour mieux comprendre l'étendue de la fonctionnalité de protocole prise en charge dans la version préliminaire d'Azure AD B2C, consultez notre page de [Référence sur le protocole OAuth 2.0 et OpenID Connect](active-directory-b2c-protocols.md).

## Restriction sur les jetons

La plupart des jetons émis par la version préliminaire d'Azure AD B2C sont implémentés en tant que jetons Web JSON (JWT). Toutefois, toutes les informations contenues dans les jetons Web JSON (appelées « revendications ») ne sont pas tout à fait correctes ou elles sont manquantes. Certains exemples incluent les revendications « sub » et « preferred\_username ». Attendez-vous à de grandes modifications par rapport à la version préliminaire. Pour mieux comprendre les jetons émis actuellement par le service Azure AD B2C, lisez notre [référence sur les jetons](active-directory-b2c-tokens.md).

## Problèmes de gestion des utilisateurs sur le portail Azure

Les fonctionnalités B2C sont accessibles sur le portail Azure en version préliminaire. Toutefois, vous pouvez utiliser le portail Azure pour accéder aux autres fonctionnalités du répertoire, y compris la gestion de l'utilisateur. Actuellement, il existe quelques problèmes connus avec la gestion de l'utilisateur (onglet **Utilisateurs**) sur le portail Azure en version préliminaire.

- Pour un compte utilisateur local (c'est-à-dire, un client qui s'inscrit avec une adresse de messagerie et un mot de passe ou un nom d'utilisateur et un mot de passe), le champ **Nom d'utilisateur** ne correspond pas à l'identificateur (adresse de messagerie ou nom d'utilisateur) utilisé pendant l'inscription. Ceci est dû au fait que le champ affiché dans le portail Azure est en fait le nom principal de l'utilisateur (UPN), qui n'est pas utilisé dans les scénarios B2C. Pour afficher l'identificateur du compte local, recherchez l'objet utilisateur dans l'[Explorateur graphique](https://graphexplorer.cloudapp.net/). Vous rencontrerez le même problème avec un utilisateur de compte social (c'est-à-dire un client qui s'inscrit avec Facebook, Google+, etc.), mais dans ce cas, il n'existe aucun identificateur d'utilisateur à proprement parler.

    ![Compte local - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Pour un compte local d'utilisateur, vous ne pourrez pas modifier les champs et enregistrer les modifications dans l'onglet **Profil**. Nous résoudrons cela bientôt.

## Restriction sur la suppression des répertoires Azure AD B2C

Vous ne pourrez pas supprimer un répertoire Azure AD B2C dans le portail Azure.

<!---HONumber=Sept15_HO3-->