---
title: "Azure Stream Analytics et intégration de Machine Learning | Microsoft Docs"
description: "Comment utiliser une fonction définie par l’utilisateur et Machine Learning dans un travail Stream Analytics"
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/14/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: d1ffb9aba0eb1e17d1efd913f536f6f3997fccbb
ms.openlocfilehash: 7b635b1810536f5b3eb1371d687e9c355e604e41

---

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Analyse du sentiment à l'aide d'Azure Stream Analytics et Azure Machine Learning
Cet article est conçu pour vous aider à configurer rapidement un travail Azure Stream Analytics simple avec l’intégration d’Azure Machine Learning. Nous mettrons à profit un modèle d’analyse de sentiment Machine Learning de la galerie Cortana Intelligence pour analyser la diffusion des données de texte et déterminer l’évaluation du sentiment en temps réel. Les informations de cet article peuvent vous aider à comprendre les scénarios tels que l’analyse de sentiments en temps réel de la diffusion en continu des données Twitter, l’analyse des enregistrements de conversation client grâce au personnel du support technique, l’évaluation des commentaires sur les forums, blogs et vidéos, sans oublier les nombreux autres scénarios d’évaluation prédictive en temps réel.

Cet article propose un exemple de fichier CSV avec le texte comme entrée dans le stockage d’objets Blob Azure, illustré dans l’image suivante. La tâche applique le modèle d’analyse de sentiments en tant que fonction définie par l’utilisateur (UDF) sur l’exemple de données de texte à partir du magasin d’objets blob. Le résultat final est placé dans le même magasin d’objets blob, dans un autre fichier CSV. 

![Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

L’image suivante illustre cette configuration. Pour un scénario plus réaliste, vous pouvez remplacer le stockage d’objets blob par la diffusion de données Twitter en continu à partir d’une entrée Event Hub Azure. Vous pouvez également créer une visualisation [Microsoft Power BI](https://powerbi.microsoft.com/) en temps réel agrégée du sentiment.    

![Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Composants requis
Voici la configuration requise pour exécuter les tâches présentées dans cet article :

* Un abonnement Azure actif.
* Un fichier CSV contenant des données. Vous pouvez télécharger le fichier indiqué dans la Figure 1 à partir de [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv), ou créer votre propre fichier. Pour cet article, nous partons du principe que vous utilisez celui proposé en téléchargement sur GitHub.

À un niveau élevé, pour effectuer les tâches décrites dans cet article, vous devez procédez comme suit :

1. Téléchargez un fichier d’entrée CSV vers le stockage d’objets blob Azure.
2. Ajoutez un modèle d’analyse de sentiments à partir de la galerie Cortana Intelligence à votre espace de travail Azure Machine Learning.
3. Déployez ce modèle en tant que service web dans l’espace de travail Machine Learning.
4. Créez un travail Stream Analytics qui appelle ce service web en tant que fonction afin de déterminer le sentiment de saisie de texte.
5. Démarrez le travail Stream Analytics et examen du résultat.

## <a name="create-a-storage-blob-and-upload-the-csv-input-file"></a>Créer un objet blob de stockage et télécharger le fichier d’entrée CSV
Pour cette étape, vous pouvez utiliser n’importe quel fichier CSV, comme celui disponible sur GitHub. Il est très facile de télécharger le fichier csv, car il s’agit d’une option incluse dans la création d’un objet blob de stockage.

Pour notre didacticiel, créez un compte de stockage en cliquant sur **Nouveau**, puis en recherchant « compte de stockage » et en sélectionnant l’icône ainsi obtenue pour le compte de stockage. Fournissez les informations requises pour la création du compte. Indiquez un **nom** (azuresamldemosa dans mon exemple), créez un **groupe de ressources** ou utilisez un groupe existant, et spécifiez un **emplacement** (il est important que toutes les ressources créées dans cette démonstration utilisent si possible le même emplacement).

![créer un compte de stockage](./media/stream-analytics-machine-learning-integration-tutorial/create-sa.png)

Une fois cette opération terminée, vous pouvez cliquer sur le service Blob et créer un conteneur d’objets blob.

![créer un conteneur d’objets blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

Indiquez ensuite un **nom** pour le conteneur (azuresamldemoblob dans mon exemple) et vérifiez que le **type d’accès** est défini sur 'blob'.

![créer un type d’accès blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

Nous pouvons désormais remplir l’objet blob avec nos données. Sélectionnez **Fichiers**, puis sélectionnez le fichier sur le lecteur local que vous avez téléchargé à partir de GitHub. J’ai sélectionné Objet blob de blocs et une taille de 4 Mo, qui devrait être adaptée pour cette démonstration. Sélectionnez ensuite **Télécharger** pour que le portail crée un objet blob avec l’exemple de texte.

![créer un fichier de téléchargement d’objets blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa4.png)

Maintenant que les exemples de données sont dans un objet blob, il est temps d’activer le modèle d’analyse des sentiments dans la galerie Cortana Intelligence.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Ajout du modèle d’analyse de sentiments de la galerie Cortana Intelligence
1. Téléchargez le [modèle d’analyse prédictive de sentiment](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) à partir de la galerie Cortana Intelligence.  
2. Dans Machine Learning Studio, sélectionnez **Ouvrir dans Studio**.  
   
   ![Stream Analytics Machine Learning, ouvrir Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Connectez-vous pour accéder à l’espace de travail. Sélectionnez le site qui convient le mieux à votre site.
4. Cliquez sur **Exécuter** en bas de la page.  
5. Une fois le processus correctement exécuté, sélectionnez **Déployer le service web**.
6. Le modèle d’analyse de sentiments est prêt à être utilisé. Pour valider, sélectionnez le bouton **Test** et saisissez un texte d’entrée comme « J’aime Microsoft ». Le test doit retourner un résultat similaire à ce qui suit :

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![Stream Analytics Machine Learning, données d’analyse](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

Dans la colonne **Applications**, sélectionnez le lien vers le classeur **Excel 2010 ou version antérieure** afin d’obtenir votre clé API et l’URL dont vous aurez besoin plus tard pour configurer le travail Stream Analytics. (Cette étape est obligatoire uniquement pour utiliser un modèle Machine Learning à partir d’un autre espace de travail de compte Azure. Cet article suppose que c’est le cas pour les besoins de ce scénario.)  

Notez l’URL et la clé d’accès du service web depuis le fichier Excel téléchargé comme indiqué ci-dessous :  

![Stream Analytics Machine Learning, aperçu rapide](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Créer une tâche d’analyse Stream Analytics qui utilise le modèle Machine Learning
1. Accédez au [portail Azure](https://portal.azure.com).  
2. Cliquez sur **Nouveau** > **Intelligence + analyse** > **Stream Analytics**. Saisissez un nom pour votre travail dans **Nom du travail**, spécifiez un groupe de ressources existant ou créez-en un nouveau, et entrez l’emplacement approprié pour le travail dans le champ **Emplacement**.    
   
   ![créer un travail](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   
3. Une fois le travail créé, dans l’onglet **Entrées**, sélectionnez **Ajouter une entrée**.  
   
   ![Stream Analytics Machine Learning, ajouter une saisie Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input.png)  

4. Sélectionnez **Ajouter**, puis spécifiez un **alias d’entrée**. Sélectionnez **Flux de données**, **Stockage d’objets blob** comme entrée, puis sélectionnez **Suivant**.  
5. Sur la page **Paramètres du stockage d’objets blob** de l’assistant, fournissez le nom du conteneur d’objets blob du compte de stockage défini précédemment, au moment du téléchargement des données. Cliquez sur **Suivant**. Pour **Format de sérialisation de l’événement**, sélectionnez **CSV**. Acceptez les valeurs par défaut pour le reste de la page **configuration de la sérialisation** . Cliquez sur **OK**.  
   
   ![ajouter un conteneur d’objets blob d’entrée](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input-blob.png)

6. Dans l’onglet **Sorties**, sélectionnez **Ajouter une sortie**.  
   
   ![Stream Analytics Machine Learning, ajouter une sortie](./media/stream-analytics-machine-learning-integration-tutorial/create-output.png)  

7. Cliquez sur **Stockage d’objets blob**, puis entrez les mêmes paramètres, sauf pour le conteneur. La valeur **Entrée** a été configurée pour assurer la lecture à partir du conteneur nommé « test » dans lequel le fichier **CSV** a été téléchargé. Pour **Output**, entrez « testouput ».
8. Vérifiez que les **paramètres de sérialisation** de la sortie sont définis sur **CSV**, puis sélectionnez le bouton **OK**.
   
   ![Stream Analytics Machine Learning, ajouter une sortie](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

9. Dans l’onglet **Fonctions**, sélectionnez **Ajouter une fonction Machine Learning**.  
   
   ![Stream Analytics Machine Learning, ajouter une fonction Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  

10. Sur la page **Paramètres du Service Web Machine Learning** , recherchez l’espace de travail de Machine Learning, le service web et le point de terminaison par défaut. Pour cet article, appliquez les paramètres manuellement pour vous familiariser avec la configuration du service web d’un espace de travail quelconque dont vous connaissez l’URL et avez la clé API. Entrez le point de terminaison **URL** et **Clé API**. Cliquez sur **OK**. Notez que l’**alias de la fonction** est « sentiment ».  
    
    ![Stream Analytics Machine Learning, service web Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/add-function-endpoints.png)    

11. Dans l’onglet **Requête** , modifiez la requête comme suit :    
    
    ```
    WITH sentiment AS (  
      SELECT text, sentiment(text) as result from datainput  
    )  
    
    Select text, result.[Scored Labels]  
    Into testoutput  
    From sentiment  
    ```    

12. Cliquez sur **Enregistrer** pour enregistrer la feuille de calcul.

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>Démarrage du travail Stream Analytics et observation du résultat
1. Cliquez sur **Démarrer** en haut de la page du travail.
2. Dans la **boîte de dialogue Démarrer la requête**, sélectionnez **Heure personnalisée**, puis sélectionnez un jour antérieur au moment où le fichier CSV a été chargé sur le stockage d’objets blob. Cliquez sur **OK**.  
3. Accédez au stockage d’objets blob à l’aide de l’outil que vous avez utilisé pour charger le fichier CSV, par exemple Visual Studio.
4. Quelques minutes après le début du travail, le conteneur de sortie est créé et un fichier CSV téléchargé dans celui-ci.  
5. Ouvrez le fichier dans l’éditeur CSV par défaut. Un écran semblable à celui-ci devrait s’afficher :  
   
   ![Stream Analytics Machine Learning, vue CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>Conclusion
Cet article explique comment créer une tâche Stream Analytics qui lit en continu des données texte et applique en temps réel une analyse des sentiments aux données. Vous pouvez réaliser toutes ces opérations sans avoir à vous soucier des subtilités de la création d’un modèle d’analyse des sentiments. Il s’agit de l’un des avantages de la suite Cortana Intelligence.

Vous pouvez également afficher les mesures liées à la fonction Azure Machine Learning. Pour cela, sélectionnez l’onglet **Surveiller** . Trois fonctions liées aux métriques sont affichées.  

* **Requêtes de fonction** indique le nombre de requêtes adressées au service web Machine Learning.  
* **Événements de fonction** indique le nombre d’événements dans la requête. Par défaut, chaque requête envoyée à un service web Machine Learning contient jusqu'à 1 000 événements.  
  
    ![Stream Analytics Machine Learning, affichage du moniteur Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/job-monitor.png)  




<!--HONumber=Feb17_HO2-->


