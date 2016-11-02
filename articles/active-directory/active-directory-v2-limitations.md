<properties
    pageTitle="Limitations et restrictions du point de terminaison V2.0 | Microsoft Azure"
    description="Une liste des limitations et restrictions associées au point de terminaison v2.0 Azure AD."
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
    ms.date="09/30/2016"
    ms.author="dastrock"/>


# <a name="should-i-use-the-v2.0-endpoint?"></a>Dois-je utiliser le point de terminaison v2.0 ?

Lorsque vous créez des applications qui s’intègrent à Azure Active Directory, vous devez déterminer si les protocoles d’authentification et le point de terminaison v2.0 répondent à vos besoins.  Le point de terminaison Azure AD d’origine est toujours intégralement pris en charge. À certains égards, il est plus riche en fonctionnalités que le point de terminaison v2.0.  Toutefois, le point de terminaison v2.0 [octroie d’importants avantages](active-directory-v2-compare.md) aux développeurs, ce qui peut vous inciter à utiliser le nouveau modèle de programmation.

À ce stade, notre recommandation d’utilisation du point de terminaison v2.0 est la suivante :

- Si vous souhaitez prendre en charge des comptes personnels Microsoft dans votre application, vous devez utiliser le point de terminaison v2.0.  Mais veillez à bien comprendre les limitations répertoriées dans cet article, en particulier celles se rapportant spécifiquement aux comptes professionnels et scolaires.
- Si votre application requiert uniquement la prise en charge de comptes professionnels et scolaires, nous vous conseillons d’utiliser [les points de terminaison Azure AD d’origine](active-directory-developers-guide.md).

Au fil du temps, le point de terminaison v2.0 se développera et les restrictions répertoriées ici seront éliminées. Ainsi, vous n’aurez qu’à utiliser le point de terminaison v2.0.  En attendant, cet article vous aide à déterminer si le point de terminaison v2.0 est approprié pour vous.  Nous continuerons à mettre à jour cet article au fil du temps afin de prendre en compte l’évolution du point de terminaison v2.0. Aussi, prenez le temps de remettre régulièrement vos exigences en correspondance avec les fonctionnalités du point de terminaison v2.0.

Si vous disposez d’une application associée à Azure AD qui ne recourt pas au point de terminaison v2.0, il n’est pas nécessaire de repartir de zéro.  À l’avenir, nous vous fournirons un moyen de configurer vos applications Azure AD existantes pour l’utilisation avec le point de terminaison v2.0.

## <a name="restrictions-on-apps"></a>Restrictions sur les applications
Les types d’application suivants ne sont actuellement pas pris en charge par le point de terminaison v2.0.  Pour obtenir une description des types d’applications pris en charge, consultez [cet article](active-directory-v2-flows.md).

##### <a name="standalone-web-apis"></a>API Web autonome
Avec le point de terminaison v2.0, vous avez la possibilité de [concevoir une API web sécurisée à l’aide d’OAuth 2.0](active-directory-v2-flows.md#web-apis).  Toutefois, cette API web pourra recevoir uniquement les jetons d’une application qui partage le même ID d’application.  La création d’une API web accessible par un client présentant un ID d’application différent n’est pas prise en charge.  Ce client ne pourra pas demander ou obtenir d’autorisation d’accès à votre API web.

Pour voir comment créer une API Web qui accepte des jetons d’un client présentant un ID d’application identique, consultez les exemples d’API Web de point de terminaison v2.0 dans [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).

##### <a name="web-api-on-behalf-of-flow"></a>Flux On-Behalf-Of d’API web
De nombreuses architectures incluent une API Web qui doit appeler une autre API Web en aval, toutes deux sécurisées par le point de terminaison v2.0.  Ce scénario est courant dans les clients natifs qui disposent d’une API web principale, qui à son tour appelle un service Microsoft Online ou une autre API web personnalisée qui prend en charge Azure AD.

Ce scénario peut être pris en charge à l’aide de la concession des informations d’identification du porteur OAuth 2.0 Jwt, également appelé flux On-Behalf-Of.  Toutefois, le flux On-Behalf-Of n’est actuellement pas pris en charge pour le point de terminaison v2.0.  Pour observer le fonctionnement de ce flux dans le service Azure AD disponible généralement, consultez l’ [’exemple de code On-Behalf-Of sur GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## <a name="restrictions-on-app-registrations"></a>Restrictions sur les inscriptions d’application
À ce stade, l’ensemble des applications dont l’intégration avec le point de terminaison v2.0 est envisagée doivent créer une nouvelle inscription d’application à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com).  Aucune application existante Azure AD ou de compte Microsoft, ou application inscrite dans un portail différent du nouveau portail d’inscription d’application n’est compatible avec le point de terminaison v2.0.  Nous prévoyons de fournir un moyen pour permettre l’utilisation des applications existantes en tant qu’applications v2.0. Cependant, il n’existe actuellement aucun chemin de migration d’une application vers le point de terminaison v2.0.

De la même manière, les applications inscrites dans le nouveau portail d’inscription des applications ne fonctionneront pas avec le point de terminaison d’authentification d’origine Azure AD.  Vous pouvez, cependant, utiliser les applications créées dans le portail d’inscription des applications pour procéder à une intégration avec le point de terminaison d’authentification du compte Microsoft, `https://login.live.com`.

En outre, les mises en garde suivantes s’appliquent aux inscriptions d’applications créées à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com) :

- La propriété **homepage**, également appelée **URL de connexion**, n’est pas prise en charge.  Sans page d’accueil, ces applications ne seront pas affichées dans le volet Office MyApps.
- Actuellement, seuls deux secrets d’application sont autorisés par ID d’application.
- Une inscription d’application peut uniquement être affichée et gérée par un compte de développeur unique.  Elle ne peut pas être partagée entre plusieurs développeurs.
- Il existe plusieurs restrictions sur le format de redirect_uri autorisé.  Consultez la section suivante pour plus d’informations.

## <a name="restrictions-on-redirect-uris"></a>Restrictions de l’URI de redirection
Les applications inscrites dans le nouveau portail d’inscription des applications sont actuellement limitées à un jeu limité de valeurs redirect_uri.  Le redirect_uri pour les services et applications web doit commencer par le schéma `https` et toutes les valeurs redirect_uri doivent partager un seul domaine DNS.  Par exemple, il n’est pas possible d’inscrire une application web qui a des URI de redirection (redirect_uris) :

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Le système d’inscription compare le nom DNS complet du redirect_uri existant avec le nom DNS du redirect_uri que vous ajoutez. La demande d’ajout du nom DNS échoue si l’une des conditions suivantes est remplie :  

- Si le nom DNS complet du nouveau redirect_uri ne correspond pas au nom DNS du redirect_uri existant
- Si le nom DNS complet du nouveau redirect_uri n’est pas un sous-domaine du redirect_uri existant

Par exemple, si l’application possède actuellement un redirect_uri :

`https://login.contoso.com`

Il est alors possible d’ajouter :

`https://login.contoso.com/new`

qui correspond exactement au nom DNS, ou :

`https://new.login.contoso.com`

qui est un sous-domaine DNS de login.contoso.com.  Si vous souhaitez disposer d’une application avec login-east.contoso.com et login-west.contoso.com en tant que redirect_uris, vous devez ajouter les redirect_uris suivants dans l’ordre :

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Les deux derniers peuvent être ajoutés, car ils sont des sous-domaines du première redirect_uri, contoso.com. Cette limitation sera supprimée dans une version ultérieure.

Pour savoir comment inscrire une application dans le nouveau portail d’inscription des applications, consultez [cet article](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services-&-apis"></a>Restrictions sur les services et API
Le point de terminaison v2.0 prend actuellement en charge la connexion de toute application inscrite dans le nouveau portail d’inscription des applications, à condition qu’elles appartiennent à la liste des [flux d’authentification pris en charge](active-directory-v2-flows.md).  Toutefois, ces applications pourront obtenir des jetons d’accès OAuth 2.0 uniquement pour un ensemble très limité de ressources.  Le point de terminaison v2.0 émet des valeurs access-token uniquement pour :

- L’application qui a demandé le jeton.  Une application peut acquérir une valeur access-token pour son propre compte, si l’application logique est composée de plusieurs composants ou niveaux.  Pour voir ce scénario en action, consultez nos didacticiels [Prise en main](active-directory-appmodel-v2-overview.md#getting-started) .
- La messagerie Outlook, le calendrier et les API REST de Contacts, qui se trouvent à l’adresse https://outlook.office.com.  Pour savoir comment écrire une application qui accède à ces API, consultez ces didacticiels [Prise en main Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) .
- Les API Microsoft Graph.  Pour en savoir plus sur Microsoft Graph et sur toutes les données disponibles, consultez le site [https://graph.microsoft.io](https://graph.microsoft.io).

Aucun autre service n’est actuellement pris en charge.  Davantage de services Microsoft Online seront ajoutés prochainement, tout comme la prise en charge de vos propres services et API Web personnalisés.

## <a name="restrictions-on-libraries-&-sdks"></a>Restrictions sur les bibliothèques et les kits de développement logiciel
La prise en charge de la bibliothèque pour le point de terminaison v2.0 est limitée à ce stade.  Si vous souhaitez utiliser le point de terminaison v2.0 dans une application de production, vous disposez des options suivantes :

- Si vous générez une application web, vous pouvez en toute sécurité utiliser notre middleware mis à la disposition générale côté serveur afin de vous connecter et de procéder à la validation des jetons.  Vous recourrez notamment au middleware OWIN Open ID Connect pour ASP.NET et à notre plug-in NodeJS Passport.  Des exemples de code utilisant notre middleware sont également disponibles dans notre section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started) .
- Pour d’autres plateformes et pour les applications natives et mobiles, vous pouvez également procéder à l’intégration avec le point de terminaison v2.0 en envoyant et en recevant directement des messages de protocole dans votre code d’application.  Les protocoles v2.0 OpenID Connect et OAuth [ont été explicitement documentés](active-directory-v2-protocols.md) afin de vous aider à effectuer une telle intégration.
- Enfin, vous pouvez utiliser les bibliothèques open source Open ID Connect et OAuth pour procéder à l’intégration avec le point de terminaison v2.0.  Le protocole v2.0 devrait être compatible avec de nombreuses bibliothèques de protocole open source, sans modification majeure.  La disponibilité de telles bibliothèques varie en fonction des langues et des plates-formes, et les sites web [Open ID Connect](http://openid.net/connect/) et [OAuth 2.0](http://oauth.net/2/) conservent une liste d’implémentations populaires. Consultez [Azure Active Directory (AD) v2.0 et bibliothèques d’authentification](active-directory-v2-libraries.md) pour plus d’informations, ainsi que la liste des bibliothèques clientes open source et des exemples qui ont été testés avec le point de terminaison v2.0.

Nous avons également publié une version préliminaire de la [bibliothèque d’authentification Microsoft (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) pour .NET uniquement.  N’hésitez pas à essayer cette bibliothèque dans les applications de serveur et clientes .NET. Toutefois, en tant que bibliothèque en version préliminaire, elle n’est pas accompagnée d’une prise en charge de qualité générale.

## <a name="restrictions-on-protocols"></a>Restrictions sur les protocoles
Le point de terminaison v2.0 prend uniquement en charge Open ID Connect et OAuth 2.0.  Toutefois, certaines des fonctionnalités de ces protocoles n’ont pas été intégrées dans le point de terminaison v2.0, notamment :

- OpenID Connect `end_session_endpoint`, qui permet à une application de mettre fin à la session de l’utilisateur avec le point de terminaison v2.0.
- Les id_tokens émis par le point de terminaison v2.0 contiennent uniquement un identificateur par paire pour l’utilisateur.  Cela signifie que deux applications différentes reçoivent des ID différents pour le même utilisateur.  Notez que, en interrogeant le point de terminaison Microsoft Graph `/me`, vous serez en mesure d’obtenir un ID concordant pour l’utilisateur qui pourra être utilisé entre les applications.
- À ce stade, les id_tokens émis par le point de terminaison v2.0 ne contiennent pas de revendication `email` pour l’utilisateur, même si vous obtenez l’autorisation de l’utilisateur de consulter sa messagerie.
- Le point de terminaison des informations utilisateur OpenID Connect. Pour l’instant, le point de terminaison des informations utilisateur n’est pas implémenté sur le point de terminaison v2.0.  Toutefois, toutes les données de profil utilisateur que vous êtes susceptible de recevoir sur ce point de terminaison sont disponibles sur le point de terminaison Microsoft Graph `/me` .
- Revendications de rôle et de groupe.  À ce stade, le point de terminaison v2.0 ne prend pas en charge l’émission de revendications de rôle ou de groupe dans les jetons id_tokens.

Pour mieux comprendre l’étendue de la fonctionnalité de protocole prise en charge dans le point de terminaison v2.0, consultez notre page de [référence sur les protocoles OAuth 2.0 et OpenID Connect](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-&-school-accounts"></a>Restrictions pour les comptes professionnels et scolaires
Il existe quelques fonctionnalités spécifiques aux utilisateurs d’organisation/d’entreprise Microsoft qui ne sont pas encore prises en charge par le point de terminaison v2.0.

##### <a name="device-based-conditional-access,-native-and-mobile-apps,-and-the-microsoft-graph"></a>Accès conditionnel basé sur les appareils, applications natives et mobiles, et Microsoft Graph
Le point de terminaison v2.0 ne prend pas encore en charge l’authentification des appareils pour les applications mobiles et natives, telles que les applications natives qui s’exécutent sur iOS ou Android.  Ceci peut empêcher votre application native d’appeler Microsoft Graph pour certaines organisations.  L’authentification des appareils est obligatoire lorsqu’un administrateur définit pour une application une stratégie d’accès conditionnel basé sur les appareils.  Pour le point de terminaison v2.0, le scénario le plus probable pour l’accès conditionnel basé sur les appareils consiste à ce qu’un administrateur définisse une stratégie sur une ressource dans Microsoft Graph, comme l’API Outlook.  Si un administrateur définit cette stratégie et que votre application native demande un jeton à Microsoft Graph, la demande échouera, car l’authentification des appareils n’est pas encore prise en charge.  Cependant, les applications web qui demandent des jetons à Microsoft Graph sont prises en charge lorsque des stratégies basées sur les appareils sont configurées.  Dans le cas de l’application web, l’authentification des appareils est effectuée via le navigateur web de l’utilisateur.

En tant que développeur, vous n’avez sans doute aucun contrôle sur le moment où les stratégies sont définies sur des ressources Microsoft Graph. Vous ne savez sans doute même pas lorsque cela se produit.  Si vous créez une application pour des utilisateurs professionnels et scolaires, utilisez [le point de terminaison Azure AD d’origine](active-directory-developers-guide.md) jusqu’à ce que le point de terminaison v2.0 prenne en charge l’authentification des appareils.  Pour plus d’informations sur l’accès conditionnel basé sur les appareils, consultez [cet article](active-directory-conditional-access.md#device-based-conditional-access).

##### <a name="windows-integrated-authentication-for-federated-tenants"></a>Authentification Windows intégrée pour les clients fédérés
Si vous avez utilisé ADAL (et par conséquent le point de terminaison Azure AD d’origine) dans des applications Windows, vous avez sans doute profité de l’octroi d’assertion SAML.  Cet octroi permet aux utilisateurs de clients Azure AD fédérés de s’authentifier en mode silencieux avec leur instance d’Active Directory sur site, sans avoir à entrer leurs informations d’identification.  L’octroi d’assertion SAML n’est actuellement pas pris en charge sur le point de terminaison v2.0.



<!--HONumber=Oct16_HO2-->


