---
title: "Documentation d&quot;aide de zones géographiques multiples | Microsoft Docs"
description: "Apprenez à créer un espace de travail et à publier un service web dans une région Azure autre que South Central United States (SCUS)."
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: rmca14
tags: 
ms.assetid: ed0ca8a8-fa53-4e56-b824-2d7e44641967
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: tedway; neerajkh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b95d7d7b11af4dcd3ed814f31697f5d1b617f64b


---
# <a name="multi-geo-help-documentation"></a>Documentation d'aide de zones géographiques multiples
Cet article explique comment vous pouvez créer un espace de travail et publier un service web dans d'autres régions Azure.

## <a name="create-a-workspace"></a>Créer un espace de travail
1. Connectez-vous au portail Azure Classic.
2. Cliquez sur **+NOUVEAU** > **SERVICES DE DONNÉES** > **MACHINE LEARNING** > **CRÉATION RAPIDE**.  Sous **EMPLACEMENT**, sélectionner une autre région, par exemple, l'**Asie du Sud-est**.
   ![Aide zone géographique multiple image 1][1]
3. Sélectionnez l'espace de travail, puis cliquez sur **Se connecter à ML Studio**.
   ![Aide zone géographique multiple image 2][2]
4. Vous disposez désormais d'un espace de travail dans une autre région que vous pouvez utiliser comme tout autre espace de travail. Pour basculer d'un espace de travail à l'autre, recherchez le coin supérieur droit de votre écran. Cliquez sur la liste déroulante, sélectionnez la région, puis sélectionnez l'espace de travail. Tout est lié à la région dans laquelle l'espace de travail se trouve. Par exemple, tous les services web créés à partir d'un espace de travail seront dans la même région que l'espace de travail.
   ![Aide zone géographique multiple image 3][3]

## <a name="open-an-experiment-from-gallery"></a>Ouvrir une expérience à partir de la galerie
Si vous ouvrez une expérience à partir de la galerie, vous pouvez également sélectionner la région dans laquelle vous souhaitez copier l'expérience.

![Aide zone géographique multiple image 4][4a]

## <a name="web-service-management"></a>Gestion des services web
Pour gérer par programmation les services web, par exemple pour la reformation, utilisez l’adresse propre à la région :

* https://asiasoutheast.management.azureml.net
* https://europewest.management.azureml.net

### <a name="things-to-note"></a>Points à noter
1. Vous pouvez uniquement copier des expériences entre espaces de travail appartenant à la même région de cette façon. Si vous devez copier l’expérience des espaces de travail dans différentes régions, vous pouvez utiliser l’applet de commande [PowerShell](http://aka.ms/amlps) [*Copy-AmlExperiment*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) pour le faire. Une autre solution de contournement consiste à publier l’expérience dans la galerie en mode non répertorié, puis à l’ouvrir dans l’espace de travail à partir de l’autre région.
2. Le sélecteur de région affiche uniquement les espaces de travail d'une seule région à la fois. À l'avenir, vous serez en mesure d'afficher une liste complète des espaces de travail auxquels vous avez accès dans toutes les régions simultanément.  
3. Un accès à espace libre ou invité (anonyme) est créé et hébergé dans la région South Central U.S. À l'avenir, vous serez en mesure de créer des espaces de travail avec accès gratuit/invité dans la région que vous choisissez.  
4. Les services web déployés à partir d'un espace de travail en Asie du Sud-est sont également  hébergés en Asie du Sud-est. À l'avenir, vous aurez la latitude de créer des expériences dans une région, et de déployer des points de terminaison de service web générés dans différentes régions.  

## <a name="more-information"></a>Plus d’informations
Poser une question sur le [forum Azure Machine Learning](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png



<!--HONumber=Nov16_HO3-->


