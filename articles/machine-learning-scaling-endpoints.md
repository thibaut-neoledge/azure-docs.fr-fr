<properties 
	pageTitle="Mise à l’échelle des points de terminaison des API" 
	description="Mise à l’échelle de points de terminaison de service Web dans Microsoft Azure Machine Learning." 
	services="machine-learning" 
	authors="hiteshmadan" 
	manager="padou" 
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="02/19/2015"
	ms.author="himad"/>


# Mise à l’échelle des points de terminaison des API

Les points de terminaison de service Web dans Azure Machine Learning ont des niveaux de limitation sélectionnables afin de correspondre à la vitesse à laquelle le point de terminaison est utilisé.

Il existe deux facteurs qui déterminent le niveau de limitation imposé à un point de terminaison : niveau de limitation : faible ou élevé. Seuls les clients qui paient sont autorisés à définir le niveau de limitation élevé. Nombre maximal d’appels simultanés : 4 pour la limitation de niveau faible ; 20 à 200 pour le niveau de limitation élevé


Les API synchrones sont généralement utilisées dans les situations où vous souhaitez une latence faible. La latence implique ici le temps nécessaire à l’API pour compléter une demande et ne prend pas en compte les retards de réseau. Supposons que vous avez une API avec une latence de 50 millisecondes. Pour utiliser toute la capacité disponible avec le niveau de limitation élevé et le nombre maximal d’appels simultanés = 20, vous devez appeler cette API 20 \* 1000 / 50 = 400 fois par seconde. De même, un nombre maximum d’appels simultanés de 200 vous permettra d’appeler l’API 4000 fois par seconde, en supposant une latence de 50 millisecondes.

Si vous envisagez d’appeler l’API avec une charge supérieure à ce que le nombre maximal d’appels simultanés de 200 prend en charge, vous devez créer plusieurs points de terminaison sur le même service Web et répartir la charge aléatoirement sur ces derniers.

N’oubliez pas que l’utilisation d’un nombre très élevé d’accès concurrentiel peut être préjudiciable si vous n’utilisez pas l’API avec un taux tout aussi élevé. Vous pouvez constater des délais d’attente et/ou des pics de latence sporadiques si vous placez une charge relativement faible sur une API configurée pour une charge élevée.

Notez que l’ajustement des paramètres de limitation n’a d’impact que sur le comportement de l’API synchrone. Vous devez ajuster ces paramètres si vous obtenez fréquemment des réponses 503 - Service non disponible sur l’API synchrone.

L’interface utilisateur de gestion permet de basculer le niveau de limitation. Pour avoir un nombre d’accès concurrentiel personnalisé à associer au niveau de limitation élevé, utilisez l’API Patch Endpoint.

- Ouvrez manage.windowsazure.com
- Accédez à l’onglet Machine Learning
- Cliquez sur votre espace de travail.
- Accédez au service Web avec votre point de terminaison ![Accédez au service Web](./media/machine-learning-scaling-endpoints/figure-1.png)

- Cliquez sur le point de terminaison, puis sur l’onglet Configurer ![Accédez à la configuration du point de terminaison](./media/machine-learning-scaling-endpoints/figure-2.png)


- Passez le niveau de limitation à élevé et cliquez sur Enregistrer.



<!--HONumber=54-->