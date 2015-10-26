<properties
	pageTitle="Version préliminaire d'Azure Active Directory B2C : FAQ | Microsoft Azure"
	description="Forum aux questions sur Azure Active Directory B2C"
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
	ms.date="10/08/2015"
	ms.author="swkrish"/>

# Version préliminaire d'Azure Active Directory B2C : FAQ

Cette page répond aux questions fréquemment posées sur la version préliminaire d'Azure Active Directory (AD) B2C. N'hésitez pas à la consulter pour vous tenir au courant des mises à jour.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### Puis-je utiliser Azure AD B2C dans mon client Azure AD existant, basé sur les employés ?

Actuellement, les fonctionnalités Azure AD B2C ne peuvent pas être activées dans votre client Azure AD existant. Il est recommandé de créer un client séparé pour utiliser les fonctionnalités d'Azure AD B2C, c'est-à-dire pour gérer vos clients.

### Puis-je utiliser Azure AD B2C pour activer la connexion à des réseaux sociaux (Facebook et Google+) dans Office 365 ?

Azure AD B2C ne peut pas être utilisé avec Office 365. En général, il ne peut pas fournir une authentification pour les applications SaaS (Salesforce, Workday, etc.). Il permet la gestion des identités et de l'accès pour les applications web et mobiles grand public et n'est pas conçu pour les scénarios basés sur les employés ou les partenaires.

### Que sont les « Comptes locaux » dans Azure AD B2C ? En quoi sont-ils différents des « Comptes professionnels ou scolaires » dans Azure AD ?

Dans un client Azure AD, chaque utilisateur (à l'exception des utilisateurs avec des comptes Microsoft existants) se connecte avec une adresse de messagerie sous la forme `<xyz>@<tenant domain>`, où `<tenant domain>` est l'un des domaines vérifiés dans le client ou le domaine `<...>.onmicrosoft.com` initial. Ce type de compte est un « compte professionnel ou scolaire », également appelé un « compte professionnel ».

Dans un client Azure AD B2C, la plupart des applications veulent que l'utilisateur se connecte avec n'importe quelle adresse de messagerie arbitraire (par exemple, joe@comcast.net, bob@gmail.com, sarah@contoso.com ou jim@live.com)). Ce type de compte est un « compte local ». Aujourd'hui, nous prenons également en charge les noms d'utilisateur arbitraires (chaînes simples) en tant que comptes locaux (par exemple, joe, bob, sarah ou jim). Vous pouvez choisir l'un de ces deux « types » de compte local dans le service Azure AD B2C.

### Quels fournisseurs d'identité sociaux prenez-vous en charge maintenant ? Lesquels envisagez-vous de prendre en charge à l'avenir ?

Nous prenons actuellement en charge Facebook, Google+, LinkedIn et Amazon. Nous ajouterons la prise en charge de Microsoft Account et d'autres fournisseurs d'identité sociaux populaires en fonction de la demande des clients.

### Puis-je configurer des « Étendues » pour collecter plus d'informations sur les clients depuis différents fournisseurs d'identité sociaux ?

Non, mais cette fonctionnalité est sur notre feuille de route. Les étendues par défaut utilisées pour notre jeu de fournisseurs d'identité sociaux pris en charge sont :

- Facebook : courrier électronique
- Google+ : courrier électronique
- Amazon : profil
- LinkedIn : r\_emailaddress r\_basicprofile

### Mon application doit-elle être exécutée sous Azure pour fonctionner avec Azure AD B2C ?

Non, vous pouvez héberger votre application n'importe où (dans le cloud ou sur site). Pour interagir avec Azure AD B2C, il suffit qu'elle puisse envoyer et recevoir des requêtes HTTP sur les points de terminaison accessibles publiquement.

### J'ai plusieurs répertoires Azure AD B2C. Comment puis-je les gérer sur le portail Azure en version préliminaire ?

Chaque client Azure AD B2C a son propre volet de fonctionnalités B2C sur le portail Azure en version préliminaire. Lisez [ceci](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) pour savoir comment accéder à un volet de fonctionnalités B2C d'un client spécifique sur le portail Azure en version préliminaire. Le fait de basculer entre des répertoires Azure AD B2C sur le portail Azure en version préliminaire ne conserve pas le volet de fonctionnalités B2C ouvert dans la plupart des navigateurs.

### Comment puis-je personnaliser les courriers électroniques de vérification (le contenu et le champ de l'expéditeur, c'est-à-dire le champ « De: ») envoyés par Azure AD B2C ?

Lisez [cet article sur la personnalisation de l'interface](active-directory-b2c-reference-ui-customization.md) pour plus de détails.

### Comment puis-je migrer mes noms d'utilisateur, mots de passe et profils existants à partir de ma base de données vers Azure AD B2C ?

Vous pouvez utiliser l'API Azure AD Graph (voir notre exemple [ici](active-directory-b2c-devquickstarts-graph-dotnet.md)) pour écrire l'outil de migration. Nous fournirons diverses options de migration et des outils prêts à l'emploi ultérieurement.

### Puis-je utiliser Azure AD Connect pour migrer les identités de clients stockées dans mon répertoire Active Directory local vers Azure AD B2C ?

Non, Azure AD Connect n'est pas conçu pour fonctionner avec Azure AD B2C. Nous fournirons diverses options de migration et des outils prêts à l'emploi ultérieurement.

### Azure AD B2C fonctionne-t-il avec les systèmes CRM, tels que Microsoft Dynamics ?

Pas actuellement. L'intégration de ces systèmes est sur notre feuille de route.

### Azure AD B2C fonctionne-t-il avec SharePoint On-Premises 2016 ou version antérieure ?

Pas actuellement. Azure AD B2C ne prend pas en charge les jetons SAML 1.1 que les portails / applications de commerce électronique ont créés selon les besoins SP locaux. Notez qu'Azure AD B2C n'est pas conçu pour le scénario de partage partenaire externe Sharepoint ; consultez [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) à la place.

### Quelles sont les fonctionnalités de création de rapports et d'audit proposées par Azure AD B2C ? Sont-elles identiques à celles d'Azure AD Premium ?

Non, Azure AD B2C ne prend pas en charge le même ensemble de rapports qu'Azure AD Premium. Azure AD B2C publiera bientôt des API de base de création de rapports et d'audit.

### Puis-je localiser l'interface utilisateur des pages présentées par Azure AD B2C ? Quelles sont les langues prises en charge ?

Actuellement, Azure AD B2C est optimisé pour l'anglais uniquement. Nous prévoyons de déployer des fonctionnalités de localisation dès que possible.

### Puis-je utiliser mes propres URL sur mes pages d'inscription et de connexion présentées par Azure AD B2C ? Par exemple, puis-je remplacer l'URL login.microsoftonline.com par login.contoso.com ?

Pas actuellement. Cette fonctionnalité est sur notre feuille de route. Notez également que la « vérification » de votre domaine dans l’onglet **Domaines** de votre client sur le portail Azure ne le fera pas.

### Puis-je obtenir Azure AD B2C dans le cadre d'Enterprise Mobility Suite (EMS) ?

Non, Azure AD B2C est un service Azure avec paiement à l'utilisation et ne fait pas partie d'EMS.

### Comment signaler des problèmes avec Azure AD B2C ?

Consultez [cette rubrique de prise en charge](active-directory-b2c-support.md) pour Azure AD B2C.

### Quand Azure AD B2C sera-t-il disponible sur le marché ?

Nous ne pouvons fournir aucune informations sur la date de disponibilité pour l'instant.

## Informations complémentaires

Vous pouvez également consulter les [Limites, restrictions et contraintes de la version préliminaire](active-directory-b2c-limitations.md) actuelle.

<!---HONumber=Oct15_HO3-->