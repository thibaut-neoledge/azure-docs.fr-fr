<properties
	pageTitle="FAQ Azure Machine Learning Studio | Microsoft Azure"
	description="Présentation d'Azure Machine Learning : FAQ portant sur la facturation, les fonctionnalités et les limitations d'un service cloud pour la modélisation prédictive rationalisée."
	keywords="introduction à l’apprentissage automatique, modélisation prédictive, présentation de l’apprentissage automatique"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/18/2016"
	ms.author="garye"/>

# Forum Aux Questions (FAQ) Azure Machine Learning : facturation, fonctionnalités, limitations et support

Ce Forum Aux Questions contient des questions et des réponses sur Azure Machine Learning, un service cloud pour le développement de modèles prédictifs et l’opérationnalisation des solutions via les services web. Ce FAQ couvre les questions sur l'utilisation du service, notamment le modèle de facturation, les fonctionnalités, les limitations et le support.

## Questions générales

**Qu'est-ce que Microsoft Azure Machine Learning ?**

Microsoft Azure Machine Learning est un service entièrement géré qui permet de créer, de tester, de faire fonctionner et de gérer des solutions analytiques prédictives dans le cloud. À l’aide d’un simple navigateur, vous pouvez vous inscrire, télécharger des données et commencer immédiatement des expériences d’apprentissage automatique. Grâce à la modélisation prédictive par glisser-déplacer, la vaste palette de modules et la bibliothèque de modèles de départ, les tâches d'apprentissage automatique les plus courantes sont simples et rapides. Pour plus d’informations, consultez la [Vue d’ensemble du service Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Pour une présentation de l'apprentissage automatique couvrant la terminologie et les concepts principaux, consultez [Présentation d'Azure Machine Learning](machine-learning-what-is-machine-learning.md).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Qu'est-ce que Machine Learning Studio ?**

Machine Learning Studio est un environnement de banc d’essai accessible via un navigateur Web. Machine Learning Studio héberge une palette de modules avec une interface de composition visuelle vous permettant de créer un flux de travail de science des données de bout en bout sous la forme d’une expérience.

Pour plus d’informations sur Machine Learning Studio, consultez [Présentation de Machine Learning Studio](machine-learning-what-is-ml-studio.md).

**Qu'est-ce que le service API Machine Learning ?**

Le service d’API Machine Learning vous permet de déployer des modèles prédictifs tels que ceux intégrés dans Machine Learning Studio sous forme de services web évolutifs tolérants aux erreurs. Les services Web créés par le service API Machine Learning sont des API REST qui fournissent une interface pour les communications entre les applications externes et vos modèles analytiques prédictifs.

Voir [Connexion à un service Web Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md) pour plus d’informations.


## Questions sur la facturation

**Comment fonctionne la facturation dans Machine Learning ?**

Pour des informations sur la facturation et la tarification, consultez [Tarification Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

**Une version d’évaluation de Machine Learning est-elle disponible ?**

 Lorsque vous vous inscrivez pour obtenir la version gratuite d’Azure, vous pouvez essayer n’importe quel service Azure pendant un mois. Pour en savoir plus sur la version d’évaluation gratuite d’Azure, visitez [FAQ sur la version d’évaluation d’Azure](/pricing/free-trial-faq/).

## Questions sur Machine Learning Studio

### Création d’une expérience

**Existe-t-il un contrôle de version ou une intégration Git pour les graphiques d’expérience ?**

Non, cependant Machine Learning Studio conserve chaque itération d’une expérience, qui ne peut pas être modifiée par d’autres utilisateurs. Pour en savoir plus, consultez la page [Gérer les itérations des expériences dans Machine Learning Studio](machine-learning-manage-experiment-iterations.md).

### Importation et exportation de données pour Machine Learning

**Quelles sources de données sont prises en charge par Machine Learning ?**

Les données peuvent être chargées dans l’expérience Machine Learning Studio de l’une des trois manières suivantes : en chargeant un fichier local sous forme de jeu de données, en utilisant un module pour importer des données provenant de services de données cloud, ou en important un jeu de données enregistré depuis une autre expérience. Voir [Importez vos données d’apprentissage dans Azure Machine Learning Studio](machine-learning-data-science-import-data.md) pour en savoir plus sur les formats de fichiers pris en charge.


#### <a id="ModuleLimit"></a>Quelle est la taille maximale du jeu de données de mes modules ?

Les modules de Machine Learning Studio prennent en charge les jeux de données d’une taille maximale de 10 Go de données numériques denses pour les scénarios d’utilisation courants. Si un module accepte plusieurs entrées, le total de toutes les tailles d’entrée est de 10 Go. L'échantillonnage de jeux de données plus importants par le biais de requêtes Hive ou de requêtes de base de données SQL ou via un prétraitement Learning by Counts avant l'ingestion est également pris en charge.

Les types de données suivants peuvent être développés en jeux de données plus importants lors de la normalisation des fonctionnalités et sont limités à moins de 10 Go :

- Partiellement alloué
- Par catégorie
- Chaînes
- Données binaires

Les modules suivants sont limités à des jeux de données inférieurs à 10 Go :

- modules de recommandation
- module SMOTE
- modules de script : R, Python, SQL
- modules dont la taille des données de sortie peut être supérieure à la taille des données d’entrée, comme Join ou Feature Hashing.
- la validation croisée, les paramètres de balayage, la régression ordinale et plusieurs classes de un contre tous, lorsque le nombre d’itérations est très élevé.

Pour les tailles de jeux de données supérieures à quelques gigaoctets, la méthode conseillée consiste à télécharger les données sur Azure Storage ou Base de données SQL (Azure), ou à utiliser HDInsight, au lieu d'effectuer un téléchargement directement à partir d'un fichier local.


####<a id="UploadLimit"></a>Quelles sont les limites de téléchargement de données ?
Pour les tailles de jeux de données supérieures à quelques gigaoctets, téléchargez les données sur Azure Storage ou Base de données SQL Azure, ou utilisez HDInsight, au lieu d'effectuer un téléchargement directement à partir d'un fichier local.

**Puis-je lire les données à partir d’Amazon S3 ?**

Si vous avez une petite quantité de données et que vous souhaitez l’exposer via une URL http, vous pouvez utiliser le module [Reader][reader]. Transférez les grandes quantités de données vers Azure Storage, puis utilisez le module [Reader][reader] pour les importer dans votre expérience.
<!--
<SEE CLOUD DS PROCESS>
-->

**Existe-t-il une capacité d’entrée d’image intégrée ?**

Découvrez la capacité d’entrée d’image intégrée dans la référence [Importer des images][image-reader].

### Modules

**L’algorithme, la source de données, le format de données ou l’opération de transformation des données que je recherche n’est pas Azure Machine Learning Studio. Quelles sont mes options ?**

Vous pouvez visiter le [forum des commentaires des utilisateurs](http://go.microsoft.com/fwlink/?LinkId=404231) pour voir les requêtes de fonctionnalité que vous suivez. Ajoutez votre vote à une requête si une fonctionnalité que vous recherchez a déjà fait l’objet d’une requête. Si la fonctionnalité que vous recherchez n’existe pas, créez une nouvelle requête. Vous pouvez également afficher le statut de votre demande dans ce forum. Nous suivons cette liste de près et mettons à jour régulièrement le statut de disponibilité des fonctionnalités. Par ailleurs, la prise en charge intégrée pour R et Python permet de créer des transformations personnalisées en fonction des besoins.


**Puis-je importer mon code existant dans Machine Learning Studio ?**

Oui. Vous pouvez importer votre code R ou Python existant dans Machine Learning Studio, l’exécuter au sein de la même expérience avec Azure Machine Learning, puis déployer la solution en tant que service web via Azure Machine Learning. Pour plus d'informations, consultez [Prolongez votre expérience avec R](machine-learning-extend-your-experiment-with-r.md) et [Exécution des scripts d’apprentissage automatique Python dans Azure Machine Learning Studio](machine-learning-execute-python-scripts.md).

**Est-il possible d’utiliser [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language), par exemple, pour définir un modèle ?**

Non, cela n’est pas pris en charge, mais du code R ou Python personnalisé peut être utilisé pour définir un module.

**Combien de modules puis-je exécuter en parallèle dans mon expérience ?**

Vous pouvez exécuter jusqu’à 4 modules en parallèle dans une expérience.


### Traitement des données

**Existe-t-il une possibilité de visualiser les données (autre que les visualisations R) de manière interactive au sein de l’expérience ?**

En cliquant sur la sortie d’un module, vous pouvez visualiser les données et obtenir des statistiques.

**Lors de l’aperçu des résultats ou des données dans le navigateur, le nombre de lignes et de colonnes est limité. Pourquoi ?**

Dans la mesure où les données sont transmises au navigateur et peuvent être volumineuses, la taille des données est limitée afin d’éviter de ralentir Machine Learning Studio. Pour visualiser les données/résultats, il est préférable de télécharger les données et d’utiliser Excel ou un autre outil.

### Algorithmes

**Quels sont les algorithmes ML existants pris en charge dans Machine Learning Studio ?**

Machine Learning Studio fournit des algorithmes de pointe tels que les arbres de décision optimisés évolutifs, les systèmes de recommandation bayésiens, les réseaux neuronaux profonds et les jungles de décision développés chez Microsoft Research. Des modules d’apprentissage automatique open source évolutifs tels que Vowpal Wabbit sont également inclus. Machine Learning Studio prend en charge les algorithmes d’apprentissage automatique pour la classification, la régression et le clustering multiclasses et binaires. Consultez la liste complète des [Modules de Machine Learning][machine-learning-modules].

**Suggérez-vous automatiquement le bon algorithme Machine Learning à utiliser pour mes données ?**

Non. Toutefois, Machine Learning Studio propose plusieurs façons de comparer les résultats de chaque algorithme afin de déterminer le plus approprié dans votre cas.

**Y a-t-il des instructions sur le choix d’un algorithme plutôt qu’un autre pour les algorithmes fournis ?** Voir [Comment choisir un algorithme](machine-learning-algorithm-choice.md).

**Les algorithmes fournis sont-ils écrits en Python ou en R ?**

Non. Ces algorithmes sont principalement écrits dans des langages compilés pour obtenir de meilleures performances.

**Des détails relatifs aux algorithmes sont-ils disponibles ?**

La documentation présente des informations sur les algorithmes, et les paramètres fournis pour le paramétrage sont décrits afin d’optimiser l’algorithme pour votre usage.

**Existe-t-il une prise en charge pour l’apprentissage en ligne ?**

Non. Actuellement, seul le recyclage par programme est pris en charge.

**Est-il possible de visualiser les couches d’un modèle de réseau neuronal à l’aide du module intégré ?**

Non.

**Puis-je créer mes propres modules en C# ou un autre langage ?**

Actuellement, les modules personnalisés peuvent uniquement être créés en R.

### Module R

**Quels packages R sont disponibles dans Machine Learning Studio ?**

Machine Learning Studio prend actuellement en charge les packages 400+ CRAN R. Voici la [liste actuelle](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de tous les packages inclus. Consultez également [Prolongez votre expérience avec R ](machine-learning-extend-your-experiment-with-r.md) pour savoir comment récupérer vous-même cette liste. Si le package souhaité ne figure pas dans cette liste, indiquez son nom sur le [forum de commentaires des utilisateurs](http://go.microsoft.com/fwlink/?LinkId=404231).

**Est-il possible de créer un module R personnalisé ?**

Oui. Consultez [Créer des modules R personnalisés dans Microsoft Azure Machine Learning](machine-learning-custom-r-modules.md) pour plus d’informations.

**Existe-t-il un environnement REPL pour R ?**

Non. Il n’existe aucun environnement REPL pour R dans Studio.

### Module Python

**Est-il possible de créer un module Python personnalisé ?**

Pas actuellement, mais vous pouvez utiliser un ou plusieurs modules [exécuter le Script Python][python] pour obtenir le même résultat.

**Existe-t-il un environnement REPL pour Python ?**

Vous pouvez utiliser les Notebooks Jupyter dans Machine Learning Studio. Pour plus d’informations, consultez [Présentation des notebooks Jupyter dans Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## Service Web

###Recyclage de modèles par programme

**Comment reformer les modèles Azure Machine Learning par programme ?**

Utilisez les API de recyclage. Consultez [Réformation des modèles Machine Learning par programme](machine-learning-retrain-models-programmatically.md) pour plus d’informations. Un exemple de code est également disponible dans la [Démonstration de recyclage de formation Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

### Créer

**Puis-je déployer le modèle localement ou dans une application sans connexion Internet ?**

Non.


**Existe-t-il une latence de référence attendue pour tous les services Web ?**

Consultez les [Limites d’abonnement Azure](../azure-subscription-service-limits.md)

### Utilisation

**Quand ai-je avantage à exécuter mon modèle prédictif en tant que service d’exécution de lots plutôt qu’un service Web requête-réponse ?**

Le service de requête-réponse (RRS, Request-Response Service) est un service web à faible latence et à grande échelle qui permet de fournir une interface aux modèles sans état créés et déployés à partir de l’environnement Expérimentation. Le service d’exécution de lots (BES, Batch Execution Service) est un service de notation asynchrone des enregistrements de données. L’entrée de données pour le service BES est similaire à celle utilisée dans le service RRS. La différence principale réside dans le fait que le service BES lit un bloc d'enregistrements à partir de diverses sources telles que le service BLOB et de tables dans Azure, Base de données SQL Azure, HDInsight (requêtes Hive) et les sources HTTP. Pour plus d’informations, consultez [Utilisation des service Web Microsoft Azure Machine Learning](machine-learning-consume-web-services.md).

**Comment puis-je mettre à jour le modèle pour le service Web déployé ?**

Pour mettre à jour un modèle prédictif pour un service déjà déployé, il suffit de modifier et de réexécuter l'expérience utilisée pour créer et enregistrer le modèle formé. Une fois qu’une nouvelle version du modèle formé est disponible, Machine Learning Studio vous demande si vous voulez mettre à jour votre service web. Pour plus d’informations sur la mise à jour d’un service web déployé, consultez [Déploiement d’un service web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

Vous pouvez également utiliser les API de recyclage. Consultez [Réformation des modèles Machine Learning par programme](machine-learning-retrain-models-programmatically.md) pour plus d’informations. Un exemple de code est également disponible dans la [Démonstration de recyclage de formation Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/).

**Comment puis-je surveiller mon service web déployé en production ?**

Une fois qu’un modèle prédictif a été déployé, vous pouvez le surveiller à partir du portail Azure Classic. Chaque service déployé dispose de son propre tableau de bord, à partir duquel vous pouvez consulter les informations de surveillance correspondant à ce service.

**Existe-t-il un endroit où je peux voir le résultat de mon service RRS/BES ?**

Pour les enregistrements de ressources, c'est généralement dans la réponse du service web que vous voyez le résultat. Vous pouvez également l'écrire dans Azure Blob Storage. Pour les environnements d'initialisation, la sortie est écrite dans un objet blob par défaut. Vous pouvez également écrire la sortie dans une base de données ou une table à l'aide du module [Exporter les données][writer].

**Puis-je créer des services web uniquement à partir de modèles créés dans Machine Learning Studio ?**

Non. Vous pouvez également créer des services web directement à partir de Notebooks Jupyter et RStudio.

**Où puis-je trouver plus d'informations sur les codes d'erreur ?**

Consultez la page [Codes d'erreur de Module Machine Learning](https://msdn.microsoft.com/library/azure/dn905910.aspx) pour obtenir la liste des codes d'erreur et des descriptions.

## Extensibilité

**Quelle est l’évolutivité du service Web ?**

Actuellement, le point de terminaison par défaut est doté de 20 demandes RR simultanées par point de terminaison. Vous pouvez l’augmenter jusqu’à 200 demandes simultanées par point de terminaison et vous pouvez augmenter chaque service web jusqu’à 10 000 points de terminaison par service web, comme indiqué dans l’article [Mise à l’échelle des points de terminaison API](machine-learning-scaling-endpoints.md). Pour BES, chaque point de terminaison permet de traiter 40 demandes simultanées. Au-delà de 40 demandes, les demandes supplémentaires sont mises en file d’attente. Ces requêtes en file d’attente seront exécutées automatiquement au fur et à mesure que la file diminue.


**Les travaux R sont-ils répartis entre les nœuds ?**

Non.


**Quelle quantité de données puis-je utiliser pour l'apprentissage ?**

Les modules de Machine Learning Studio prennent en charge les jeux de données d’une taille maximale de 10 Go de données numériques denses pour les scénarios d’utilisation courants. Si un module prend plus d’une entrée, la taille totale de toutes les entrées est de 10 Go. L'échantillonnage de jeux de données plus importants par le biais de requêtes Hive ou de requêtes de base de données SQL, ou via un prétraitement avec des modules [ Learning by Counts][counts] avant l'ingestion est également pris en charge.

Les types de données suivants peuvent être développés en jeux de données plus importants lors de la normalisation des fonctionnalités et sont limités à moins de 10 Go :

- partiellement alloué
- par catégorie
- chaînes
- données binaires

Les modules suivants sont limités à des jeux de données inférieurs à 10 Go :

- modules de recommandation
- module SMOTE
- modules de script : R, Python, SQL
- modules dont la taille des données de sortie peut être supérieure à la taille des données d’entrée, comme Join ou Feature Hashing.
- La validation croisée, le réglage des hyperparamètres de modèle, la régression ordinale et plusieurs classes de un contre tous, lorsque le nombre d’itérations est très élevé.

Pour les jeux de données supérieurs à quelques gigaoctets, la méthode conseillée consiste à télécharger les données sur Azure Storage ou la base de données SQL Azure, ou à utiliser HDInsight au lieu d'effectuer un téléchargement directement à partir d'un fichier local.


**Existe-t-il des limitations de taille de vecteurs ?**

Les lignes et colonnes sont limitées selon la limitation Max Int de .NET : 2 147 483 647.

**La taille de la machine virtuelle utilisée pour exécuter le service web peut-elle être ajustée ?**

Non.

## Sécurité et disponibilité

**Qui a accès au point de terminaison http pour le service web par défaut ? Comment puis-je limiter l’accès au point de terminaison ?**

Après le déploiement d’un service web, un point de terminaison par défaut est créé pour ce service. Le point de terminaison par défaut peut être appelée à l'aide de sa clé d'API. Il est possible d'ajouter des points de terminaison supplémentaires avec leurs propres clés à partir du portail Azure Classic ou par le biais d'un programme à l'aide des API de gestion des services web. Des clés d’accès sont nécessaires pour effectuer des appels au service web. Pour plus d’informations, voir [Connexion à un service Web Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md).


**Que se passe-t-il si mon compte de stockage Asure est introuvable ?**

Machine Learning Studio s’appuie sur un compte de stockage Azure fourni par l’utilisateur pour enregistrer les données intermédiaires lors de l’exécution du flux de travail. Ce compte de stockage est fourni à Machine Learning Studio lors de la création d’un espace de travail. Une fois l’espace de travail créé, si le compte de stockage est supprimé et ne peut plus être trouvé, l’espace de travail cesse de fonctionner et toutes les expériences de cet espace de travail échouent.

Si vous avez supprimé par inadvertance le compte de stockage, la seule façon de le récupérer consiste à recréer ce compte de stockage avec exactement le même nom, dans la même région que celui que vous avez supprimé. Après cela, effectuez une nouvelle synchronisation de la clé d’accès.


**Que se passe-t-il si ma clé d’accès au compte de stockage n’est pas synchronisée ?**

Machine Learning Studio s’appuie sur un compte de stockage Azure fourni par l’utilisateur pour enregistrer les données intermédiaires lors de l’exécution du flux de travail. Ce compte de stockage est fourni à Machine Learning Studio lors de la création d’un espace de travail et les clés d’accès sont associées à cet espace de travail. Une fois l’espace de travail créé, si les clés d’accès sont modifiées, l’espace de travail ne peut plus accéder au compte de stockage et cesse de fonctionner. Toutes les expériences de cet espace de travail échouent.

Si vous avez modifié les clés d’accès du compte de stockage, veillez à resynchroniser les clés d’accès dans le paramètre d’espace de travail dans le portail Azure Classic.


## Azure Marketplace

Consultez le [Forum aux questions pour la publication et l’utilisation d’applications dans Machine Learning Marketplace](machine-learning-marketplace-faq.md)

## Support et formations

**Où puis-je obtenir des formations pour Azure Machine Learning ?**

La [Documentation du centre Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) héberge des didacticiels vidéo ainsi que des manuels d’utilisation. Ces derniers présentent les services et passent en revue le cycle de vie de science des données lié à l’importation et au nettoyage des données, à la conception de modèles prédictifs et à leur déploiement en production avec Azure Machine Learning.

Nous continuerons à ajouter régulièrement de nouveaux documents au Centre Machine Learning. Vous pouvez envoyer des demandes pour obtenir des documents supplémentaires sur le Centre Machine Learning à partir du [forum de commentaires des utilisateurs](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Vous pouvez également rechercher des formations sur [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Comment puis-je bénéficier d’un support pour Azure Machine Learning ?**

Pour obtenir un support technique pour Azure Machine Learning, accédez au [Support Azure](/support/options/) et sélectionnez **Machine Learning**.

Azure Machine Learning dispose également d'un forum communautaire sur MSDN, où vous pouvez poser des questions en rapport avec Azure Machine Learning. Le forum est géré par l'équipe d'Azure Machine Learning. Visitez le [Forum Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx

<!---HONumber=AcomDC_0420_2016-->