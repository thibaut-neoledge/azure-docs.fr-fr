<properties 
	pageTitle="Qu'est-ce qu'une application logique ?" 
	description="En savoir plus sur les applications logiques App Service" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="04/07/2016"
	ms.author="klam"/>

#Qu'est-ce qu'une application logique ?

| Référence rapide |
| --------------- |
| [Langage de définition des applications logiques](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [Logic Apps : Documentation sur les API managées](https://azure.microsoft.com/documentation/articles/apis-list) |
| [Forum Logic Apps](https://social.msdn.microsoft.com/Forums/home?forum=azurelogicapps) |

Azure App Service est une plateforme PaaS entièrement gérée (plateforme en tant que service) pour les développeurs. Elle les aide à créer des applications web, mobiles et d’intégration. Logic Apps fait partie de cette suite et permet à n’importe quel utilisateur technique ou développeur d’automatiser l’exécution des processus d’entreprise et des flux de travail à l’aide d’un concepteur visuel facile à utiliser.

De plus, vous pouvez combiner Logic Apps avec des [API managées][managedapis] intégrées pour résoudre facilement des scénarios d’intégration délicats :

![Concepteur d’application de flux](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Comme indiqué, les applications logiques vous permettent d’automatiser vos processus métiers. Voici quelques exemples :
 
* Vous pouvez automatiquement répliquer de nouveaux enregistrements dans votre base de données SQL, puis envoyer des messages au bureau principal.   
* Vous pouvez rechercher automatiquement les tweets négatifs et les envoyer à un canal secondaire.

Ces types de scénarios peuvent être configurés à partir du concepteur visuel, sans la moindre ligne de code. Commencez dès maintenant à [créer votre application logique][create].

## Pourquoi des applications logiques ?

Logic Apps permet aux développeurs de concevoir des flux de travail qui démarrent à partir d’un déclencheur et exécutent une série d’étapes. Chaque étape appelle une API tout en prenant soin de l’authentification et des meilleures pratiques telles que la création de points de contrôle et l’exécution en continu.

Si vous souhaitez automatiser des processus d’entreprise (par exemple, rechercher des tweets négatifs et les publier dans un canal secondaire ou répliquer de nouveaux enregistrements clients à partir de SQL, à mesure de leur arrivée, dans votre système CRM), Logic Apps permet une intégration facile de sources de données disparates du cloud vers un emplacement local. Découvrez nos [API managées][managedapis] pour plus d’inspiration et [démarrez][create] maintenant pour voir ce que vous pouvez faire.

En outre, avec nos [API managées par BizTalk][biztalk], vous pouvez effectuer une mise à l’échelle afin de faire évoluer les scénarios d’intégration avec la puissance d’un [moteur de règles][rules], la [gestion des partenaires commerciaux][tpm], etc.

- **Outils de conception faciles à utiliser** : vous pouvez concevoir des applications logiques de bout en bout dans le navigateur. Commencez avec un déclencheur, par exemple un simple calendrier ou chaque occurrence d'un tweet en rapport avec votre société. Ensuite, orchestrez des actions à l'aide de la galerie de connecteurs enrichie.

- **Simplicité de composition SaaS** : même les tâches de composition qui sont faciles à décrire sont difficiles à implémenter dans le code. Avec les applications logiques, la connexion de systèmes disparates est un jeu d'enfant. Vous souhaitez créer une tâche dans votre logiciel CRM basée sur l'activité de vos comptes Facebook ou Twitter ? Vous souhaitez connecter votre solution de marketing cloud à votre système de facturation local ? Les applications logiques constituent le moyen le plus rapide et le plus fiable de fournir des solutions à ces problèmes.

- **Prise en main rapide à partir de modèles** : pour vous aider à démarrer, nous vous proposons une [galerie de modèles][templates] qui vous permettent de créer rapidement des solutions courantes. De la connectivité SaaS simple aux solutions BizTalk les plus sophistiquées, et même certaines qui ne sont que pour s’amuser, la galerie est le moyen le plus rapide de comprendre la puissance des applications logiques.

- **Extensibilité intégrée** : vous ne trouvez pas l’API dont vous avez besoin ? Logic Apps est conçue pour fonctionner avec les applications API. Vous pouvez facilement créer votre propre application API à utiliser comme API personnalisée. Créez une application rien que pour vous ou partagez-la et valorisez-la sur le Marketplace.

- **Une réelle puissance d’intégration** : commencez en douceur et évoluez selon vos besoins. Les applications logiques peuvent facilement exploiter toute la puissance de BizTalk, solution d'intégration Microsoft bien connue, pour permettre aux professionnels de l'intégration de créer les solutions dont ils ont besoin. Découvrez plus en détail les [fonctionnalités BizTalk fournies avec Logic Apps][biztalk].

## Concepts d'application logique

Voici quelques-uns des éléments clés qui composent l'expérience d'application logique.

- **Flux de travail** : les applications logiques offrent un moyen graphique de modéliser vos processus d’entreprise sous la forme d’une série d’étapes ou d’un flux de travail.
- **API managées** : vos applications logiques doivent accéder aux données et aux services. Les API managées sont créées spécifiquement pour vous aider à vous connecter à des données et à les utiliser. Consultez la liste des API disponibles à présent dans [API managées][managedapis].
- **Déclencheurs** : certaines API managées peuvent aussi jouer le rôle de déclencheur. Un déclencheur démarre une nouvelle instance d'un flux de travail suite à un événement spécifique, comme l'arrivée d'un message électronique ou une modification de votre compte Azure Storage.
-  **Actions** : chaque étape après le déclencheur dans un flux de travail est appelée une action. Chaque action correspond généralement à une opération dans vos applications API personnalisées ou managées.
- **BizTalk** : pour les scénarios d’intégration plus avancés, Logic Apps inclut des fonctionnalités de BizTalk. BizTalk est la plateforme d'intégration Microsoft leader sur le marché. Les applications API BizTalk vous permettent d'inclure facilement une validation, une transformation, des règles et bien plus encore dans vos flux de travail d'applications logiques. Pour en savoir plus, consultez [Qu’est-ce qu’un connecteur et une application API BizTalk ?][biztalk].

## Mise en route  

 - Pour vous familiariser avec les applications logiques, effectuez le didacticiel [Créer une application logique][create].  
 - [Logic Apps vous permet d’automatiser vos processus métiers](http://channel9.msdn.com/Events/Build/2016/T694) 
 - [Apprenez à intégrer vos systèmes avec Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
- Pour plus d’informations sur la plateforme Azure App Service, consultez [Azure App Service][appservice].

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-create-a-trading-partner-agreement.md
[rules]: app-service-logic-use-biztalk-rules.md
[templates]: app-service-logic-use-logic-app-templates.md

<!------HONumber=AcomDC_0413_2016-->