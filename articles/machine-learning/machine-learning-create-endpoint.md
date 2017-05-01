---
title: "Création de points de terminaison de service web dans Machine Learning | Microsoft Docs"
description: "Création de points de terminaison de service web dans Azure Machine Learning"
services: machine-learning
documentationcenter: 
author: hiteshmadan
manager: padou
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.author: himad
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 700761e24565310df0792a209ce6e41699f3d0e8
ms.lasthandoff: 03/31/2017


---
# <a name="creating-endpoints"></a>Création de points de terminaison
> [!NOTE]
>  Cette rubrique décrit les techniques applicables à un service web Machine Learning **classique**.
> 
> 

Lorsque vous créez des services web que vous vendez à vos clients, vous devez fournir à ceux-ci des modèles formés qui restent liés à l’expérience à partir de laquelle le service web a été créé. En outre, des mises à jour de l’expérience peuvent être appliquées de façon sélective à un point de terminaison, sans remplacer les personnalisations.

À cette fin, le Azure Machine Learning vous permet de créer plusieurs points de terminaison pour un service web déployé. Chaque point de terminaison du service web est adressé, limité et géré de façon indépendante. Chaque point de terminaison est une URL unique, et la clé d’autorisation que vous pouvez distribuer à vos clients.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Ajout de points de terminaison à un service web
Il existe trois façons d’ajouter un point de terminaison à un service web.

* Par programmation
* Via le portail des services web Azure Machine Learning
* Via le portail Azure Classic

Une fois le point de terminaison créé, vous pouvez l’exploiter via des API synchrones, des API de lot et des feuilles de calcul Microsoft Excel. Outre l'ajout de points de terminaison via cette interface utilisateur, vous pouvez également utiliser les API de gestion de point de terminaison pour ajouter de manière programmée des points de terminaison.

> [!NOTE]
> Si vous avez ajouté des points de terminaison au service web, vous ne pouvez pas supprimer le point de terminaison par défaut.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>Ajout d’un point de terminaison par programme
Vous pouvez ajouter un point de terminaison à votre service web par programme en utilisant l’exemple de code [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Ajout d’un point de terminaison à l’aide du portail des services web Azure Machine Learning
1. Dans Machine Learning Studio, dans la colonne de navigation de gauche, cliquez sur Services web.
2. En bas du tableau de bord du service web, cliquez sur **Gérer les points de terminaison**. Le portail des services web Azure Machine Learning s’ouvre sur la page des points de terminaison pour le service web.
3. Cliquez sur **Nouveau**.
4. Tapez un nom et une description pour le point de terminaison. Les noms de point de terminaison doivent compter au maximum 24 caractères, et doivent être composés de lettres minuscules ou de chiffres. Sélectionnez le niveau de journalisation et activez les exemples de données si nécessaire. Pour plus d’informations sur la journalisation, voir [Activation de la journalisation pour les services web Machine Learning](machine-learning-web-services-logging.md).

## <a name="adding-an-endpoint-using-the-azure-classic-portal"></a>Ajout d’un point de terminaison via le portail Azure Classic
1. Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com), puis cliquez sur **Machine Learning** dans la colonne de gauche. Cliquez sur l’espace de travail contenant le service web qui vous intéresse.
   
    ![Accès à l’espace de travail](./media/machine-learning-create-endpoint/figure-1.png)
2. Cliquez sur **Services web**.
   
    ![Accéder aux services web](./media/machine-learning-create-endpoint/figure-2.png)
3. Cliquez sur le service web qui vous intéresse pour afficher la liste des points de terminaison disponibles.
   
    ![Accès au point de terminaison](./media/machine-learning-create-endpoint/figure-3.png)
4. En bas de la page, cliquez sur **Ajouter un point de terminaison**. Tapez un nom et une description, en vérifiant qu’il n’existe aucun point de terminaison du même nom dans ce service web. Conservez le niveau de limitation défini par défaut, sauf si vous avez des exigences particulières. Pour en savoir plus sur la limitation, voir [Mise à l’échelle de points de terminaison des API](machine-learning-scaling-webservice.md).
   
    ![Création d’un point de terminaison](./media/machine-learning-create-endpoint/figure-4.png)

## <a name="next-steps"></a>Étapes suivantes
[Utilisation d’un service web Azure Machine Learning](machine-learning-consume-web-services.md).


