<properties 
	pageTitle="Surveillance d’un hôte Docker dans Application Insights" 
	description="Vous pouvez visualiser les compteurs de performances, les événements et les exceptions de l’hôte Docker dans Application Insights, avec les données de télémétrie des applications en conteneur." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="awills"/>
 
# Surveillance d’un hôte Docker dans Application Insights

Les compteurs de performances et les rapports d’exception fournis par un hôte [Docker](https://www.docker.com/) peuvent être représentés dans Application Insights. Installez l’application [Application Insights](app-insights-overview.md) dans un conteneur de votre hôte pour afficher les compteurs de performances globaux de l’hôte, ainsi que d’autres images.

Dans le cas d’un hôte Docker, vous distribuez vos applications dans des conteneurs légers avec toutes les dépendances. Elles s’exécuteront sur n’importe quelle machine hôte exécutant un moteur Docker.

Application Insights peut analyser les performances et l’activité de votre hôte Docker et de ses conteneurs.

![exemple](./media/app-insights-docker/00.png)

(Et bien sûr, si vous possédez la source des applications des conteneurs, [installez le Kit de développement logiciel (SDK) Application Insights](app-insights-java-live) sur chacun d’eux pour bénéficier de la journalisation détaillée des performances et du suivi de l’utilisation.)

## Création d’une ressource Application Insights dans Azure

Les ressources Application Insights correspondent à l’emplacement où vous allez afficher et analyser vos données. Elles sont hébergées sur le portail Microsoft Azure.

1.	Vous devrez vous abonner à [Microsoft Azure](https://azure.com). (Une option de paiement à l’utilisation est disponible : vous ne payez rien si vous n’utilisez pas les services et vous pouvez utiliser le niveau gratuit d’Application Insights.)
2.	Connectez-vous au [portail Azure](https://portal.azure.com) et créez une ressource Application Insights. Comme type d’application, choisissez Autre. (Ce choix affecte uniquement la sélection initiale des graphiques que vous voyez. Il ne revêt donc pas une très grande importance.)

    ![exemple](./media/app-insights-docker/01-new.png)
3.	Vous pouvez toujours accéder à votre nouvelle ressource de surveillance en utilisant l’option Parcourir, Tous. Mais vous avez probablement choisi d’intégrer une vignette correspondant à votre application sur la page d’accueil du tableau de bord qui s’affiche lorsque vous accédez au portail.

    Ouvrez maintenant la nouvelle ressource.
4.	Ajoutez la vignette Docker : choisissez **Modifier**, puis **Ajouter des vignettes**, et faites glisser la vignette Docker à partir de la galerie. Dans un premier temps, seuls s’afficheront des graphiques vides.

    ![exemple](./media/app-insights-docker/03.png)

5. Cliquez sur la liste déroulante **Essentials** et sélectionnez la clé d’instrumentation. Vous l’utiliserez pour indiquer au Kit de développement logiciel (SDK) où envoyer ses données de télémétrie.

Ne fermez pas cette fenêtre de navigateur : vous y reviendrez ultérieurement pour consulter vos données de télémétrie.


## Installation d’Application Insights sur votre hôte
 
Maintenant que vous avez défini un emplacement pour l’affichage des données de télémétrie, vous pouvez configurer l’application qui les recueillera et les enverra. 1. Connectez-vous à votre hôte Docker. 2. Recherchez l’image de Microsoft Application Insights sur Docker Hub et faites-la glisser vers votre hôte. 3. Définissez la clé d’instrumentation :

    docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444

4. Exécutez l’image.

## Affichage de vos données de télémétrie

Revenez à votre ressource Application Insights dans le portail Azure.

Vous verrez bientôt des données arriver à partir du conteneur Docker, en particulier si d’autres conteneurs sont en cours d’exécution sur votre moteur Docker.

Cliquez sur les graphiques pour plus de détails.

Voici quelques-uns des affichages que vous pouvez voir.

### Compteurs de performances par hôte, activité par image


![exemple](./media/app-insights-docker/10.png)

Cliquez sur le nom d’une image pour plus de détails. Pour personnaliser l’affichage, cliquez sur un graphique ou sur l’en-tête de la grille, ou utilisez l’option Ajouter un graphique.

[En savoir plus sur Metrics Explorer](app-insights-metrics-explorer.md).

## Événements individuels


![exemple](./media/app-insights-docker/12.png)

Pour examiner les événements individuels, cliquez sur [Rechercher](app-insights-diagnostic-search.md). Utilisez le système de recherche et de filtre pour rechercher des événements particuliers. Cliquez sur un événement pour obtenir plus de détails.
 
## Exceptions par nom de conteneur
 

![exemple](./media/app-insights-docker/14.png)



## Questions et réponses

*Quelles sont les fonctionnalités d’Application Insights qui ne sont pas disponibles dans Docker ?*

* Analyse détaillée des compteurs de performances par conteneur et par image.
* Intégration des données de conteneur et d’application dans un tableau de bord.
* [Exportation des données de télémétrie](app-insights-export-telemetry.md) pour approfondir l’analyse vers une base de données, Power BI ou un autre tableau de bord.

*Comment obtenir des données de télémétrie à partir de l’application elle-même ?*

* Installez le Kit de développement logiciel (SDK) Application Insights dans l’application. Découvrez comment faire pour : les [applications web Java](app-insights-java-get-started.md), les [applications web Windows](app-insights-asp-net.md).

<!---HONumber=AcomDC_1125_2015-->