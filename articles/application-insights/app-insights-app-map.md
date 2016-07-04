<properties 
	pageTitle="Mise en correspondance d’applications dans Application Insights | Microsoft Azure" 
	description="Une présentation visuelle des dépendances entre les composants d’application, intégrant des indicateurs de performance clés et des alertes." 
	services="application-insights" 
    documentationCenter=""
	authors="SoubhagyaDash" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/15/2016" 
	ms.author="awills"/>
 
# Mise en correspondance d’applications dans Application Insights

Dans [Visual Studio Application Insights](app-insights-overview.md), la mise en correspondance d’applications est une présentation visuelle des relations de dépendance des composants de votre application. Chaque composant affiche des indicateurs de performance clés, tels que la charge, les performances, les échecs et les alertes, pour vous aider à détecter n’importe quel composant à l’origine de problèmes de performances ou de défaillances. Vous pouvez cliquer sur tout composant pour accéder à des diagnostics plus détaillés à partir d’Application Insights et, si votre application utilise des services Azure, des diagnostics d’Azure, notamment les recommandations de SQL Database Advisor.

À l’instar des autres graphiques, vous pouvez épingler une mise en correspondance d’applications au tableau de bord Azure, où elle sera entièrement fonctionnelle.

## Ouvrir la mise en correspondance d’applications

Ouvrir la mise en correspondance à partir du Panneau de vue d’ensemble de votre application :

![ouvrir la mise en correspondance d’applications](./media/app-insights-app-map/01.png)

![mise en correspondance d’applications](./media/app-insights-app-map/02.png)

La mise en correspondance affiche les éléments suivants :

* Tests de disponibilité
* Composant côté client (surveillé avec le Kit de développement logiciel (SDK) JavaScript)
* Composant côté serveur
* Dépendances des composants client et serveur

Vous pouvez développer et réduire les groupes de liens de dépendance :

![réduire](./media/app-insights-app-map/03.png)
 
Si vous avez un grand nombre de dépendances d’un type (SQL, HTTP, etc.), elles peuvent apparaître groupées.


![dépendances groupées](./media/app-insights-app-map/03-2.png)
 
 
## Détecter les problèmes

Chaque nœud possède des indicateurs de performance pertinents, tels que les taux de charge, de performances et de défaillances pour ce composant.

Les icônes d’avertissement mettent en évidence les problèmes éventuels. Un avertissement orange signifie qu’il existe des défaillances dans les requêtes, les vues de page ou les appels de dépendance. Un avertissement rouge signifie un taux de défaillance de plus de 5 %.


![icônes de défaillance](./media/app-insights-app-map/04.png)

 
En outre, des alertes actives s’affichent :


![alertes actives](./media/app-insights-app-map/05.png)
 
Si vous utilisez SQL Azure, une icône vous indique des recommandations éventuelles sur la façon dont vous pouvez améliorer les performances.


![Recommandation d’Azure](./media/app-insights-app-map/06.png)

Cliquez sur une icône pour obtenir plus de détails :


![recommandation d’Azure](./media/app-insights-app-map/07.png)
 
 
## Clics pour le diagnostic

Chacun des nœuds sur la mise en correspondance offre des clics ciblés pour le diagnostic. Les options varient selon le type du nœud.

![options de serveur](./media/app-insights-app-map/09.png)

 
Pour les composants qui sont hébergés dans Azure, les options incluent des liens directs.


## Filtres et période

Par défaut, la mise en correspondance récapitule toutes les données disponibles pour la période choisie. Toutefois, vous pouvez filtrer pour inclure uniquement les noms ou les dépendances d’opérations spécifiques.

* Nom de l’opération : cela inclut les vues de page et les types de demandes côté serveur. Avec cette option, la mise en correspondance affiche l’indicateur de performance clé sur le nœud côté serveur/client pour les opérations sélectionnées uniquement. Elle montre les dépendances appelées dans le contexte de ces opérations spécifiques.
* Nom de base de la dépendance : cela inclut les dépendances côté navigateur AJAX et les dépendances côté serveur. Si vous créez un rapport de télémétrie d’une dépendance personnalisée avec l’API TrackDependency, elle sera également affichée ici. Vous pouvez sélectionner les dépendances à afficher sur la mise en correspondance. Notez qu’à ce stade, cela ne filtrera pas les demandes côté serveur ou les vues de page côté client.


![Définir les filtres](./media/app-insights-app-map/11.png)

 
 
## Enregistrer les filtres

Pour enregistrer les filtres que vous avez appliqués, épinglez la vue filtrée sur un [tableau de bord](app-insights-dashboards.md).


![Épingler au tableau de bord](./media/app-insights-app-map/12.png)
 


## Commentaires

Veuillez [fournir des commentaires via l’option Commentaires du portail](app-insights-get-dev-support.md).


![Image MapLink-1](./media/app-insights-app-map/13.png)

<!---HONumber=AcomDC_0622_2016-->