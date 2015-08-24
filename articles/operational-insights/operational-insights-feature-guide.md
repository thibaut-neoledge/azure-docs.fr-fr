<properties
	pageTitle="Guide des fonctionnalités d'Operational Insights"
	description="Operational Insights est un service d'analyse qui permet aux administrateurs d'analyser en profondeur les environnements locaux et cloud. Il permet d'interagir avec les données machine historiques et en temps réel afin de développer rapidement des analyses personnalisées, et il fournit des modèles d'analyse de données développés par Microsoft et la communauté."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/06/2015"
	ms.author="banders"/>

# Guide des fonctionnalités d’Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Cet article vise à mieux vous faire comprendre les problèmes qu’Operational Insights peut aider à résoudre, ce que l’environnement d’Operational Insights comprend et comment vous pouvez créer un compte Operational Insights et vous connecter au service.

## Transformation des données de la machine

Operational Insights est un service d'analyse qui permet aux administrateurs d'analyser en profondeur les environnements locaux et cloud. Il permet d'interagir avec les données machine historiques et en temps réel afin de développer rapidement des analyses personnalisées, et il fournit des modèles d'analyse de données développés par Microsoft et la communauté.

Grâce à Operational Insights, vous pouvez transformer les données machine en intelligence opérationnelle avec les fonctionnalités suivantes.


|**Icône** | **Ce que vous devez faire** | **Résultat**|
|---|---|---|
|![](./media/operational-insights-feature-guide/cap-plan.png) | [Planification de la capacité](operational-insights-capacity.md) | Vous pouvez utiliser la solution de planification de la capacité dans Microsoft Azure Operational Insights pour vous aider à comprendre la capacité de votre infrastructure serveur. |
| ![](./media/operational-insights-feature-guide/update.png) | [Évaluation des mises à jour du système](operational-insights-updates.md) | Vous pouvez utiliser la solution des mises à jour du système dans Microsoft Azure Operational Insights pour vous aider à appliquer les mises à jour manquantes aux serveurs dans votre infrastructure. |
| ![](./media/operational-insights-feature-guide/log-search.png) | [Recherche de journal](operational-insights-search.md) | Vous utilisez la fonctionnalité de recherche de journal pour créer des requêtes pour la transformation, le filtrage et les rapports relatifs aux résultats. La recherche utilise des données d'événements et les journaux IIS pour les recherches de journal dans Operational Insights. |
| ![](./media/operational-insights-feature-guide/malware.png) | [Évaluation des logiciels malveillants](operational-insights-antimalware.md) | Vous pouvez utiliser la solution anti-programme malveillant de Microsoft Azure Operational Insights pour protéger les serveurs de votre infrastructure contre les programmes malveillants. |
| ![](./media/operational-insights-feature-guide/sec-audit.png) | [Ports et sécurité](operational-insights-security-audit.md) | Vous pouvez utiliser la solution d’analyse décisionnelle de sécurité et d’audit d’Operational Insights qui vous permet d’obtenir une vue détaillée de la sécurité informatique de votre organisation. Elle fournit des requêtes de recherche intégrées pour détecter les problèmes importants qui requièrent votre attention. |
| ![](./media/operational-insights-feature-guide/assessment.png) | [Évaluation d’Active Directory et de SQL](operational-insights-assessment.md) | Les solutions d’évaluation permettent d’évaluer les risques et l’intégrité de vos environnements de serveurs à intervalles réguliers. |
| ![](./media/operational-insights-feature-guide/alert.png) | [Gestion des alertes](operational-insights-alerts.md) | La solution Gestion des alerte permet de gérer les alertes à partir de serveurs surveillés par Operations Manager. |


Vous pouvez également :

- **Transmettre des données de la machine vers le système avec ou sans agent, ou conjointement à System Center Operations Manager**. Pour plus d’informations, consultez :
	- [Connexion à Operational Insights à partir de System Center Operations Manager](operational-insights-connect-scom.md)
	- [Connexion directe des ordinateurs à Operational Insights](operational-insights-direct-agent.md)
	- [Analyse des données des serveurs dans Microsoft Azure](operational-insights-analyze-data-azure.md)
- **Effectuer toutes les actions précédentes durant vos déplacements grâce à l’application mobile**
	- Pour plus d’informations sur l’application Windows Phone, consultez la page sur l’[application mobile Operational Insights](http://www.windowsphone.com/fr-fr/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865).

## Environnement Operational Insights

L'environnement Operational Insights est composé des éléments suivants :

- espaces de travail hébergés dans Microsoft Azure qui sont des conteneurs pour les comptes Azure,
- service web Operational Insights, qui est hébergé dans le cloud,
- agents distincts qui se connectent directement au service web
- ou service joint à System Center Operations Manager, mais non requis.


Si vous avez utilisé la version précédente d'Operational Insights, appelée System Center Advisor, le logiciel Advisor est peut-être installé dans votre environnement local. Toutefois, le logiciel Advisor n'est pas pris en charge avec Operational Insights.

L'utilisation du logiciel Operational Insights en tant que service Operations Manager implique un ou plusieurs groupes d'administration et au moins un agent par groupe. Les agents Operations Manager collectent des données de vos serveurs et les analysent à l’aide de solutions (semblables à des packs d’administration dans System Center Operations Manager). Les données sont régulièrement transmises à partir d’Operations Manager vers Operational Insights (si nécessaire, en passant par un serveur proxy), sans stocker la moindre donnée dans l’une des bases de données d’Operations Manager, ce qui permet d’éviter toute charge supplémentaire.

De même, les agents installés sur des ordinateurs individuels peuvent se connecter directement au service web pour envoyer les données collectées à traiter.

Les données de chaque solution sont analysées, indexées et présentées sur le portail Operational Insights. Vous pouvez afficher toutes les alertes, ainsi que les conseils de correction, les évaluations de configuration, les problèmes de capacité d'infrastructure, l'état de mise à jour système, les avertissements anti-programme malveillant et les données consignées qui leur sont associés. Vous pouvez également effectuer des recherches de journaux détaillés ad hoc et explorations.

![Image d’un diagramme de présentation d’Operational Insights](./media/operational-insights-feature-guide/environment.png)

### Où l’application Operational Insights est-elle disponible ?
Microsoft Azure Operational Insights est hébergée aux États-Unis. Bien que cette application soit en anglais, le service est disponible dans un certain nombre de marchés supplémentaires. Pour plus d’informations, consultez la page [Disponibilité internationale](http://go.microsoft.com/fwlink/?LinkId=229842).

<!---HONumber=August15_HO7-->