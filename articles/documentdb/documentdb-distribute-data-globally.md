---
title: "Distribution mondiale des données avec DocumentDB | Microsoft Docs"
description: "Apprenez-en plus sur la géoréplication à l’échelle de la planète, le basculement et la récupération des données à l’aide de bases de données mondiales à partir d’Azure DocumentDB, un service de base de données NoSQL entièrement géré."
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 746fefab12aae11774a7d582e39ac2bc7eb9c88b
ms.lasthandoff: 04/25/2017


---
# <a name="documentdb---a-globally-distributed-database-service-on-azure"></a>DocumentDB, un service de base de données mondialement distribué sur Azure
Avec plus de 30 régions géographiques, Azure est partout et continue de s’étendre. Présente dans le monde entier, l’une des fonctionnalités différenciées qu’Azure offre à ses développeurs est la possibilité de générer, de déployer et de gérer facilement des applications mondialement distribuées. DocumentDB est le système de base de données multilocataire et mondialement distribué de Microsoft, conçu pour permettre aux développeurs de créer des applications à l’échelle de la planète. DocumentDB vous permet de mettre à l’échelle de manière flexible, le débit et le stockage pour le nombre de régions géographiques de votre choix. Le service offre une latence faible garantie à 99,99 %, une haute disponibilité à 99,99 %, un débit prévisible et [plusieurs modèles de cohérence bien définis](documentdb-consistency-levels.md), tous pris en charge par des contrats de niveau de service complets. En raison de son [moteur de base de données sans schéma et optimisé pour l’écriture](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), par défaut, DocumentDB est capable d’indexer automatiquement toutes les données reçues, et distribue les requêtes intégrées aux langages [SQL](documentdb-sql-query.md), [MongoDB](documentdb-protocol-mongodb.md) et [JavaScript](documentdb-programming.md#javascript-language-integrated-query-api) de manière indépendante. En tant que service cloud, DocumentDB est dès le départ conçu avec des fonctions multilocataires et de distribution mondiale.

**Une collection DocumentDB unique, partitionnée et distribuée dans trois régions Azure**

![Collection Azure DocumentDB partitionnée et distribuée dans trois régions Azure](./media/documentdb-distribute-data-globally/documentdb-global-apps.png)

Comme nous l’avons appris lors de la création DocumentDB, l’ajout de la distribution globale ne peut pas venir après coup. Cette fonction ne peut pas être « rajoutée » sur un système de base de données à un seul site. Les possibilités offertes par une base de données mondialement distribuée s’étendent au-delà de la récupération d’urgence géographique traditionnelle proposée par les bases de données à un seul site. Les bases de données à un seul site offrant la fonctionnalité de récupération d’urgence géographique sont un sous-ensemble strict des bases de données mondialement distribuées. 

Avec la distribution mondiale clé en main de DocumentDB, les développeurs n’ont pas à créer leur propre génération de modèles automatique de réplication en utilisant soit le modèle d’expression Lambda (par exemple, [réplication AWS DynamoDB](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) sur le journal de base de données ou en effectuant des « doubles écritures » dans plusieurs régions. Nous déconseillons ces approches, car il est impossible de garantir leur exactitude et de fournir des contrats de niveau de service fiables. 

Dans cet article, nous fournissons une vue d’ensemble des fonctionnalités de distribution mondiale de DocumentDB. Nous décrivons également l’approche unique de DocumentDB pour fournir des contrats SLA complets. 

## <a id="EnableGlobalDistribution"></a>Activation de la distribution mondiale clé en main
DocumentDB fournit les fonctionnalités suivantes pour vous permettre d’écrire facilement des applications à l’échelle planétaire. Ces fonctionnalités sont disponibles par l’intermédiaire des [API REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/) basées sur le fournisseur de ressources de DocumentDB, ainsi que dans le portail Azure.

### <a id="RegionalPresence"></a>Omniprésence régionale 
Azure étend constamment sa présence géographique en ajoutant de nouvelles régions en ligne. DocumentDB est disponible dans toutes les régions Azure, par défaut. Cela vous permet d’associer une région géographique avec votre compte de base de données DocumentDB dès qu’Azure ouvre une nouvelle région aux entreprises.

**DocumentDB est disponible dans toutes les régions Azure, par défaut**

![DocumentDB disponible dans toutes les régions Azure, par défaut](./media/documentdb-distribute-data-globally/azure-regions.png)

### <a id="UnlimitedRegionsPerAccount"></a>Associer un nombre illimité de régions avec votre compte de base de données DocumentDB
DocumentDB vous permet d’associer n’importe quel nombre de régions Azure avec votre compte de base de données DocumentDB. En dehors des restrictions de délimitations géographiques (par exemple, pour la Chine et l’Allemagne), il n’existe aucune restriction quant au nombre de régions qui peuvent être associées à votre compte de base de données DocumentDB. La figure suivante illustre un compte de base de données configuré pour couvrir 21 régions Azure.  

**Compte de base de données DocumentDB d’un locataire couvrant 21 régions Azure**

![Compte de base de données DocumentDB couvrant 21 régions Azure](./media/documentdb-distribute-data-globally/documentdb-spanning-regions.png)

### <a id="PolicyBasedGeoFencing"></a>Délimitations géographiques basées sur des stratégies
DocumentDB est conçu pour disposer de fonctionnalités de délimitation géographique basée sur des stratégies. La délimitation géographique est un composant important pour garantir la gouvernance des données et les restrictions de conformité, des données, et peut empêcher l’association d’une région spécifique avec votre compte. Les exemples de délimitation géographique incluent (mais sans s’y limiter) l’étendue de la distribution mondiale aux régions dans un cloud souverain (par exemple, la Chine et l’Allemagne), ou dans les limites d’imposition du gouvernement (par exemple, l’Australie). Les stratégies sont contrôlées à l’aide des métadonnées de votre abonnement Azure.

### <a id="DynamicallyAddRegions"></a>Ajouter et supprimer des régions dynamiquement
DocumentDB vous permet d’ajouter (associer) ou de supprimer (dissocier) les régions de votre compte de base de données à tout moment (voir [figure précédente](#UnlimitedRegionsPerAccount)). En vertu de la réplication de données à travers les partitions en parallèle, DocumentDB garantit une disponibilité dans les 30 minutes que lorsqu’une nouvelle région est en ligne, n’importe où dans le monde et jusqu’à 100 To. 

### <a id="FailoverPriorities"></a>Priorités de basculement
Pour contrôler l’ordre exact des basculements régionaux lors d’une panne dans plusieurs régions, DocumentDB vous permet d’associer la priorité à différentes régions associées au compte de la base de données (voir figure suivante). DocumentDB garantit que la séquence de basculement automatique a lieu dans l’ordre de priorité que vous avez spécifié. Pour plus d’informations sur les basculements régionaux, consultez [Basculements régionaux automatiques pour la continuité des activités dans DocumentDB](documentdb-regional-failovers.md).

**Un locataire de DocumentDB peut configurer l’ordre de priorité de basculement (volet droit) pour les régions associées à un compte de base de données**

![Configuration des priorités de basculement avec Azure DocumentDB](./media/documentdb-distribute-data-globally/documentdb-failover-priorities.png)

### <a id="OfflineRegions"></a>Déconnecter une région dynamiquement
DocumentDB vous permet de mettre votre compte de base de données hors connexion dans une région spécifique, puis de le remettre en ligne ultérieurement. Les régions marquées comme étant déconnectées ne participent pas activement à la réplication et ne font pas partie de la séquence de basculement. Cela vous permet de geler la dernière image correcte connue de la base de données dans l’une des régions en lecture avant de déployer des mises à niveau potentiellement dangereuses pour votre application.

### <a id="ConsistencyLevels"></a>Plusieurs modèles de cohérence bien définis pour les bases de données mondialement répliquées
DocumentDB expose [plusieurs niveaux de cohérence bien définis](documentdb-consistency-levels.md) pris en charge par des contrats de niveau de service. Vous pouvez choisir un modèle de cohérence spécifique (à partir de la liste des options disponibles) en fonction des charges de travail/scénarios. 

### <a id="TunableConsistency"></a>Cohérence ajustable pour les bases de données répliquées mondialement
DocumentDB vous permet de remplacer par programmation et d’assouplir le choix de cohérence par défaut à la demande, lors de l’exécution. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Régions de lecture et d’écriture configurables de manière dynamique
DocumentDB vous permet de configurer les régions (associées à la base de données) en « lecture », en « écriture » ou en « lecture/écriture ». 

### <a id="ElasticallyScaleThroughput"></a>Mise à l’échelle flexible du débit à travers les régions Azure
Vous pouvez mettre à l’échelle une collection DocumentDB de manière flexible en configurant le débit par programme. Le débit est appliqué à toutes les régions dans lesquelles la collection est distribuée.

### <a id="GeoLocalReadsAndWrites"></a>Lectures et écritures géo-locales
Le principal avantage d’une base de données mondialement distribuée est de proposer un accès aux données ne présentant qu’une faible latence n’importe où dans le monde. DocumentDB offre des garanties de latence faible à 99,99 % pour diverses opérations de base de données. La solution garantit que toutes les lectures sont acheminées vers la région de lecture locale le plus proche. Pour répondre à une demande de lecture, le quorum se localise dans la région dans laquelle la lecture est émise et utilisé ; il en va de même pour les écritures. Une écriture est reconnue uniquement après qu’une majorité de réplicas ont durablement validé l’écriture localement, mais sans être contrôlés sur des réplicas à distance pour reconnaître les écritures. En d’autres termes, le protocole de réplication de DocumentDB fonctionne en partant du principe que les quorums en lecture et en écriture sont toujours locaux pour les régions en lecture et en écriture, respectivement, dans lesquelles les demandes sont émises.

### <a id="ManualFailover"></a>Initier manuellement le basculement régional
DocumentDB vous permet de déclencher le basculement du compte de base de données pour valider les propriétés de disponibilité de l’application entière de *bout en bout* (au-delà de la base de données). Étant donné que les propriétés de sécurité et d’activité de la détection de défaillances et du choix de responsable sont garanties, DocumentDB garantit *l’absence de perte de données* lors des opérations de basculement manuelles initiées par les locataires.

### <a id="AutomaticFailover"></a>Basculement automatique
DocumentDB prend en charge le basculement automatique en cas d’une ou de plusieurs pannes de courant régionales. Lors d’un basculement régional, DocumentDB gère la latence de lecture, le temps de disponibilité, la cohérence et les contrats de niveau de service de débit. DocumentDB fournit une limite supérieure pour le temps d’achèvement d’une opération de basculement automatique. Il s’agit de la fenêtre de perte de données potentielle pendant une panne régionale.

### <a id="GranularFailover"></a>Conçu pour différentes granularités de basculement
Actuellement, les fonctionnalités de basculement automatiques et manuelles sont exposées au niveau de granularité du compte de base de données. Notez que, en interne, DocumentDB est conçu pour offrir un basculement *automatique* au niveau de granularité le plus fin d’une base de données, d’une collection, voire d’une partition (d’une collection possède un éventail de clés). 

### <a id="MultiHomingAPIs"></a>API de multihébergement dans DocumentDB
DocumentDB vous permet d’interagir avec la base de données à l’aide des points de terminaison logiques (sans région) ou physiques (spécifiques à une région). L’utilisation de points de terminaison logiques garantit que l’application peut en toute transparence être multihébergée en cas de basculement. Les points de terminaison physiques, quant à eux, fournissent un contrôle précis à l’application pour rediriger les lectures et écritures vers des régions spécifiques.

### <a id="ReadPreferencesAPIforMongoDB"></a> Préférences de lecture configurables dans l’API pour MongoDB
L’API pour MongoDB vous permet de spécifier les préférences de lecture de votre collection pour une base de données globalement distribuée. Pour les lectures à faible latence et la haute disponibilité globale, nous vous recommandons de définir les préférences de lecture de votre collection sur *La plus proche*. Une préférence de lecture définie sur *La plus proche* est configurée pour lire à partir de la région la plus proche.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Pour les applications avec une région primaire en lecture/écriture et une région secondaire pour les scénarios de récupération d’urgence, nous vous recommandons de définir les préférences de lecture de votre collection sur *Secondary preferred* (Secondaire par défaut). Une préférence de lecture définie sur *Secondary preferred* (Secondaire par défaut) est configurée pour lire à partir de la région secondaire lorsque la région primaire n’est pas disponible.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Enfin, si vous souhaitez spécifier manuellement vos régions de lecture, vous pouvez définir la balise de la région dans vos préférences de lecture.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

### <a id="TransparentSchemaMigration"></a>Schéma de base de données et migration d’index transparents et cohérents 
DocumentDB est entièrement [sans schéma](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). La conception unique de son moteur de base de données permet d’indexer automatiquement et de manière synchrone toutes les données reçues sans nécessiter de schéma ou d’un index secondaire de votre part. Cela vous permet d’itérer votre application mondialement distribuée rapidement et sans soucis liés au schéma de base de données et à la migration d’index de base de données ou à la coordination des déploiements d’application en plusieurs phases de modifications de schéma. DocumentDB garantit que les modifications apportées à l’indexation de stratégies explicitement faites par vous n’entraînent pas de dégradation des performances ou de la disponibilité.  

### <a id="ComprehensiveSLAs"></a>Contrats de niveau de service complets (au-delà de la simple haute disponibilité)
En tant que service de base de données mondialement distribué, DocumentDB propose des contrats SLA bien définis pour la **perte de données**, la **disponibilité**, la **latence à 99,99 %**, le **débit** et la **cohérence** pour la base de données dans son ensemble, quel que soit le nombre de régions associées à la base de données.  

## <a id="LatencyGuarantees"></a>Garanties de latence
Le principal avantage d’un service de base de données mondialement distribué comme DocumentDB est de proposer un accès à vos données ne présentant qu’une faible latence n’importe où dans le monde. DocumentDB offre une latence faible garantie à 99,99 % pour diverses opérations de base de données. Le protocole de réplication que DocumentDB emploie garantit que les opérations de base de données (dans l’idéal, lectures et écritures) sont toujours effectuées dans la région locale du client. Le contrat SLA pour la latence DocumentDB inclut 99,99 % pour les lectures, les écritures indexées (de manière synchrone) et les requêtes pour différentes tailles de demande et de réponse. Les garanties de latence pour les écritures incluent les validations de quorum majoritaire durables dans le centre de données local.

### <a id="LatencyAndConsistency"></a>Relation de latence avec la cohérence 
Pour qu’un service mondialement distribué offre une cohérence forte dans une configuration mondialement distribuée, il doit effectuer une réplication synchrone des écritures ou des lectures interrégionales synchrones ; la vitesse de la lumière et la fiabilité du réseau étendu déterminent qu’une cohérence forte entraîne des latences élevées et une faible disponibilité des opérations de base de données. Par conséquent, pour offrir une garantie de latence faible à 99,99 % et une disponibilité à 99,99 %, le service doit utiliser la réplication asynchrone. Cela nécessite que le service offre également des [choix de cohérence bien définis et flexibles](documentdb-consistency-levels.md) – plus faibles que puissants (pour offrir des garanties de latence faible et de disponibilité) et, dans l’idéal, plus puissants que la cohérence « finale » (pour proposer un modèle de programmation intuitif).

DocumentDB garantit qu’une opération de lecture n’est pas requise pour contacter des réplicas dans plusieurs régions afin de fournir la garantie de niveau de cohérence spécifique. De même, la solution garantit qu’une opération d’écriture n’est pas bloquée pendant que les données sont répliquées dans toutes les régions (par exemple, les écritures sont répliquées de manière asynchrone dans différentes régions). Dans le cas de comptes de base de données multirégions, plusieurs niveaux de cohérence flexibles sont disponibles. 

### <a id="LatencyAndAvailability"></a>Relation de la latence avec la disponibilité 
La latence et la disponibilité sont les deux faces d’une même pièce. Dans un état stable, nous parlons de latence de l’opération et, en cas de défaillance, nous parlons de disponibilité. Du point de vue de l’application, une opération de base de données lente ne peut pas être différenciée d’une base de données qui n’est pas disponible. 

Pour distinguer une latence élevée de l’indisponibilité, DocumentDB fournit une limite supérieure absolue sur la latence de diverses opérations de base de données. Si l’opération de base de données prend plus de temps que la limite supérieure pour se terminer, DocumentDB renvoie une erreur de délai d’attente. Le contrat de niveau de service de disponibilité DocumentDB garantit que les délais d’expiration sont imputés au contrat de niveau de service de disponibilité. 

### <a id="LatencyAndThroughput"></a>Relation de latence avec le débit
DocumentDB ne vous fait pas choisir entre la latence et le débit. La solution respecte le contrat de niveau de service pour la latence à 99,99 % et vous fournit le débit que vous avez configuré. 

## <a id="ConsistencyGuarantees"></a>Garanties de cohérence
Bien que le [modèle de cohérence fort](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) soit la référence en matière de programmabilité, il est proposé au prix d’une latence élevée (dans un état stable) et d’une perte de disponibilité (en cas de défaillance). 

DocumentDB vous offre un modèle de programmation bien défini vous permettant d’analyser à la cohérence des données répliquées. Afin de vous permettre de créer des applications multihébergées, les modèles de cohérence exposés par DocumentDB sont conçus pour être sans région, et indépendants de la région où les lectures et les écritures sont fournies. 

Le contrat de niveau de service de la cohérence de DocumentDB garantit que 100 % des demandes de lecture respectent la garantie de cohérence pour le niveau de cohérence que vous avez demandé (le niveau de cohérence par défaut sur le compte de base de données ou la valeur remplacée lors de la demande). Une demande de lecture est considérée comme respectant le contrat de niveau de service de cohérence si toutes les garanties de cohérence associées au niveau de cohérence sont satisfaites. Le tableau suivant répertorie les garanties de cohérence qui correspondent aux niveaux de cohérence spécifiques proposés par DocumentDB.

**Garanties de cohérence associées à un niveau de cohérence donné dans DocumentDB**

<table>
    <tr>
        <td><strong>Niveau de cohérence</strong></td>
        <td><strong>Caractéristiques de la cohérence</strong></td>
        <td><strong>CONTRAT SLA</strong></td>
    </tr>
    <tr>
        <td rowspan="3">session</td>
        <td>Lecture de votre propre écriture</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td>Lecture unitone</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td>Préfixe cohérent</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td rowspan="3">Bounded staleness (En fonction de l'obsolescence)</td>
        <td>Lecture unitone (au sein d’une région)</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td>Préfixe cohérent</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td>Obsolescence limitée &lt; K, T</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td>Eventual (Éventuel)</td>
        <td>Préfixe cohérent</td>
        <td>100 %</td>
    </tr>
    <tr>
        <td>Remarque</td>
        <td>Linéarisable</td>
        <td>100 %</td>
    </tr>
</table>

### <a id="ConsistencyAndAvailability"></a>Relation de la cohérence avec la disponibilité
Le [résultat d’impossibilité](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) du [théorème CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) prouve qu’il est en effet impossible que le système reste disponible et offre une cohérence linéarisable en cas de défaillance. Les services de base de données doivent choisir d’être CP ou AP. Les systèmes CP renoncent à la disponibilité en faveur d’une cohérence linéarisable, tandis que les systèmes AP renoncent à la [cohérence linéarisable](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) en faveur de la disponibilité. DocumentDB n’enfreint jamais le niveau de cohérence demandé, ce qui en fait un système formellement CP. Toutefois, dans la pratique, la cohérence n’est pas une proposition à prendre ou à laisser : il existe plusieurs modèles de cohérence bien définis dans le spectre de cohérence entre linéarisable et cohérence finale. Dans DocumentDB, nous avons essayé d’identifier plusieurs des modèles de cohérence assouplis avec une applicabilité au monde réel et un modèle de programmation intuitif. DocumentDB navigue entre les compromis sur la cohérence et sur la disponibilité, en proposant un contrat de niveau de service de disponibilité à 99,99 %, ainsi que [plusieurs niveaux de cohérence assouplis, mais bien définis](documentdb-consistency-levels.md). 

### <a id="ConsistencyAndAvailability"></a>Relation de la cohérence avec la latence
Une variation plus complète du CAP a été proposée par le professeur Daniel Abadi ; elle est appelée [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), qui constitue également un compromis en termes de latence et de cohérence dans un état stable. Elle indique que, dans un état stable, le système de base de données doit choisir entre la cohérence et la latence. Avec plusieurs modèles de cohérence assouplis (pris en charge par la réplication asynchrone et la lecture en locale, quorums d’écriture), DocumentDB garantit que toutes les lectures et écritures sont effectuées dans les régions de lecture et d’écriture, respectivement.  Ainsi, DocumentDB offre des garanties de faible latence dans la région pour les niveaux de cohérence.  

### <a id="ConsistencyAndThroughput"></a>Relation de la cohérence avec le débit
Étant donné que l’implémentation d’un modèle de cohérence spécifique varie selon le choix d’un [type de quorum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), le débit varie également selon le choix de la cohérence. Par exemple, dans DocumentDB, le débit avec des lectures fortement cohérentes représente environ la moitié de celui des lectures cohérentes. 
 
**Relation de la capacité de lecture pour un niveau de cohérence spécifique dans DocumentDB**

![Relation entre cohérence et débit](./media/documentdb-distribute-data-globally/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>Garanties de débit 
DocumentDB vous permet, en toute flexibilité, de mettre à l’échelle le débit, ainsi que le stockage, dans des différentes régions en fonction de la demande. 

**Une collection DocumentDB unique, partitionnée (sur trois partitions) et distribuée dans trois régions Azure**

![Collections distribuées et partitionnées d’Azure Document DB](./media/documentdb-distribute-data-globally/documentdb-throughput.png)

Une collection DocumentDB est distribuée dans deux dimensions : au sein d’une région, puis entre les régions. Voici comment procéder : 

* Dans une région unique, une collection DocumentDB est mise à l’échelle en termes de partitions de ressources. Chaque partition de ressources gère un ensemble de clés et est fortement cohérente et hautement disponible en vertu de la réplication de machines d’état parmi un jeu de réplicas. DocumentDB est un système entièrement régi par les ressources où une partition de ressources est chargée de fournir sa part du débit pour le budget de ressources système qui lui est alloué. La mise à l’échelle d’une collection DocumentDB est complètement transparente : DocumentDB gère les partitions de ressources, et les fractionne et les fusionne en fonction des besoins. 
* Chaque partition de ressources est ensuite distribuée dans plusieurs régions. Les partitions de ressources possédant le même jeu de clés dans différentes régions forment le jeu de partitions (voir [figure précédente](#ThroughputGuarantees)).  Les partitions de ressources au sein d’un jeu de partitions sont coordonnées à l’aide de la réplication de machines d’état dans les différentes régions. Selon le niveau de cohérence configuré, les partitions de ressources au sein d’un jeu de partitions sont configurées dynamiquement à l’aide de différentes topologies (étoile, cascade, arborescence, etc.). 

Grâce à une gestion des partitions très réactive, à l’équilibrage de charge et à une gouvernance stricte des ressources, DocumentDB vous permet des mises à l’échelle flexibles dans plusieurs régions Azure sur une collection DocumentDB. La modification du débit sur une collection est une opération d’exécution dans DocumentDB ; comme pour d’autres opérations de base de données, DocumentDB garantit la limite supérieure absolue de la latence pour votre demande de modification du débit. Par exemple, la figure suivante montre la collection d’un client dont le débit est configuré en toute flexibilité (1 à 10 millions de demandes/s entre deux régions) en fonction de la demande.
 
**Collection d’un client dont le débit est configuré en toute flexibilité (1 à 10 millions de demandes/s)**

![Débit configuré en toute flexibilité d’Azure DocumentDB](./media/documentdb-distribute-data-globally/documentdb-elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>Relation du débit avec la cohérence 
Identique à la[Relation de la cohérence avec le débit](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Relation du débit avec la disponibilité
DocumentDB maintient sa disponibilité lorsque des modifications sont apportées au débit. DocumentDB gère en toute transparence les partitions (notamment le fractionnement, la fusion, les opérations de clonage) et s’assure que les opérations n’altèrent pas la performance ou la disponibilité, tandis que l’application augmente ou diminue le débit en toute flexibilité. 

## <a id="AvailabilityGuarantees"></a>Garanties de disponibilité
DocumentDB offre un contrat de niveau de service de disponibilité à 99,99 % pour chacune des opérations de données et de plan de contrôle. Comme indiqué précédemment, les garanties de disponibilité de DocumentDB incluent une limite supérieure absolue de la latence pour toutes les opérations de données et de plan de contrôle. Les garanties de disponibilité sont fermes et ne changent pas avec le nombre de régions ou la distance géographique entre les régions. Les garanties de disponibilité s’appliquent au basculement manuel et automatique. DocumentDB offre des API multihébergement transparentes qui garantissent que votre application fonctionne sur les points de terminaison logiques et achemine en toute transparence les demandes vers la nouvelle région en cas de basculement. En d’autres termes, votre application ne doit pas être redéployée lors du basculement régional, et les contrats de niveau de service de disponibilité sont maintenus.

### <a id="AvailabilityAndConsistency"></a>Relation de la disponibilité avec la cohérence, la latence et le débit
La relation de la disponibilité avec la cohérence, la latence et le débit est décrite dans [Relation de la cohérence avec la disponibilité](#ConsistencyAndAvailability), [Relation de la latence avec la disponibilité](#LatencyAndAvailability) et [Relation du débit avec la disponibilité](#ThroughputAndAvailability). 

## <a id="GuaranteesAgainstDataLoss"></a>Garanties et comportement du système en cas de « perte de données »
Dans DocumentDB, chaque partition (d’une collection) est rendue hautement disponible par un certain nombre de réplicas, réparties entre 10 à 20 domaines d’erreur au moins. Toutes les écritures sont validées de façon synchrone et durablement par un quorum majoritaire de réplicas avant d’être confirmées au client. La réplication asynchrone est appliquée de manière coordonnée entre les partitions réparties dans plusieurs régions. DocumentDB garantit qu’il n’y a aucune perte de données en cas de basculement manuel initié par un locataire. Pendant le basculement automatique, DocumentDB garantit une limite supérieure égale à l’intervalle d’obsolescence limitée configuré pour la fenêtre de perte de données dans le cadre de son contrat de niveau de service.

## <a id="CustomerFacingSLAMetrics"></a>Mesures de contrat de niveau de service orientées client
DocumentDB expose en toute transparence les mesures de débit, de latence, de cohérence et de disponibilité. Ces mesures sont accessibles par programme et dans le portail Azure (voir la figure suivante). Vous pouvez également définir des alertes sur différents seuils à l’aide d’Azure Application Insights.
 
**Mesures de cohérence, de latence, de débit et de disponibilité disponibles en toute transparence pour chaque locataire**

![Mesures du contrat de niveau de service visible par le client d’Azure DocumentDB](./media/documentdb-distribute-data-globally/documentdb-customer-slas.png)

## <a id="Next Steps"></a>Étapes suivantes
* Pour mettre en œuvre la réplication mondiale sur votre compte DocumentDB à l’aide du portail Azure, consultez la section [Comment effectuer la réplication de base de données mondiale DocumentDB à l’aide du portail Azure](documentdb-portal-global-replication.md).
* Pour savoir comment implémenter des architectures multimaîtres avec DocumentDB, consultez [Architectures de base de données multimaîtres avec Azure DocumentDB](documentdb-multi-region-writers.md).
* Pour plus d’informations sur les basculements automatiques et manuels de tâches dans DocumentDB, consultez [Basculements régionaux dans Azure DocumentDB](documentdb-regional-failovers.md).

## <a id="References"></a>Références
1. Eric Brewer. [Towards Robust Distributed Systems](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) (Vers des systèmes distribués robustes)
2. Eric Brewer. [CAP Twelve Years Later – How the rules have changed](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf) (CAP douze ans plus tard : en quoi les règles ont-elles changé ?)
3. Gilbert, Lynch. - [Brewer&#39;s Conjecture and Feasibility of Consistent, Available, Partition Tolerant Web Services](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) (Conjecture et faisabilité de services web cohérents, disponibles et tolérant le partitionnement)
4. Daniel Abadi. [Consistency Tradeoffs in Modern Distributed Database Systems Design](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf) (Compromis en termes de cohérence dans la conception des systèmes de base de données distribués modernes)
5. Martin Kleppmann. [Please stop calling databases CP or AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html) (Arrêtez d’appeler les bases de données CP ou AP)
6. Peter Bailis et al. [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (Probabilités en fonction de l’obsolescence limitée (PBS) pour les quorums partiels pratiques)
7. Naor and Wool. [Load, Capacity and Availability in Quorum Systems](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) (Charge, capacité et disponibilité dans les systèmes de quorum)
8. Herlihy and Wing. [Lineralizability: A correctness condition for concurrent objects](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) (Linéarisabilité : Une condition d’exactitude pour les objets simultanés)
9. [Contrat de niveau de service Azure DocumentDB](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/)

