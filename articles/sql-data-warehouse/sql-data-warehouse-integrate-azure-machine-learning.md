<properties
   pageTitle="Utilisation de Microsoft Azure Machine Learning avec SQL Data Warehouse | Microsoft Azure"
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
   ms.date="09/22/2015"
   ms.author="sahajs"/>

# Utilisation de Microsoft Azure Machine Learning avec SQL Data Warehouse

Microsoft Azure Machine Learning est un service d’analyse prédictive entièrement géré qui vous permet de développer des modèles d’analyse prédictive de vos données dans SQL Data Warehouse et de les publier en tant que services Web prêts à l’emploi. Pour découvrir les principes de base de l’analyse prédictive et de Machine Learning, consultez l’[Introduction à Machine Learning sur Microsoft Azure][]. Vous pouvez ensuite apprendre comment créer, former, évaluer et tester un modèle Machine Learning à l’aide du [didacticiel consacré à la création d’une expérience][].

Dans cet article, vous apprendrez comment effectuer les opérations suivantes à l’aide de [Microsoft Azure Machine Learning Studio][] : - Lire des données de votre base de données pour créer, former et évaluer un modèle prédictif - Écrire des données sur votre base de données


## Lire des données à partir de SQL Data Warehouse

Nous lirons les données de la table Product dans la base de données AdventureWorksDW.

### Étape 1

Démarrez une nouvelle expérience en cliquant sur l’option + NOUVEAU située en bas de la fenêtre de Machine Learning Studio, sélectionnez EXPÉRIENCE, puis « Expérience vide ». Sélectionnez le nom d’expérience par défaut, situé en haut de la zone de dessin, et remplacez-le par un nom significatif, par exemple : Prédiction de prix d’un vélo.

### Étape 2

Recherchez le module Lecteur dans la palette d’ensemble de données et de modules située sur la gauche de la zone de dessin d’expérience. Faites glisser le module sur la zone de dessin d’expérience. ![][drag_reader]

### Étape 3

Sélectionnez le module Lecteur et renseignez le panneau des propriétés.

1. Sélectionnez la base de données SQL Azure en tant que source de données.
2. Nom du serveur de base de données : tapez le nom du serveur. Pour le rechercher, accédez au [portail Azure][].

![][server_name]

3. Nom de la base de données : tapez le nom d’une base de données sur le serveur que vous venez de définir. 
4. Nom du compte utilisateur du serveur : tapez le nom d’utilisateur du compte disposant des autorisations d’accès à la base de données. 
5. Mot de passe du compte utilisateur du serveur : renseignez le mot de passe du compte utilisateur spécifié.
6. Accepter un certificat de serveur : utilisez cette option (moins sûre) si vous ne souhaitez pas consulter le certificat du site avant de lire les données.
7. Requête de base de données : tapez une instruction SQL qui décrit les données que vous souhaitez lire. Dans ce cas, nous lirons les données de la table Product à l’aide de la requête suivante.


```
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### Étape 4

1. Démarrez l’expérience en cliquant sur l’option Démarrer sous la zone de dessin de l’expérience.
2. Une fois l’expérience terminée, une coche verte s’affiche en regard du module Lecteur pour indiquer la réussite des opérations. Notez que le statut Exécution terminée s’affiche dans le coin supérieur droit de la fenêtre.

![][run]

3. Pour voir à quoi ressemblent les données importées, cliquez sur le port de sortie situé en bas du jeu de données d’automobile, puis sélectionnez Visualiser.


## Créer, former et évaluer un modèle

Vous pouvez désormais utiliser cet ensemble de données pour : - Créer un modèle : traitez les données et définissez des fonctions - Former le modèle : sélectionnez et appliquez un algorithme d’apprentissage - Évaluer et tester le modèle : prédisez le prix du nouveau vélo
	
![][model]

Pour en savoir plus sur la création, la formation, l’évaluation et le test d’un modèle Machine Learning, utilisez le [didacticiel consacré à la création d’une expérience][].

## Écrire des données sur Microsoft Azure SQL Data Warehouse

Nous écrirons l’ensemble de résultats sur la table ProductPriceForecast de la base de données AdventureWorksDW.

### Étape 1

Recherchez le module Lecteur dans la palette d’ensemble de données et de modules située sur la gauche de la zone de dessin d’expérience. Faites glisser le module sur la zone de dessin d’expérience.

![][drag_writer]

### Étape 2

Sélectionnez le module Lecteur et renseignez le volet des propriétés.

1. Sélectionnez la base de données SQL Microsoft Azure en tant que Destination des données.
2. Nom du serveur de base de données : tapez le nom du serveur. Pour le rechercher, accédez au [portail Azure][]. 
3. Nom de la base de données : tapez le nom d’une base de données sur le serveur que vous venez de définir. 
4. Nom du compte utilisateur du serveur : tapez le nom d’utilisateur d’un compte disposant des autorisations d’écriture sur la base de données. 
5. Mot de passe du compte utilisateur du serveur : renseignez le mot de passe du compte utilisateur spécifié.
6. Accepter un certificat de serveur (non sûre) : sélectionnez cette option si vous ne souhaitez pas consulter le certificat.
7. Liste de colonnes séparées par des virgules à enregistrer : fournit une liste de l’ensemble de données ou des colonnes de résultats que vous souhaitez générer.
8. Nom de table de données : spécifiez ici le nom de la table de données.
9. Liste des colonnes de table de données séparées par des virgules : spécifiez les noms de colonnes à utiliser dans la nouvelle table. Les noms des colonnes peuvent être différents de ceux de l’ensemble de données source, mais vous devez indiquer les noms de colonnes définis pour la table de sortie.
10. Nombre de lignes écrites par opération SQL Azure : vous pouvez configurer le nombre de lignes écrites sur une base de données SQL lors d’une opération.

![][writer_properties]

### Étape 3

1. Démarrez l’expérience en cliquant sur l’option Démarrer sous la zone de dessin de l’expérience.
2. Une fois l’expérience terminée, une coche verte s’affiche en regard de chaque module pour indiquer la réussite de leurs opérations. 

## Étapes suivantes

Pour consulter une vue d’ensemble de l’intégration, accédez à la rubrique [Vue d’ensemble sur l’intégration de SQL Data Warehouse][].

Pour obtenir des conseils supplémentaires en matière de développement, consultez la rubrique [Vue d’ensemble sur le développement SQL Data Warehouse][].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[Vue d’ensemble sur le développement SQL Data Warehouse]: ./sql-data-warehouse-overview-develop/
[Vue d’ensemble sur l’intégration de SQL Data Warehouse]: ./sql-data-warehouse-overview-integration/
[didacticiel consacré à la création d’une expérience]: ./machine-learning-create-experiment/
[Introduction à Machine Learning sur Microsoft Azure]: ./machine-learning-what-is-machine-learning/
[Microsoft Azure Machine Learning Studio]: https://studio.azureml.net/Home
[portail Azure]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/

<!---HONumber=Oct15_HO1-->