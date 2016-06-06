<properties
   pageTitle="Mise à l’échelle du service web | Microsoft Azure"
   description="Apprenez à mettre à l’échelle un service web en augmentant l’accès concurrentiel et en ajoutant de nouveaux points de terminaison."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="Azure Machine Learning, services web, opérationalisation, mise à l’échelle, point de terminaison, accès concurrentiel"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="05/16/2016"
   ms.author="neerajkh"/>

# Mise à l’échelle du service web

## Augmentation de l’accès concurrentiel

Par défaut, chaque service web publié est configuré pour prendre en charge 20 requêtes simultanées. Vous pouvez augmenter cet accès concurrentiel jusqu’à 200 requêtes simultanées via le [portail Azure Classic](https://manage.windowsazure.com/), comme illustré dans la figure ci-dessous.

Accédez au [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur l’icône Machine Learning sur la gauche, sélectionnez l’espace de travail utilisé pour la publication du service web, cliquez sur le service web de votre choix, sélectionnez le point de terminaison qui a besoin de l’augmentation de l’accès concurrentiel, puis cliquez sur **CONFIGURER**. Utilisez le curseur pour augmenter l’accès concurrentiel, puis cliquez sur **ENREGISTRER** dans le panneau inférieur.

Pour augmenter l’accès concurrentiel, consultez [Mise à l’échelle des points de terminaison des API](machine-learning-scaling-endpoints.md).

   ![Machine Learning, mise à l’échelle de points de terminaison.][1]

## Ajouter de nouveaux points de terminaison pour le même service web

La mise à l’échelle du service web est une tâche courante pour prendre en charge plus de 200 requêtes simultanées, augmenter la disponibilité par le biais de plusieurs points de terminaison ou fournir un point de terminaison distinct pour un consommateur différent du service web. L’utilisateur peut augmenter l’échelle en ajoutant des points de terminaison supplémentaires pour le même service web. L’utilisateur peut ajouter des points de terminaison supplémentaires par le biais du [portail Azure Classic](https://manage.windowsazure.com/), comme illustré dans la figure ci-dessous :

Accédez au [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur l’icône Machine Learning sur la gauche, sélectionnez l’espace de travail utilisé pour la publication du service web, cliquez sur le service web souhaité, cliquez sur **AJOUTER UN POINT DE TERMINAISON** dans le volet inférieur, puis renseignez le nom, la description et l’accès concurrentiel souhaités pour le nouveau point de terminaison.

Pour ajouter de nouveaux points de terminaison, consultez [Création de points de terminaison](machine-learning-create-endpoint.md).

   ![Machine Learning, ajout de nouveaux points de terminaison.][2]

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=AcomDC_0525_2016-->