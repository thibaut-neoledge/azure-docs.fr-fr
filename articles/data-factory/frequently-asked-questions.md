---
title: "Azure Data Factory : Forum Aux Questions | Microsoft Docs"
description: Forum aux Questions sur Azure Data Factory.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: shlo
ms.openlocfilehash: 95b088d2fdc331dc33e973172d32892693fcb648
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory : Forum aux Questions
Cet article s’applique à la version 2 du service Azure Data Factory. Il contient une liste de questions fréquemment posées (FAQ) ainsi que leurs réponses.  

## <a name="what-is-azure-data-factory"></a>qu'est-ce qu'Azure Data Factory ? 
Data Factory est un service cloud d’intégration de données entièrement géré qui automatise le déplacement et la transformation des données. À l’instar d’une usine qui utilise des machines visant à transformer des matières premières en produits manufacturés, Azure Data Factory orchestre des services existants qui collectent des données brutes et les transforment en informations prêtes à l’emploi. Azure Data Factory vous permet de créer des flux de travail pilotés par les données pour déplacer des données entre des banques de données locales et cloud et traiter/transformer des données avec des services de calcul comme Azure HDInsight, Azure Data Lake Analytics et SQL Server Integration Services (SSIS) Integration Runtime. Data Factory permet d’exécuter votre traitement de données sur un service cloud Azure ou d’utiliser votre propre environnement Compute auto-hébergé comme SSIS, SQL Server et Oracle. Après avoir créé un pipeline qui exécute l’action dont vous avez besoin, vous pouvez planifier son exécution périodique (toutes les heures, tous les jours, toutes les semaines, etc.) ou déclencher le pipeline à partir d’une occurrence d’événement. Pour plus d’informations, consultez [Présentation d’Azure Data Factory](introduction.md).

## <a name="whats-different-in-version-2"></a>En quoi diffère la version 2 ?
Azure Data Factory version 2 est fondé sur le service de déplacement et de transformation de données Azure Data Factory d’origine, mais a été développé pour offrir un ensemble plus vaste de scénarios d’intégration de données au cloud. Azure Data Factory version 2 présente les fonctionnalités suivantes :

- Flux de contrôle et mise à l’échelle
- Déploiement et exécution de packages SSIS dans Azure

Après la sortie de la version 1, nous nous sommes rendu compte que les clients cherchaient à concevoir des scénarios d’intégration de données hybrides complexes nécessitant le déplacement et le traitement des données dans le cloud, localement et sur des machines virtuelles dans le cloud. Ces exigences ont mis en évidence la nécessité de transférer et de traiter les données dans des environnements de réseau virtuel sécurisés et de permettre une montée en charge en fournissant une puissance de traitement à la demande.

Puisque les pipelines de données deviennent des éléments essentiels de la stratégie d’analyse commerciale, nous avons constaté que ces activités de données cruciales avaient besoin d’une planification flexible pour prendre en charge les charges de données incrémentielles et les exécutions déclenchées par des événements. Enfin, à mesure que ces orchestrations gagnent en complexité, le service est également dans l’obligation de prendre en charge des paradigmes de flux de travail communs, tels que la création de branches, le bouclage et le traitement conditionnel.

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
    - Bouclage des conteneurs
        - ForEach 
- Flux basés sur déclencheur
    - Les pipelines peuvent être déclenchés à la demande ou selon une durée chronométrée.
- Flux delta
    - Utilisez des paramètres et définissez la marque de borne haute pour la copie delta lors du déplacement des tables de dimension ou de référence à partir d’un magasin relationnel, localement, ou dans le cloud pour charger les données dans le lac 

Pour plus d’informations, consultez l’article [Didacticiel : flux de contrôle](tutorial-control-flow.md).

### <a name="deploy-ssis-packages-to-azure"></a>Déployer des packages SSIS vers Azure 
Si vous souhaitez déplacer vos charges de travail SSIS, vous pouvez créer une fabrique de données version 2 et approvisionner un runtime Azure-SSIS IR. Le runtime Azure-SSIS IR est un cluster entièrement géré de machines virtuelles Azure (nœuds) dédié à l’exécution de vos packages SSIS dans le cloud. Pour obtenir des instructions pas à pas, consultez le didacticiel : [Déployer des packages SSIS vers Azure](tutorial-deploy-ssis-packages-azure.md). 
 

### <a name="sdks"></a>Kits de développement logiciel (SDK)
Si vous êtes un utilisateur expérimenté et recherchez une interface de programmation, la version 2 offre un ensemble enrichi de kits de développement logiciel (SDK) qui peut être utilisé pour créer, gérer et surveiller les pipelines à l’aide de votre IDE favori.

- SDK .NET : le Kit de développement logiciel (SDK) .NET est mis à jour pour la version 2. 
- PowerShell : les cmdlets PowerShell sont mises à jour pour la version 2. Les noms des cmdlets version 2 contiennent **DataFactoryV2**. Exemple : Get-AzureRmDataFactoryV2. 
- SDK Python : ce Kit de développement logiciel (SDK) est une nouveauté de la version 2.
- API REST : l’API REST est mise à jour pour la version 2.  

Les Kits de développement logiciel (SDK) mis à jour pour la version 2 ne sont pas compatibles avec les clients de la version 1. 

### <a name="monitoring"></a>Surveillance
Actuellement, la version 2 prend en charge la surveillance des fabriques de données uniquement avec les Kits de développement logiciel (SDK). Le portail n’assure pas encore cette prise en charge. 

## <a name="what-is-integration-runtime"></a>Qu’est-ce que Integration Runtime (IR) ?
IR est l’infrastructure de calcul utilisée par Azure Data Factory pour fournir les fonctionnalités d’intégration de données suivantes entre différents environnements réseau :

- **Déplacement des données** : déplace des données entre les banques de données d’un réseau public et celles d’un réseau privé (sur un réseau privé local ou virtuel). Les connecteurs intégrés, la conversion de format, le mappage de colonnes et les transferts de données performants et évolutifs sont pris en charge.
- **Répartition des activités** : répartit et analyse les activités de transformation exécutées sur un large éventail de services de calcul tels que Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, etc.
- **Exécution des packages SSIS** : exécute en mode natif les packages SSIS dans un environnement Compute Azure géré.

Vous pouvez déployer une ou plusieurs instances d’Integration Runtime en fonction de vos besoins pour déplacer ou transformer des données.  Integration Runtime peut s’exécuter dans un réseau public Azure ou un réseau privé (localement, réseau virtuel Azure ou AWS VPC [Amazon Web Services Virtual Private Cloud]). 

Pour plus d’informations, consultez l’article [Integration Runtime in Azure Data Factory](concepts-integration-runtime.md) (Integration Runtime dans Azure Data Factory).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Quelle est la limite au nombre de runtimes d’intégration ?
Le nombre d’instances d’Integration Runtime dont vous pouvez disposer dans une fabrique de données n’est pas limité. Il existe toutefois une limite quant au nombre de cœurs de machines virtuelles (VM) qu’Integration Runtime peut utiliser par abonnement pour l’exécution du package SSIS. Pour plus d’informations, consultez la section [Limites de Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="when-to-use-version-2-rather-than-version-1"></a>Quand est-il préférable d’utiliser la version 2 plutôt que la version 1 ? 
Si vous ne connaissez pas Azure Data Factory, passez directement à la version 2. Si vous utilisez déjà la version 1, régénérez vos fabriques de données dans la version 2.

> [!WARNING]
> La version 2 de Data Factory est une préversion et n’est pas mise à la disposition générale. Par conséquent, elle ne relève pas des mêmes engagements de contrat de niveau de service (SLA) Azure que la version 1 de Data Factory mise à la disposition générale. 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>Quels sont les concepts de niveau supérieur de la version 2 ?
Un abonnement Azure peut contenir une ou plusieurs instances Azure Data Factory (ou fabriques de données). Azure Data Factory s’articule autour de quatre composants clés. Ils fonctionnent ensemble et vous dotent de la plateforme sur laquelle composer des flux de travail orientés données constitués d’étapes de déplacement et de transformation des données.

### <a name="pipeline"></a>Pipeline
Une fabrique de données peut avoir un ou plusieurs pipelines. Un pipeline est un regroupement logique des activités nécessaires pour effectuer une unité de travail. Ensemble, les activités d’un pipeline effectuent une tâche. Par exemple, un pipeline peut contenir un groupe d’activités qui ingère des données à partir d’un objet Blob Azure, puis exécute une requête Hive sur un cluster HDInsight pour partitionner les données. L’avantage de cette opération, c’est que le pipeline vous permet de gérer les activités en tant que groupe et non pas individuellement. Les activités d’un pipeline peuvent être chaînées pour fonctionner de manière séquentielle ou peuvent fonctionner en parallèle de façon indépendante.

### <a name="activity"></a>Activité
Les activités représentent une étape de traitement dans un pipeline. Par exemple, vous pouvez utiliser une activité de copie pour copier des données d’une banque de données vers une autre. De même, vous pouvez utiliser une activité Hive qui exécute une requête Hive sur un cluster Azure HDInsight afin de convertir ou d’analyser vos données. Data Factory prend en charge trois types d’activités : les activités de déplacement des données, les activités de transformation des données et les activités de contrôle

### <a name="datasets"></a>JEUX DE DONNÉES
Les jeux de données représentent les structures des données dans les magasins. Ils pointent ou référencent simplement en tant qu’entrées ou sorties les données que vous voulez utiliser dans vos activités. 

### <a name="linked-services"></a>Services liés
Les services liés ressemblent à des chaînes de connexion. Ils définissent les informations de connexion nécessaires à Data Factory pour se connecter à des ressources externes. Voyez les choses de la façon suivante : un service lié définit la connexion à la source de données et un jeu de données représente la structure des données. Par exemple, un service lié Stockage Azure spécifie la chaîne de connexion pour se connecter au compte de stockage Azure. Et un jeu de données blob Azure spécifie le conteneur d’objets blob et le dossier qui contient les données.

Data Factory fait appel aux services liés pour deux raisons :

- Pour représenter une **banque de données** et notamment une instance SQL Server, une base de données Oracle, un partage de fichiers locaux ou un compte de stockage d’objets blob Azure. Pour obtenir la liste des banques de données prises en charge, consultez l’article sur l’[activité de copie](copy-activity-overview.md).
- Pour représenter une **ressource de calcul** qui peut héberger l’exécution d’une activité. Par exemple, l’activité HDInsightHive s’exécute sur un cluster HDInsight Hadoop. Pour obtenir la liste des activités de transformation et des environnements Compute pris en charge, consultez l’article sur la [transformation des données](transform-data.md).

### <a name="triggers"></a>Déclencheurs
Les déclencheurs correspondent à l’unité de traitement qui détermine le moment auquel une exécution de pipeline doit être lancée. Il existe différents types de déclencheurs pour différents types d’événements ; dans la préversion, nous prenons en charge un déclencheur par planificateur de temps horloge. 


### <a name="pipeline-runs"></a>Exécutions de pipeline
Une exécution du pipeline est une instance de l’exécution du pipeline. Les exécutions de pipeline sont généralement instanciées par la transmission des arguments aux paramètres définis dans les pipelines. Les arguments peuvent être transmis manuellement ou être inclus dans la définition du déclencheur.

### <a name="parameters"></a>Paramètres
Les paramètres sont des paires clé-valeur de configuration en lecture seule.  Ils sont définis dans le pipeline, et les arguments des paramètres définis sont transmis au cours de l’exécution à partir du contexte d’exécution créé par un déclencheur ou un pipeline exécuté manuellement. Les activités contenues dans le pipeline utilisent les valeurs des paramètres.
Un jeu de données est un paramètre fortement typé et une entité réutilisable/pouvant être référencée. Une activité peut faire référence à des jeux de données et peut utiliser les propriétés définies dans la définition du jeu de données

Un service lié est également un paramètre fortement typé qui contient les informations de connexion à une banque de données ou à un environnement Compute. C’est également une entité réutilisable/pouvant être référencée.

### <a name="control-flow"></a>Flux de contrôle
Orchestration des activités du pipeline, qui inclut le chaînage des activités en une séquence, la création de branche et des paramètres. Ces éléments peuvent être définis au niveau du pipeline et les arguments sont transmis lors de l’appel du pipeline à la demande ou à partir d’un déclencheur. L’orchestration inclut également la transmission d’états personnalisés et le bouclage des conteneurs, autrement dit, des itérateurs ForEach.


Pour plus d’informations sur les concepts de Data Factory, consultez les articles suivants :

- [Jeux de données et services liés](concepts-datasets-linked-services.md)
- [Pipelines et activités](concepts-pipelines-activities.md)
- [Runtime d’intégration](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Quel est le modèle de tarification de Data Factory ?
Pour plus d’informations sur la tarification d’Azure Data Factory, consultez la [page de tarification de Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="what-regions-support-azure-data-factory-version-2"></a>Quelles sont les régions qui prennent en charge Azure Data Factory version 2 ?
Vous pouvez actuellement créer des fabriques de données de version 2 dans les régions États-Unis de l’Est et États-Unis de l’Est 2. Toutefois, une fabrique de données peut utiliser Integration Runtime dans une autre région pour déplacer des données entre les banques de données, les activités de répartition dans les services de calcul ou la distribution de packages SSIS.  Pour plus d’informations, consultez la section relative aux [emplacements de Data Factory](concepts-integration-runtime.md#integration-runtime-location).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Comment se tenir informé sur Data Factory ?
Utilisez les sites suivants pour vous tenir informé sur Azure Data Factory :

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Page d’accueil de la documentation](/azure/data-factory)
- [Page d’accueil du produit](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Présentation technique approfondie 

### <a name="can-i-have-version-1-and-version-2-pipelines-run-side-by-side"></a>Puis-je exécuter côte à côte des pipelines version 1 et version 2 ?
Non. Les fabriques de données de version 2 ou de version 1 ne peuvent pas avoir des entités (services liés, jeux de données, pipelines, etc.) de l’autre version.   

### <a name="do-i-still-need-to-define-datasets-in-version-2"></a>Dois-je toujours définir des jeux de données dans la version 2 ?
Le jeu de données n’est plus une entité obligatoire pour la plupart des activités. Il est requis pour les activités Machine Learning, de copie, de recherche et de validation ainsi que pour les activités personnalisées qui utilisent le schéma et d’autres informations de métadonnées dans le jeu de données pour la transformation. Les autres activités ne nécessitent plus de jeux de données.

### <a name="can-i-chain-two-activities-without-a-dataset-in-version-2"></a>Puis-je chaîner deux activités sans jeu de données dans la version 2 ?
Oui. Vous pouvez chaîner des activités dans la version 2 sans jeux de données. Pour ce faire, utilisez la propriété **dependsOn** dans la définition JSON de votre pipeline. 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>Toutes les activités de la version 1 sont-elles prises en charge dans la version 2 ? 
Oui, elles le sont toutes.

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>Comment planifier un pipeline de version 2 ? 
Vous pouvez utiliser le déclencheur Scheduler pour planifier un pipeline de version 2. Il utilise un planning horaire et permet aux utilisateurs de planifier les pipelines périodiquement ou à l’aide de modèles récurrents basés sur le calendrier (par exemple, toutes les semaines, les lundis à 18 h 00 et jeudis à 21 h 00). Pour plus d’informations, consultez l’article [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>Puis-je transmettre des paramètres à un pipeline exécuté dans la version 2 ?
Oui, les paramètres sont un concept de niveau supérieur de premier ordre dans la version 2. Vous pouvez les définir au niveau du pipeline et transmettre des arguments lors de l’exécution du pipeline à la demande ou à l’aide d’un déclencheur.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Puis-je définir des valeurs par défaut pour les paramètres des pipelines ? 
Oui. Vous pouvez définir des valeurs par défaut pour les paramètres des pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-passed-to-a-pipeline-run"></a>Une activité dans un pipeline peut-elle consommer des arguments transmis à un pipeline exécuté ? 
Oui. Chaque activité du pipeline peut consommer la valeur de paramètre transmise au pipeline exécuté avec la construction `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Une propriété de sortie d’activité peut-elle être consommée dans une autre activité ? 
Oui. Une sortie d’activité peut être consommée dans une activité suivante avec la construction @activity.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Comment gérer correctement les valeurs Null dans une sortie d’activité ? 
Vous pouvez utiliser la construction `@coalesce` dans les expressions pour gérer correctement les valeurs Null. 

### <a name="can-i-use-retries-timeouts-at-the-activity-level-in-version-2"></a>Puis-je utiliser de nouvelles tentatives, des délais d’expiration au niveau des activités dans la version 2 ?
Oui. Vous pouvez configurer de nouvelles tentatives et des délais d’expiration au niveau des activités pour régir l’exécution des activités dans la version 2 comme dans la version 1. 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des instructions pas à pas pour créer une fabrique de données de version 2, consultez les didacticiels suivants :

- [Quickstart : create a data factory](quickstart-create-data-factory-dot-net.md) (Démarrage rapide : créer une fabrique de données et un pipeline à l’aide du Kit de développement logiciel (SDK) .NET)
- [Didacticiel : copier des données dans le cloud](tutorial-copy-data-dot-net.md)

