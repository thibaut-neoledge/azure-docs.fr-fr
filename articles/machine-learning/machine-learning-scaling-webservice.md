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
   ms.date="07/06/2016"
   ms.author="neerajkh"/>

# Mise à l’échelle du service web

>[AZURE.NOTE] Cette rubrique décrit les techniques applicables à un service web classique.

Par défaut, chaque service web publié est configuré pour prendre en charge 20 requêtes simultanées, avec un maximum de 200 requêtes. Alors que le portail Azure classique permet de définir cette valeur, Azure Machine Learning optimise automatiquement ce paramètre pour améliorer les performances de votre service web et la valeur de portail est ignorée.

Si vous envisagez d’appeler l’API avec une charge supérieure à ce que le nombre maximal d’appels simultanés de 200 prend en charge, vous devez créer plusieurs points de terminaison sur le même service web et répartir la charge aléatoirement sur ces derniers.

## Ajouter de nouveaux points de terminaison pour le même service web

La mise à l’échelle du service web est une tâche courante pour prendre en charge plus de 200 requêtes simultanées, augmenter la disponibilité par le biais de plusieurs points de terminaison ou fournir un point de terminaison distinct pour un consommateur différent du service web. Vous pouvez augmenter l’échelle en ajoutant des points de terminaison supplémentaires pour le même service web via le [portail Azure classique](https://manage.windowsazure.com/), comme indiqué dans la figure ci-dessous :

N’oubliez pas que l’utilisation d’un nombre très élevé d’accès concurrentiel peut être préjudiciable si vous n’utilisez pas l’API avec un taux tout aussi élevé. Vous pouvez constater des délais d’attente et/ou des pics de latence sporadiques si vous placez une charge relativement faible sur une API configurée pour une charge élevée.

Les API synchrones sont généralement utilisées dans les situations où vous souhaitez une latence faible. La latence implique ici le temps nécessaire à l’API pour compléter une demande et ne prend pas en compte les retards de réseau. Supposons que vous avez une API avec une latence de 50 millisecondes. Pour utiliser toute la capacité disponible avec le niveau de limitation élevé et le nombre maximal d’appels simultanés = 20, vous devez appeler cette API 20 * 1000 / 50 = 400 fois par seconde. De même, un nombre maximum d’appels simultanés de 200 vous permettra d’appeler l’API 4000 fois par seconde, en supposant une latence de 50 millisecondes.

Accédez au [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur l’icône Machine Learning sur la gauche, sélectionnez l’espace de travail utilisé pour la publication du service web, cliquez sur le service web souhaité, cliquez sur **AJOUTER UN POINT DE TERMINAISON** dans le volet inférieur, puis renseignez le nom, la description et l’accès concurrentiel souhaités pour le nouveau point de terminaison.

Pour ajouter de nouveaux points de terminaison, consultez [Création de points de terminaison](machine-learning-create-endpoint.md).

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=AcomDC_0720_2016-->