<properties 
	pageTitle="FAQ Azure Machine Learning Studio | Microsoft Azure" 
	description="Présentation d'Azure Machine Learning : FAQ portant sur la facturation, les fonctionnalités et les limitations d'un service cloud pour la modélisation prédictive rationalisée." 
	keywords="machine learning introduction,predictive modeling,what is machine learning"
	services="machine-learning" 
	documentationCenter="" 
	authors="pablissima" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/07/2015" 
	ms.author="paulettm"/>

#Forum Aux Questions (FAQ) Azure Machine Learning : facturation, fonctionnalités, limitations et support

Ce Forum Aux Questions contient des questions et des réponses sur Azure Machine Learning, un service cloud pour la modélisation prédictive et l’opérationnalisation des solutions via les services web. Ce FAQ couvre les questions sur l'utilisation du service, notamment le modèle de facturation, les fonctionnalités, les limitations et le support.
 
##Questions générales

**Qu'est-ce que Microsoft Azure Machine Learning ?**
 
Microsoft Azure Machine Learning est un service entièrement géré qui permet de créer, de tester, de faire fonctionner et de gérer des solutions analytiques prédictives dans le cloud. À l’aide d’un simple navigateur, vous pouvez vous inscrire, télécharger des données et commencer immédiatement des expériences d’apprentissage automatique. Grâce à la modélisation prédictive par glisser-déplacer, la vaste palette de modules et la bibliothèque de modèles de départ, les tâches d'apprentissage automatique les plus courantes sont simples et rapides. Pour plus d’informations, consultez la [Vue d’ensemble du service Azure Machine Learning](/services/machine-learning/). Pour une présentation de l'apprentissage automatique couvrant la terminologie et les concepts principaux, consultez [Présentation d'Azure Machine Learning](machine-learning-what-is-machine-learning.md).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
**Qu'est-ce que Machine Learning Studio ?**

Machine Learning Studio est un environnement de banc d’essai accessible via un navigateur Web. Machine Learning Studio héberge une palette de modules avec une interface de composition visuelle vous permettant de créer un workflow de science des données de bout en bout sous la forme d’une expérience.

Pour des informations générales sur Machine Learning Studio, consultez [Présentation de Machine Learning Studio](machine-learning-what-is-ml-studio.md).

**Qu'est-ce que le service API Machine Learning ?**

Le service API Machine Learning vous permet de déployer des modèles prédictifs conçus dans Machine Learning Studio en tant que services Web évolutifs et à tolérance de pannes. Les services Web créés par le service API Machine Learning sont des API REST qui fournissent une interface pour les communications entre les applications externes et vos modèles analytiques prédictifs.

Voir [Connexion à un service Web Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md) pour plus d’informations.


##Questions sur la facturation

**Comment fonctionne la facturation dans Machine Learning ?**

Pour des informations sur la facturation et la tarification, consultez [Tarification Machine Learning](http://azure.microsoft.com/pricing/details/machine-learning/).

**Une version d’évaluation de Machine Learning est-elle disponible ?**

 Lorsque vous vous inscrivez pour obtenir la version gratuite d’Azure, vous pouvez essayer n’importe quel service Azure pendant un mois. Pour en savoir plus sur la version d’évaluation gratuite Azure, visitez la page [Forum aux questions sur la version d’évaluation gratuite d’Azure](/pricing/free-trial-faq/).

## Questions sur Machine Learning Studio

###Création d’une expérience	
**Existe-t-il un contrôle de version ou une intégration Git pour les graphiques d’expérience ?**

Non. Toutefois, chaque fois qu’un test est exécuté, la version du graphique est conservée et ne peut pas être modifiée par d’autres utilisateurs.

###Importation et exportation de données pour Machine Learning
**Quelles sources de données sont prises en charge par Machine Learning ?**

Les données peuvent être chargées dans Machine Learning Studio de deux façons : en téléchargeant les fichiers locaux en tant que jeu de données ou en utilisant le module de lecture pour importer les données. Les fichiers locaux peuvent être téléchargés en ajoutant de nouveaux jeux de données dans Machine Learning Studio. Voir [Importez vos données d’apprentissage dans Azure Machine Learning Studio](machine-learning-import-data.md) pour en savoir plus sur les formats de fichiers pris en charge.


####<a id="ModuleLimit"></a>Quelle est la taille maximale du jeu de données de mes modules ?

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

Si vous avez une petite quantité de données et que vous souhaitez l’exposer via une URL http, vous pouvez utiliser le module [Reader][reader]. Transférez les grandes quantités de données vers Azure Storage, puis utilisez le module [Reader][reader] pour les importer dans votre expérience. <!--
<SEE CLOUD DS PROCESS>
-->

**Existe-t-il une capacité d’entrée d’image intégrée ?**

Vous pouvez en savoir plus sur la capacité d’entrée d’image dans la référence du [lecteur d’images][image-reader].

###Modules 

**L’algorithme, la source de données, le format de données ou l’opération de transformation des données que je recherche n’est pas disponible dans Azure ML Studio. Que puis-je faire ?**

Vous pouvez consulter le [forum des commentaires des utilisateurs](http://go.microsoft.com/fwlink/?LinkId=404231) pour afficher les demandes de fonctionnalités dont nous effectuons le suivi. Ajoutez votre vote à cette demande si une fonctionnalité que vous recherchez a déjà été demandée. Si la fonctionnalité que vous recherchez n'existe pas, créez une nouvelle demande. Vous pouvez également afficher le statut de votre demande dans ce forum. Nous suivons cette liste de près et mettons à jour régulièrement le statut de disponibilité des fonctionnalités. En outre, avec la prise en charge intégrée de R et de Python, des transformations personnalisées peuvent être créées en fonction des besoins.


**Puis-je importer mon code existant dans ML Studio ?**

Oui. Vous pouvez importer votre code R existant dans ML Studio et l’exécuter au sein de la même expérience avec les modules d’apprentissage fournis par Azure Machine Learning. Vous pouvez ensuite le publier en tant que service Web au moyen d’Azure Machine Learning. Consultez [Prolongez votre expérience avec R](machine-learning-extend-your-experiment-with-r.md).

**Est-il possible d’utiliser [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language), par exemple, pour définir un modèle ?**

Non, cela n’est pas pris en charge, mais du code R ou Python personnalisé peut être utilisé pour définir un module.


###Traitement des données 
**Existe-t-il une possibilité de visualiser les données (autre que les visualisations R) de manière interactive au sein de l’expérience ?**

En cliquant sur la sortie d’un module, vous pouvez visualiser les données et obtenir des statistiques.

**Lors de l’aperçu des résultats ou des données dans le navigateur, le nombre de lignes et de colonnes est limité. Pourquoi ?**

Dans la mesure où les données sont transmises au navigateur et peuvent être volumineuses, la taille des données est limitée afin d’éviter de ralentir ML Studio. Il est préférable de télécharger les données/résultats et d’utiliser Excel ou un autre outil pour visualiser l’ensemble des données.

###Algorithmes
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

###Module R 
**Quels packages R sont disponibles dans Machine Learning Studio ?**

Actuellement, Machine Learning Studio prend en charge plus de 400 packages R et cette liste est en constante évolution. Voir [Prolongez votre expérience avec R](machine-learning-extend-your-experiment-with-r.md) pour des informations sur l’obtention de la liste des packages R pris en charge. Si le package souhaité ne figure pas dans cette liste, indiquez son nom sur le [forum de commentaires des utilisateurs](http://go.microsoft.com/fwlink/?LinkId=404231).

**Est-il possible de créer un module R personnalisé ?**

Oui. Consultez [Créer des modules R personnalisés dans Microsoft Azure Machine Learning](machine-learning-custom-r-modules.md) pour plus d’informations.

**Existe-t-il un environnement REPL pour R ?**

Non. Il n’existe aucun environnement REPL pour R dans Studio.

###Module Python 

**Est-il possible de créer un module Python personnalisé ?**

Actuellement non. Toutefois, le module Python standard ou un jeu de modules permet d’obtenir le même résultat.

**Existe-t-il un environnement REPL pour Python ?**

Non. Il n’existe aucun environnement REPL pour Python dans Studio.

## Service Web
###Recyclage de modèles par programme

**Comment recycler les modèles AzureML par programme ?** Utilisez les API de recyclage. Un exemple de code est disponible [ici](https://azuremlretrain.codeplex.com/).

###Créer

**Puis-je déployer le modèle localement ou dans une application sans connexion Internet ?** Non.


**Existe-t-il une latence de référence attendue pour tous les services Web ?**

Consultez les [Limites d’abonnement Azure](../azure-subscription-service-limits.md)

###Utilisation
**Quand ai-je avantage à exécuter mon modèle prédictif en tant que service d’exécution de lots plutôt qu’un service Web requête-réponse ?**

Le service de requête-réponse (RRS, Request-Response Service) est un service Web à faible latence et à grande échelle qui permet de fournir une interface aux modèles sans état créés et publiés à partir de l’environnement Expérimentation. Le service d’exécution de lots (BES, Batch Execution Service) est un service de notation asynchrone des enregistrements de données. L’entrée de données pour le service BES est similaire à celle utilisée dans le service RRS. La différence principale réside dans le fait que le service BES lit un bloc d'enregistrements à partir de diverses sources telles que le service BLOB et de tables dans Azure, Base de données SQL Azure, HDInsight (requêtes Hive) et les sources HTTP. Pour plus d’informations, consultez [Utilisation des service Web Microsoft Azure Machine Learning](machine-learning-consume-web-services.md).

**Comment puis-je mettre à jour le modèle pour le service Web déployé ?**

Pour mettre à jour un modèle prédictif pour un service déjà déployé, il suffit de modifier et de réexécuter l'expérience utilisée pour créer et enregistrer le modèle formé. Une fois que la nouvelle version du modèle formé est disponible, ML Studio vous invite à indiquer si vous voulez mettre à jour votre service web en phase intermédiaire. Lorsque la mise à jour est appliquée au service Web en phase intermédiaire, la même mise à jour est disponible pour vous permettre de l’appliquer au service Web de production également. Consultez la rubrique [Publication d’un service Web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md) pour plus d’informations sur la mise à jour d’un service Web déployé.


**Comment puis-je surveiller mon service Web déployé en production ?**

Une fois qu’un modèle prédictif a été mis en production, vous pouvez le surveiller à partir du portail Azure. Chaque service déployé dispose de son propre tableau de bord, à partir duquel vous pouvez consulter les informations de surveillance correspondant à ce service.

**Existe-t-il un endroit où je peux voir le résultat de mon service RRS/BES ?**

Oui. Vous devez fournir un emplacement de stockage d’objets blob où sera placée la sortie de RRS/BES.



##Extensibilité 

**Quelle est l’évolutivité du service Web ?**

Actuellement, la valeur maximale est de 20 demandes simultanées par point de terminaison, même si elle peut s’adapter à 80 points de terminaison. Cela se traduit par 4 800 demandes simultanées si nous utilisons toutes les ressources (300 travaux).


**Les travaux R sont-ils répartis entre les nœuds ?**

Non.


**Quelle quantité de données puis-je utiliser pour l’apprentissage ?**

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


**Existe-t-il des limitations de taille de vecteurs ?**

Les lignes et colonnes sont limitées selon la limitation Max Int de .NET : 2 147 483 647.

**La taille de machine virtuelle sur laquelle le service est exécuté peut-elle être ajustée ?**

Non.

##Sécurité et disponibilité 

**Qui a accès au point de terminaison HTTP pour le service Web déployé en production par défaut ? Comment puis-je limiter les accès au point de terminaison ?**

Une fois qu'un modèle prédictif a été mis en production, le portail Azure répertorie l'URL pour les services web déployés. Les URL de service en phase intermédiaire sont accessibles à partir de l’environnement Machine Learning Studio sous la section des services Web, et les URL de service de production sont accessibles à partir du portail Azure, sous la section Machine Learning. Des clés d’accès sont fournies pour les services Web de phase intermédiaire et de production pour le tableau de bord du service Web, respectivement dans Machine Learning Studio et l’environnement de portail Azure. Elles sont nécessaires pour effectuer des appels au service web en production et en phase intermédiaire. Pour plus d’informations, voir [Connexion à un service Web Machine Learning](machine-learning-connect-to-azure-machine-learning-web-service.md).

**Que se passe-t-il si mon compte de stockage est introuvable ?**

Machine Learning Studio s’appuie sur un compte de stockage Azure fourni par l’utilisateur pour enregistrer les données intermédiaires lors de l’exécution du workflow. Ce compte de stockage est fourni à Machine Learning Studio lors de la création d’un espace de travail. Une fois l’espace de travail créé, si le compte de stockage est supprimé et ne peut plus être trouvé, l’espace de travail cesse de fonctionner et toutes les expériences de cet espace de travail échouent.
 
Si vous avez supprimé par inadvertance le compte de stockage, la seule façon de le récupérer consiste à recréer ce compte de stockage avec exactement le même nom, dans la même région que celui que vous avez supprimé. Après cela, effectuez une nouvelle synchronisation de la clé d’accès.
 

**Que se passe-t-il si ma clé d’accès au compte de stockage n’est pas synchronisée ?** Machine Learning Studio s’appuie sur un compte de stockage Azure fourni par l’utilisateur pour enregistrer les données intermédiaires lors de l’exécution du workflow. Ce compte de stockage est fourni à Machine Learning Studio lors de la création d’un espace de travail et les clés d’accès sont associées à cet espace de travail. Une fois l’espace de travail créé, si les clés d’accès sont modifiées, l’espace de travail ne peut plus accéder au compte de stockage et cesse de fonctionner. Toutes les expériences de cet espace de travail échouent.

Si vous avez modifié les clés d’accès de compte de stockage, veillez à resynchroniser les clés d’accès dans le paramètre d’espace de travail dans le portail Azure.


##Azure Marketplace 

Consultez le [Forum aux questions pour la publication et l’utilisation d’applications Machine Learning sur Azure Marketplace](machine-learning-marketplace-faq.md)

##Support et formations 

**Où puis-je obtenir des formations pour Azure ML ?**

La [Documentation du centre Azure Machine Learning](/services/machine-learning/) héberge des didacticiels vidéo ainsi que des manuels d’utilisation. Ces derniers présentent les services et passent en revue le cycle de vie de science des données lié à l’importation et au nettoyage des données, à la conception de modèles prédictifs et à leur déploiement en production avec Azure ML.

Nous continuerons à ajouter régulièrement de nouveaux documents dans le Centre Machine Learning. Vous pouvez envoyer des demandes pour obtenir des documents supplémentaires sur le Centre Machine Learning à partir du [forum de commentaires des utilisateurs](https://windowsazure.uservoice.com/forums/257792-machine-learning).

Vous pouvez également rechercher des formations sur [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning)

**Comment puis-je bénéficier d’un support pour Azure Machine Learning ?**

Pour obtenir un support technique pour Azure Machine Learning, accédez au [Support Azure](/support/options/) et sélectionnez **Machine Learning**.

Azure Machine Learning dispose également d'un forum communautaire sur MSDN, où vous pouvez poser des questions en rapport avec Azure ML. Le forum est géré par l'équipe d'Azure ML. Visitez le [Forum Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 

<!---HONumber=July15_HO1-->