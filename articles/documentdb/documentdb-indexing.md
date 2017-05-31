---
redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c1547d9910ce2bbfda79718ba74cdd8e7ca468f5
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017



---
# <a name="automatic-indexing-in-azure-cosmos-db"></a>Indexation automatique dans Azure Cosmos DB
Cet article est extrait de l’article ["Indexation du schéma non spécifié avec Azure Cosmos DB"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) qui sera présenté à la [41e conférence VLDB](http://www.vldb.org/2015/) entre le 31 août et le 4 septembre 2015. C’est une introduction au fonctionnement de l’indexation dans Azure Cosmos DB. 

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

* Comment Azure Cosmos DB déduit-t-il le schéma à partir d’un document JSON ?
* Comment Azure Cosmos DB crée-t-il un index entre des documents disparates ?
* Comment Azure Cosmos DB effectue-t-il une indexation automatique à grande échelle ?

## <a id="HowDocumentDBIndexingWorks"></a> Fonctionnement de l'indexation dans DocumentDB
[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) est une véritable base de données sans schéma, conçue pour JSON. Il ne part pas du principe que vous utilisez des schémas ou des définitions d’index secondaire et n’en réclame pas pour indexer les données à grande échelle. Cela vous permet de définir rapidement les modèles de données d’application et d’y effectuer des itérations à l’aide d’Azure Cosmos DB. Dès que vous ajoutez des documents à une collection, Azure Cosmos DB indexe automatiquement toutes leurs propriétés et vous pouvez donc les interroger. L'indexation automatique vous permet de stocker des documents appartenant aux schémas arbitraires sans vous soucier des schémas ou des index secondaires.

Afin d’éliminer tout risque d’impédance incohérente entre la base de données et les modèles de programmation d’application, Azure Cosmos DB s’appuie sur la simplicité de JSON et l’absence de spécification de schéma. Il n’établit aucun présupposé sur les documents et permet de faire varier les documents d’une collection Azure Cosmos DB au sein du schéma, en plus des valeurs spécifiques d’instance. Contrairement à d’autres bases de données de documents, le moteur de base de données d’Azure Cosmos DB opère directement au niveau de la grammaire JSON, en restant agnostique au concept de schéma de document et en atténuant la frontière entre la structure et les valeurs d’instance des documents. Il permet également d’indexer automatiquement les documents sans schéma ou index secondaire.

L’indexation dans Azure Cosmos DB tire parti du fait que la grammaire JSON autorisation la **représentation de documents sous forme arborescente**. Pour qu'un document JSON soit représenté sous forme d'arborescence, il est nécessaire de créer un nœud racine factice qui comporte le reste des nœuds réels du document en dessous. Chaque étiquette incluant les index de tableau d'un document JSON devient un nœud de l'arborescence. La figure ci-dessous illustre un exemple de document JSON et sa représentation correspondante sous forme d'arborescence.

> [!NOTE]
> Le caractère de JSON est autodescriptif, ce qui veut dire que chaque document inclut le schéma (les métadonnées) et les données, par exemple, `{"locationId": 5, "city": "Moscow"}` révèle qu'il existe deux propriétés `locationId` et `city`, et que celles-ci disposent de valeurs de propriétés numériques et de valeurs de propriétés de chaîne. Azure Cosmos DB permet de déduire le schéma des documents et de les indexer lorsqu’ils sont insérés ou remplacés, sans jamais avoir à définir les schémas ou les index secondaires.
> 
> 

**Documents JSON sous forme d'arborescences :**

![Documents sous forme d'arborescences](media/documentdb-indexing/DocumentsAsTrees.png)

Par exemple, dans l’illustration ci-dessus :

* La propriété JSON `{"headquarters": "Belgium"}` de l'exemple ci-dessus correspond au chemin /headquarters/Belgium.
* Le tableau JSON `{"exports": [{"city": “Moscow"}`, `{"city": Athens"}]}` correspond aux chemins d’accès `/exports/[]/city/Moscow` et `/exports/[]/city/Athens`.

Avec l’indexation automatique, (1) chaque chemin d’une arborescence de documents est indexé (à moins que le développeur ait configuré explicitement la stratégie d’indexation de manière à exclure certains modèles de chemin). (2) Chaque mise à jour d’un document dans une collection Azure Cosmos DB entraîne la mise à jour de la structure de l’index (c.-à-d. l’ajout ou la suppression de nœuds). Une des exigences principales de l'indexation automatique de documents est de veiller à ce que le coût d’indexation et de requête d’un document via une structure fortement imbriquée (à 10 niveaux) soit identique à celui d'un document JSON plat, constitué de paires clé-valeur à un seul niveau d’imbrication. Par conséquent, une représentation normalisée, sous forme de chemin d'accès, constitue la fondation sur laquelle les sous-systèmes de requête et d'indexation automatique ont été créés.

Le fait de traiter des valeurs d’instance et de schéma de la même manière que des chemins d’accès signifie que, tout comme les documents individuels, l’index de deux documents, qui crée un mappage entre les chemins d’accès et les ID de document contenant ce chemin d’accès, peut être logiquement représenté sous forme d’arborescence. Azure Cosmos DB utilise ceci pour générer une arborescence d’index à partir de l’union de toutes les arborescences qui représentent les documents de la collection. L’arborescence d’index dans les collections Azure Cosmos DB s’étend à mesure que des documents sont ajoutés à la collection ou mis à jour.

**Index Azure Cosmos DB sous la forme d’une arborescence :**

![Index sous forme d'arborescence](media/documentdb-indexing/IndexAsTree.png)

Bien qu’ils n’utilisent aucun schéma, les langages de requête SQL et JavaScript d’Azure Cosmos DB fournissent des filtres et des projections relationnelles, une navigation hiérarchique à travers les documents, des opérations spatiales et l’appel de fonctions définies par l’utilisateur entièrement écrites en JavaScript. Le moteur de requêtes d’Azure Cosmos DB permet de prendre en charge ces requêtes, car il peut intervenir directement sur cette représentation arborescente de l’index des données.

La stratégie d'indexation par défaut indexe automatiquement toutes les propriétés des documents et fournit des requêtes cohérentes (l'index est ainsi mis à jour de façon synchrone avec l'écriture du document). Comment Azure Cosmos DB prend-il en charge les mises à jour cohérentes de l’arborescence d’index à grande échelle ? Azure Cosmos DB utilise des techniques de maintenance d’index structurés en journaux, sans verrouillage et optimisées pour l’écriture. Cela signifie qu’Azure Cosmos DB peut prendre en charge un volume important d’écritures rapides, tout en continuant de servir les requêtes cohérentes. 

L’indexation d’Azure Cosmos DB est conçue pour prendre en charge une architecture mutualisée et optimiser le stockage. Pour des raisons économiques, la surcharge de stockage sur disque de l'index est limitée et prévisible. Les mises à jour d’index sont également effectuées dans le budget des ressources système allouées par collection Azure Cosmos DB.

## <a name="NextSteps"></a> Étapes suivantes
* Téléchargez ["Indexation du schéma non spécifié avec Azure Cosmos DB"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)qui sera présenté à la 41e conférence VLDB entre le 31 août et le 4 septembre 2015.
* [Interrogation avec le langage SQL de DocumentDB](documentdb-sql-query.md)
* Pour en savoir plus sur la personnalisation de l’index Azure Cosmos DB, cliquez [ici](documentdb-indexing-policies.md).


