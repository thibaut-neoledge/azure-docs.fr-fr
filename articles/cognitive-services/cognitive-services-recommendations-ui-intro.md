---
title: "Création d’un modèle avec l’interface utilisateur Recommendations | Microsoft Docs"
description: "Azure Machine Learning Recommendations - Création d’un modèle avec l’interface utilisateur Recommendations"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: b264fe44-f94e-40ae-9754-60ad7d6cfeb9
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 5f98395e2beba671192e50f8e53c6198e1efed29


---
# <a name="building-a-model-with-the-recommendations-ui"></a>Création d’un modèle avec l’interface utilisateur Recommendations
Ce document est un guide pas à pas. Notre objectif est de vous guider à travers les étapes nécessaires pour former un modèle à l’aide de la [l’Interface utilisateur Recommendations](https://recommendations-portal.azurewebsites.net/).
Effectuer cet exercice vous permettra de comprendre le processus de création d’un modèle avant de procéder par programme. Il vous familiarise également avec l’interface utilisateur, ce qui est pratique lorsque vous commencez à utiliser le service.

Cet exercice prend environ 30 minutes.

<a name="Step1"></a>

## <a name="step-1---sign-in-to-the-recommendations-ui"></a>Étape 1 - Authentification dans l’interface utilisateur Recommendations
1. Si vous ne l’avez pas encore fait, vous devez vous [inscrire](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) pour un nouvel abonnement à [l’API Recommendations](https://www.microsoft.com/cognitive-services/en-us/recommendations-api). Vous pouvez vous inscrire pour le service sur**Azure**, à l’adresse [http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) en vous connectant avec votre compte Azure. Des instructions détaillées sur le processus d’inscription sont disponibles dans la *Tâche 1* et le [Guide de prise en main](cognitive-services-recommendations-quick-start.md) 
2. Une fois que vous avez obtenu une **clé** pour votre abonnement à l’API Recommendations, consultez la page [Interface utilisateur de Recommendations](https://recommendations-portal.azurewebsites.net/). 
3. Connectez-vous au portail en entrant votre clé dans le champ **Clé du compte**, puis cliquez sur le bouton **Connexion**.
   
    ![Interface utilisateur Recommendations : boîte de dialogue de connexion.][reco_signin]

<a name="Step2"></a>

## <a name="step-2---lets-gather-some-training-data"></a>Étape 2 : Collecte de données d’apprentissage
Avant de pouvoir créer une build, le moteur a besoin de deux informations : un fichier de catalogue et un ensemble de fichiers d’utilisation. 

Le fichier de catalogue contient des informations sur les articles que vous proposez à votre client. Un fichier d’utilisation contient des informations sur l’utilisation des articles ou sur les transactions réalisées auprès de votre entreprise.

Généralement, vous récupérez ces éléments d'information en interrogeant votre base de données de stockage. Aujourd'hui, nous vous proposons des exemples de données pour que vous puissiez apprendre à utiliser l’API Recommendations.

Vous pouvez télécharger les données à partir de [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData). Copiez et décompressez le fichier **Books.Zip** dans un dossier sur votre ordinateur local. Par exemple, **c:\data**.

Vous trouverez des informations détaillées sur le schéma du catalogue et les fichiers d’utilisation dans l’article [Collecte de données pour la formation d’un modèle](cognitive-services-recommendations-collecting-data.md).

Pour cet exercice, nous allons travailler avec un petit fichier afin que vous n’ayez pas à attendre trop longtemps pour la formation. Si vous souhaitez essayer un fichier plus réaliste, nous avons également inclus **MsStoreData.zip**, qui contient des exemples de transactions auprès de Microsoft Store, au [même emplacement](http://aka.ms/RecoSampleData).

<a name="Step3"></a>

## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>Étape 3 : création d’un projet et téléchargement du catalogue et des données d’utilisation
Après connexion à la [l’Interface utilisateur Recommendations](https://recommendations-portal.azurewebsites.net/), vous voyez la page Projets. Si vous avez déjà créé des projets, vous devriez les voir ici.
Un projet (également appelé *modèle* dans la référence de l’API) est un conteneur pour votre catalogue et vos données d’utilisation. Vous pouvez créer plusieurs *builds*dans le projet. Nous vous indiquerons la procédure à suivre lors des prochaines étapes.

1. Pour créer un nouveau projet, saisissez le nom dans la zone de texte (par exemple « MonPremierModèle » serait valide) et cliquez sur **Ajouter un projet**.
   
    ![Interface utilisateur Recommendations : Page Projets.][reco_projects]
2. Une fois le projet créé, cliquez sur le bouton **Rechercher le fichier** dans la section **Ajouter un fichier de catalogue**. 
   Téléchargez le catalogue que vous avez obtenu à l’étape 2. Si vous l’avez enregistré à l’adresse *c:\data*, vous devez accéder à ce dossier.
   
     ![Interface utilisateur Recommendations : Ajout de données à un projet.][reco_firstmodel]
3. Une fois le catalogue téléchargé, cliquez sur le bouton **Rechercher le fichier** dans la section **Ajouter des fichiers d’utilisation**. Ajoutez le fichier usage_large.txt.

> **Que se passe-t-il si j’utilise un grand fichier de données d’utilisation ?**
> 
> Seuls les fichiers d’utilisation inférieurs à 200 Mo peuvent être téléchargés. Cela dit, le système peut contenir jusqu'à 2 Go de données de transaction, vous pouvez donc télécharger plusieurs fichiers si nécessaire.
> Vous n’aurez peut-être pas besoin de tant de données pour générer un bon modèle. Ce n’est pas seulement la taille des données qui est importante, mais aussi la qualité de ces dernières. Il est courant de voir des données d’utilisation où la plupart des transactions concernent essentiellement un certain nombre d’articles populaires, au détriment des autres.
> 
> 

<a name="Step4"></a>

## <a name="step-4---lets-do-some-training"></a>Étape 4 : passage à la formation
Maintenant que vous avez téléchargé le catalogue et les données d’utilisation, nous sommes prêts à former le moteur afin qu’il puisse apprendre des modèles à partir de nos données.

1. Cliquez sur le bouton **Nouvelle build**.
2. Sélectionnez **Recommandations** en tant que type de build. Notez que nous prenons également en charge les types de build Classement et Fréquemment achetés ensemble.
   
   ![Interface utilisateur Recommendations : boîte de dialogue de build.][reco_build_dialog.png]

    Une build FBT vous permet d’identifier des modèles pour les produits qui sont généralement achetés/consommées dans la même transaction.
    Une build de classement est utilisée pour identifier les fonctionnalités intéressantes. 
    Nous n’allons pas étudier les builds Classement et Fréquemment achetés ensemble dans cet atelier, mais si le sujet vous intéresse vous pouvez consulter la [Page de documentation sur les types de build et la qualité des modèles](cognitive-services-recommendations-buildtypes.md).

1. Cliquez sur le bouton **Créer**. Le processus de création prend environ cinq minutes si vous utilisez les données de Books. Les jeux de données plus volumineux demandent plus de temps.

<a name="Step5"></a>

## <a name="step-5---lets-find-out-what-the-machine-learned"></a>Étape 5 - analyse de ce que la machine a appris
Une fois la build terminée, vous remarquerez une nouvelle build dans la liste des builds. Cette build peut être interrogée pour obtenir des recommandations d’articles et d’utilisateurs.

1. Une fois la build terminée, cliquez sur **Score**. Cela vous permet de jouer avec le modèle et de voir les articles recommandés.
   
    ![Interface utilisateur Recommendations : bouton Score][reco_score_button]
2. Sélectionnez un article pour voir les articles renvoyés comme recommandations pour cet article. Notez que s’il n’y a pas suffisamment de transactions pour prédire une recommandation pour un article particulier, le système ne renverra pas de recommandation pour cet article.  Si pour une raison quelconque, vous avez un article qui ne renvoie aucune recommandation, essayez d’évaluer d’autres articles.

<a name="Step6"></a>

## <a name="step-6---next-steps"></a>Étape 6 - Étapes suivantes
Félicitations ! vous avez formé un modèle, puis obtenu des recommandations à partir de ce modèle.  L’Interface utilisateur Recommendations est un outil utile qui vous permet de visualiser l’état de vos projets et builds. 

Maintenant que vous disposez d’un modèle, vous souhaitez peut-être savoir comment effectuer toutes les étapes ci-dessus par programme. Afin d’apprendre à appeler l’API par programme, nous vous invitons à consulter la [Référence de l’API Recommendations](http://go.microsoft.com/fwlink/?LinkId=759348) et à télécharger [l’Exemple d’application Recommendations](http://go.microsoft.com/fwlink/?LinkID=759344).

[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png



<!--HONumber=Feb17_HO2-->


