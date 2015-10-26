<properties
   pageTitle="Documentation d'aide de zones géographiques multiples | Microsoft Azure"
   description="Apprenez à créer un espace de travail et à publier un service web dans une région Azure autre que South Central United States (SCUS)."
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="paulettm"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2015"
   ms.author="tedway; neerajkh"/>

# Documentation d'aide de zones géographiques multiples

Actuellement, toutes les ressources de formation de Machine Azure sont hébergées dans la région Azure South Central United States (SCUS). Cet article explique comment vous pouvez créer un espace de travail et publier un service web dans d'autres régions Azure.

## Création d'un espace de travail

1. Connectez-vous au portail de gestion Azure.

2.  Cliquez sur **+ Nouveau** > **SERVICES DE DONNÉES** > **MACHINE LEARNING** > **CRÉATION RAPIDE**. Sous **Emplacement**, sélectionner une autre région, par exemple, l'**Asie du Sud-est**. ![Aide zone géographique multiple image 1][1]
3. Sélectionnez l'espace de travail, puis cliquez sur **Se connecter à ML Studio**. ![Aide zone géographique multiple image 2][2]

4. Vous disposez désormais d'un espace de travail dans une autre région que vous pouvez utiliser comme tout autre espace de travail. Pour basculer d'un espace de travail à l'autre, recherchez le coin supérieur droit de votre écran. Cliquez sur la liste déroulante, sélectionnez la région, puis sélectionnez l'espace de travail. Tout est lié à la région dans laquelle l'espace de travail se trouve. Par exemple, tous les services web créés à partir d'un espace de travail seront dans la même région que l'espace de travail. ![Aide zone géographique multiple image 3][3]

## Ouvrir une expérience à partir de la galerie

Si vous ouvrez une expérience à partir de la galerie, vous pouvez également sélectionner la région dans laquelle vous souhaitez copier l'expérience.

![Aide zone géographique multiple image 4][4a]

## Gestion des services web

Pour gérer par programme les services web, par exemple pour le recyclage, utilisez l'adresse spécifique à la région : ****https://asiasoutheast.management.azureml.net**

### Points à noter

1.	Vous pouvez uniquement copier des expériences entre espaces de travail appartenant à la même région. À l'avenir, nous allons activer des expériences de copie entre les espaces de travail dans plusieurs régions.
2.	Le sélecteur de région affiche uniquement les espaces de travail d'une seule région à la fois. À l'avenir, vous serez en mesure d'afficher une liste complète des espaces de travail auxquels vous avez accès dans toutes les régions simultanément.  
3.	Un accès à espace libre ou invité (anonyme) est créé et hébergé dans la région South Central U.S. À l'avenir, vous serez en mesure de créer des espaces de travail avec accès gratuit/invité dans la région que vous choisissez.  
4.	Les services web déployés à partir d'un espace de travail en Asie du Sud-est sont également hébergés en Asie du Sud-est. À l'avenir, vous aurez la latitude de créer des expériences dans une région, et de déployer des points de terminaison de service web générés dans différentes régions.  

## Plus d’informations

Poser une question sur le [forum Azure Machine Learning](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png

<!---HONumber=Oct15_HO3-->