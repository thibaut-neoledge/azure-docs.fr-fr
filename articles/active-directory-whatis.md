<properties
	pageTitle="Qu’est-ce qu’Azure Active Directory ?"
	description="Azure Active Directory permet d'étendre vos identités locales existantes dans le cloud ou de développer des applications intégrées Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2015"
	ms.author="curtand"/>


# Qu’est-ce qu’Azure Active Directory ?


[Comment cela fonctionne-t-il ?](active-directory-works.md)<br> [Prise en main](active-directory-get-started.md)<br> [Étapes suivantes](active-directory-next-steps.md)<br> [En savoir plus](active-directory-learn-map.md)

Azure Active Directory (Azure AD) propose un moyen simple pour votre entreprise d'étendre vos investissements locaux pour tirer parti des services cloud, avec la flexibilité et la sécurité qu’Active Directory a toujours fourni. Il s’agit d’une plateforme cloud qui résout les problèmes de l'ère du cloud en étendant l'infrastructure d'identité que vous avez déjà. Vous n'avez pas besoin de déplacer quoi que ce soit. Vous pouvez continuer à tirer parti de vos investissements existants et des fonctionnalités locales tout en exploitant Azure AD afin de bénéficier d'une gestion d'accès et d'identité sur le cloud.

Avec Azure AD, vous pouvez exercer un contrôle centralisé de l'accès aux applications et aux ressources, ajouter facilement des ressources existantes (services cloud ou applications locales) et intégrer des applications que vous développez.
- Vos utilisateurs peuvent obtenir des fonctionnalités de libre-service et un accès par authentification unique à toutes leurs applications. Ils n’auront ainsi pas besoin de savoir où se trouve chaque application ou de se souvenir d'un mot de passe distinct.
- Votre entreprise peut collaborer dans le cloud avec des partenaires commerciaux, des fournisseurs et des clients d'entreprise que vous pouvez inviter dans des sites SharePoint (ou participer à leurs sites SharePoint).

Avec Azure AD, vous pouvez tout gérer depuis un seul emplacement et faire que ce contrôle soit reflété dans le cloud et en local. Il n'existe aucune duplication d'administration : Azure AD s'intègre directement à ce que vous avez déjà. [En savoir plus](active-directory-aadconnect.md).




> [AZURE.NOTE]Pour utiliser Azure Active Directory, vous avez besoin d'un compte Azure. Si vous ne possédez pas encore un compte Azure, vous pouvez [vous inscrire gratuitement pour en obtenir un gratuitement](http://azure.microsoft.com/pricing/free-trial/).


## Que peut faire Azure Active Directory pour moi ?

S'agissant d'un service complet, Azure AD offre différents avantages à des personnes aux rôles variés au sein d'une organisation.

- Si vous êtes un décideur commercial, l’utilisation d’Azure AD vous permet de réaliser vos objectifs d’applications cloud et de mobilité du personnel, avec la certitude que vos exigences de gouvernance seront satisfaites.
- Si vous êtes un fournisseur de services, utilisez Azure AD pour répondre facilement à vos besoins d'identité et d'accès, connecter vos services aux solutions d'identité existantes de vos clients, tout en atteignant également les clients Microsoft Azure et Office 365. Azure AD peut également répondre à tous vos besoins en matière d'accès back-office. Ainsi, que ceux-ci soient internes ou externes, vous pouvez être sûr que les bonnes personnes disposent de l'accès approprié.
- Si vous êtes un professionnel de l'informatique, utilisez Azure AD pour augmenter le contrôle et la visibilité des opérations à la « vitesse du cloud ». Grâce à Azure AD, vous saurez ce dont les utilisateurs se servent et vous les responsabiliserez grâce au libre-service. <br> <br>

![][1]

##Neuf choses importantes à savoir sur Azure AD

### Gérer les mots de passe Active Directory à partir de n'importe quel endroit

Utilisez Azure Active Directory pour permettre aux utilisateurs de gérer leurs mots de passe Active Directory ou Azure Active Directory à partir de n'importe quel périphérique et de n'importe quel emplacement. Un administrateur peut gérer les stratégies de mot de passe et de notification et peut voir l'activité d'audit détaillée de ces opérations de réinitialisation de mot de passe au moment où elles se produisent. [En savoir plus sur la gestion des mots de passe Azure AD](http://aka.ms/ssproverview)

### Définir les règles d'accès aux ressources cloud

Vous pouvez définir des règles et des stratégies qui contrôlent l'accès et les conditions d'accès aux applications et aux ressources cloud. Par exemple, vous pouvez exiger Multi-Factor Authentication (MFA) et gérer l'accès selon le périphérique ou l'emplacement utilisé. [En savoir plus sur Azure MFA](multi-factor-authentication.md).

### Authentification unique à une application

Azure Active Directory fournit une authentification unique sécurisée vers les applications cloud et locales, notamment Microsoft Office 365 et des milliers d'applications SaaS telles que Salesforce, Workday, DocuSign, ServiceNow et Box. [En savoir plus sur les applications SaaS](http://azure.microsoft.com/marketplace/active-directory/).

### Fonctionne avec n'importe quel périphérique

Les utilisateurs peuvent lancer des applications à partir d'un panneau personnalisé d’accès par le web, d’une application mobile, d’Office 365, ou des portails d’entreprise personnalisés à l’aide de leurs informations d'identification professionnelles existantes, et avoir la même expérience, qu’ils travaillent sur des périphériques iOS, Mac OS X, Android, ou Windows.

### Définir des règles pour l'accès externe

Les utilisateurs externes peuvent atteindre le réseau local derrière le pare-feu, en toute sécurité, à l'aide du proxy d'application intégrée. Toutes les règles et stratégies que vous définissez, authentification multi-facteur comprise, peuvent être appliquées pour l'accès aux applications cloud ou aux applications locales héritées à l'aide du proxy d'application - sans avoir à les réécrire ou à les exposer directement sur Internet. [En savoir plus sur le Proxy d'application Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx).

### Surveiller l'accès de vos utilisateurs

Enfin, Azure AD fournit à portée de main les informations des différents événements survenant dans votre organisation. Avec une création de rapports et des analyses avancées, vous obtenez des informations uniques sur l'accès de vos utilisateurs. Par exemple, à l'aide de la détection d'applications, vous saurez quelles applications sont activement utilisées au sein de votre organisation. [En savoir plus sur la détection d'applications sur cloud d'Azure AD](https://appdiscovery.azure.com/).

### Création avancée de rapports, d’audits et d'analyses
Azure AD fournit plusieurs rapports quotidiens d'accès et d'utilisation pour les administrateurs ([rapports Azure AD](active-directory-view-access-usage-reports.md)), et la création de rapports personnalisés et les présentations approfondies sont disponibles à l'aide de l'API de création de rapports ([API de création de rapports Azure AD](active-directory-reporting-api-getting-started.md)). De plus, les rapports sont disponibles dans les éditions payantes d'Azure AD, notamment la création d’audits des actions privilégiées ([création d’audits Azure AD](active-directory-view-access-usage-reports.md)).

### Une expérience simple et sécurisée pour tout le monde

Exemples :
- Les utilisateurs finaux bénéficient d'une expérience simple : ils placent leurs profils, leurs applications et peuvent gérer leur accès aux ressources à un seul endroit, sans aucune formation spécialisée. L'authentification multi-facteur, les applications SaaS, les outils hybrides et les fonctionnalités de libre-service sont prêts à l'emploi.

- Les administrateurs ont accès au portail de gestion Azure AD et à Windows PowerShell pour une gestion complète.

- Les développeurs disposent d'un jeu cohérent d'API RESTful et d'un accès aisé à la publication et à l'utilisation d'interfaces d'application.

### Choisissez votre version

Azure AD est disponible en trois versions :

- L'édition gratuite est simplement un service de répertoire cloud.
- L'édition de base est un service de répertoire cloud qui fournit également l'accès à l'application SaaS.
- L'édition Premium est une solution de service de répertoire managée complète, basée sur des règles et en libre-service.

Pour vérifier les fonctionnalités décrites ici, vous devez activer votre [version d'évaluation gratuite Azure](http://azure.microsoft.com/trial/get-started-active-directory/).

[En savoir plus sur les différentes éditions d'Azure AD](active-directory-editions.md)


## Ressources supplémentaires

* [Inscription à Azure en tant qu’organisation](sign-up-organization.md)
* [Identité Azure](fundamentals-identity.md)

<!--Image references-->
[1]: ./media/active-directory-whatis/Azure_Active_Directory.png

<!---HONumber=58-->