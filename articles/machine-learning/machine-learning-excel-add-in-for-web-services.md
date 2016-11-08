---
title: Complément Excel pour les services web Machine Learning | Microsoft Docs
description: Comment utiliser les services web Azure Machine Learning directement dans Excel sans écrire de code.
services: machine-learning
documentationcenter: ''
author: tedway
manager: jhubbard
editor: cgronlun
tags: ''

ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/05/2016
ms.author: tedway;garye

---
# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Complément Excel pour les services web Machine Learning
Excel permet d’appeler facilement des services web, directement sans écrire du code.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Procédure d’utilisation d’un service web existant dans un classeur
1. Ouvrez l’ [exemple de fichier Excel](http://aka.ms/amlexcel-sample-2), qui contient le complément Excel et les données concernant les passagers sur le Titanic.
2. Choisissez le service web en cliquant dessus. Dans cet exemple, nous allosn utiliser « Titanic Survivor Predictor (Excel Add-in Sample) [Score] ».
   
    ![Sélectionner un service web][01]
3. Vous accéderez alors à la section **Prédire** .  Ce classeur contient déjà des exemples de données mais, pour un classeur vide, vous pouvez également sélectionner une cellule dans Excel et cliquer sur **Utiliser les exemples de données**.
4. Sélectionnez les données avec les en-têtes et cliquez sur l'icône de plage de données d'entrée.  Assurez-vous que la case « Mes données ont des en-têtes » est activée.
5. Sous **Sortie**, entrez le numéro de la cellule dans laquelle vous souhaitez insérer le résultat, en l’occurrence, « H1 ».
6. Cliquez sur **Prédire**.
   
    ![Section Prédire][02]

## <a name="steps-to-add-a-new-web-service"></a>Procédure d’ajout d’un nouveau service web
Déployez un service web ou utilisez un service existant. Pour plus d’informations sur le déploiement d’un service web, voir [Étape 5 du didacticiel pas à pas : Déploiement du service web Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md).

Obtenez la clé API de votre service web. L’emplacement à partir duquel vous l’obtenez diffère selon que vous avez publié un service web Machine Learning classique ou un nouveau service web Machine Learning.

**Utiliser un service web classique** 

1. Dans Machine Learning Studio, cliquez sur la section **SERVICES WEB** dans le volet de gauche, puis sélectionnez le service web.
   
    ![Studio - Sélectionner un service web][04]
2. Copiez la clé API du service web.
   
    ![Studio clé API][05]
3. Sous l’onglet **TABLEAU DE BORD** pour le service web, cliquez sur le lien **DEMANDE-RÉPONSE**.
4. Recherchez la section **URI de requête** .  Copiez et enregistrez l’URL.

> [!NOTE]
> Il est désormais d’obtenir la clé API d’un service web Machine Learning classique via le portail [Services web Azure Machine Learning](https://services.azureml.net).
> 
> 

**Utiliser un nouveau service web**

1. Dans le portail [Services web Azure Machine Learning](https://services.azureml.net), cliquez sur **Services web**, puis sélectionnez votre service web. 
2. Cliquez sur **Consommer**.
3. Recherchez la section **des informations de base sur la consommation** . Copiez et enregistrez la **Clé primaire** et l’URL de **Demande-réponse**.

## <a name="steps-to-add-a-new-web-service"></a>Procédure d’ajout d’un nouveau service web
1. Déployez un service web ou utilisez un service existant. Pour plus d’informations sur le déploiement d’un service web, voir [Étape 5 du didacticiel pas à pas : Déploiement du service web Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md).
2. Cliquez sur **Consommer**.
3. Recherchez la section **des informations de base sur la consommation** . Copiez et enregistrez la **Clé primaire** et l’URL de **Demande-réponse**.
4. Dans Excel, accédez à la section **Services web** (si vous vous trouvez dans la section **Prédire**, cliquez sur la flèche Précédent pour accéder à la liste des services web).
   
    ![Accéder à la sélection du service web][03]
5. Cliquez sur **Ajouter un service web**.
6. Collez l’URL dans la zone de texte du complément Excel intitulée **URL**.
7. Collez l’API/Clé primaire dans la zone de texte intitulée **Clé API**.
8. Cliquez sur **Add**.
   
    ![URL et clé API pour un service web classique.][06]
9. Pour utiliser le service web, suivez les instructions de la section « Procédure d’utilisation d’un service web existant » ci-dessus.

## <a name="sharing-your-workbook"></a>Partage de votre classeur
Si vous enregistrez votre classeur, la clé API/primaire pour les services web que vous avez ajoutés est également enregistrée. Cela signifie que vous devez uniquement partager le classeur avec des personnes de confiance.

Posez les questions que vous voulez dans la section de commentaire suivante ou dans notre [forum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png



<!--HONumber=Oct16_HO2-->


