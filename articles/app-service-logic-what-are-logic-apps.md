<properties 
	pageTitle="Qu'est-ce qu'une application logique ?" 
	description="En savoir plus sur les applications logiques App Service" 
	authors="joshtwist" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="jtwist"/>

#Qu'est-ce qu'une application logique ?

Azure App Service est une offre PaaS (Platform as a Service) entièrement gérée, destinée aux développeurs professionnels, qui permet de bénéficier d'un ensemble complet de fonctionnalités pour les scénarios web, mobiles et d'intégration. Les applications logiques font partie de la suite App Service et permettent à n'importe quel utilisateur technique ou développeur d'automatiser l'exécution des processus d'entreprise via un concepteur visuel facile à utiliser.

De plus, vous pouvez les combiner avec les applications API et les connecteurs de notre Marketplace pour aider à mettre facilement en œuvre les scénarios d'intégration les plus délicats.

![Flow app designer](./media/app-service-learn-about-flows-preview/Designer.png)

##Explosion des environnements SaaS, PaaS et hybrides

L'ère du cloud donne lieu à une explosion dans l'utilisation des systèmes SaaS et PaaS, d'où une pression sans cesse croissante sur les développeurs. En même temps, les backlogs d'applications ne cessent de s'allonger et ces solutions hétérogènes et distribuées présentent de nouveaux défis d'intégration.  De plus, les entreprises doivent exploiter leurs données et services locaux dans leurs solutions, et ce en toute sécurité.

La création de solutions qui couvrent tous ces systèmes présente de nombreux pièges et des défis certains qui exigent beaucoup de temps de la part des équipes de développement.

##Pourquoi des applications logiques ?

Les applications logiques permettent aux développeurs de concevoir des flux de travail qui articulent l'intention via un déclencheur et une série d'étapes, chacune appelant une application API App Service tout en prenant soin de l'authentification et des meilleures pratiques telles que l'exécution durable.

- **Outils de conception faciles à utiliser** : vous pouvez concevoir des applications logiques de bout en bout dans le navigateur. Commencez avec un déclencheur, par exemple un simple calendrier ou chaque occurrence d'un tweet en rapport avec votre société. Ensuite, orchestrez des actions à l'aide de la galerie de connecteurs enrichie.

- **Simplicité de composition SaaS** : même les tâches de composition qui sont faciles à décrire sont difficiles à implémenter dans le code. Avec les applications logiques, la connexion de systèmes disparates est un jeu d'enfant. Vous souhaitez créer une tâche dans votre logiciel CRM basée sur l'activité de vos comptes Facebook ou Twitter ? Vous souhaitez connecter votre solution de marketing cloud à votre système de facturation local ? Les applications logiques constituent le moyen le plus rapide et le plus fiable de fournir des solutions à ces problèmes.

- **Prise en main rapide à partir de modèles** : pour vous aider à démarrer, nous vous proposons une galerie de modèles qui vous permettent de créer rapidement des solutions courantes. De la connectivité SaaS simple aux solutions BizTalk les plus sophistiquées, et même certaines qui ne sont que  'for fun' - la galerie est le moyen le plus rapide de comprendre la puissance des applications logiques.

- **Extensibilité intégrée** : vous ne trouvez pas le connecteur dont vous avez besoin ? Les applications logiques font partie de la suite App Service et sont conçues pour fonctionner avec des applications API. Vous pouvez facilement créer votre propre application API à utiliser comme connecteur. Créez une application rien que pour vous ou partagez-la et valorisez-la sur le Marketplace.

- **Une réelle puissance d'intégration** : commencez en douceur et évoluez selon vos besoins. Les applications logiques peuvent facilement exploiter toute la puissance de BizTalk, solution d'intégration Microsoft bien connue, pour permettre aux professionnels de l'intégration de créer les solutions dont ils ont besoin. Découvrez plus en détail les [Fonctionnalités BizTalk fournies avec App Services][biztalk].

## Concepts d'application logique

Voici quelques-uns des éléments clés qui composent l'expérience d'application logique. 

- **Flux de travail** : les applications logiques offrent un moyen graphique de modéliser vos processus d'entreprise sous la forme d'une série d'étapes ou d'un flux de travail.
- **Connecteurs** : vos applications logique doivent accéder aux données et aux services. Un connecteur est un type spécial d'application API. Il est créé spécifiquement pour vous aider à vous connecter à des données et à les utiliser. Pour en savoir plus, consultez [Qu'est-ce qu'un connecteur et une application API BizTalk ?][biztalk].
- **Déclencheurs** : certains connecteurs peuvent aussi jouer le rôle de déclencheur. Un déclencheur démarre une nouvelle instance d'un flux de travail suite à un événement spécifique, comme l'arrivée d'un message électronique ou une modification de votre compte Azure Storage.
- **BizTalk** : pour les scénarios d'intégration avancés, Azure App Services offre des fonctionnalités Biztalk. BizTalk est la plateforme d'intégration Microsoft leader sur le marché. Les applications API BizTalk vous permettent d'inclure facilement une validation, une transformation, des règles et bien plus encore dans vos flux de travail d'applications logiques. Pour en savoir plus, consultez [Qu'est-ce qu'un connecteur et une application API BizTalk ?][biztalk].

## Prise en main

Pour vous familiariser avec les applications logiques, effectuez le didacticiel [Créer une application logique][create].

Pour plus d'informations sur la plateforme Azure App Service, consultez [Azure App Service][appservice].

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md




<!--HONumber=49-->