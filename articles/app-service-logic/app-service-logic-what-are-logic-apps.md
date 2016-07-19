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
	ms.date="07/12/2016"
	ms.author="klam"/>

#Qu'est-ce qu'une application logique ?

| Référence rapide |
| --------------- |
| [Langage de définition des applications logiques](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [Documentation du connecteur des applications logiques](../connectors/apis-list.md) |
| [Forum Logic Apps](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=azurelogicapps) |

Logic Apps permet de simplifier et d’implémenter des processus métier complexes. Son concepteur visuel modélise et automatise vos processus métier comme une série d’étapes appelée flux de travail. Vous pouvez concevoir des flux de travail pour qu’un déclencheur les démarre et qu’ils exécutent chacune de vos étapes. Chaque étape appelle une API, tout en sécurisant l’authentification et les meilleures pratiques telles que la création de points de contrôle et l’exécution en continu.

Les avantages de Logic Apps sont les suivants :

- Gain de temps en créant des processus complexes à l’aide d’outils de conception faciles à comprendre
- Implémentation facile de tâches, qui autrement seraient difficiles à intégrer dans le code
- Mise en route rapide à partir de modèles
- Personnalisation de votre application logique à l’aide d’API
- Connexion de systèmes disparates
- Monétisation de votre application logique sur le marché
- Démarrage facile et évolutif

Logic Apps est un iPaaS (plateforme d’intégration en tant que service) entièrement pris en charge, qui permet aux développeurs d’éviter tout travail supplémentaire pour gérer l’hébergement, l’évolutivité, la disponibilité et la gestion.

Surtout, vous pouvez combiner Logic Apps à des [connecteurs gérés][managedapis] intégrés, afin de résoudre facilement des scénarios d’intégration délicats :

![Concepteur d’application de flux](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Comme indiqué, Logic Apps vous permet d’automatiser vos processus métier. Voici quelques exemples :
 
* Vous pouvez automatiquement répliquer de nouveaux enregistrements dans votre base de données SQL, puis envoyer des messages au bureau principal.
* Vous pouvez rechercher automatiquement les tweets négatifs et les envoyer à un canal secondaire.
* Lorsqu’un fichier est créé sur un serveur FTP, analysez-le, ajoutez de nouveaux enregistrements dans Dynamics CRM et créez un élément dans une liste SharePoint.

Ces types de scénarios peuvent être configurés à partir du concepteur visuel, sans la moindre ligne de code. Commencez dès maintenant à [créer votre application logique][create].

## Pourquoi des applications logiques ?

Si vous souhaitez automatiser des processus métier (par exemple, rechercher des tweets négatifs et les publier dans un canal secondaire ou répliquer de nouveaux enregistrements clients à partir de SQL, à mesure de leur arrivée, dans votre système CRM), Logic Apps facilite l’intégration de sources de données disparates depuis le cloud vers un emplacement local. Découvrez nos [connecteurs gérés][managedapis] pour stimuler votre inspiration et [lancez-vous][create] pour voir ce que vous pouvez faire.

En outre, avec notre [Enterprise Integration Account (Compte d’intégration d’entreprise)][biztalk], vous pouvez mettre en place des scénarios d’intégration mâtures grâce à la puissance d’une [messagerie XML][xml], la [gestion des partenaires commerciaux][tpm], etc.

- **Outils de conception faciles à utiliser** : vous pouvez concevoir des applications logiques de bout en bout dans le navigateur. Commencez avec un déclencheur, par exemple un simple calendrier ou chaque occurrence d'un tweet en rapport avec votre société. Ensuite, orchestrez des actions à l'aide de la galerie de connecteurs enrichie.

- **Simplicité de composition SaaS** : même les tâches de composition qui sont faciles à décrire sont difficiles à implémenter dans le code. Logic Apps facilite la connexion de systèmes disparates. Vous souhaitez créer une tâche dans votre logiciel CRM basée sur l'activité de vos comptes Facebook ou Twitter ? Vous souhaitez connecter votre solution de marketing cloud à votre système de facturation local ? Les applications logiques constituent le moyen le plus rapide et le plus fiable de fournir des solutions à ces problèmes.

- **Prise en main rapide à partir de modèles** : pour vous aider à démarrer, nous vous proposons une [galerie de modèles][templates] qui vous permettent de créer rapidement des solutions courantes. De la connectivité SaaS simple aux solutions BizTalk les plus sophistiquées, et même certaines qui ne sont que pour s’amuser, la galerie est le moyen le plus rapide de comprendre la puissance des applications logiques.

- **Extensibilité intégrée** : vous ne trouvez pas le connecteur dont vous avez besoin ? Logic Apps est conçue pour fonctionner avec les applications API. Vous pouvez facilement créer votre propre application API à utiliser comme API personnalisée. Créez une application rien que pour vous ou partagez-la et valorisez-la sur le Marketplace.

- **Une réelle puissance d’intégration** : commencez en douceur et évoluez selon vos besoins. Les applications logiques peuvent facilement exploiter toute la puissance de BizTalk, solution d'intégration Microsoft bien connue, pour permettre aux professionnels de l'intégration de créer les solutions dont ils ont besoin. En savoir plus sur [Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md).

## Concepts d'application logique

Voici quelques-uns des éléments clés qui composent l'expérience d'application logique.

- **Flux de travail** : les applications logiques offrent un moyen graphique de modéliser vos processus d’entreprise sous la forme d’une série d’étapes ou d’un flux de travail.
- **Connecteurs gérés** : vos applications logiques doivent accéder aux données et aux services. Les connecteurs gérés sont créés pour vous aider à vous connecter à des données et à les utiliser. Consultez la liste des connecteurs disponibles dans les [connecteurs gérés][managedapis].
- **Déclencheurs** : certains connecteurs gérés peuvent se comporter comme un déclencheur. Un déclencheur démarre une nouvelle instance d'un flux de travail suite à un événement spécifique, comme l'arrivée d'un message électronique ou une modification de votre compte Azure Storage.
-  **Actions** : chaque étape après le déclencheur dans un flux de travail est appelée une action. Chaque action correspond généralement à une opération sur votre connecteur géré ou vos applications API personnalisées.
- **Enterprise Integration Pack** : pour des scénarios d’intégration plus avancés, Logic Apps fournit des fonctionnalités de BizTalk. BizTalk est la plateforme d’intégration de Microsoft, leader sur le marché. Les connecteurs Enterprise Integration Pack vous permettent d’inclure facilement la validation, la transformation et bien plus encore dans vos flux de travail d’application logique.

## Mise en route  

 - Pour vous familiariser avec les applications logiques, effectuez le didacticiel [Créer une application logique][create].
 - [Afficher des exemples et des scénarios courants](app-service-logic-examples-and-scenarios.md)
 - [Logic Apps vous permet d’automatiser vos processus métiers](http://channel9.msdn.com/Events/Build/2016/T694)
 - [Apprenez à intégrer vos systèmes avec Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
- Pour plus d’informations sur la plateforme Azure App Service, consultez [Azure App Service][appservice].

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[templates]: app-service-logic-use-logic-app-templates.md

<!---HONumber=AcomDC_0713_2016-->