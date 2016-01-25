<properties
   pageTitle="Analyse de données avec Azure Machine Learning | Microsoft Azure"
   description="Didacticiel sur l’utilisation de Microsoft Azure Machine Learning avec Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Analyse des données avec Azure Machine Learning
Ce didacticiel vous montre comment créer un modèle Machine Learning prédictif avec Azure Machine Learning à l’aide des données Azure SQL Data Warehouse. Dans ce didacticiel, nous allons créer une campagne marketing ciblée pour Adventure Works, le magasin de vélos, en prévoyant si un client est susceptible d’acheter ou non un vélo.


## Configuration requise
Pour parcourir ce didacticiel, vous avez besoin des éléments suivants :

- SQL Data Warehouse avec la base de données exemple AdventureWorksDW.

[Créer un entrepôt SQL Data Warehouse][] vous montre comment configurer une base de données avec des exemples de données. Si vous disposez déjà d’une base de données SQL Data Warehouse, mais que vous ne disposez pas d’exemples de données, vous pouvez [charger manuellement des exemples de données][].


## Étape 1 : Obtention des données 
Les données seront lues à partir de la vue dbo.vTargetMail de la base de données AdventureWorksDW.

1. Connectez-vous à [Azure Machine Learning Studio][], puis cliquez sur Mes expériences.
2. Cliquez sur **+NOUVEAU** et sélectionnez **Expérience vide**.
3. Entrez le nom de votre expérience : Marketing ciblé.
4. Faites glisser le module **Lecteur** du volet des modules dans la zone de dessin.
5. Spécifiez les détails de votre base de données SQL Data Warehouse dans le volet Propriétés. 
6. Spécifiez la **requête** de base de données pour lire les données intéressantes.
   
   ```
   SELECT [CustomerKey]
      ,[GeographyKey]
      ,[CustomerAlternateKey]
      ,[MaritalStatus]
      ,[Gender]
      ,cast ([YearlyIncome] as int) as SalaryYear
      ,[TotalChildren]
      ,[NumberChildrenAtHome]
      ,[EnglishEducation]
      ,[EnglishOccupation]
      ,[HouseOwnerFlag]
      ,[NumberCarsOwned]
      ,[CommuteDistance]
      ,[Region]
      ,[Age]
      ,[BikeBuyer]
  FROM [dbo].[vTargetMail]
   ```

Démarrez l’expérience en cliquant sur l’option **Démarrer** sous la zone de dessin de l’expérience. ![Exécuter l’expérience][1]


Une fois que l’expérience s’est terminée avec succès, cliquez sur le port de sortie au bas du module Reader et sélectionnez **Visualiser** pour voir les données importées. ![Afficher les données importées][3]





## Étape 2 : Nettoyer les données
Nous allons abandonner certaines colonnes inutiles pour le modèle.

1. Faites glisser le module **Colonnes de projet** sur la zone de dessin.
2. Cliquez sur **Lancer le sélecteur de colonne** dans le volet Propriétés pour spécifier les colonnes que vous souhaitez supprimer. ![Colonnes de projet][4]

3. Excluez deux colonnes : CustomerAlternateKey et GeographyKey. ![Supprimer les colonnes inutiles][5]




## Étape 3 : Générer le modèle
Nous allons fractionner les données dans la proportion 80 et 20 : 80 % pour l’apprentissage d’un modèle Machine Learning et 20 % pour tester le modèle. Nous nous engageons à utiliser des algorithmes « À deux classes » pour ce problème de classification binaire.

1. Faites glisser le module **Fractionner** dans la zone de dessin.
2. Entrez 0,8 comme Fraction de lignes dans le premier jeu de données du volet Propriétés. ![Fractionner les données en jeu d’apprentissage et de test][6]
3. Faites glisser le module **Arbre de décision optimisé à deux classes** dans la zone de dessin.
4. Faites glisser le module **Effectuer le traitement de données pour apprentissage du modèle** dans la zone de dessin et spécifiez les entrées. Cliquez sur l’option **Lancer le sélecteur de colonne** figurant dans le volet Propriétés.
      - Première entrée : algorithme ML.
      - Deuxième entrée : données sur lesquelles essayer l’algorithme. ![Connecter le module Former le modèle][7]
5. Sélectionnez la colonne **BikeBuyer** comme colonne à prédire. ![Sélectionner la colonne à prédire][8]





## Étape 4 : Noter le modèle
Maintenant, nous allons voir comment le modèle s’exécute sur les données de test. Nous allons comparer l’algorithme de notre choix avec un autre algorithme et voir celui qui fonctionne le mieux.

1. Faites glisser le module **Noter le modèle** dans la zone de dessin. Première entrée : modèle formé Deuxième entrée : données de test ![Notation du modèle][9]
2. Faites glisser **Machines de points Bayes à deux classes** dans la zone de dessin de l’expérience. Nous allons comparer comment cet algorithme fonctionne par rapport à l’arbre de décision optimisé à deux classes.
3. Copiez et collez les modules de Former le modèle et le modèle Noter le modèle dans la zone de dessin.
4. Faites glisser le module **Évaluer le modèle** module dans la zone de dessin pour comparer les deux algorithmes.
5. **Exécutez** l’expérience. ![Exécuter l’expérience][10]
6. Cliquez sur le port de sortie situé au bas du module Évaluer le modèle, puis sélectionnez Visualiser. ![Visualiser les résultats de l’évaluation][11]



Les mesures fournies sont la courbe ROC (caractéristiques du fonctionnement du récepteur), le diagramme de rappel de précision et la courbe d’élévation. En examinant ces mesures, nous pouvons voir que le premier modèle fonctionne mieux que le second. Pour regarder les prévisions du premier modèle, cliquez sur le port de sortie du modèle de notation, puis cliquez sur Visualiser. ![Visualiser les résultats de la notation][12]

Vous verrez deux colonnes supplémentaires ajoutées à votre groupe de données de test.

- Probabilités évaluées : probabilité qu’un client soit un acheteur potentiel de vélo.
- Étiquette de marquage : classification effectuée par le modèle – acheteur de vélo (1) ou non (0). Ce seuil de probabilité pour l’étiquetage est défini à 50 % et peut être ajusté.

En comparant la colonne BikeBuyer (réelle) avec les étiquettes de marquage (prévision), vous pouvez voir comment le modèle a fonctionné. Au cours des opérations suivantes, vous pouvez utiliser ce modèle pour élaborer des prévisions pour les nouveaux clients et publier ce modèle en tant que service web ou écrire les résultats dans SQL Data Warehouse.

Pour en savoir plus sur la création de modèles Machine Learning prédictifs, reportez-vous à [Introduction à Machine Learning sur Azure][].



<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure Machine Learning studio]: https://studio.azureml.net/
[Introduction à Machine Learning sur Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[charger manuellement des exemples de données]: sql-data-warehouse-get-started-manually-load-samples.md
[Créer un entrepôt SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!---HONumber=AcomDC_0114_2016-->