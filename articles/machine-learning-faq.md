<properties 
	pageTitle="Forum Aux Questions (FAQ) Azure Machine Learning | Azure" 
	description="Forum Aux Questions (FAQ) sur Microsoft Azure Machine Learning" 
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
	ms.date="02/17/2015" 
	ms.author="paulettm"/>

# Forum Aux Questions (FAQ) Microsoft Azure Machine Learning

### GÉNÉRAL

**1. Qu'est-ce que Microsoft Azure Machine Learning ?**

Microsoft Azure Machine Learning est un service entièrement géré qui permet de créer, de tester, de faire fonctionner et de gérer des solutions analytiques prédictives dans le cloud. À l'aide d'un simple navigateur, vous pouvez désormais vous inscrire pour accéder à Azure Machine Learning, télécharger des données et commencer immédiatement à faire l'expérience de l'apprentissage automatique (Machine Learning). La composition visuelle, la vaste palette de modules et la bibliothèque de modèles de mise en route simplifient et optimisent les tâches d'apprentissage automatique. Il est facile de convertir un modèle en un service web : en quelques clics, un modèle prédictif conçu dans ML Studio peut être converti en API REST publique encapsulant la logique de transformation de données personnalisée et des modèles complexes d'apprentissage automatique.

**2. Qu'est-ce qu'Azure Machine Learning Studio ?**

Azure ML Studio est un environnement de banc d'essai accessible par le biais d'un navigateur web. ML Studio héberge une palette de modules avec une interface de composition visuelle vous permettant de créer un workflow de science des données de bout en bout sous la forme d'une expérience. Cet environnement propose des modules pour l'ingestion et la transformation des données, ainsi qu'une série de fonctionnalités permettant de concevoir, de noter et d'évaluer des modèles prédictifs. Certains des algorithmes les plus avancés alimentant l'apprentissage automatique dans Bing et Xbox ont été conçus dans ML Studio. Des modules d'apprentissage automatique open source évolutifs tels que Vowpal Wabbit sont également inclus. ML Studio prend en charge R. Vous pouvez intégrer vos codes R existants dans vos expériences. ML Studio vous permet de combiner ces algorithmes avec votre code R afin de créer des modèles prédictifs. ML Studio facilite les tâches de collaboration en vous permettant d'inviter votre équipe dans vos espaces de travail, où ils ont la possibilité d'afficher et de modifier vos expériences.

**3. Qu'est-ce que le service API d'Azure ML ?**

Le service API ML vous permet de déployer des modèles prédictifs conçus dans ML Studio en tant que service web évolutif et à tolérance de pannes. Les services web créés par le service API ML sont des API REST qui fournissent une interface pour les communications entre les applications externes et votre modèle analytique prédictif. Le service web fournit une façon de communiquer avec un modèle prédictif en temps réel afin de recevoir des résultats de prédiction et de les intégrer dans une application cliente externe. Le service API ML s'appuie sur Microsoft Azure pour le déploiement, l'hébergement et la gestion des API REST d'Azure ML. Deux types de services sont créés au moyen du service API d'Azure ML : le service Exécution de lots pour les accès par lots et le service Requête-Réponse pour les réponses synchrones à faible latence.

Un modèle prédictif peut être placé en phase intermédiaire au sein de votre espace de travail. Le service API ML génère également des pages d'aide pour les services web. Ces pages fournissent des exemples de code permettant d'appeler le service web dans C#, R et Python. Vous pouvez tester votre service web en effectuant des appels interactifs au service. Le service web en phase intermédiaire peut ensuite être mis en production en quelques clics seulement. Une fois en production, vous pouvez surveiller des services déployés ainsi qu'effectuer le suivi de l'utilisation et des erreurs sur le portail Azure. Pour mettre à jour les services web, il suffit de mettre à jour le modèle dans ML Studio et de transmettre par notification push les modifications au service en phase intermédiaire.

**4. Comment accéder à Microsoft Azure Machine Learning ?**

Pour commencer à utiliser Azure Machine Learning, visitez la [page de prise en main][page de prise en main]. Visitez la page [Centre Azure Machine Learning][Centre Azure Machine Learning] pour accéder aux mises à jour du service, pour consulter les actualités sur le blog de l'équipe ML, pour participer à notre communauté Machine Learning par le biais des forums, pour accéder à l'aide des produits, pour afficher la galerie de modèles et pour fournir des commentaires sur le service afin de nous aider à établir la feuille de route des produits.

### FACTURATION

**5. Comment fonctionne la facturation dans Machine Learning ?**

Le service Azure ML Studio est facturé par heure de calcul pour l'expérimentation active et la facturation se fait au prorata pour les heures partielles. Le service API d'Azure ML est facturé par 1 000 appels API de prédiction et par heure de calcul lorsqu'une prédiction est exécutée de façon active. La facturation s'effectue au prorata pour les quantités inférieures à 1 000 prédictions et les heures de calcul partielles.

Des frais sont agrégés par espace de travail pour votre abonnement. Au sein de chaque espace de travail, vous pouvez voir les frais relatifs aux trois éléments.

-   Heures d'expérience Studio : ce compteur agrège tous les frais de calcul alloués par des expériences en cours d'exécution dans ML Studio et des prédictions en cours dans l'environnement intermédiaire.
-   Heures de prédiction du service API : ce compteur inclut les frais de calcul alloués par les services web s'exécutant en production.
-   Prédictions de service API (pour 1 000) : ce compteur inclut les frais alloués par appel à votre service web de production.

Pour plus d'informations, visitez la page de tarification [http://azure.microsoft.com/pricing/details/machine-learning/][http://azure.microsoft.com/pricing/details/machine-learning/].

**6. Une version d'évaluation d'Azure Machine Learning est-elle disponible ?**

Azure ML fait partie de la version d'évaluation d'Azure. Lorsque vous vous inscrivez pour obtenir la version gratuite d'Azure, vous pouvez essayer n'importe quel service Azure pendant un mois. Pour plus d'informations sur la version d'évaluation d'Azure, visitez la page [http://azure.microsoft.com/pricing/free-trial-faq/][http://azure.microsoft.com/pricing/free-trial-faq/].

### MACHINE LEARNING STUDIO

**7. Quelles sources de données sont prises en charge par Azure ML ?**

Les données peuvent être chargées dans ML Studio de deux façons : en téléchargeant les fichiers locaux en tant que jeu de données ou en utilisant le module de lecture pour importer les données. Les fichiers locaux peuvent être téléchargés sous forme de jeux de données, en ajoutant de nouveaux jeux de données dans ML Studio. Consultez la rubrique d'aide **Obtention des données** dans ML Studio pour en savoir plus sur les formats de fichiers pris en charge.

Le module **Lecteur** peut lire des données provenant du service de Table Azure, du service Blob Azure, de Base de données SQL (Azure) ou de HDInsight. Vous pouvez également extraire des données à partir d'une source de données via HTTP. Consultez la rubrique d'aide du module **Lecteur** dans ML Studio pour plus d'informations.

**8. Quelle est la taille maximale de mon jeu de données ?**

ML Studio prend en charge les jeux de données d'apprentissage d'une taille maximale de 10 Go. Il n'y a pas de limite à la taille des jeux de données pour les services web. L'échantillonnage de jeux de données plus importants par le biais de requêtes Hive ou SQL avant l'ingestion est également pris en charge. Si vous utilisez des volumes de données supérieurs à 10 Go, vous pouvez créer plusieurs jeux de données et utiliser les modules « Partition et échantillon », « Fractionner » ou « Joindre » pour recombiner ces jeux de données dans ML Studio afin de créer des jeux d'apprentissage permettant de concevoir des modèles prédictifs. Consultez l'aide relative aux modules dans ML Studio pour plus d'informations sur ces modules.

Pour les tailles de jeux de données supérieures à 2 Go, la méthode conseillée consiste à télécharger les données sur Azure Storage ou Base de données SQL (Azure), ou à utiliser HDInsight, au lieu d'effectuer un téléchargement directement à partir d'un fichier local.

**9. Quels sont les algorithmes ML existants pris en charge dans ML Studio ?**

ML Studio fournit des algorithmes ML de pointe tels que les arbres de décision optimisés évolutifs, les systèmes de recommandation bayésiens, les réseaux neuronaux profonds et les jungles de décision développés chez Microsoft Research. Des modules d'apprentissage automatique open source évolutifs tels que Vowpal Wabbit sont également inclus. ML Studio prend en charge les algorithmes d'apprentissage automatique pour la classification, la régression et le clustering multiclasses et binaires. La liste complète des algorithmes d'apprentissage automatique est disponible dans l'aide de ML Studio.

**10. L'algorithme Machine Learning, la source de données, le format de données ou l'opération de transformation des données que je recherche n'est pas disponible dans Azure ML Studio. Quelles sont mes possibilités ?**

Vous pouvez consulter le [forum des commentaires des utilisateurs][forum des commentaires des utilisateurs] pour afficher les demandes de fonctionnalités dont nous effectuons le suivi. Ajoutez votre vote à cette demande si une fonctionnalité que vous recherchez a déjà été demandée. Si la fonctionnalité que vous recherchez n'existe pas, créez une nouvelle demande. Vous pouvez également afficher le statut de votre demande dans ce forum. Nous suivons cette liste de près et mettons à jour régulièrement le statut de disponibilité des fonctionnalités.

**11. Puis-je importer mon code existant dans ML Studio ?**

Actuellement, ML Studio prend en charge R. Vous pouvez dès lors importer vos codes R existants dans ML Studio et les exécuter au sein de la même expérience avec les modules d'apprentissage fournis par Azure ML. Vous pouvez ensuite le publier en tant que service web au moyen d'Azure ML. Azure ML est la façon la plus rapide de convertir les ressources analytiques de R en services web de production de niveau professionnel. Consultez la rubrique d'aide de ML Studio **Extensibilité avec R** pour apprendre à importer vos codes R et la visualisation dans ML Studio.

**12. Quels packages R sont disponibles dans ML Studio ?**

Actuellement, ML Studio prend en charge plus de 350 packages R et cette liste est en constante évolution. Consultez la rubrique d'aide de ML Studio **Extensibilité avec R** pour découvrir comment obtenir une liste des packages R pris en charge. Si le package souhaité ne figure pas dans cette liste, indiquez son nom sur le [forum de commentaires des utilisateurs][forum des commentaires des utilisateurs].

### SERVICE API ML

**13. Quand ai-je avantage à exécuter mon modèle prédictif en tant que service d'exécution de lots plutôt qu'un service web requête-réponse ?**

Le service de requête-réponse (RRS, Request-Response Service) est un service web à faible latence et à grande échelle qui permet de fournir une interface aux modèles sans état créés et publiés à partir de l'environnement Expérimentation. Le service d'exécution de lots (BES, Batch Execution Service) est un service de notation asynchrone des enregistrements de données. L'entrée de données pour le service BES est similaire à celle utilisée dans le service RRS. La différence principale réside dans le fait que le service BES lit un bloc d'enregistrements à partir de diverses sources telles que les objets blob, les tables dans Azure, Base de données SQL (Azure), HDInsight (requêtes Hive) et les sources HTTP. Les résultats de la notation sont exportés dans un fichier dans le stockage d'objets blob Azure et le point de terminaison de stockage est renvoyé dans la réponse.

Le service BES est utile dans un scénario où un nombre élevé de points de données doit être évalué, sous forme de lot, ou lorsqu'une partie importante de vos données se trouve déjà sous format de fichier dans un stockage Azure ou un cluster Hadoop. Le service web peut transformer les données lues avant de les envoyer au modèle, de sorte qu'il vous suffit de pointer vos données de transaction de fin de semaine vers un service de lots qui transformera et fournira les résultats.

Le service RRS s'avère utile dans un scénario où l'analyse prédictive est requise en temps quasi-réel pour alimenter un tableau de bord en direct, ainsi que pour guider les actions des utilisateurs ou le contenu fourni par le biais d'une application mobile ou web.

**14. Comment puis-je mettre à jour le modèle pour la production du service déployé ?**

Pour mettre à jour un modèle prédictif pour un service déjà déployé, il suffit de modifier et de réexécuter l'expérience utilisée pour créer et enregistrer le modèle formé. Une fois que la nouvelle version du modèle formé est disponible, ML Studio vous invite à indiquer si vous voulez mettre à jour votre service web en phase intermédiaire. Lorsque la mise à jour est appliquée au service web en phase intermédiaire, la même mise à jour est disponible pour vous permettre de l'appliquer au service web de production également. Consultez la rubrique d'aide de ML Studio **Mise à jour du service web** pour plus d'informations sur la mise à jour d'un service web déployé.

### SÉCURITÉ ET DISPONIBILITÉ

**15. Qui a accès au point de terminaison HTTP pour le service web déployé en production par défaut ? Comment puis-je limiter les accès au point de terminaison ?**

Une fois qu'un modèle prédictif a été mis en production, le portail Azure répertorie l'URL pour les services web déployés. Les URL de service en phase intermédiaire sont accessibles à partir de l'environnement ML Studio sous la section des services web, et les URL de service de production sont accessibles à partir du portail Azure, sous la section Machine Learning. Des clés d'accès sont fournies pour les services web de phase intermédiaire et de production pour le tableau de bord du service web respectivement dans ML Studio et l'environnement de portail Azure. Elles sont nécessaires pour effectuer des appels au service web en production et en phase intermédiaire.

**16. Comment puis-je surveiller mon service web déployé en production ?**

Une fois qu'un modèle prédictif a été mis en production, vous pouvez le surveiller à partir du portail Azure. Chaque service déployé dispose de son propre tableau de bord, à partir duquel vous pouvez consulter les informations de surveillance correspondant à ce service.

### SUPPORT ET FORMATIONS

**17. Où puis-je obtenir des formations pour Azure ML ?**

Le [Centre Azure Machine Learning][Centre Azure Machine Learning] héberge des didacticiels vidéo ainsi que des manuels d'utilisation. Ces derniers fournissent présentent les services et passent en revue le cycle de vie de science des données lié à l'importation des données, au nettoyage des données, à la conception de modèles prédictifs et à leur déploiement en production avec Azure ML.

Les didacticiels vidéo offrent une visite guidée de ML Studio et du service API ML. Ils illustrent l'étendue du service, principalement utilisé pour la saisie de données, le nettoyage et le traitement de modules, ainsi que pour la conception et le déploiement de modèles prédictifs. Ils abordent également des tâches telles que l'approvisionnement d'espaces de travail et le déploiement des modèles en phase intermédiaire vers la phase de production.

Nous continuerons à ajouter régulièrement de nouveaux documents dans le Centre Machine Learning. Vous pouvez envoyer des demandes pour obtenir des documents supplémentaires sur le Centre Machine Learning à partir du [forum de commentaires des utilisateurs][forum de commentaires des utilisateurs].

**18. Comment puis-je bénéficier d'un support pour Azure ML ?**

Dans la mesure où Azure ML fait partie de l'offre de support Azure, il bénéficie également d'un support. Pour bénéficier d'un support technique dans Azure ML, sélectionnez « Machine Learning » en tant que service pour voir s'afficher une catégorie de rubriques vous permettant de soumettre votre ticket de support. Pour plus d'informations sur l'offre de support Azure, consultez la page [http://azure.microsoft.com/support/options/][http://azure.microsoft.com/support/options/]

Azure Machine Learning dispose également d'un forum communautaire sur MSDN, où vous pouvez poser des questions en rapport avec Azure ML. Le forum est géré par l'équipe d'Azure ML. Visitez le [Forum Azure][Forum Azure].

  [page de prise en main]: http://go.microsoft.com/fwlink/?LinkId=404226
  [Centre Azure Machine Learning]: http://azure.microsoft.com/documentation/services/machine-learning/
  [http://azure.microsoft.com/pricing/details/machine-learning/]: http://azure.microsoft.com/pricing/details/machine-learning/
  [http://azure.microsoft.com/pricing/free-trial-faq/]: http://azure.microsoft.com/pricing/free-trial-faq/
  [forum des commentaires des utilisateurs]: http://go.microsoft.com/fwlink/?LinkId=404231
  [forum de commentaires des utilisateurs]: https://windowsazure.uservoice.com/forums/257792-machine-learning
  [http://azure.microsoft.com/support/options/]: http://azure.microsoft.com/support/options/
  [Forum Azure]: http://social.msdn.microsoft.com/Forums/windowsazure/fr-fr/home?forum=MachineLearning

<!--HONumber=46--> 
