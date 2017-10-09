---
title: "Introduction à Azure Data Factory | Microsoft Docs"
description: "Découvrez Azure Data Factory, un service d’intégration de données cloud qui gère et automatise le déplacement et la transformation des données."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 09e514aee503b7cb045c81d8ddcb855ced9b072b
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="introduction-to-azure-data-factory"></a>Présentation d'Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-introduction.md)
> * [Version 2 - Préversion](introduction.md)

Dans le monde du Big Data, les données brutes, désorganisées sont souvent stockées dans des systèmes de stockage relationnels, non relationnels, et dans d’autres encore. Toutefois, lorsqu’elles sont isolées, les données brutes n’ont pas le contexte ou la signification appropriés pour fournir des informations significatives aux analystes, aux « scientifiques des données » ou aux décideurs en entreprise. Ce qu’il faut, c’est un service qui permette à des processus d’orchestration et d’opérationnalisation d’affiner ces gigantesques banques de données brutes pour les transformer en informations exploitables. Azure Data Factory est un service cloud géré créé pour ces projets complexes d’extraction, de transformation et de chargement (ETL), d’extraction, de chargement et de transformation (ELT), et d’intégration des données.

Prenons l’exemple d’une société d’édition de jeux qui rassemble plusieurs pétaoctets de journaux sur les jeux générés par les jeux dans le cloud. Elle souhaite analyser ces journaux afin d’obtenir un aperçu des préférences des clients, des données démographiques, etc. pour identifier les opportunités de vente incitative et de vente croisée, développer des nouvelles fonctionnalités, optimiser la croissance et fournir une meilleure expérience à ses clients.

Pour analyser ces journaux, l’entreprise doit utiliser des données de référence comme des informations sur le client, des informations sur les jeux et des informations sur la campagne marketing qui sont contenues dans un magasin de données local. L’entreprise souhaite utiliser ces données issues du magasin de données local et les combiner avec d’autres données de journal dont elle dispose dans un magasin de données dans le cloud. Pour extraire des informations, elle souhaite traiter les données combinées au moyen d’un cluster Spark dans le cloud (HDInsight), et enfin publier les données transformées dans un entrepôt de données cloud, tel qu’Azure SQL Data Warehouse, afin de pouvoir créer facilement un rapport à partir de celles-ci. Ce flux de travail doit être automatisé, surveillé et géré sur une base quotidienne, et exécuté lorsque les fichiers sont placés dans un conteneur de magasin d’objets blob.

Azure Data Factory est la plateforme qui résout les scénarios de données de ce type. Il s’agit d’un **service d’intégration de données basé sur le cloud qui vous permet de créer des flux de travail orientés données dans le cloud pour orchestrer et automatiser le déplacement des données et la transformation des données**. Grâce à Azure Data Factory, vous pouvez créer et planifier des flux de travail orientés données (appelés pipelines) capables d’ingérer des données provenant de différents magasins de données, de traiter/transformer les données à l’aide de services de calcul comme Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics et Azure Machine Learning, et de publier des données de sortie dans des magasins de données tels qu’Azure SQL Data Warehouse pour que des applications décisionnelles (BI) puissent les utiliser. Enfin, via Azure Data Factory, les données brutes peuvent être organisées en magasins de données et lacs de données pertinents en vue d’améliorer les prises de décisions.

![vue de niveau supérieur de Data Factory](media/introduction/big-picture.png)

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez [Introduction à Data Factory version 1](v1/data-factory-introduction.md).

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?
Les pipelines (flux de travail orientés données) dans Azure Data Factory effectuent généralement les quatre étapes suivantes :

![Quatre étapes d’un flux de travail orienté données](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Se connecter et collecter

Les entreprises disposent de données de différents types situées dans différentes sources locales, dans le cloud, structurées, non structurées et semi-structurées, toutes récupérées à des intervalles et à des vitesses variables. Quand vous mettez en place un système de production d’informations, la première étape consiste à vous connecter à toutes les sources nécessaires de données et de traitement (par exemple, les services SaaS, les bases de données, les partages de fichiers, et les services FTP et web), puis à déplacer les données vers un emplacement centralisé en vue de leur traitement, en fonction des besoins.

Sans Data Factory, les entreprises doivent concevoir des composants personnalisés chargés du déplacement des données ou écrire des services personnalisés pour intégrer ces sources de données et leur traitement. De tels systèmes sont onéreux et leur intégration et maintenance tout particulièrement difficiles. Souvent, ils ne sont pas équipés des dispositifs de surveillance et d’alerte habituellement indispensables aux entreprises, ni même des commandes qu’un service entièrement géré peut offrir.

Avec Data Factory, vous pouvez utiliser l’[activité de copie](copy-activity-overview.md) dans un pipeline de données pour déplacer les données source des magasins de données locaux et dans le cloud vers un magasin de données centralisé dans le cloud, à des fins d’analyse ultérieure. Par exemple, vous pouvez collecter des données d’un Azure Data Lake Store, puis les transformer à l’aide d’un service de calcul Azure Data Lake Analytics. Vous pouvez aussi collecter des données dans un stockage Blob Azure, puis les transformer à l’aide d’un cluster Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformer et enrichir
Une fois que les données sont présentes dans un magasin de données centralisé dans le cloud, vous souhaitez que les données collectées soient traitées ou transformées à l’aide de services de calcul tels que HDInsight Hadoop, Spark, Data Lake Analytics et Machine Learning. Vous souhaitez générer de manière fiable des données transformées selon une planification facile à gérer et contrôlée afin de fournir aux environnements de production des données approuvées.

### <a name="publish"></a>Publier
Maintenant que les données brutes ont été affinées sous une forme utilisable par l’entreprise, vous pouvez les charger dans Azure Data Warehouse, Azure SQL DB, Azure CosmosDB ou n’importe quel moteur d’analyse auquel vos utilisateurs peuvent accéder à partir de leurs outils d’analyse décisionnelle.

### <a name="monitor"></a>Surveiller
Une fois que vous avez créé et déployé votre pipeline d’intégration de données afin de bénéficier de la valeur commerciale fournie par les données affinées, vous pouvez surveiller les activités planifiées et les pipelines pour connaître les taux de réussite et d’échec. Azure Data Factory intègre une prise en charge de la surveillance des pipelines via Azure Monitor, les API, PowerShell, OMS et les panneaux de contrôle d’intégrité du portail Azure.

## <a name="whats-different-in-version-2"></a>En quoi diffère la version 2 ?
Azure Data Factory version 2 est fondé sur le service de déplacement et transformation Azure Data Factory d’origine, mais a été développé pour offrir un ensemble plus vaste de scénarios d’intégration de données issues du cloud. Azure Data Factory V2 offre les fonctionnalités suivantes :

- Flux de contrôle et mise à l’échelle
- Déploiement et exécution de packages SSIS dans Azure

Après la sortie de la version 1, nous nous sommes rendu compte que les clients avaient besoin de concevoir des scénarios d’intégration de données hybrides complexes nécessitant le déplacement et le traitement des données dans le cloud, localement et sur des machines virtuelles dans le cloud. Ces exigences ont mis en évidence la nécessité de transférer et de traiter les données dans des environnements de réseau virtuel sécurisés et de permettre une montée en charge en fournissant une puissance de traitement à la demande.

Puisque les pipelines de données deviennent des éléments essentiels de la stratégie d’analyse commerciale, nous avons constaté que ces activités de données cruciales avaient besoin de la flexibilité nécessaire pour prendre en charge les charges de données incrémentielles et les exécutions déclenchées par des événements. Enfin, à mesure que ces orchestrations gagnent en complexité, le service est également dans l’obligation de prendre en charge des paradigmes de flux de travail communs, tels que la création de branches, le bouclage et le traitement conditionnel.

Avec la version 2, vous pouvez également faire migrer des packages SSIS (SQL Server Integration Services) existants vers le cloud afin de promouvoir et d’insérer SSIS en tant que service Azure géré au sein d’ADF au moyen de la nouvelle fonctionnalité de « runtimes d’intégration ». L’ajout d’un runtime d’intégration SSIS dans la version 2, vous permet d’exécuter, de gérer, de surveiller et de générer des packages SSIS dans le cloud.

### <a name="control-flow-and-scale"></a>Flux de contrôle et mise à l’échelle 
Pour prendre en charge les divers flux d’intégration et modèles dans l’entrepôt de données moderne, Data Factory a mis en œuvre un nouveau modèle flexible de pipeline de données qui n’est plus lié à des données de série chronologique. Avec cette version, vous pouvez modéliser des instructions conditionnelles et créer une branche dans le flux de contrôle d’un pipeline de données afin de transmettre explicitement des paramètres dans et entre ces flux.

Vous avez désormais la liberté de modéliser n’importe quel style de flux requis pour l’intégration des données et de le distribuer à la demande ou de façon répétée selon une planification d’horloge. Quelques flux courants qui n’étaient pas possibles auparavant sont désormais activés :   

- Flux de contrôle :
    - Chaînage des activités en une séquence au sein d’un pipeline
    - Création d’une branche d’activités au sein d’un pipeline
    - Paramètres
        - Les paramètres peuvent être définis au niveau du pipeline et les arguments transmis lors de l’appel du pipeline à la demande ou à partir d’un déclencheur.
        - Les activités peuvent utiliser les arguments transmis au pipeline.
    - Transmission d’un état personnalisé
        - Les sorties de l’activité, notamment l’état, peuvent être utilisées par une activité suivante du pipeline.
    - Mise en boucle des conteneurs
        - Pour chaque 
- Flux basés sur déclencheur
    - Les pipelines peuvent être déclenchés à la demande ou selon une durée chronométrée.
- Flux delta
    - Utilisez des paramètres et définissez la marque de borne haute pour la copie delta lors du déplacement des tables de dimension ou de référence à partir d’un magasin relationnel, localement, ou dans le cloud pour charger les données dans le lac 

Pour plus d’informations, consultez l’article [Didacticiel : flux de contrôle](tutorial-control-flow.md).

### <a name="deploy-ssis-packages-to-azure"></a>Déployer des packages SSIS vers Azure 
Si vous souhaitez déplacer vos charges de travail SSIS, vous pouvez créer une fabrique de données version 2 et approvisionner un runtime d’intégration Azure-SSIS (IR). Le runtime d’intégration Azure-SSIS est un cluster entièrement géré de machines virtuelles Azure (nœuds) dédié à l’exécution de vos packages SSIS dans le cloud. Pour obtenir des instructions pas à pas, consultez le didacticiel : [Déployer des packages SSIS vers Azure](tutorial-deploy-ssis-packages-azure.md). 
 

## <a name="rich-cross-platform-sdks"></a>Kits de développement logiciel (SDK) enrichis inter-plateformes
Si vous êtes un utilisateur expérimenté et recherchez une interface de programmation, la version 2 offre un ensemble enrichi de kits de développement logiciel (SDK) qui peut être utilisé pour créer, gérer et surveiller les pipelines à l’aide de votre IDE favori.

- Kit de développement logiciel (SDK) .NET
- PowerShell
- Kit de développement logiciel (SDK) Python

Vous pouvez également utiliser des API REST pour créer des fabriques de données. 

## <a name="load-the-data-into-a-lake"></a>Charger les données dans un lac
Data Factory offre plus de 30 connecteurs permettant de charger des données d’environnements hybrides et hétérogènes dans Azure.  Reportez-vous au [Guide des performances et réglages](copy-activity-performance.md) pour connaître les derniers résultats de performances issus de tests internes et obtenir des suggestions de paramétrage. Par ailleurs, nous avons récemment activé la haute disponibilité et l’extensibilité du runtime d’intégration autohébergé que vous installez dans un environnement de réseau privé pour répondre aux besoins de disponibilité et d’évolutivité des grandes entreprises de niveau 1.

## <a name="top-level-concepts-in-version-2"></a>Concepts de niveau supérieur dans la version 2
Un abonnement Azure peut contenir une ou plusieurs instances Azure Data Factory (ou fabriques de données). Azure Data Factory s’articule autour de quatre composants clés. Ils fonctionnent ensemble et vous dotent de la plateforme sur laquelle composer des flux de travail orientés données constitués d’étapes de déplacement et de transformation des données.

### <a name="pipeline"></a>Pipeline
Une fabrique de données peut avoir un ou plusieurs pipelines. Un pipeline est un regroupement logique des activités nécessaires pour effectuer une unité de travail. Ensemble, les activités d’un pipeline effectuent une tâche. Par exemple, un pipeline peut contenir un groupe d’activités qui ingère des données à partir d’un objet Blob Azure, puis exécute une requête Hive sur un cluster HDInsight pour partitionner les données. L’avantage de cette opération, c’est que le pipeline vous permet de gérer les activités en tant que groupe et non pas individuellement. Les activités d’un pipeline peuvent être chaînées pour fonctionner de manière séquentielle, ou peuvent fonctionner en parallèle de façon indépendante

### <a name="activity"></a>Activité
Les activités représentent une étape de traitement dans un pipeline. Par exemple, vous pouvez utiliser une activité de copie pour copier des données d’une banque de données vers une autre. De même, vous pouvez utiliser une activité Hive qui exécute une requête Hive sur un cluster Azure HDInsight afin de convertir ou d’analyser vos données. Data Factory prend en charge trois types d’activités : les activités de déplacement des données, les activités de transformation des données et les activités de contrôle

### <a name="datasets"></a>JEUX DE DONNÉES
Les jeux de données représentent les structures des données dans les magasins. Ils pointent ou référencent simplement en tant qu’entrées ou sorties les données que vous voulez utiliser dans vos activités. 

### <a name="linked-services"></a>Services liés
Les services liés ressemblent à des chaînes de connexion. Ils définissent les informations de connexion nécessaires à Data Factory pour se connecter à des ressources externes. Voyez les choses de la façon suivante : un service lié définit la connexion à la source de données et un jeu de données représente la structure des données. Par exemple, un service lié Stockage Azure spécifie la chaîne de connexion pour se connecter au compte de stockage Azure. Et un jeu de données blob Azure spécifie le conteneur d’objets blob et le dossier qui contient les données.

Data Factory fait appel aux services liés pour deux raisons :

- Pour représenter une **banque de données** et notamment une instance SQL Server, une base de données Oracle, un partage de fichiers locaux ou un compte de stockage d’objets blob Azure. Pour obtenir la liste des magasins de données pris en charge, consultez l’article [Activité de copie](copy-activity-overview.md).
- Pour représenter une **ressource de calcul** qui peut héberger l’exécution d’une activité. Par exemple, l’activité HDInsightHive s’exécute sur un cluster HDInsight Hadoop. Pour obtenir la liste des activités de transformation et des environnements de calcul pris en charge, consultez l’article [Transformation des données](transform-data.md).

### <a name="triggers"></a>Déclencheurs
Les déclencheurs correspondent à l’unité de traitement qui détermine le moment où une exécution de pipeline doit être lancée. Il existe différents types de déclencheurs pour différents types d’événements. Dans la préversion, nous prenons en charge un déclencheur par planification de durée chronométrée. 


### <a name="pipeline-runs"></a>Exécutions de pipeline
Une exécution du pipeline est une instance de l’exécution du pipeline. Les exécutions de pipeline sont généralement instanciées par la transmission des arguments aux paramètres définis dans les pipelines. Les arguments peuvent être transmis manuellement ou être inclus dans la définition du déclencheur.

### <a name="parameters"></a>Paramètres
Les paramètres sont des paires clé-valeur de configuration en lecture seule.  Les paramètres sont définis dans le pipeline et les arguments des paramètres définis sont transmis au cours de l’exécution à partir du contexte d’exécution créé par un déclencheur ou un pipeline exécuté manuellement. Les activités contenues dans le pipeline utilisent les valeurs des paramètres.
Un jeu de données est un paramètre fortement typé et une entité réutilisable/pouvant être référencée. Une activité peut faire référence à des jeux de données et peut utiliser les propriétés définies dans la définition du jeu de données

Un service lié est également un paramètre fortement typé qui contient les informations de connexion à un magasin de données ou à un environnement Compute. C’est également une entité réutilisable/pouvant être référencée.

### <a name="control-flow"></a>Flux de contrôle
Orchestration des activités du pipeline, qui inclut le chaînage des activités en une séquence, la création de branche et des paramètres. Ces éléments peuvent être définis au niveau du pipeline et les arguments sont transmis lors de l’appel du pipeline à la demande ou à partir d’un déclencheur. L’orchestration inclut également la transmission d’états personnalisés et la mise en boucle des conteneurs, autrement dit, des itérateurs Pour chaque.


Pour plus d’informations sur les concepts de Data Factory, consultez les articles suivants :

- [Jeux de données et services liés](concepts-datasets-linked-services.md)
- [Pipelines et activités](concepts-pipelines-activities.md)
- [Runtime d’intégration](concepts-integration-runtime.md)

## <a name="supported-regions"></a>Régions prises en charge :

Vous pouvez actuellement créer des fabriques de données dans les régions Est des États-Unis et Est des États-Unis 2. Une fabrique de données peut toutefois accéder à des magasins de données et à des services de calcul situés dans d’autres régions Azure pour déplacer des données entre des magasins de données ou pour traiter des données à l’aide des services de calcul.

Azure Data Factory ne permet pas en soi de stocker des données. Il vous permet de créer des workflows pilotés par les données afin d’orchestrer le déplacement de données entre les magasins de données pris en charge, ainsi que le traitement des données à l’aide des services de calcul situés dans d’autres régions ou dans un environnement local. Il vous permet également de surveiller et gérer des workflows au moyen de programmes et à l’aide des mécanismes de l’interface utilisateur.

Même si Data Factory est disponible uniquement dans les régions Est des États-Unis et Est des États-Unis 2, le service de déplacement des données intégré à Data Factory est disponible mondialement dans plusieurs régions. Si un magasin de données se trouve derrière un pare-feu, le déplacement des données est assuré au moyen d’une passerelle de gestion des données installée dans votre environnement local.

Supposons que vos environnements de calcul (cluster Azure HDInsight et Azure Machine Learning, par exemple) s’exécutent hors de la région Europe de l’ouest. Vous pouvez dans ce cas créer et utiliser une instance Azure Data Factory en Europe du Nord et l’utiliser pour planifier des tâches sur vos environnements de calcul en Europe de l’ouest. Quelques millisecondes suffisent à Data Factory pour déclencher la tâche dans votre environnement de calcul, mais l’heure d’exécution du travail dans votre environnement informatique ne change pas.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment créer une fabrique de données en suivant les instructions détaillées fournies dans les démarrages rapides suivants : [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [API REST](quickstart-create-data-factory-rest-api.md) et le portail Azure. 

