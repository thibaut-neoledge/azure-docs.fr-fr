<properties 
	pageTitle="Création de points de terminaison" 
	description="Création de points de terminaison de service web dans Microsoft Azure Machine Learning." 
	services="machine-learning" 
	authors="hiteshmadan" 
	manager="padou" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="02/19/2015"
	ms.author="himad"/>


# Création de points de terminaison

Le logiciel Microsoft Azure Machine Learning vous permet de créer plusieurs points de terminaison pour un service web publié. Chaque point de terminaison fait l'objet de limitations personnalisées et est traité et géré indépendamment des autres points de terminaison de ce service web. Il existe une URL et une clé d'autorisation uniques pour chaque point de terminaison.

Cela permet aux utilisateurs Microsoft Azure Machine Learning de créer des services web, qu'ils peuvent ensuite vendre à terme à leurs clients. Chaque point de terminaison peut être personnalisé et présenter ses propres modèles formés, tout en restant lié à l'expérience ayant permis de créer ce service web. En outre, les mises à jour apportées à l'expérience peuvent être appliquées de façon sélective à un point de terminaison, sans remplacer les personnalisations.

## Étapes de création d'un point de terminaison
- Accédez au site manage.windowsazure.com, puis cliquez sur l'élément Machine Learning, dans la colonne de gauche. Cliquez sur l'espace de travail présentant le service web qui vous intéresse.
![Navigate to workspace](./media/machine-learning-create-endpoint/figure-1.png)


- Cliquez sur l'onglet Services web.
![Navigate to web services](./media/machine-learning-create-endpoint/figure-2.png)


- Cliquez sur le service web qui vous intéresse pour afficher la liste des points de terminaison disponibles.
![Navigate to endpoint](./media/machine-learning-create-endpoint/figure-3.png)


- Cliquez sur le bouton Ajouter un point de terminaison, situé en bas de la page. Saisissez un nom et une description, en vérifiant qu'il n'existe aucun point de terminaison portant le même nom dans ce service web. Conservez le niveau de limitation défini par défaut, sauf si vous avez des exigences particulières.
Pour en savoir plus sur la limitation, visitez la page [Mise à l'échelle de points de terminaison d'API](machine-learning-scaling-endpoints.md).
![Create endpoint](./media/machine-learning-create-endpoint/figure-4.png)


Une fois le point de terminaison créé, vous pouvez l'exploiter via des API synchrones, des API de lot et des feuilles de calcul Microsoft Excel.

<!--HONumber=49--> 