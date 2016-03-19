<properties 
	pageTitle="Didacticiel : Analyse du sentiment à l'aide d'Azure Stream Analytics et Azure Machine Learning | Microsoft Azure" 
	description="Tirer parti des fonctions définies par l’utilisateur et de l’apprentissage machine dans les travaux d’analyse de flux"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"
/>

# Didacticiel : Analyse des sentiments à l'aide de Stream Analytics et Machine Learning #

Ce didacticiel est conçu pour vous aider à configurer rapidement un travail Stream Analytics simple avec l’intégration de Machine Learning. Nous allons mettre à profit un modèle d’analyse de sentiment Machine Learning de la galerie Analytique Cortana pour analyser la diffusion des données de texte et détermine l’évaluation du sentiment en temps réel. Il s’agit d’un didacticiel qui permet de comprendre les scénarios tels que l’analyse de sentiments en temps réel de la diffusion en continu des données twitter, l’analyse des enregistrements de conversation client grâce au personnel du support technique, aux commentaires sur les forums/blogs/vidéos et de nombreux autres scénarios d’évaluation prédictive en temps réel.
  
Dans ce didacticiel, un exemple de fichier CSV avec du texte (comme indiqué dans la figure 1 ci-dessous) est fourni en tant qu’entrée dans le magasin d’objets Blob Azure. La tâche applique le modèle d’analyse de sentiments en tant que fonction définie par l’utilisateur (UDF) sur l’exemple de données de texte à partir du magasin d’objets blob. Le résultat final sera placé dans le même magasin d’objets blob Azure, dans un autre fichier CSV. Un schéma de cette configuration est fourni dans la figure 2 ci-dessous). Pour un scénario plus réaliste, cette entrée de magasin d’objets blob peut être remplacée par la diffusion de données twitter en continu à partir d’une entrée Event Hub Azure. De plus, une visualisation [Power BI](https://powerbi.microsoft.com/) en temps réel agrégée du sentiment peut être générée. Les itérations à venir de cet article incluront ces extensions.

Figure 1 :

![figure 1 didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

Figure 2 :

![figure 2 didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

## Composants requis

La configuration requise pour cet article est la suivante :

1.	Un abonnement Azure actif.
2.	Un fichier CSV contenant des données. Celui de la Figure 2 est disponible au téléchargement [sur GitHub](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv), ou vous pouvez créer le vôtre. Ce didacticiel a été rédigé en partant du principe que c’est le tableau fourni au téléchargement qui est utilisé.

À un haut niveau, les étapes suivantes vont être exécutées :

1.	Téléchargement d’un fichier d’entrée CSV dans le stockage d’objets blob
2.	Ajout d’un modèle d’analyse de sentiments à partir de la galerie Cortana Analytics à votre espace de travail Machine Learning
3.	Déploiement de ce modèle en tant que service web dans l’espace de travail Azure Machine Learning
4.	Création d’un travail Stream Analytics qui appelle ce service web en tant que fonction afin de déterminer le sentiment de saisie de texte
5.	Démarrage du travail Stream Analytics et observation du résultat 


## Téléchargement d’un fichier d’entrée CSV vers le stockage d’objets blob

Pour cette étape, vous pouvez utiliser n’importe quel fichier CSV, y compris celui qui est spécifié dans la présentation. Pour télécharger le fichier, vous pouvez utiliser l’[Explorateur du stockage Azure](http://storageexplorer.com/) ou Visual Studio, ainsi qu’un code personnalisé. Pour ce didacticiel, des exemples sont fournis pour Visual Studio.

1.	Développez Azure et cliquez avec le bouton droit sur le **stockage**. Choisissez **Attacher un stockage externe** et indiquez le **Nom de compte** et la **Clé de compte**.  

    ![explorateur de serveurs du didacticiel machine learning stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	Développez le stockage que vous venez d’attacher, puis choisissez **Créer un conteneur d’objets blob** et fournissez un nom logique. Une fois la création terminée, double-cliquez sur le conteneur pour afficher son contenu (vide à ce stade).

    ![création d’objet blob du didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	Téléchargez le fichier CSV en cliquant sur l’icône **Charger l’objet blob**, puis choisissez un **fichier sur le disque local**.

## Ajoutez le modèle d’analyse de sentiments de la galerie Cortana Analytics

1.	Téléchargez le [modèle d’analyse prédictive de sentiment ](https://gallery.cortanaanalytics.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) dans la galerie Cortana Analytics  
2.	Cliquez sur **Ouvrir** dans Studio :  

    ![ouvrir le studio machine learning du didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	Connectez-vous pour accéder à l’espace de travail. Choisissez le site qui convient le mieux à votre site.
4.	Cliquez sur **Exécuter** en bas de l’écran de Studio  
5.	Une fois exécuté, cliquez sur **Déployer le service web**.
6.	Le modèle d’analyse de sentiments est désormais prêt à l’utilisation. Pour valider, cliquez sur le bouton **test**, saisissez un texte tel que « J’aime Microsoft ». Le test doit retourner un résultat similaire à ce qui suit :

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![données d’analyse du didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

Cliquez sur le lien vers le classeur **Excel 2010 ou version antérieure** pour obtenir votre clé d’API et l’URL dont vous aurez besoin plus tard pour configurer le travail Stream Analytics. (Cette opération est obligatoire uniquement dans le cas de l’utilisation d’un modèle Machine Learning à partir d’un autre espace de travail de compte Azure. Ce didacticiel suppose que c’est le cas pour les besoins de ce scénario)

![expérience d’analyse du didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-experiement.png)

Notez l’URL et la clé d’accès du service web depuis le fichier excel téléchargé comme indiqué ci-dessous :

![Aperçu rapide d’analyse du didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## Créez une tâche d’analyse Stream Analytics qui utilise le modèle Machine Learning

1.	Accédez au [Portail de gestion Azure](https://manage.windowsazure.com).  
2.	Cliquez sur **Nouveau**, **Services de données**, **Stream Analytics** et **Création rapide**. Fournissez le **Nom de la tâche**, la **Région** correspondant à la tâche et choisissez un **Compte de stockage de surveillance régionale**.    
3.	Une fois le travail créé, accédez à l’onglet **Entrées**, puis cliquez sur **Ajouter une entrée**.  

    ![saisie machine learning d’ajout de données du didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	Sur la première page de la fenêtre d’assistant **Ajouter une entrée**, sélectionnez **flux de données**, puis cliquez sur Suivant. Sur la deuxième page, sélectionnez **Stockage d’objets blob** comme entrée, puis cliquez sur **Suivant**.
5.	Sur la page **Paramètres du stockage d’objets blob** de l’assistant, fournissez le nom de conteneur d’objets blob du compte de stockage défini précédemment, au moment du téléchargement des données. Cliquez sur **Suivant**. Choisissez **CSV** en tant que **format de sérialisation de l’événement**. Acceptez les valeurs par défaut pour le reste de la **configuration de la sérialisation**. Cliquez sur **OK**.  
6.	Accédez à l’onglet **Sorties**, puis cliquez sur **Ajouter une sortie**.  

    ![résultat d’ajout d’analyse du didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	Choisissez **Stockage d’objets blob** et fournissez les mêmes paramètres, à l’exception du conteneur. L’**entrée** a été configurée pour assurer la lecture à partir du conteneur nommé « test » dans lequel le fichier **CSV** a été téléchargé. Pour **Sortie**, entrez « RésultatTest ». Les noms de conteneur doivent être différents et vous devez vérifier que ce conteneur existe.
8.	Cliquez sur **Suivant** pour configurer les **paramètres de sérialisation** de sortie. Comme pour Entrée, choisissez **CSV** et cliquez sur le bouton **OK**.
9.	Accédez à l’onglet **Fonctions**, puis cliquez sur **Ajouter une fonction Machine Learning**.  

    ![fonction machine learning d’ajout de didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	Sur la page **Paramètres du Service Web Machine Learning**, recherchez l’espace de travail de Machine Learning, le service web et le point de terminaison par défaut. Pour ce didacticiel, appliquez les paramètres manuellement pour vous familiariser avec la configuration du service web d’un espace de travail quelconque dont vous connaissez l’URL et avez la clé. Fournir le point de terminaison **URL** et **Clé API**. Cliquez ensuite sur **OK**.

    ![service web machine learning de didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	Accédez à l’onglet **Requête** et modifiez la requête comme indiqué ci-dessous :

```
	WITH subquery AS (  
		SELECT text, sentiment(text) as result from input  
	)  
	  
	Select text, result.[Score]  
	Into output  
	From subquery  
```

12. Cliquez sur **Enregistrer** pour enregistrer la feuille de calcul.    

## Démarrage du travail Stream Analytics et examen du résultat

1.	Cliquez sur **Démarrer** au bas de la page travail. 
2.	Dans la **boîte de dialogue Démarrer la requête**, choisissez **Heure personnalisée** et sélectionnez une heure antérieure au moment où le fichier CSV a été téléchargé sur le stockage d’objets Blob. Cliquez sur **OK**.  

    ![heure personnalisée du didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	Accédez au stockage d’objets blob à l’aide de l’outil utilisé lorsque le fichier CSV a été téléchargé. Ce didacticiel utilise Visual Studio.
4.	Quelques minutes après le début du travail, le conteneur de sortie est créé et un fichier CSV téléchargé dans celui-ci.  
5.	Un double-clic sur le fichier ouvre l’éditeur de volume partagé de cluster par défaut et affiche quelque chose qui se présente comme ci-dessous :  

    ![vue csv du didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## Conclusion

Dans ce didacticiel, un travail Stream Analytics a été créé et lit des données de texte diffusées en continu et applique l’analyse des sentiments à ces dernières en continu. Vous avez pu réaliser toutes ces opérations sans avoir à vous soucier des subtilités de la création d’un modèle d’analyse des sentiments. Il s’agit de l’un des avantages de la suite Cortana Analytics.

Vous pouvez également observer les mesures liées à la fonction Azure Machine Learning. Cliquez sur l’onglet **SURVEILLER**. Trois fonctions liées aux métriques sont présentes.
  
- REQUÊTES DE FONCTION indique le nombre de requêtes adressées au service web machine learning.  
- FONCTIONS D’ÉVÉNEMENTS indique le nombre d’événements de la requête. Par défaut, chaque requête au service web ML contient jusqu’à 1 000 événements.  
- ÉCHEC DE REQUÊTES DE FONCTION indique le nombre de requêtes de service web machine learning ayant échoué.  

    ![affichage du moniteur ml du didacticiel machine learning de stream analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!---HONumber=AcomDC_0204_2016-->