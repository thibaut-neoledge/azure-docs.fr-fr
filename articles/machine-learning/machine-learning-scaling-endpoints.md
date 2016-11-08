---
title: Mise à l’échelle des points de terminaison des API | Microsoft Docs
description: Mise à l’échelle de points de terminaison de service Web dans Microsoft Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: hiteshmadan
manager: padou
editor: ''

ms.service: machine-learning
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 05/25/2016
ms.author: himad

---
# Mise à l’échelle des points de terminaison des API
Les points de terminaison de service Web dans Azure Machine Learning ont des niveaux de limitation sélectionnables afin de correspondre à la vitesse à laquelle le point de terminaison est utilisé.

Pour contrôler le taux de limitation sur le point de terminaison, utilisez le curseur sur le portail Azure Classic pour définir le nombre maximum d’appels simultanés (de 20 à 200)

Les API synchrones sont généralement utilisées dans les situations où vous souhaitez une latence faible. La latence implique ici le temps nécessaire à l’API pour compléter une demande et ne prend pas en compte les retards de réseau. Supposons que vous avez une API avec une latence de 50 millisecondes. Pour utiliser toute la capacité disponible avec le niveau de limitation élevé et le nombre maximal d’appels simultanés = 20, vous devez appeler cette API 20 * 1000 / 50 = 400 fois par seconde. De même, un nombre maximum d’appels simultanés de 200 vous permettra d’appeler l’API 4000 fois par seconde, en supposant une latence de 50 millisecondes.

Si vous envisagez d’appeler l’API avec une charge supérieure à ce que le nombre maximal d’appels simultanés de 200 prend en charge, vous devez créer plusieurs points de terminaison sur le même service Web et répartir la charge aléatoirement sur ces derniers.

N’oubliez pas que l’utilisation d’un nombre très élevé d’accès concurrentiel peut être préjudiciable si vous n’utilisez pas l’API avec un taux tout aussi élevé. Vous pouvez constater des délais d’attente et/ou des pics de latence sporadiques si vous placez une charge relativement faible sur une API configurée pour une charge élevée.

Notez que l’ajustement des paramètres de limitation n’a d’impact que sur le comportement de l’API synchrone (RRS). Vous devez ajuster ces paramètres si vous obtenez fréquemment des réponses 503 - Service non disponible sur l’API synchrone.

L’interface utilisateur de gestion permet de fournir un nombre d’accès concurrentiel personnalisé pour augmenter le nombre d’accès concurrentiels par défaut (20) du point de terminaison.

1. Ouvrez manage.windowsazure.com
2. Accédez à l’onglet Machine Learning
3. Cliquez sur votre espace de travail.
4. Accédez au service Web avec votre point de terminaison ![Accédez au service Web](./media/machine-learning-scaling-endpoints/figure-1.png)
5. Cliquez sur le point de terminaison, puis sur l’onglet Configurer ![Accédez à la configuration du point de terminaison](./media/machine-learning-scaling-webservice/machlearn-2.png)
6. Déplacez le curseur pour augmenter le niveau de concurrence, puis cliquez sur Enregistrer.

<!---HONumber=AcomDC_0622_2016-->