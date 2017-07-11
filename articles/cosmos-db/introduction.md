---
title: "Introduction à Azure Cosmos DB | Microsoft Docs"
description: "En savoir plus sur Azure Cosmos DB. Cette base de données multimodèle distribuée à l’échelle mondiale est conçue pour offrir une faible latence, une scalabilité élastique et une haute disponibilité."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 49eb2e4f7d57de44a3b7a877dfdd138f4c374436
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017

---

<a id="welcome-to-azure-cosmos-db" class="xliff"></a>

# Bienvenue dans Azure Cosmos DB

Azure Cosmos DB est un service de base de données multimodèle mondialement distribué de Microsoft. En un clic, le service Azure Cosmos DB vous permet de faire évoluer en toute flexibilité et de façon indépendante le débit et le stockage sur n’importe quel nombre de régions géographiques Azure. Il offre des garanties en termes de débit, de latence, de disponibilité et de cohérence avec des [contrats SLA complets](https://aka.ms/acdbsla), ce qu’aucun autre service de base de données ne peut offrir.

![Azure Cosmos DB est le service de base de données distribué mondialement de Microsoft qui propose une augmentation de la taille des instances, une faible latence, cinq modèles de cohérence et des contrats SLA offrant des garanties complètes](./media/introduction/azure-cosmos-db.png)

Azure Cosmos DB contient un moteur de base de données non basé sur un schéma, régi par les ressources et optimisé pour les écritures. Celui-ci prend en charge plusieurs modèles de données : clé-valeur, documents, graphiques et colonnes. Il prend également en charge de nombreuses API permettant d’accéder aux données, notamment [MongoDB](mongodb-introduction.md), [DocumentDB SQL](documentdb-introduction.md), [Gremlin](graph-introduction.md) (préversion) et [Tables Azure](table-introduction.md) (préversion), d’une manière extensible. 

Azure Cosmos DB a été lancé en 2010 pour répondre aux problématiques des développeurs auxquelles font face les applications à grande échelle chez Microsoft. Comme la création d’applications distribuées mondialement n’est pas un problème unique à Microsoft, nous mettons le service à disposition en externe pour tous les développeurs par le biais d’Azure DocumentDB. Azure Cosmos DB est la prochaine grande étape dans l’évolution de DocumentDB et nous le mettons maintenant à disposition. Dans le cadre de cette version d’Azure Cosmos DB, les clients DocumentDB (avec leurs données) sont automatiquement des clients Azure Cosmos DB. La transition est transparente et les clients ont maintenant accès à une large gamme de nouvelles fonctionnalités offertes par Azure Cosmos DB. 

<a id="capability-comparison" class="xliff"></a>

## Comparaison des fonctionnalités

Azure Cosmos DB fournit les meilleures fonctionnalités des bases de données relationnelles et non relationnelles.

| Fonctionnalités | Bases de données relationnelles | Bases de données non relationnelles (NoSQL) |  Azure Cosmos DB |
| --- | --- | --- | --- |
| Diffusion mondiale | x | x | ✓ Clé en main, plus de 30 régions, hébergement multiple |
| Mise à l’échelle horizontale | x | ✓  | ✓ Stockage et débit scalables indépendamment | 
| Garanties de latence | x | ✓  | ✓ < à 10 ms pour les lectures, < à 15 ms pour les écritures au 99e centile | 
| Haute disponibilité | x | ✓  | ✓ Toujours activé, compromis, basculement automatique et manuel |
| Modèle de données + API | Relationnel + SQL | Multi-modèle + API OSS | Multi-modèle + SQL + API OSS (autres fonctionnalités disponibles prochainement) |
| Contrats SLA | ✓  | x | ✓ Contrats SLA complets en matière de latence, de débit, de cohérence et de disponibilité |

<a id="key-capabilities" class="xliff"></a>

## Fonctionnalités clés
En tant que service de base de données distribué mondialement, Azure Cosmos DB fournit les fonctionnalités suivantes pour vous aider à créer des applications scalables, très réactives et distribuées mondialement :

* [**Une distribution mondiale clé en main**](#global-distribution)
    * Votre application est instantanément disponible à vos utilisateurs, partout. C’est maintenant le cas pour vos données.
    * Ne vous inquiétez pas concernant le matériel, l’ajout de nœuds, de machines virtuelles ou de cœurs. Vos données sont accessibles en un clic. 

* [**Plusieurs modèles de données et API populaires pour la consultation et l’interrogation des données**](#data-models)
    * Prise en charge de plusieurs modèles de données, y compris clé-valeur, document, graphique et colonnes.
    * API extensibles pour Node.js, Java, .NET, .NET Core, Python et MongoDB.
    * SQL et Gremlin pour les requêtes. 

* [**Mettre à l’échelle le débit et le stockage de façon élastique et à la demande, dans le monde entier**](#horizontal-scale)
    * Faites évoluer facilement le débit avec une granularité au niveau de la [seconde](request-units.md) et de la [minute](https://aka.ms/acdbrupm) et modifiez-le à tout moment. 
    * Mettez à l’échelle le stockage de façon [automatique et transparente](partition-data.md) pour couvrir vos besoins en taille aujourd’hui et pour toujours.

* [**Créer des applications hautement réactives et stratégiques**](#low-latency) 
    * Accédez à vos données avec des latences d’une milliseconde au 99e centile, n’importe où dans le monde. 

* [**Assurer une disponibilité en continu**](#high-availability)
    * Disponibilité de 99,99 % dans une même région.
    * Déployez dans n’importe quel nombre de [régions Azure](https://azure.microsoft.com/regions) pour bénéficier d’une haute disponibilité.
    * [Simulez la défaillance](regional-failover.md) d’une ou plusieurs régions avec comme garantie l’absence de perte de données. 

* [**Écrire des applications distribuées mondialement, de la bonne façon**](#consistency)
    * Les [cinq modèles de cohérence](consistency-levels.md) offrent une cohérence allant d’une cohérence forte de type SQL à une cohérence éventuelle de type NoSQL et toutes les cohérences intermédiaires. 
  
* [**Garantie de remboursement**](#sla) 
    * Vos données sont accessibles rapidement ou vous êtes remboursé. 
    * [Contrats SLA](https://aka.ms/acdbsla) pour la disponibilité, la latence, le débit et la cohérence. 

* [**Aucune gestion de schéma ou d’index de base de données**](#schema-free)
    * Vous ne devez plus conserver le schéma et les index de base de données synchronisés avec le schéma de l’application. Les schémas ne sont plus utilisés. 

* [**Faible coût total de possession**](#tco)
    * Cinq à dix fois [plus économique](https://aka.ms/documentdb-tco-paper) qu’une solution non managée.
    * Prix trois fois moins élevé que DynamoDB.

<a id="global-distribution"></a>

<a id="global-distribution" class="xliff"></a>

## Diffusion mondiale
Les conteneurs Azure Cosmos DB sont distribués selon deux dimensions : 

1. Dans une région donnée, toutes les ressources sont partitionnées horizontalement à l’aide des partitions de ressources (distribution locale). 
2. Chaque partition de ressource est également répliquée entre les régions géographiques (distribution mondiale). 

![Schéma démontrant la distribution mondiale d’Azure Cosmos DB](./media/introduction/azure-cosmos-db-global-distribution.png) 

Lorsque vous devez mettre le débit et le stockage à l’échelle, Cosmos DB effectue de façon transparente les opérations de gestion des partitions dans l’ensemble des régions. Indépendamment de l’échelle, de la distribution ou des défaillances, Cosmos DB continue à fournir une image système unique des ressources mondialement distribuées. 

La distribution mondiale des ressources Cosmos DB est fournie [clé en main](distribute-data-globally.md). À tout moment, en quelques clics de bouton (ou par programmation avec un seul appel d’API), vous pouvez associer n’importe quel nombre de régions géographiques avec votre compte de base de données. 

Quel que soit la quantité de données ou le nombre de régions, Cosmos DB offre la garantie que chaque région nouvellement associée peut commencer le traitement des demandes des clients sous une heure au 99e centile. Pour cela, vous devez paralléliser les données d’amorçage et de copie de toutes les partitions de ressources sources dans la région qui vient d’être associée. Les clients peuvent également supprimer une région existante ou mettre hors connexion une région qui a été précédemment associée à leur compte de base de données.

<a id="data-models"></a>
<a id="multi-model-multi-api-support" class="xliff"></a>

## Prise en charge de plusieurs modèles et API
 Azure Cosmos DB prend nativement en charge plusieurs modèles de données (documents, clé-valeur, graphiques et colonnes). Le modèle de contenu principal du moteur de base de données Cosmos DB repose sur un modèle atome-enregistrement-séquence (ARS). Les atomes sont constitués d’un petit ensemble de types primitifs (chaîne, booléen et nombre). Les enregistrements sont des structures composées de ces types. Les séquences sont des tableaux comprenant des atomes, des enregistrements ou des séquences. 
 
 Le moteur de base de données peut efficacement traduire et projeter différents modèles de données sur le modèle de données basé sur ARS. Le modèle de données principal de Cosmos DB est accessible en mode natif à partir de langages de programmation dynamiquement typés et peut être exposé en l’état au format JSON. 
 
 Le service prend également en charge les API de base de données les plus courantes pour la consultation et l’interrogation des données. Le moteur de base de données Cosmos DB prend actuellement en charge [DocumentDB SQL](documentdb-introduction.md), [MongoDB](mongodb-introduction.md), [Tables Azure](table-introduction.md) (préversion) et [Gremlin](graph-introduction.md) (préversion). Vous pouvez continuer à créer des applications à l’aide des API open source populaires et à bénéficier de tous les avantages d’un service de base de données distribué mondialement, entièrement managé et testé sur le terrain. 

<a id="horizontal-scale"></a>
<a id="horizontal-scaling-of-storage-and-throughput" class="xliff"></a>

## Mise à l’échelle horizontale du stockage et du débit
Toutes les données d’un conteneur Cosmos DB (par exemple une collection de documents, un tableau ou un graphique) sont partitionnées horizontalement et gérées en toute transparence par les partitions de la ressource. Une partition de ressource est un conteneur hautement disponible et cohérent des données partitionnées par une paire [partition-clé spécifiée par le client](partition-data.md). Elle fournit une image système unique pour un ensemble de ressources qu’elle gère. Il s’agit d’un composant fondamental de la scalabilité et de la distribution. Cosmos DB est conçu pour vous permettre de mettre à l’échelle le débit de façon élastique selon les modèles de trafic d’application dans différentes régions géographiques pour prendre en charge les charges de travail fluctuantes selon l’emplacement géographique et l’heure. Le service gère les partitions en toute transparence sans compromettre la disponibilité, la cohérence, la latence ou le débit d’un conteneur Cosmos DB.  
 
![Azure Cosmos DB est scalable horizontalement](./media/introduction/azure-cosmos-db-partitioning.png) 

Vous pouvez mettre à l’échelle de façon élastique le débit d’un conteneur Azure Cosmos DB en approvisionnant par programme le débit à l’aide d’[unités de requête par seconde (RU/s)](request-units.md). En interne, le service gère en toute transparence les partitions de ressources pour fournir le débit sur le conteneur concerné. Cosmos DB offre la garantie que le débit est disponible pour une utilisation dans toutes les régions associées au conteneur. Le nouveau débit est effectif dans un délai de 5 secondes après application de la modification de la valeur de débit configurée. 

Vous pouvez configurer le débit sur un conteneur Cosmos DB sur les granularités [par minute (RU/m)](request-units-per-minute.md), par seconde ou les deux. Le débit configuré au niveau de granularité par minute est utilisé pour gérer les pics inattendus de la charge de travail en cours survenant à un niveau de granularité par seconde. 

<a id="low-latency"></a>
<a id="low-latency-guarantees-at-the-99th-percentile" class="xliff"></a>

## Faible latence garantie au 99e centile
Dans le cadre de son contrat SLA, Cosmos DB garantit une faible latence de bout en bout au 99e centile à ses clients. Pour un élément standard de 1 Ko, Cosmos DB garantit la latence de bout en bout des lectures sous 10 ms et des écritures indexées sous 15 ms au 99e centile, dans la même région Azure. Les latences médianes sont nettement plus faibles (inférieures à 5 ms).  Avec une limite supérieure de traitement de chaque transaction de base de données, Cosmos DB permet aux clients de faire facilement la distinction entre des transactions ayant une latence élevée et une base de données qui n’est pas disponible.

<a id="high-availability"></a>
<a id="transparent-multi-homing-and-9999-high-availability" class="xliff"></a>

## Hébergement multiple transparent et haute disponibilité de 99,99 %
Vous pouvez associer dynamiquement des « priorités » aux régions associées à votre compte de base de données Azure Cosmos DB. Les priorités servent à diriger les demandes dans des régions spécifiques en cas de défaillance d’une région. Dans le cas peu probable d’un sinistre régional, Cosmos DB effectue automatiquement un basculement dans l’ordre de priorité.

Pour tester la disponibilité de bout en bout de l’application, vous pouvez [déclencher manuellement le basculement](regional-failover.md) (débit limité à deux opérations par heure). Cosmos DB garantit l’absence de perte de données pendant les basculements régionaux manuels. En cas de sinistre régional, Cosmos DB garantit une limite supérieure sur la perte de données pendant le basculement automatique initialisé par le système. Vous n’avez pas besoin de redéployer votre application après un basculement régional et les contrats SLA de disponibilité sont maintenus par Azure Cosmos DB. 

Pour ce scénario, Cosmos DB vous permet d’interagir avec les ressources à l’aide de points de terminaison logiques (sans région) ou physiques (propres à une région). La première option garantit que l’application peut en toute transparence être multihébergée en cas de basculement. La deuxième option fournit un contrôle précis à l’application pour rediriger les lectures et écritures vers des régions spécifiques. Cosmos DB est associé à un contrat SLA garantissant une disponibilité de 99,99 % pour chaque compte de base de données. Les garanties de disponibilité sont indépendantes de l’échelle (débit et stockage approvisionnés), du nombre de régions ou de la distance géographique entre les régions associées à une base de données. 

<a id="consistency"></a>
<a id="multiple-well-defined-consistency-models" class="xliff"></a>

## Différents modèles de cohérence bien définis
Les bases de données distribuées commerciales se répartissent en deux catégories : les bases de données qui n’offrent pas de choix de cohérence bien définis et démontrables et celles qui offrent deux possibilités de programmabilité extrêmes (cohérence éventuelle et forte). La première option embrouille les développeurs d’applications en raison des menus détails de leurs protocoles de réplication et les oblige à faire des compromis difficiles entre cohérence, disponibilité, latence et débit. La deuxième option demande de choisir l’un des deux extrêmes. En dépit de l’abondance des études et des propositions concernant plus de 50 modèles de cohérence, la communauté des bases de données distribuées n’a pas été en mesure de commercialiser des niveaux de cohérence allant au-delà de la cohérence forte ou éventuelle. 

Cosmos DB vous permet de choisir entre [cinq modèles de cohérence bien définis](consistency-levels.md) couvrant tout l’éventail de cohérences : fort, obsolescence limitée, [session](http://dl.acm.org/citation.cfm?id=383631), préfixe cohérent et éventuel. 

![Azure Cosmos DB offre le choix entre plusieurs modèles de cohérence bien définis (souples)](media/introduction/azure-cosmos-db-consistency-levels.png)

Le tableau suivant illustre les garanties spécifiques que chaque niveau de cohérence fournit.
 
**Niveaux de cohérence et garanties**

| Niveau de cohérence | Garanties |
| --- | --- |
| Remarque | Linéarisabilité |
| Obsolescence limitée | Préfixe cohérent. Retard des lectures par rapport aux écritures par k préfixes ou un intervalle t |
| session   | Préfixe cohérent. Lectures unitones, écritures unitones, lecture de vos écritures, l’écriture suit les lectures |
| Préfixe cohérent | Les mises à jour retournées sont un préfixe de toutes les mises à jour, sans interruption |
| Eventual (Éventuel)  | Lectures en désordre |

Vous pouvez configurer le niveau de cohérence par défaut de votre compte Cosmos DB (et remplacer ultérieurement la cohérence sur une demande de lecture spécifique). En interne, le niveau de cohérence par défaut s’applique aux données au sein des groupes de partitions qui peuvent s’étendre dans différentes régions. 


<a id="sla"></a>
<a id="guaranteed-service-level-agreements" class="xliff"></a>

## Contrats SLA garantis

Cosmos DB est le premier service de base de données managé qui offre un contrat SLA avec des [garanties](https://aka.ms/acdbsla) de 99,99 % pour la disponibilité, le débit, la faible latence et la cohérence.
* Disponibilité : contrat SLA garantissant une disponibilité à 99,99 % pour chacune des opérations de planification des contrôles et données.
* Débit : finalisation de 99,99 % des demandes 
* Latence : 99,99 % de latences inférieures à 10 ms au 99e centile
* Cohérence : 100 % des demandes de lecture répondent à la garantie de cohérence pour le niveau de cohérence demandé.


<a id="schema-free"></a>
<a id="schema-free" class="xliff"></a>

## Sans schéma

Les bases de données relationnelles et NoSQL vous forcent à gérer le schéma et les index, la gestion des versions et la migration. Et tout ceci est extrêmement délicat dans une installation distribuée mondialement. Mais ne vous inquiétez pas : Cosmos DB n’a pas ce problème. Avec Cosmos DB, vous n’avez pas besoin de gérer des schémas et des index, de gérer la gestion des versions de schéma ou de vous soucier des temps d’arrêt des applications pendant la migration des schémas. Le moteur de base de données Cosmos DB est entièrement indépendant du schéma : il indexe automatiquement toutes les données qu’il reçoit sans schéma ni index et répond aux requêtes ultrarapides. 

<a id="tco"></a>
<a id="low-cost-of-ownership" class="xliff"></a>

## Faible coût total de possession

 Lorsque toutes les considérations en matière de coût total de possession sont prises en compte, les services cloud managés comme Azure Cosmos DB peuvent être cinq à dix fois plus rentables que leurs homologues open source exécutés sur des machines virtuelles ou en local. Par ailleurs, Azure Cosmos DB est jusqu’à deux à trois fois moins cher que DynamoDB pour les charges de travail aux volumes élevés. Pour en savoir plus, consultez le [livre blanc sur le coût total de possession](https://aka.ms/documentdb-tco-paper). 

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
Bien démarrer avec Azure Cosmos DB grâce à l’un de nos guides de démarrage rapide :

* [Bien démarrer avec l’API DocumentDB d’Azure Cosmos DB](create-documentdb-dotnet.md)
* [Bien démarrer avec l’API MongoDB d’Azure Cosmos DB](create-mongodb-nodejs.md)
* [Bien démarrer avec l’API Graph d’Azure Cosmos DB](create-graph-dotnet.md)
* [Bien démarrer avec l’API Table d’Azure Cosmos DB](create-table-dotnet.md)

