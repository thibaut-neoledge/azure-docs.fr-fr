<properties
	pageTitle="Scénarios du processus de science des données Azure en action dans Azure Machine Learning | Microsoft Azure"
	description="Sélectionnez les scénarios appropriés pour le processus d’analyse prédictive avancée dans Azure Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="msolhab"
	manager="paulettm"
	editor="" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na" 
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="msolhab;bradsev" />


# Scénarios d’analyses avancées dans Azure Machine Learning

Cet article présente les divers exemples de sources de données et les scénarios cibles qui peuvent être gérés par le processus d’analyse Cortana (CAP). Il illustre les options disponibles dans les séquences de traitement basées sur les caractéristiques des données, les emplacements sources et les référentiels cibles dans Azure.

L’**arbre de décision** qui permet de sélectionner les exemples de scénarios qui conviennent dans le cas de vos données et objectifs est présenté à la dernière section.

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Les sections suivantes présentent quelques exemples de scénarios. Pour chaque scénario, un flux possible de science des données ou d’analyse avancée et les ressources Azure connexes sont répertoriés.

>[AZURE.NOTE] **Pour tous les scénarios suivants, vous devez :**

*   [Créer un compte de stockage](storage-whatis-account.md)
*   [Créer un espace de travail Azure Machine Learning](machine-learning/machine-learning-create-workspace.md)


## <a name="smalllocal"></a>Scénario 1 : jeu de données tabulaires petit à moyen dans des fichiers locaux

![Fichiers locaux petits à moyens][1]

#### Autres ressources Azure : aucune

1.  Connectez-vous à [Azure Machine Learning Studio](https://studio.azureml.net/).

2.  Téléchargez un jeu de données.

3.  Créez un flux d’expérience Azure Machine Learning commençant par le ou les jeux téléchargés.

## <a name="smalllocalprocess"></a>Scénario 2 : jeu de données petit à moyen de fichiers locaux nécessitant un traitement

![Fichiers locaux petits à moyens avec traitement][2]

#### Autres ressources Azure : Machine virtuelle Azure (serveur IPython Notebook)

1.  Créez une machine virtuelle Azure exécutant IPython Notebook.

2.  Téléchargez des données vers un conteneur de stockage Azure.

3.  Pré-traitez et nettoyez les données dans IPython Notebook, en accédant aux données à partir du conteneur de stockage Azure.

4.  Transformez les données sous forme de tableau nettoyé.

5.  Enregistrez les données transformées dans des objets blob Azure.

6.  Connectez-vous à [Azure Machine Learning Studio](https://studio.azureml.net/).

7.  Lisez les données des objets blob Azure à l’aide du module [Lecteur][reader].

8. Créez un flux d’expérience Azure Machine Learning commençant par le ou les jeux de données ingérés.

## <a name="largelocal"></a>Scénario 3 : jeu de données volumineux de fichiers locaux, ciblant des objets blob Azure

![Fichiers locaux volumineux][3]

#### Autres ressources Azure : Machine virtuelle Azure (serveur IPython Notebook)

1.  Créez une machine virtuelle Azure exécutant IPython Notebook.

2.  Téléchargez des données vers un conteneur de stockage Azure.

3.  Pré-traitez et nettoyez les données dans IPython Notebook, en accédant aux données des objets blob Azure.

4.  Le cas échéant, transformez les données sous forme de tableau nettoyé.

5.  Le cas échéant, explorez des données et créez des fonctionnalités.

6.  Extrayez un échantillon de données petit à moyen.

7.  Enregistrez les données échantillonnées dans des objets blob Azure.

8. Connectez-vous à [Azure Machine Learning Studio](https://studio.azureml.net/).

9. Lisez les données des objets blob Azure à l’aide du module [Lecteur][reader].

10. Créez un flux d’expérience Azure Machine Learning commençant par le ou les jeux de données ingérés.


## <a name="smalllocaltodb"></a>Scénario 4 : jeu de données petit à moyen de fichiers locaux, ciblant SQL Server dans une machine virtuelle Azure

![Fichiers locaux petits à moyens vers une base de données SQL dans Azure][4]

#### Autres ressources Azure : Machine virtuelle Azure (SQL Server / serveur IPython Notebook)

1.  Créez une Machine virtuelle Azure exécutant SQL Server + IPython Notebook.

2.  Téléchargez des données vers un conteneur de stockage Azure.

3.  Pré-traitez et nettoyez les données dans un conteneur de stockage Azure à l’aide d’IPython Notebook.

4.  Le cas échéant, transformez les données sous forme de tableau nettoyé.

5.  Enregistrez les données dans des fichiers locaux de la machine virtuelle (IPython Notebook est en cours d’exécution sur l’ordinateur virtuel, les lecteurs locaux font référence aux lecteurs de machine virtuelle).

6.  Chargez des données dans la base de données SQL Server s’exécutant sur une machine virtuelle Azure.

    a. Option 1 : utilisation de SQL Server Management Studio.

		i.  Login to SQL Server VM
        ii. Run SQL Server Management Studio.
        iii. Create database and target tables.
        iv. Use one of the bulk import methods to load the data from VM-local files.

    b. Option #2: utilisation d’IPython Notebook – pas recommandé pour les jeux de données de tailles moyenne et supérieure

        i.  Use ODBC connection string to access SQL Server on VM.
        ii. Create database and target tables.
        iii. Use one of the bulk import methods to load the data from VM-local files.

7.  Le cas échéant, explorez des données et créez des fonctionnalités. Notez que les fonctionnalités ne doivent pas être matérialisées dans les tables de base de données. Notez seulement la requête nécessaire pour les créer.

8. Choisissez une taille d’échantillon de données, si nécessaire et/ou souhaité.

9. Connectez-vous à [Azure Machine Learning Studio](https://studio.azureml.net/).

10. Lisez les données directement à partir de SQL Server à l’aide du module [Lecteur][reader]. Collez la requête nécessaire qui extrait les champs, crée les fonctionnalités et échantillonne les données si besoin directement dans la requête [Lecteur][reader].

11. Créez un flux d’expérience Azure Machine Learning commençant par le ou les jeux de données ingérés.

## <a name="largelocaltodb"></a>Scénario 5 : jeu de données volumineux dans des fichiers locaux, ciblant SQL Server dans une machine virtuelle Azure

![Fichiers locaux volumineux vers une base de données SQL dans Azure][5]

#### Autres ressources Azure : Machine virtuelle Azure (SQL Server / serveur IPython Notebook)

1.  Créez une machine virtuelle Azure exécutant SQL Server et le serveur IPython Notebook.

2.  Téléchargez des données vers un conteneur de stockage Azure.

3.  (Facultatif) Pré-traitez et nettoyez les données.

    a. Pré-traitez et nettoyez les données dans IPython Notebook, en accédant aux données des objets blob Azure.

    b. Le cas échéant, transformez les données sous forme de tableau nettoyé.

    c. Enregistrez les données dans des fichiers locaux de la machine virtuelle (IPython Notebook est en cours d’exécution sur l’ordinateur virtuel, les lecteurs locaux font référence aux lecteurs de machine virtuelle).

4.  Chargez des données dans la base de données SQL Server s’exécutant sur une machine virtuelle Azure.

    a. Connectez-vous à la machine virtuelle SQL Server.

    b. Si les données ne sont pas déjà enregistrées, téléchargez les fichiers de données à partir du conteneur de stockage Azure vers le dossier de machine virtuelle local.

    c. Exécutez SQL Server Management Studio

    d. Créez la base de données et les tables cibles.

	e. Utilisez l’une des méthodes d’importation en bloc pour charger les données.

    f. Si les jointures de table sont nécessaires, créez des index pour accélérer les jointures.

 > [AZURE.NOTE] Pour accélérer le chargement des formats de données volumineux, il est recommandé de créer des tables partitionnées et d’importer en bloc les données en parallèle. Pour plus d’informations, consultez la rubrique [Importation de données en parallèle dans des tables partitionnées SQL](machine-learning/machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Le cas échéant, explorez des données et créez des fonctionnalités. Notez que les fonctionnalités ne doivent pas être matérialisées dans les tables de base de données. Notez seulement la requête nécessaire pour les créer.

6.  Choisissez une taille d’échantillon de données, si nécessaire et/ou souhaité.

7.  Connectez-vous à [Azure Machine Learning Studio](https://studio.azureml.net/).

8. Lisez les données directement à partir de SQL Server à l’aide du module [Lecteur][reader]. Collez la requête nécessaire qui extrait les champs, crée les fonctionnalités et échantillonne les données si besoin directement dans la requête [Lecteur][reader].

9. Flux d’expérience Azure Machine Learning simple commençant par le jeux de données téléchargés

## <a name="largedbtodb"></a>Scénario 6 : jeu de données volumineux dans une base de données SQL Server locale, ciblant SQL Server dans une machine virtuelle Azure

![Base de données SQL volumineuse sur site vers une base de données SQL dans Azure][6]

#### Autres ressources Azure : Machine virtuelle Azure (SQL Server / serveur IPython Notebook)

1.  Créez une machine virtuelle Azure exécutant SQL Server et le serveur IPython Notebook.

2.  Utilisez l’une des méthodes d’exportation des données pour exporter les données à partir de SQL Server vers des fichiers de vidage.

    a. Remarque : si vous décidez de déplacer toutes les données à partir de la base de données locale, il existe une autre méthode (plus rapide) pour déplacer la base de données entière vers l’instance SQL Server dans Azure. Ignorez les étapes d’exportation des données, de création de la base de données et de chargement/importation des données dans la base de données cible, et suivez l’autre méthode.

3.  Téléchargez les fichiers de vidage vers le conteneur de stockage Azure.

4.  Chargez les données dans une base de données SQL Server sur une machine virtuelle Azure.

    a. Connectez-vous à la machine virtuelle SQL Server.

    b. Téléchargez les fichiers de données à partir d’un conteneur de stockage Azure vers le dossier de machine virtuelle local.

    c. Exécutez SQL Server Management Studio

    d. Créez la base de données et les tables cibles.

	e. Utilisez l’une des méthodes d’importation en bloc pour charger les données.

	f. Si les jointures de table sont nécessaires, créez des index pour accélérer les jointures.

> [AZURE.NOTE] Pour accélérer le chargement des formats de données volumineux, créez des tables partitionnées et importez en bloc les données en parallèle. Pour plus d’informations, consultez la rubrique [Importation de données en parallèle dans des tables partitionnées SQL](machine-learning/machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Le cas échéant, explorez des données et créez des fonctionnalités. Notez que les fonctionnalités ne doivent pas être matérialisées dans les tables de base de données. Notez seulement la requête nécessaire pour les créer.

6.  Choisissez une taille d’échantillon de données, si nécessaire et/ou souhaité.

7.  Connectez-vous à [Azure Machine Learning Studio](https://studio.azureml.net/).

8. Lisez les données directement à partir de SQL Server à l’aide du module [Lecteur][reader]. Collez la requête nécessaire qui extrait les champs, crée les fonctionnalités et échantillonne les données si besoin directement dans la requête [Lecteur][reader].

9. Flux d’expérience Azure Machine Learning simple commençant par le jeux de données téléchargé.

### Autre méthode pour copier une base de données complète à partir d’un serveur SQL local vers une base de données SQL Azure

![Détacher la base de données locale et l’attacher à la base de données SQL dans Azure][7]

#### Autres ressources Azure : Machine virtuelle Azure (SQL Server / serveur IPython Notebook)

Pour répliquer l’ensemble de la base de données SQL Server dans votre machine virtuelle SQL Server, vous devez copier une base de données à partir d’un emplacement/serveur vers un autre, en supposant que la base de données puisse être mise temporairement hors connexion. Pour cela, utilisez l’interface utilisateur graphique de l’Explorateur d’objets SQL Server Management Studio ou les commandes Transact-SQL équivalentes.

1. Détachez la base de données à l’emplacement source. Pour plus d’informations, consultez la rubrique [Détacher une base de données](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx).
2. Dans l’Explorateur Windows ou l’invite de commandes Windows, copiez les fichiers de la base de données détachée et les fichiers journaux à l’emplacement cible sur la machine virtuelle SQL Server dans Azure.
3. Attachez les fichiers copiés à l’instance SQL Server cible. Pour plus d’informations, consultez la rubrique [Attacher une base de données](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx).

[Déplacer une base de données à l’aide de la méthode de détachement et d’attachement (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>Scénario 7 : données volumineuses (« Big Data ») dans des fichiers locaux, ciblant une base de données Hive dans des clusters Hadoop Azure HDInsight

![Données volumineuses (« Big Data ») dans la base de données Hive cible locale][9]

#### Autres ressources Azure : cluster Hadoop Azure HDInsight et machine virtuelle Azure (serveur IPython Notebook)

1.  Créez une machine virtuelle Azure exécutant le serveur IPython Notebook.

2.  Créez un cluster Hadoop Azure HDInsight.

3.  (Facultatif) Pré-traitez et nettoyez les données.

    a. Pré-traitez et nettoyez les données dans IPython Notebook, en accédant aux données des objets blob Azure.

    b. Le cas échéant, transformez les données sous forme de tableau nettoyé.

    c. Enregistrez les données dans des fichiers locaux de la machine virtuelle (IPython Notebook est en cours d’exécution sur l’ordinateur virtuel, les lecteurs locaux font référence aux lecteurs de machine virtuelle).

4.  Téléchargez des données vers le conteneur par défaut du cluster Hadoop sélectionné à l’étape 2.

5.  Chargez des données dans la base de données Hive du cluster Hadoop Azure HDInsight.

    a. Connectez-vous au nœud principal du cluster Hadoop.

    b. Ouvrez la ligne de commande Hadoop.

    c. Entrez le répertoire racine Hive en utilisant la commande `cd %hive_home%\bin` sur la ligne de commande Hadoop.

    d. Exécutez les requêtes Hive pour créer la base de données et les tables, puis chargez des données depuis le stockage d’objets blob vers des tables Hive.

 	> [AZURE.NOTE] Si les données sont volumineuses, les utilisateurs peuvent créer la table Hive avec des partitions. Ils peuvent ensuite utiliser une boucle `for` dans la ligne de commande Hadoop sur le nœud principal pour charger les données dans la table Hive une partition à la fois.

6.  Le cas échéant, explorez des données et créez des fonctionnalités dans la ligne de commande Hadoop. Notez que les fonctionnalités ne doivent pas être matérialisées dans les tables de base de données. Notez seulement la requête nécessaire pour les créer.

	a. Connectez-vous au nœud principal du cluster Hadoop.

    b. Ouvrez la ligne de commande Hadoop.

    c. Entrez le répertoire racine Hive en utilisant la commande `cd %hive_home%\bin` sur la ligne de commande Hadoop.

	d. Exécutez les requêtes Hive sur la ligne de commande Hadoop sur le nœud principal du cluster Hadoop pour explorer les données et créer des fonctionnalités en fonction des besoins.

7.  Si nécessaire et/ou souhaité, échantillonnez les données pour les adapter à Azure Machine Learning Studio.

8.  Connectez-vous à [Azure Machine Learning Studio](https://studio.azureml.net/).

9. Lisez les données directement à partir des `Hive Queries` à l’aide du module [Lecteur][reader]. Collez la requête nécessaire qui extrait les champs, crée les fonctionnalités et échantillonne les données si besoin directement dans la requête [Lecteur][reader].

10. Flux d’expérience Azure Machine Learning simple commençant par le jeux de données téléchargé.

## <a name="decisiontree"></a>Arbre de décision pour le choix du scénario
------------------------

Le schéma suivant résume les scénarios décrits ci-dessus et les processus de science des données ainsi que les choix technologiques effectués qui vous guident vers chacun des scénarios détaillés. Notez que le traitement des données, l’exploration, la conception de fonctionnalités et l’échantillonnage peuvent survenir dans un(e) ou plusieurs méthodes/environnements (dans l’environnement source, l’environnement intermédiaire et/ou l’environnement cible) et peuvent s’effectuer de manière itérative en fonction des besoins. Le schéma illustre uniquement les flux possibles et ne fournit pas d’énumération exhaustive.

![Exemples de scénarios de procédure pas à pas pour le processus DS][8]

### Analyse avancée en action, exemples

Pour connaître les procédures pas à pas de bout en bout pour Azure Machine Learning qui utilisent le processus et la technologie d’analyse avancée à l’aide de jeux de données publics, consultez :


* [Processus d’analyse Cortana en action : utilisation de SQL Server](machine-learning/machine-learning-data-science-process-sql-walkthrough.md).
* [Processus d’analyse Cortana en action : utilisation de clusters Hadoop HDInsight](machine-learning/machine-learning-data-science-process-hive-walkthrough.md).


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!----HONumber=AcomDC_0211_2016-->