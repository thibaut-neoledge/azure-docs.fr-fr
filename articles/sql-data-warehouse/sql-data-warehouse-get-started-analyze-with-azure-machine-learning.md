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
   ms.date="10/21/2015"
   ms.author="sahajs"/>

# Analyse des données avec Azure Machine Learning
Ce didacticiel vous montre comment créer un modèle Machine Learning prédictif avec Azure Machine Learning à l’aide des données Azure SQL Data Warehouse. Dans ce didacticiel, nous allons utiliser la base de données AdventureWorksDW et classer les clients de l’atelier de bicyclettes Adventure Works comme acheteurs potentiels de vélos ou non afin de créer une campagne marketing ciblée.


## Configuration requise
Pour parcourir ce didacticiel, vous avez besoin des éléments suivants :

- SQL Data Warehouse avec la base de données exemple AdventureWorksDW.

[Créer un entrepôt SQL Data Warehouse][] vous montre comment configurer une base de données avec des exemples de données. Si vous disposez déjà d’une base de données SQL Data Warehouse, mais que vous n’avez pas d’exemple de données, vous pouvez [charger manuellement des exemples de données][]


## Étape 1 : Obtention des données 
Les données seront lues à partir de la vue dbo.vTargetMail de la base de données AdventureWorksDW.

1. Connectez-vous à [Azure Machine Learning Studio][], puis cliquez sur Mes expériences.
2. Cliquez sur **+NOUVEAU** et sélectionnez **Expérience vide**.
3. Entrez le nom de votre expérience : Marketing ciblé.
4. Faites glisser le module **Lecteur** du volet des modules dans la zone de dessin.
5. Spécifiez les détails de votre base de données SQL Data Warehouse dans le volet Propriétés. 
6. Spécifiez la requête de base de données pour lire les données intéressantes.

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
7. Démarrez l’expérience en cliquant sur l’option **Démarrer** sous la zone de dessin de l’expérience.
8. Cliquez sur le port de sortie situé en bas du module Lecteur, puis sélectionnez **Visualiser** pour voir les données importées.



## Étape 2 : Nettoyer les données
Nous allons abandonner certaines colonnes inutiles pour le modèle.

1. Faites glisser le module **Colonnes de projet** sur la zone de dessin.
2. Cliquez sur **Lancer le sélecteur de colonne** dans le volet Propriétés pour spécifier les colonnes que vous souhaitez supprimer.
3. Excluez deux colonnes : CustomerAlternateKey et GeographyKey.



## Étape 3 : Générer le modèle
Nous allons fractionner les données dans la proportion 80 et 20 : 80 % pour l’apprentissage d’un modèle Machine Learning et 20 % pour tester le modèle. Nous nous engageons à utiliser des algorithmes « À deux classes » pour ce problème de classification binaire.

1. Faites glisser le module **Fractionner** dans la zone de dessin.
2. Entrez 0,8 comme Fraction de lignes dans le premier jeu de données du volet Propriétés.
3. Faites glisser le module **Arbre de décision optimisé à deux classes** dans la zone de dessin.
4. Faites glisser le module **Effectuer le traitement de données pour apprentissage du modèle** dans la zone de dessin et spécifiez : première entrée : algorithme ML. Deuxième entrée : données sur lesquelles essayer l’algorithme.
5. Cliquez sur **Lancer le sélecteur de colonne** dans le volet Propriétés pour spécifier la colonne sur laquelle le modèle doit effectuer les prévisions : BikeBuyer.


## Étape 4 : Noter le modèle
Maintenant, nous allons voir comment le modèle s’exécute sur les données de test. Nous allons comparer l’algorithme de notre choix avec un autre algorithme et voir celui qui fonctionne le mieux.

1. Faites glisser le module **Noter le modèle** dans la zone de dessin. Première entrée : Modèle formé Deuxième entrée : Données de test
2. Faites glisser **Machines de points Bayes à deux classes** dans la zone de dessin de l’expérience. Nous allons comparer comment cet algorithme fonctionne par rapport à l’arbre de décision optimisé à deux classes.
3. Copiez et collez les modules de Former le modèle et le modèle Noter le modèle dans la zone de dessin.
4. Faites glisser le module **Évaluer le modèle** module dans la zone de dessin pour comparer les deux algorithmes.
5. **Exécutez** l’expérience.
6. Cliquez sur le port de sortie situé en bas du module Évaluer le modèle, puis sélectionnez Visualiser.


Les mesures fournies sont la courbe ROC (caractéristiques du fonctionnement du récepteur), le diagramme de rappel de précision et la courbe d’élévation. En examinant ces mesures, nous pouvons voir que le premier modèle fonctionne mieux que le second. Pour regarder les prévisions du premier modèle, cliquez sur le port de sortie du modèle de notation, puis cliquez sur Visualiser.


Vous verrez deux colonnes supplémentaires ajoutées à votre groupe de données de test.

- Probabilités évaluées : probabilité qu’un client soit un acheteur potentiel de vélo.
- Étiquette de marquage : classification effectuée par le modèle – acheteur de vélo (1) ou non (0). Le seuil de probabilité pour l’étiquetage est défini à 50 % et peut être ajusté.

En comparant la colonne BikeBuyer (réelle) avec les étiquettes de marquage (prévision), vous pouvez voir comment le modèle a fonctionné. Au cours des opérations suivantes, vous pouvez utiliser ce modèle pour élaborer des prévisions pour les nouveaux clients et publier ce modèle en tant que service web ou écrire les résultats dans SQL Data Warehouse.

Pour plus d’informations, reportez-vous à [Présentation de Machine Learning sur Azure][].



<!--Article references-->
[Azure Machine Learning studio]: https://studio.azureml.net/
[Présentation de Machine Learning sur Azure]: ../machine-learning/machine-learning-what-is-machine-learning.md
[charger manuellement des exemples de données]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Créer un entrepôt SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO4-->