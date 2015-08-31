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
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/08/2015"
	ms.author="klam"/>

#Qu'est-ce qu'une application logique ?

| Référence rapide |
| --------------- |
| [Langage de définition des applications logiques](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [Documentation du connecteur des applications logiques](https://azure.microsoft.com/fr-fr/documentation/articles/app-service-logic-connectors-list/) |
| [Forum Logic Apps](https://social.msdn.microsoft.com/Forums/fr-fr/home?forum=azurelogicapps) |

Azure App Service est une plateforme PaaS entièrement gérée (plateforme en tant que service) pour les développeurs. Elle les aide à créer des applications web, mobiles et d’intégration. Logic Apps fait partie de cette suite et permet à n’importe quel utilisateur technique ou développeur d’automatiser l’exécution des processus d’entreprise et des flux de travail via un concepteur visuel facile à utiliser.

De plus, vous pouvez combiner Logic Apps avec des [Connecteurs][connectors] de notre Marketplace pour résoudre facilement des scénarios d’intégration délicats.

![Concepteur d’application de flux](./media/app-service-logic-what-are-logic-apps/Designer.png)

Si vous souhaitez automatiquement répliquer de nouveaux enregistrements dans votre base de données SQL et votre messagerie à partir du bureau principal ou rechercher automatiquement les tweets négatifs et les envoyer à un canal secondaire

##Pourquoi des applications logiques ?

Logic Apps permet aux développeurs de concevoir des flux de travail qui démarrent à partir d’un déclencheur et exécutent une série d’étapes, chacune appelant une application API App Service tout en prenant soin de l’authentification et des meilleures pratiques telles que l’exécution durable.

Si vous souhaitez automatiser des processus d’entreprise (par exemple, rechercher des tweets négatifs et les publier dans un canal secondaire ou répliquer de nouveaux enregistrements clients à partir de SQL, à mesure de leur arrivée, dans votre système CRM), Logic Apps permet une intégration facile de sources de données disparates du cloud vers un emplacement local. Découvrez nos [connecteurs][connectors] pour plus d’inspiration et [démarrez][create] maintenant pour voir ce que vous pouvez faire.

En outre, avec nos [Applications API BizTalk][biztalk], vous pouvez effectuer une mise à l’échelle afin de faire évoluer les scénarios d’intégration avec la puissance d’un [moteur de règles][rules], de la [gestion des partenaires commerciaux][tpm] et bien plus encore.

- **Outils de conception faciles à utiliser** : vous pouvez concevoir des applications logiques de bout en bout dans le navigateur. Commencez avec un déclencheur, par exemple un simple calendrier ou chaque occurrence d'un tweet en rapport avec votre société. Ensuite, orchestrez des actions à l'aide de la galerie de connecteurs enrichie.

- **Simplicité de composition SaaS** : même les tâches de composition qui sont faciles à décrire sont difficiles à implémenter dans le code. Avec les applications logiques, la connexion de systèmes disparates est un jeu d'enfant. Vous souhaitez créer une tâche dans votre logiciel CRM basée sur l'activité de vos comptes Facebook ou Twitter ? Vous souhaitez connecter votre solution de marketing cloud à votre système de facturation local ? Les applications logiques constituent le moyen le plus rapide et le plus fiable de fournir des solutions à ces problèmes.

- **Prise en main rapide à partir de modèles** : pour vous aider à démarrer, nous vous proposons une [galerie de modèles][templates] qui vous permettent de créer rapidement des solutions courantes. De la connectivité SaaS simple aux solutions BizTalk les plus sophistiquées, et même certaines qui ne sont que pour s’amuser, la galerie est le moyen le plus rapide de comprendre la puissance des applications logiques.

- **Extensibilité intégrée** : vous ne trouvez pas le connecteur dont vous avez besoin ? Les applications logiques font partie de la suite App Service et sont conçues pour fonctionner avec des applications API. Vous pouvez facilement créer votre propre application API à utiliser comme connecteur. Créez une application rien que pour vous ou partagez-la et valorisez-la sur le Marketplace.

- **Une réelle puissance d’intégration** : commencez en douceur et évoluez selon vos besoins. Les applications logiques peuvent facilement exploiter toute la puissance de BizTalk, solution d'intégration Microsoft bien connue, pour permettre aux professionnels de l'intégration de créer les solutions dont ils ont besoin. Découvrez plus en détail les [Fonctionnalités BizTalk fournies avec App Services][biztalk].

## Concepts d'application logique

Voici quelques-uns des éléments clés qui composent l'expérience d'application logique.

- **Flux de travail** : les applications logiques offrent un moyen graphique de modéliser vos processus d’entreprise sous la forme d’une série d’étapes ou d’un flux de travail.
- **Connecteurs** : vos applications logiques doivent accéder aux données et aux services. Un connecteur est un type spécial d'application API. Il est créé spécifiquement pour vous aider à vous connecter à des données et à les utiliser. Consultez la liste des connecteurs désormais disponibles dans [Utilisation des connecteurs][connectors].
- **Déclencheurs** : certains connecteurs peuvent aussi jouer le rôle de déclencheur. Un déclencheur démarre une nouvelle instance d'un flux de travail suite à un événement spécifique, comme l'arrivée d'un message électronique ou une modification de votre compte Azure Storage.
-  **Actions** : chaque étape après le déclencheur dans un flux de travail est appelée une action. Chaque action correspond généralement à une opération sur votre connecteur ou vos applications API personnalisées.
- **BizTalk** : pour les scénarios d’intégration avancés, Azure App Service offre des fonctionnalités Biztalk. BizTalk est la plateforme d'intégration Microsoft leader sur le marché. Les applications API BizTalk vous permettent d'inclure facilement une validation, une transformation, des règles et bien plus encore dans vos flux de travail d'applications logiques. Pour en savoir plus, consultez [Qu’est-ce qu’un connecteur et une application API BizTalk ?][biztalk].

## Mise en route

Pour vous familiariser avec les applications logiques, effectuez le didacticiel [Créer une application logique][create].

Pour plus d’informations sur la plateforme Azure App Service, consultez [Azure App Service][appservice].

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[connectors]: app-service-logic-connectors-list.md
[tpm]: app-service-logic-create-a-trading-partner-agreement.md
[rules]: app-service-logic-use-biztalk-rules.md
[templates]: app-service-logic-use-logic-app-templates.md
 

<!---HONumber=August15_HO8-->