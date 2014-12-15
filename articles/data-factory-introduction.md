<properties title="Introduction to Azure Data Factory" pageTitle="Présentation d'Azure Data Factory" description="Learn how you can use the Azure Data Factory service to compose data processing, data storage and data movement services to create pipelines that produce trusted information." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Présentation du service Azure Data Factory
Le service **Azure Data Factory** est un service entièrement géré pour composer des services de stockage, de traitement et de déplacement des données dans des pipelines de production rationalisés, évolutifs et fiables.  Les développeurs peuvent utiliser Data Factory pour transformer des données (locales ou du cloud) semi-structurées, structurées ou non, en informations fiables. Les développeurs créent des flux de travail (pipelines) pilotés par les données qui rassemblent, agrègent et transforment des données locales, du cloud ou provenant de services Internet et configurent le traitement de données complexes via un script JSON simple. Le service Azure Data Factory permet la surveillance et la gestion de ces pipelines grâce à une riche expérience visuelle proposée via le portail Azure en version préliminaire. Les informations générées par les pipelines peuvent facilement être consommées à l'aide d'outils décisionnels et d'analyse et d'autres applications pour procurer une meilleure visibilité de l'activité et prendre des décisions éclairées.

Cet article fournit une vue d'ensemble du service Azure Data Factory, de sa valeur ajoutée et des scénarios pris en charge. 

##Vue d'ensemble d'Azure Data Factory
Les projets d'intégration de données se sont toujours articulés autour de la création de processus ETL (Extraction, Transformation et Chargement) qui extraient des données provenant de différentes sources d'une organisation, transforment ces données pour qu'elles correspondent au schéma cible d'un entrepôt de données d'entreprise (EDW, Enterprise Data Warehouse) et chargent ces données dans un EDW comme indiqué dans l'image suivante. L'EDW est ensuite accessible comme seule source approuvée pour les solutions d'analyse décisionnelle. 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

Le paysage actuel de données pour les entreprises croît de manière exponentielle en termes de volume, de diversité et de complexité, comme illustré dans l'image suivante.  Il est plus varié que jamais avec des données locales et du cloud, présentant des formes et des vitesses différentes.  Le traitement des données doit se produire à divers emplacements géographiques et inclut une combinaison de logiciels open source, de solutions commerciales et de services de traitement personnalisé, coûteux et difficiles à intégrer et à gérer.  L'agilité nécessaire pour s'adapter au contexte actuel des données volumineuses offre l'opportunité de fusionner l'EDW classique avec les fonctionnalités nécessaires d'un système de production modernes de l'information.  Le service Azure Data Factory est la plateforme de composition entre les EDW classiques et le contexte des données évolutives qui permet aux entreprises d'exploiter toutes les données disponibles pour la prise de décision pilotée par celles-ci.

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]


Le service **Azure Data Factory** permet aux entreprises d'exploiter cette diversité en fournissant une plateforme pour **composer des services de traitement, de stockage et de déplacement des données dans des pipelines de production d'informations et gérer les ressources de données approuvées.**.

Le service Azure Data Factory vous permet de :

- **travailler facilement avec des systèmes de stockage et de traitement de données divers.** 
Data Factory vous permet d'utiliser des données à l'endroit où elles sont stockées et composer des services pour le stockage, le traitement et le déplacement des données.  Par exemple, vous pouvez composer des pipelines de production d'informations pour traiter des données locales, telles que des données SQL Server, ainsi que des données du cloud, telles que des données de la base de données SQL, des objets blob et des tables Azure.  
- **transformer des données en informations approuvées.** 
La combinaison et la mise en forme de données complexes ne réussissent pas toujours au premier essai et la modification des modèles de données peut être onéreuse et chronophage.  Grâce à Data Factory, vous pouvez vous concentrer sur l'analyse transformationnelle tandis que le service s'occupe des flux.  Data Factory prend en charge le traitement Hive, Pig et C#, ainsi que des fonctionnalités de traitement clés telles que la gestion automatique de cluster Hadoop (HDInsight), les nouvelles tentatives après des échecs temporaires, les stratégies de délai d'expiration configurables et les alertes.  
- **surveiller les pipelines de données dans un seul emplacement.** 
Avec un portefeuille varié de données, il est important d'avoir une vue complète et fiable de vos services de stockage, de traitement et de déplacement des données.  Data Factory vous permet d'évaluer rapidement l'état des pipelines de données de bout en bout, d'identifier les problèmes et de prendre des mesures correctives si nécessaire. Suivez visuellement le lignage des données et les relations entre elles dans toutes vos sources. Consultez une comptabilité historique complète de l'exécution du travail, de l'intégrité du système et des dépendances à partir d'un tableau de bord de surveillance unique
- **Obtenir des aperçus étoffés de données transformées.**
Trouvez des réponses aux questions qui évoluent constamment dans votre organisation et gardez une longueur d'avance quand la production de vos données est prête à fonctionner.  Améliorez votre capacité à offrir une visibilité de votre activité en produisant des informations en temps voulu et approuvées prêtes à consommer. Utilisez des pipelines de données pour fournir des données transformées émanant du cloud aux sources locales telles que SQL Server, ou conservez-les dans vos sources de stockage du cloud, où elles seront consommées par les outils décisionnels et d'analyse et autres applications.

Aujourd'hui, pour tirer parti des avantages de Data Factory, les développeurs interagissent directement avec les pipelines de données, les services de stockage et les services de calcul individuels.  Comme le service Data Factory évolue au fil du temps, nous allons présenter les services supplémentaires de stockage et de traitement, ainsi que les nouveaux mécanismes de regroupement des services de calcul et de stockage et des pipelines de données dans des " concentrateurs ".  Nous abordons les concentrateurs dans notre présentation, car ce concept naissant apparaît dans le service en tant que précurseur pour les versions futures.

Un concentrateur Azure Data Factory est un conteneur pour les services de stockage et de calcul (appelés services liés), ainsi que pour les pipelines de données qui utilisent et s'exécutent sur ces ressources. Le conteneur " Concentrateur " permet de diviser spécifiquement un Data Factory en regroupements logiques ou de domaine.  Par exemple, une entreprise peut avoir un " concentrateur Azure Amérique de l'Ouest " qui gère tous les services liés et les pipelines du centre de données de l'Ouest des États-Unis ou un " concentrateur EDW Ventes " qui gère tous les pipelines et services liés associés au remplissage et au traitement des données pour l'EDW Ventes.  Un pipeline s'exécute sur un concentrateur unique ce qui constitue une caractéristique importante des concentrateurs. Ainsi, quand vous définissez un pipeline, tous les services liés référencés par les tables ou les activités dans ce pipeline doivent avoir le même nom de concentrateur que le pipeline lui-même.

Les concentrateurs permettent d'encapsuler le stockage et le calcul de façon à ce que les pipelines puissent référencer uniquement un concentrateur plutôt que les services spécifiques et les tables qu'il utilise. Le concentrateur peut ensuite avoir recours à des stratégies pour décider où exécuter un pipeline. Les répercussions importantes sont nombreuses, parmi lesquelles la facilité de mise à l'échelle (car un nombre plus important de services liés peut être ajouté à un concentrateur), la répartition de la charge des pipelines entre ces nouveaux services liés et la réutilisation des définitions de pipelines sur différents concentrateurs.

## Scénarios
Il existe un large éventail de scénarios pour les pipelines de données qui comprennent les systèmes en local, les systèmes cloud, les systèmes SaaS ainsi que la production de données diffusées en continu et traitées par lots.  Cette section décrit quelques exemples de scénarios actuellement pris en charge par Azure Data Factory et intégrera les scénarios de concentrateur au fil de leur évolution.

###Scénario n°1 : sources de données pour le concentrateur de données
![Source the Data Hub][image-data-factory-introduction-secenario1-source-datahub]

Les entreprises disposent de données de différents types situées dans des sources diverses.  La première étape dans la création d'un système de production d'informations consiste à se connecter à toutes les sources nécessaires de données et de traitement des données comme les services SaaS, web, FTP et de partages de fichiers et déplacer les données en fonction des besoins pour un traitement ultérieur.

Sans Data Factory, les entreprises doivent créer des composants de déplacement de données personnalisés ou écrire des services personnalisés pour intégrer ces sources de données et le traitement des données.  L'intégration et l'entretien de tels systèmes sont coûteux et difficiles à réaliser. Le niveau de surveillance et d'alerte de l'entreprise est rarement à la hauteur, ainsi que les contrôles, d'un service entièrement géré.
  
Les services de stockage et de traitement des données d'Azure Data Factory sont rassemblés dans un conteneur " Concentrateur " qui facilite et optimise les activités de calcul et de stockage, permet la gestion de la consommation des ressources unifiées et fournit des services de déplacement des données en fonction des besoins.

###Scénario n°2 : rendre la production des informations opérationnelle

Les scénarios de mise en œuvre opérationnelle viennent logiquement après les scénarios liés aux sources de données. Une fois que les données sont présentes dans un concentrateur, vous souhaitez créer et rendre opérationnels des pipelines de données pour produire des données transformées de manière fiable selon une planification contrôlée et facile à gérer pour les environnements de production avec des données approuvées.  La transformation des données dans Azure Data Factory s'effectue via le traitement Hive, Pig et C# personnalisé s'exécutant sur Hadoop (Azure HDInsight).  Ces transformations peuvent servir à nettoyer les données, masquer les champs de données critiques et effectuer d'autres opérations sur les données selon un large éventail de manières complexes.  Généralement, il est compliqué et difficile de rendre  des données opérationnelles et de conserver l'infrastructure et les services personnalisés à jour. Ce type de solution présente un certain nombre de défis en termes d'implémentation, de gestion, de montée en charge, de résolution des problèmes et de contrôle de version.
  
Avec le service entièrement géré de Data Factory, les utilisateurs peuvent rendre ces pipelines opérationnels en les définissant avec des planifications récurrentes à usage unique ou complexes et le service Data Factory se charge de tout le reste.  Grâce aux concentrateurs, la gestion des clusters pour toutes les données et le traitement dans un concentrateur est gérée pour le compte de l'utilisateur. Les utilisateurs peuvent ainsi se consacrer aux analyses transformationnelles plutôt qu'à la gestion de l'infrastructure.  Azure Data Factory supprime les défis liés à l'utilisation des services personnalisés fragiles et permet aux entreprises de produire des informations approuvées de manière reproductible et fiable.


###Scénario n°3 :  intégrer la production d'informations à la découverte de données
Les approches et technologies classiques liées au décisionnel, tout en fournissant une " source approuvée faisant autorité ", entraînent presque toujours un effet secondaire grave : un retard constant des demandes en raison d'un processus de demande de modification soigneusement contrôlé.  Pour s'adapter rapidement aux problèmes liés à l'évolution rapide de l'activité, les entreprises doivent être plus souples et connecter leurs systèmes de production d'informations et leurs systèmes de consommation des informations.  Azure Data Factory leur permet de relever le défi de connexion de ces systèmes aux pipelines de données rationalisés pour la production d'informations et le défi de la consommation d'informations en mettant à disposition des données approuvées sous des formes facilement consommables.
  
Azure Data Factory prend en charge les fonctionnalités suivantes pour activer la consommation simple des données produites :

- Déplacez facilement (de manière unique ou planifiée) les ressources de données produites vers des mini-data warehouse relationnels pour la consommation à l'aide d'outils décisionnels existants (Excel, Tableau, etc...).
- Consommez des ressources de données générées par une fabrique de données directement à l'aide de Power Query dans Excel.

Consultez les rubriques suivantes pour consommer des données à l'aide de Power Query : 

- [Power Query : connexion à Microsoft Azure Table Storage] [Power-Query-Azure-Table]
- [Power Query : connexion à Microsoft Azure Blob Storage] [Power-Query-Azure-Table]
- [Power Query : connexion à la base de données SQL Microsoft Azure] [Power-Query-Azure-SQL]
- [Power Query : connexion à Microsoft SQL Server locale][Power-Query-OnPrem-SQL] 

## Modèle d'application
Le diagramme suivant illustre le modèle d'application pris en charge par Azure Data Factory.

![Application Model][image-data-factory-application-model]

Il existe trois phases de production des informations dans Azure Data Factory :


- **Se connecter et collecter**. Dans cette étape, les données de différentes sources sont importées dans des concentrateurs de données.
- **Transformer et enrichir**. Dans cette étape, les données collectées sont traitées.
- **Publier**. Dans cette étape, les données sont publiées pour être consommées par les outils décisionnels, outils d'analyse et autres applications.

Data Factory permet aux développeurs de créer des **pipelines** qui sont des groupes de déplacement des données et/ou des **activités** de traitement qui acceptent un ou plusieurs **jeux de données** d'entrée et produisent un ou plusieurs groupes de données de sortie.  Les pipelines peuvent être exécutés une fois ou sur un ensemble de planifications (horaire, quotidienne, hebdomadaire, etc...).  Un **jeu de données** est une vue nommée de données. Les données en cours de description peuvent varier de simples octets, de données semi-structurées telles des fichiers CSV vers des **tables** ou des **modèles**.

**Les pipelines** sont constitués d'**activités** de déplacement de données (par exemple : activité de copie) qui servent souvent à importer/exporter des données à partir de toutes les **sources de données** (bases de données, fichiers, services SaaS, etc...) utilisées par l'organisation vers un **concentrateur de données**.
    
Une fois les données dans un **concentrateur**, les **pipelines** hébergés par les services de calcul du concentrateur sont utilisés pour transformer les données dans un format adapté à la consommation (par les outils décisionnels, les applications, les clients, etc.).  
  
Enfin, **les pipelines** peuvent être chaînés (comme illustré dans le diagramme) pour que les **jeux de données** de sortie des uns soient les entrées des autres.  Ainsi, les flux de données complexes sont intégrés dans des **pipelines** qui s'exécutent dans un concentrateur de données ou s'étendent sur plusieurs concentrateurs.  En utilisant les **pipelines** de cette façon, les organisations disposent de blocs de construction pour composer les meilleurs services locaux, de cloud et SaaS (Software-as-a-Service), le tout rassemblé dans une seule fabrique de données facile à gérer. 


##Terminologie
Cette section présente la terminologie liée à Azure Data Factory.

### Data Factory

**Azure Data Factory** est un service entièrement géré pour composer des services de stockage, de traitement et de déplacement des données dans des pipelines de production rationalisés, évolutifs et fiables. 

Le service Data Factory consomme, produit, gère et publie des **jeux de données**. Un abonnement Azure peut avoir une ou plusieurs instances d'Azure Data Factory. Une fabrique de données Azure peut être liée à un ou plusieurs services de stockage ou de calcul (appelés services liés).
 
Une fabrique de données Azure peut comporter un ou plusieurs pipelines qui traitent les données dans des emplacements de stockage liés à l'aide de services de calcul liés, tels qu'Azure HDInsight, mais ne contient pas de données. Celles-ci sont plutôt stockées à l'extérieur de la fabrique, dans le système de stockage existant de l'utilisateur.


### Service lié
Service qui est lié à une fabrique de données Azure. Cela peut être :


- un magasin de données tel qu'Azure Storage, la base de données SQL Azure et une base de données SQL Server locale ;
- un service de calcul comme Azure HDInsight.

Un magasin de données est un conteneur de données ou des jeux de données et Azure HDInsight est le seul service de calcul pris en charge pour l'instant. Vous devez d'abord créer un service lié pour pointer vers un magasin de données, puis définir les jeux de données pour représenter les données de ce magasin de données. 

### Jeu de données
Vue nommée de données. Les données décrites peuvent aller de données à octets simples, semi-structurées, comme des fichiers CSV, à des tables relationnelles ou même des modèles. Une **table** est un jeu de données qui comporte un schéma et est rectangulaire.

###Pipeline
Dans une fabrique de données Azure, un**pipeline** traite les données dans les services de stockage liés à l'aide de services de calcul liés. Le pipeline contient une séquence d'activités dans laquelle chaque activité effectue une opération de traitement spécifique. Par exemple, une activité de copie se charge de copier les données d'un emplacement de stockage source vers un emplacement de stockage cible et les activités Hive/Pig utilisent un cluster Azure HDInsight pour traiter des données à l'aide de requêtes Hive ou de scripts Pig.

Les étapes classiques pour créer une instance Azure Data Factory sont les suivantes :

1. Créer une fabrique de données
2. Créer un service lié pour chaque magasin de données ou service de calcul
3. Créer des jeux de données d'entrée et de sortie
4. Créer un pipeline 

###Activité
Étape de traitement de données dans un pipeline qui prend un ou plusieurs jeux de données d'entrée et produit un ou plusieurs jeux de données de sortie.  Les activités ont lieu dans un environnement d'exécution (par exemple : cluster Azure HDInsight) et lisent ou écrivent des données dans un magasin de données associé à l'instance Azure Data Factory.
 
###Concentrateur de données
Conteneur Azure pour les services de stockage et de calcul des données. Par exemple, un cluster Hadoop avec HDFS pour le stockage et Hive/Pig pour le calcul (traitement) est un concentrateur de données. De même, un entrepôt de données d'entreprise (EDW, Enterprise Data Warehouse) peut être modélisé comme un concentrateur de données (base de données pour le stockage, procédures stockées et/ou outil ETL comme services de calcul).  Les pipelines utilisent des magasins de données et les exécutent sur des ressources de calcul dans un concentrateur de données. Seul le concentrateur HDInsight est pris en charge pour l'instant.

Le concentrateur de données permet à une fabrique de données d'être divisée en regroupements logiques ou de domaine, tels que le " concentrateur Azure Amérique de l'Ouest " qui gère tous les services liés (magasins de données et calcul) et les pipelines spécifiques du centre de données de l'Ouest des États-Unis ou un " concentrateur EDW Ventes " qui gère tous les pipelines et services liés, associés au remplissage et au traitement des données pour l'EDW Ventes.

Un pipeline s'exécute sur un concentrateur unique ce qui constitue une caractéristique importante des concentrateurs. Ainsi, quand vous définissez un pipeline, tous les services liés référencés par les tables ou les activités dans ce pipeline doivent avoir le même nom de concentrateur que le pipeline lui-même. Si la propriété HubName n'est pas spécifiée pour un service lié, celui-ci est placé dans le concentrateur " par défaut ".

###Tranche
Une table dans une fabrique de données Azure se compose de tranches sur l'axe temporel. La largeur d'une tranche est déterminée par la planification en heure/jour. Quand la planification s'effectue " en heure ", une tranche est exécutée toutes les heures avec l'heure de début et de fin d'un pipeline, etc.  

Les tranches permettent aux professionnels de l'informatique de travailler avec un sous-jeu de données globales pendant une période spécifique (par exemple : la tranche qui est générée pour la durée (en heure) : De 13 à 14H00). Ils peuvent également afficher toutes les tranches de données en aval pendant un certain temps, en interne, et réexécuter une tranche en cas de défaillance.

L'exécution est une unité de traitement d'une tranche. Une tranche peut être exécutée une ou plusieurs fois en cas de nouvelles tentatives ou de défaillance. Elle est identifiée par son heure de début. 

###Passerelle de gestion de données
La passerelle de gestion de données Microsoft est un logiciel qui connecte les sources de données locales aux services cloud pour la consommation. Vous devez disposer d'au moins une passerelle installée dans votre environnement d'entreprise et l'inscrire auprès du portail Azure Data Factory avant d'ajouter des sources de données locales en tant que services liés.


##Étapes suivantes
[Prise en main de Data Factory][datafactory-getstarted]. Cet article fournit un didacticiel de bout en bout qui montre comment créer un exemple de fabrique de données Azure qui copie des données à partir d'objets blob Azure vers une base de données SQL Azure.

[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx


[datafactory-getstarted]: ../data-factory-get-started/

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-introduction-secenario1-source-datahub]:./media/data-factory-introduction/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]:./media/data-factory-introduction/Scenario2-OperationalizeInformationProduction.png

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png



