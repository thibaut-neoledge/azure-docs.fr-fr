<properties
	pageTitle="Guide technique du modèle de solution Cortana Analytics pour la maintenance prédictive dans l’industrie aérospatiale et d’autres secteurs d’activité | Microsoft Azure"
	description="Guide technique de l’utilisation du modèle de solution avec Microsoft Cortana Analytics pour la maintenance prédictive dans les secteurs de l’aérospatial, des services publics et du transport."
	services="cortana-analytics"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="garye" />

# Guide technique du modèle de solution Cortana Analytics pour la maintenance prédictive dans l’industrie aérospatiale et d’autres secteurs d’activité

## **Vue d'ensemble**

Les modèles de solution visent à accélérer le processus de création d’une démonstration E2E sur Cortana Analytics Suite. Un modèle déployé fournit à votre abonnement les composants Cortana Analytics nécessaires et à établir des relations entre eux. Il alimente également le pipeline de données avec des exemples de données générées à partir d’une application de simulation de données que vous allez télécharger et installer sur votre ordinateur local après avoir déployé le modèle de solution. Les données générées à partir du simulateur alimenteront le pipeline de données et commenceront à générer des prédictions d’apprentissage automatique que vous pourrez ensuite visualiser sur le tableau de bord Power BI. Le processus de déploiement vous guide à travers plusieurs étapes permettant de configurer les informations d’identification de votre solution. Veillez à consigner les informations d’identification, telles que le nom de la solution, le nom d’utilisateur et le mot de passe, que vous renseignez pendant le déploiement.

L’objectif de ce document est de vous présenter l’architecture de référence et les différents composants configurés dans votre abonnement dans le cadre de ce modèle de solution. Ce document explique également comment remplacer les exemples de données par vos propres données réelles afin de vous permettre d’accéder à des analyses et à des prévisions directement dérivées de vos propres données. Il décrit enfin les éléments du modèle de solution que vous devrez modifier si vous souhaitez personnaliser la solution avec vos propres données. Vous trouverez à la fin de ce document des instructions sur la façon de configurer le tableau de bord Power BI pour ce modèle de solution.

>[AZURE.TIP] Vous pouvez télécharger et imprimer une [version PDF de ce document](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).

## **Grandes lignes**

![](media/cortana-analytics-technical-guide-predictive-maintenance\predictive-maintenance-architecture.png)

Le déploiement de la solution entraîne l’activation de divers services Azure dans Cortana Analytics Suite (*c’est-à-dire* Event Hub, Stream Analytics, HDInsight, Data Factory, Machine Learning, *etc.*). Le schéma d’architecture ci-dessus illustre globalement la manière dont est construite pour le modèle de solution de maintenance prédictive pour le secteur aérospatial. Vous pouvez passer en revue ces services en cliquant dessus sur le schéma du modèle de solution créé avec le déploiement de la solution. Vous pouvez également télécharger ce [schéma en taille réelle](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

Les sections suivantes décrivent chaque élément du schéma.

## **Source et ingestion de données**

### Source de données de synthèse

Pour ce modèle, la source de données utilisée est générée à partir d’une application de bureau que vous allez télécharger et exécuter localement après la réussite du déploiement. Vous trouverez les instructions de téléchargement et d’installation de cette application dans la barre des propriétés lorsque vous sélectionnez le premier nœud appelé Predictive Maintenance Data Simulator sur le schéma du modèle de solution. Cette application alimente le service [Azure Event Hub](#azure-event-hub) avec des points de données, ou événements, qui seront utilisés dans le reste du flux de la solution. Cette source de données est composée ou dérivée des données accessibles au public depuis le [référentiel de données de la NASA](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) à l’aide du [jeu de données de simulation de la dégradation du turbomoteur](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

L’application de génération d’événements alimente le service Azure Event Hub uniquement lorsqu’elle est exécutée sur votre ordinateur.

### Azure Event Hub

Le service [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) est le destinataire des données fournies par la source de données de synthèse décrite ci-dessus.

## **Préparation et analyse des données**


### Azure Stream Analytics

Le service [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) sert à fournir des analyses en temps quasi-réel sur le flux d’entrée du service [Azure Event Hub](#azure-event-hub) et à publier les résultats dans un tableau de bord [Power BI](https://powerbi.microsoft.com). Il permet également d’archiver, dans le service [Azure Storage](https://azure.microsoft.com/services/storage/), tous les événements bruts entrants qui seront ensuite traités par le service [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

### Agrégation personnalisée HD Insights

Le service Azure HD Insight est utilisé pour exécuter des scripts [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orchestrés par Azure Data Factory) pour agréger les événements bruts ayant été archivés à l’aide du service Azure Stream Analytics.

### Azure Machine Learning

Le service [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) est utilisé (et orchestré par Azure Data Factory) pour prédire la durée de vie restante (RUL) d’un moteur d’avion particulier compte tenu des entrées reçues.

## **Publication des données**


### Service de base de données SQL Azure

Le service de [base de données SQL Azure](https://azure.microsoft.com/services/sql-database/) sert à stocker (sous le contrôle d’Azure Data Factory) les prédictions reçues par le service Azure Machine Learning et qui seront utilisées dans le tableau de bord [Power BI](https://powerbi.microsoft.com).

## **Consommation des données**

### Power BI

Le service [Power BI](https://powerbi.microsoft.com) permet d’afficher un tableau de bord contenant les agrégations et alertes fournies par le service [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), ainsi que les prédictions de durée de vie restante stockées dans la [base de données SQL Azure](https://azure.microsoft.com/services/sql-database/) qui ont été générées à l’aide du service [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Pour obtenir des instructions sur la façon de configurer le tableau de bord Power BI pour ce modèle de solution, consultez la section ci-dessous.

## **Comment importer vos propres données**

Cette section explique comment importer vos propres données dans Azure et décrit les éléments à modifier compte tenu des données que vous importez dans cette architecture.

Il est peu probable que les jeux de données que vous importez correspondent parfaitement au [jeu de données de simulation de la dégradation du turbomoteur](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) utilisé pour ce modèle de solution. Il est important de bien comprendre vos données et besoins pour déterminer la façon dont vous allez modifier ce modèle pour l’utiliser avec vos propres données. Si vous n’êtes pas familiarisé avec le service Azure Machine Learning, vous pouvez consulter la page [Didacticiel sur l’apprentissage automatique : création de votre première expérience dans Azure Machine Learning Studio](machine-learning-create-experiment.md) pour en obtenir un premier aperçu.

Dans les sections suivantes, nous allons décrire les sections du modèle que vous devrez modifier lors de l’introduction d’un nouveau jeu de données.

### Concentrateur d'événement d'Azure

Le service Azure Event Hub est très générique, au point que les données peuvent être publiées sur le Hub au format CSV ou JSON. Bien que le service Azure Event Hub n’implique aucun traitement particulier, il est important de comprendre les données qui y sont importées.

Ce document ne décrit pas le mode de réception de vos données, mais vous pouvez facilement envoyer des événements ou des données vers un service Azure Event Hub à l’aide des API Event Hub.

### Azure Stream Analytics

Le service Azure Stream Analytics permet de fournir des analyses en temps quasi-réel grâce à une lecture des flux de données et à l’envoi de données vers diverses sources.

Dans le cas du modèle de solution de maintenance prédictive pour le secteur aérospatial, la requête Azure Stream Analytics se compose de quatre sous-requêtes, chacune utilisant les événements dérivés du service Azure Event Hub et générant des sorties à quatre emplacements distincts. Ces sorties comprennent trois jeux de données Power BI et un emplacement Azure Storage.

Pour accéder à la requête Azure Stream Analytics Modèles :

-   Connectez-vous au portail Azure

-   Recherchez les travaux Stream Analytics ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-stream-analytics.png) qui ont été générés lors du déploiement de la solution (*par exemple,*, **maintenancesa02asapbi** et **maintenancesa02asablob** pour la solution de maintenance prédictive)

-   Sélectionnez

    -   ***INPUTS*** pour afficher l’entrée de la requête

    -   ***QUERY*** pour afficher la requête elle-même

    -   ***OUTPUTS*** pour afficher les différentes sorties

Pour plus d’informations sur la construction des requêtes Azure Stream Analytics, consultez la [référence de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) sur MSDN.

Dans cette solution, les requêtes génèrent trois jeux de données avec des informations d’analyse en temps quasi-réel concernant le flux de données entrant dans un tableau de bord Power BI fourni dans le cadre de ce modèle de solution. Étant donné que le format des données entrantes est implicitement connu, ces requêtes devront être modifiées en fonction de votre format de données.

La requête dans la deuxième tâche Stream Analytics **maintenancesa02asablob** génère simplement tous les événements [Event Hub](https://azure.microsoft.com/services/event-hubs/) dans [Azure Storage](https://azure.microsoft.com/services/storage/) et ne nécessite donc aucune modification, quel que soit votre format de données, puisque les informations d’événement complètes sont transmises en continu vers le stockage.

### Azure Data Factory

Le service [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orchestre le déplacement et le traitement des données. Dans le modèle de solution de maintenance prédictive pour le secteur aérospatial, la fabrique de données est constituée de trois [pipelines](../data-factory/data-factory-create-pipelines.md) qui déplacent et traitent les données à l’aide de différentes technologies. Vous pouvez accéder à votre fabrique de données en ouvrant le nœud Data Factory en bas du diagramme du modèle de solution créé avec le déploiement de la solution. Vous êtes alors redirigé vers la fabrique de données sur votre portail Azure. Si vous rencontrez des erreurs dans vos jeux de données, vous pouvez les ignorer car ces erreurs sont liées à la fabrique de données déployée avant le démarrage du générateur de données. Ces erreurs ne perturbent pas le fonctionnement de votre fabrique de données.

![](media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Cette section décrit les [pipelines](../data-factory/data-factory-create-pipelines.md) requis et les [activités](../data-factory/data-factory-create-pipelines.md) contenus dans le service [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Vous trouverez ci-dessous une vue schématique de la solution.

![](media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Deux des pipelines de cette fabrique contiennent des scripts [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) utilisés pour partitionner et agréger les données. Lorsqu’ils sont référencés, les scripts apparaissent dans le compte [Azure Storage](https://azure.microsoft.com/services/storage/) créé pendant l’installation, à l’emplacement suivant : maintenancesascript\\\script\\\hive\\\ (ou https://[Your nom de votre solution].blob.core.windows.net/maintenancesascript).

De la même manière que les requêtes [Azure Stream Analytics](#azure-stream-analytics-1), les scripts [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) ont une connaissance du format des données entrantes ; vous devrez donc modifier ces requêtes en fonction de votre format de données et des exigences d’[ingénierie des caractéristiques](machine-learning-feature-selection-and-engineering.md).

#### *AggregateFlightInfoPipeline*

Ce [pipeline](../data-factory/data-factory-create-pipelines.md) contient une seule activité, une activité [HDInsightHive](../data-factory/data-factory-hive-activity.md) qui utilise un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) exécutant un script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour partitionner les données placées dans [Azure Storage](https://azure.microsoft.com/services/storage/) pendant l’exécution de la tâche [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/).

Le script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour cette tâche de partitionnement est ***AggregateFlightInfo.hql***

#### *MLScoringPipeline*

Ce [pipeline](../data-factory/data-factory-create-pipelines.md) contient plusieurs activités dont le résultat final contient les prédictions notées à partir de l’expérience [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) associée à ce modèle de solution.

Ce pipeline contient les activités suivantes :

-   Activité [HDInsightHive](../data-factory/data-factory-hive-activity.md) utilisant un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) qui exécute un script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour effectuer les agrégations et les tâches d’ingénierie de caractéristiques nécessaires pour l’expérience [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Le script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour cette tâche de partitionnement est ***PrepareMLInput.hql***.

-   Activité [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les résultats de l’activité [HDInsightHive](../data-factory/data-factory-hive-activity.md) vers un objet blob [Azure Storage](https://azure.microsoft.com/services/storage/) unique, accessible à l’activité [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx).

-   Activité [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) qui appelle l’expérience [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) pour placer les résultats dans un objet blob [Azure Storage](https://azure.microsoft.com/services/storage/) unique.

#### *CopyScoredResultPipeline*

Ce [pipeline](../data-factory/data-factory-create-pipelines.md) contient une seule activité, une activité [copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les résultats de l’expérience [Azure Machine Learning](#azure-machine-learning) entre le service ***MLScoringPipeline*** et la [base de données SQL Azure](https://azure.microsoft.com/services/sql-database/) qui a été configurée dans le cadre de l’installation du modèle de solution.

### Azure Machine Learning

L’expérience [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) utilisée pour ce modèle de solution permet de prédire la durée de vie restante (RUL) d’un moteur d’avion. L’expérience est spécifique au jeu de données consommé et doit donc être modifiée ou remplacée compte tenu des données qui y sont importées.

Pour plus d’informations sur la création de l’expérience Azure Machine Learning, consultez la page [Maintenance prédictive : étape 1 sur 3, préparation des données et ingénierie des caractéristiques](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) (en anglais).

## **Surveiller la progression**
 Une fois le Générateur de données lancé, l’alimentation du pipeline commence et les différents composants de votre solution entrent en action selon les commandes émises par la fabrique de données. Il existe deux méthodes de surveillance du pipeline.

1. Un des travaux Stream Analytics enregistre les données brutes entrantes dans le stockage d'objets blob. Si vous cliquez sur le composant de stockage d'objets Blob de votre solution à partir de l'écran depuis lequel vous avez correctement déployé la solution, puis cliquez sur Ouvrir dans le volet droit, vous accédez au [portail de gestion](https://portal.azure.com/). Une fois dans le portail, cliquez sur Objets BLOB. Dans l'écran suivant, vous verrez une liste de conteneurs. Cliquez sur **maintenancesadata**. Dans l'écran suivant, vous verrez le dossier **rawdata**. Dans le dossier rawdata, vous verrez des dossiers nommés par exemple hour=17, hour=18, etc. Si vous voyez ces dossiers, cela signifie que les données brutes sont correctement générées sur votre ordinateur et stockées dans des objets blob. Vous devriez voir des fichiers csv avec des tailles finies en Mo dans ces dossiers.

2. La dernière étape du pipeline consiste à enregistrer des données (par exemple, les prédictions générées à partir de l’apprentissage automatique) dans la base de données SQL. Vous devrez peut-être attendre un maximum de trois heures pour les données s'affichent dans la base de données SQL. La première consiste à surveiller la quantité de données disponible dans votre base de données SQL via le [portail Azure](https://manage.windowsazure.com/). Dans le volet gauche, identifiez les bases de données SQL ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-SQL-databases.png) et cliquez dessus. Recherchez ensuite votre base de données **pmaintenancedb** puis cliquez dessus. En bas de la page suivante, cliquez sur Gérer

	![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-manage.png).

	Ici, vous pouvez cliquer sur Nouvelle requête et rechercher le nombre de lignes (par exemple, select count(*) from PMResult). À mesure que votre base de données augmente, le nombre de lignes de la table devrait augmenter.


## **Tableau de bord Power BI**

### Vue d’ensemble

Cette section décrit comment configurer un tableau de bord Power BI afin de visualiser vos données en temps réel à partir d’Azure Stream Analytics (chemin à chaud), ainsi que les résultats des prédictions par lots à partir d’Azure Machine Learning (chemin à froid).

### Configuration du tableau de bord de chemin à froid

Dans le pipeline des données de chemin à froid, le principal objectif consiste à obtenir la durée de vie restante prévisionnelle de chaque moteur d’avion au terme d’un vol (cycle). Le résultat de la prédiction est mis à jour toutes les 3 heures pour prédire la durée de vie restante des moteurs d’avion qui ont terminé un vol au cours des 3 dernières heures.

Power BI se connecte à une base de données SQL Azure en tant que source de données où sont stockés les résultats de la prédiction. Remarque : 1) au moment de déployer votre solution, une prédiction réelle s’affichera dans la base de données dans un délai de 3 heures. Le fichier pbix fourni avec le téléchargement de Generator contient certaines données d’amorçage afin que vous puissiez créer immédiatement le tableau de bord Power BI. 2) dans cette étape, la condition préalable consiste à télécharger et installer le logiciel gratuit [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Dans les étapes suivantes, nous allons vous expliquer comment connecter le fichier pbix à la base de données SQL qui a été lancée au moment du déploiement de la solution et qui contient des données (*par exemple,* résultats de prédiction) pouvant être visualisées.

1.  Obtenir les informations d’identification de la base de données.

    Avant de passer aux étapes suivantes, vous avez besoin du **nom du serveur de base de données, du nom de la base de données, du nom d’utilisateur et du mot de passe**. Voici les étapes à suivre pour obtenir ces informations.

    -   Une fois que la **« Base de données SQL Azure »** apparaît en vert sur votre schéma de modèle de solution, cliquez dessus, puis cliquez sur **« Ouvrir »**.

    -   La page du portail Azure s’affiche dans un nouvel onglet/fenêtre de navigateur. Cliquez sur **« Groupes de ressources »** dans le volet gauche.

    -   Sélectionnez l’abonnement que vous utilisez pour le déploiement de la solution, puis sélectionnez **« YourSolutionName\_ResourceGroup »**.

    -   Dans le nouveau volet contextuel, cliquez sur l’icône ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-sql.png) pour accéder à votre base de données. Le nom de votre base de données apparaît en regard de cette icône (*par exemple,* **’pmaintenancedb’**) et le **nom du serveur de base de données** est répertorié sous la propriété du nom de serveur et doit être du type **NomdeVotreSolution.database.windows.net**.

	-   Les **nom d’utilisateur** et **mot de passe** de votre base de données sont identiques aux nom d’utilisateur et mot de passe précédemment enregistrés lors du déploiement de la solution.

2.  Mettre à jour la source de données du fichier de rapport de chemin à froid avec Power BI Desktop.

    -   Dans le dossier de votre ordinateur où vous avez téléchargé et décompressé le fichier Generator, double-cliquez sur le fichier **PowerBI\\PredictiveMaintenanceAerospace.pbix**. Si des messages d’avertissement s’affichent lorsque vous ouvrez le fichier, ignorez-les simplement. En haut du fichier, cliquez sur **Modifier les requêtes**.

	    ![](media\cortana-analytics-technical-guide-predictive-maintenance\edit-queries.png)

	-	Deux tables apparaissent : **RemainingUsefulLife** et **PMResult**. Sélectionnez la première table, puis cliquez sur ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-query-settings.png) en regard de **Source** sous **ÉTAPES APPLIQUÉES** sur le volet de droite **Paramètres de requête**. Ignorer les messages d’avertissement qui s’affichent.

    -   Dans la fenêtre contextuelle, remplacez **Serveur** et **Base de données** par vos propres noms de serveur et de base de données, puis cliquez sur **OK**. Pour le nom du serveur, veillez à spécifier le port 1433 (**YourSoutionName.database.windows.net, 1433**). Ignorez les messages d’avertissement qui s’affichent à l’écran.

    -   Dans la fenêtre contextuelle suivante, deux options s’affichent dans le volet gauche (**Windows** et **Base de données**). Cliquez sur **Base de données** et renseignez vos **Nom d’utilisateur** et **Mot de passe** (il s’agit des nom d’utilisateur et mot de passe que vous avez saisis lors du déploiement de la solution et de la création de la base de données SQL Azure). Dans ***Sélectionner le niveau auquel appliquer ces paramètres***, cochez l’option du niveau de base de données. Cliquez ensuite sur **Se connecter**.

    -   Cliquez sur la deuxième table **PMResult**, puis cliquez sur ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-navigation.png) en regard de **Source** sous **ÉTAPES APPLIQUÉES** dans le volet de droite **Paramètres de requête**, puis mettez à jour les noms de serveur et de base de données comme dans les étapes ci-dessus et cliquez sur OK.

    -   Une fois redirigé sur la page précédente, fermez la fenêtre. Un message s’affiche ; cliquez sur **Appliquer**. Pour finir, cliquez sur le bouton **Enregistrer** pour enregistrer les modifications. Votre fichier Power BI a maintenant établi la connexion au serveur. Si vos visualisations sont vides, veillez à désactiver les sélections de visualisation pour visualiser toutes les données en cliquant sur l’icône de suppression en haut à droite des légendes. Utilisez le bouton Actualiser pour afficher les nouvelles données sur les visualisations. Au départ, vous verrez uniquement les données d’amorçage sur vos visualisations, étant donné que la fabrique de données est planifiée pour s’actualiser toutes les 3 heures. Au bout de 3 heures, les nouvelles prédictions sont reflétées dans vos visualisations lorsque vous actualisez les données.

3.  (Facultatif) Publier le tableau de bord de chemin à froid dans [Power BI en ligne](http://www.powerbi.com/). Notez que vous avez besoin d’un compte Power BI (ou d’un compte Office 365) pour cette étape.

    -   Cliquez sur **Publier** et attendez quelques secondes ; une fenêtre s’affiche avec une coche verte pour confirmer que la publication dans Power BI a bien été effectuée. Cliquez sur le lien sous « Ouvrir PredictiveMaintenanceAerospace.pbix dans Power BI ». Pour obtenir des instructions détaillées, consultez la page [Publier à partir de Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Pour créer un tableau de bord : cliquez sur le signe **+** en regard de la section **Tableaux de bord** dans le volet gauche. Entrez le nom de ce tableau de bord, dans notre exemple « Démo de maintenance prédictive ».

    -   Après avoir ouvert le rapport, cliquez sur ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-pin.png) pour épingler toutes les visualisations à votre tableau de bord. Pour obtenir des instructions détaillées, consultez la page [Épingler une vignette à un tableau de bord Power BI à partir d’un rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report). Accédez à la page Tableau de bord et ajustez la taille et l’emplacement de vos visualisations, puis modifier leurs titres. Pour obtenir des instructions détaillées sur la modification de vos vignettes, consultez la page [Modifier une vignette -- redimensionner, déplacer, renommer, épingler, supprimer, ajouter un lien hypertexte](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Voici un exemple de tableau de bord sur lequel sont épinglées des visualisations de chemin à froid. Selon la durée pendant laquelle vous exécutez votre simulateur de données, les numéros des visualisations peuvent être différents. <br/> ![](media\cortana-analytics-technical-guide-predictive-maintenance\final-view.png) <br/>
    -   Pour planifier l’actualisation des données, placez votre souris sur le jeu de données **PredictiveMaintenanceAerospace**, cliquez sur ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-elipsis.png), puis sélectionnez **Planifier l’actualisation**. <br/> **Remarque :** si vous voyez un message d’avertissement, cliquez sur **Modifier les informations d’identification** et assurez-vous que vos informations d’identification de base de données sont les mêmes que celles décrites à l’étape 1. <br/> ![](media\cortana-analytics-technical-guide-predictive-maintenance\schedule-refresh.png) <br/>
    -   Développez la section **Planifier l’actualisation**. Activez l’option Maintenir vos données à jour. <br/>
    -   Planifiez l’actualisation selon vos besoins. Pour plus d’informations, consultez la page [Actualisation des données dans Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### Configuration du tableau de bord de chemin à chaud

Dans les étapes suivantes, nous allons vous expliquer comment visualiser la sortie de données en temps réel des tâches Stream Analytics qui ont été générées au moment du déploiement de la solution. Les étapes suivantes nécessitent un compte [Power BI en ligne](http://www.powerbi.com/). Si vous ne possédez pas de compte, vous pouvez en [créer un](https://powerbi.microsoft.com/pricing).

1.  Ajouter une sortie Power BI dans Azure Stream Analytics (ASA).

    -  Pour configurer la sortie de votre tâche Azure Stream Analytics en tant que tableau de bord Power BI, vous devez suivre les instructions contenues de [Azure Stream Analytics et Power BI : tableau de bord d’analyse permettant de visualiser en temps réel les données de diffusion en continu](stream-analytics-power-bi-dashboard.md).
	- Recherchez la tâche Stream Analytics **maintenancesa02asapbi** dans le [portail Azure](https://manage.windowsazure.com).
	- Configurez les trois sorties de la requête ASA, à savoir **aircraftmonitor**, **aircraftalert** et **flightsbyhour**. Assurez-vous que les paramètres **Alias de sortie**, **Nom du jeu de données** et **Nom de la table** sont identiques à ceux de votre requête (**aircraftmonitor**, **aircraftalert** et **flightsbyhour**). Une fois que vous avez ajouté les trois tables de sortie et démarré la tâche Stream Analytics, vous devriez obtenir un message de confirmation (*du type* « La tâche Stream Analytics maintenancesa02asapbi a correctement démarré »).

2. Se connecter à [Power BI en ligne](http://www.powerbi.com)

    -   Dans Mon espace de travail, dans la section Jeux de données du volet gauche, les noms de ***JEUX DE DONNÉES*** **aircraftmonitor**, **aircraftalert** et **flightsbyhour** définis précédemment dans les paramètres de sortie Power BI de la tâche ASA doivent apparaître.

    -   Assurez-vous que le volet ***Visualisations*** est ouvert et qu’il s’affiche à droite de l’écran.

3. Créer la vignette « Fleet View of Sensor 11 vs. Threshold 48.26 » :

    -   Cliquez sur le jeu de données **aircraftmonitor** dans la section Jeux de données du volet gauche.

    -   Cliquez sur l’icône **Courbes**.

    -   Cliquez sur **Traités** dans le volet **Champs** afin de l’afficher sous « Axe » dans le volet **Visualisations**.

    -   Cliquez sur « s11 » et « s11\_alert » pour faire apparaître les deux sous « Valeurs ». Cliquez sur la flèche située en regard de **s11** et **s11\_alert** et remplacez « Somme » par « Moyenne ».

    -   Cliquez sur **ENREGISTRER** en haut de l’écran et nommez le rapport « aircraftmonitor ». Le rapport « aircraftmonitor » s’affichera dans la section **Rapports** du volet **Navigateur** de gauche.

    -   Cliquez sur l’icône **Épingler le visuel** en haut à droite de ce graphique en courbes. Une fenêtre « Épingler au tableau de bord » peut apparaître pour vous permettre de sélectionner un tableau de bord. Sélectionnez « Démo de Maintenance prédictive », puis cliquez sur « Épingler ».

    -   Dans le tableau de bord, pointez la souris sur cette vignette, cliquez sur l’icône Modifier située en haut à droite pour donner le titre « Fleet View of Sensor 11 vs. Threshold 48.26 » et le sous-titre « Average across fleet over time ».

4.  Créer d’autres vignettes de tableau de bord en fonction des jeux de données appropriés. Voici un exemple de tableau de bord sur lequel sont épinglées des visualisations de chemin à chaud. Selon la durée pendant laquelle vous exécutez votre simulateur de données, les numéros des visualisations peuvent être différents.

    ![](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

## **Outils d’estimation des coûts**

Les deux outils suivants peuvent vous aider à mieux comprendre les coûts impliqués dans l’exécution du modèle de solution de maintenance prédictive pour le secteur aérospatial dans votre abonnement :

-   [Microsoft Azure Cost Estimator Tool (en ligne)](https://azure.microsoft.com/pricing/calculator/)

-   [Microsoft Azure Cost Estimator Tool (de bureau)](http://www.microsoft.com/download/details.aspx?id=43376)

<!---HONumber=AcomDC_0128_2016-->