---
redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: d59ebef3cda36ba048b844f0cd2326fff66b4aa5
ms.openlocfilehash: 4c7b8965b7fe5de7a8ff839be2b9e555bd8d502c



---
# <a name="automatic-indexing-in-azure-documentdb"></a>Indexation automatique dans Azure DocumentDB
Cet article est extrait du livre [« Indexation du schéma non spécifié avec Azure DocumentDB »](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) qui sera présenté à la [41e conférence VLDB](http://www.vldb.org/2015/) entre le 31 août et le 4 septembre 2015. C’est une introduction au fonctionnement de l’indexation dans Azure DocumentDB. 

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

* Comment DocumentDB déduit-t-il le schéma à partir d'un document JSON ?
* Comment DocumentDB crée-t-il un index de documents disparates ?
* Comment DocumentDB effectue-t-il une indexation automatique à grande échelle ?

## <a name="a-idhowdocumentdbindexingworksa-how-documentdb-indexing-works"></a><a id="HowDocumentDBIndexingWorks"></a> Fonctionnement de l'indexation dans DocumentDB
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) est un véritable système de base de données sans schéma, conçu pour JSON. Il ne part pas du principe que vous utilisez des schémas ou des définitions d’index secondaire et n’en réclame pas pour indexer les données à grande échelle. Cela vous permet de définir rapidement les modèles de données d'application et d'y effectuer des itérations à l’aide de DocumentDB. Dès que vous ajoutez des documents à une collection, DocumentDB indexe automatiquement toutes les propriétés des documents et vous pouvez donc les interroger. L'indexation automatique vous permet de stocker des documents appartenant aux schémas arbitraires sans vous soucier des schémas ou des index secondaires.

Dans le but d'éliminer tout risque d’incohérence d’impédance entre la base de données et les modèles de programmation d'application, DocumentDB exploite la simplicité de JSON et le manque de spécification de schéma. Il n’établit pas d’hypothèse sur les documents et permet de varier les documents d’une collection DocumentDB au sein du schéma, en plus des valeurs spécifiques d'instance. Contrairement à d'autres bases de données de document, le moteur de base de données de DocumentDB opère directement au niveau de la grammaire JSON, en restant agnostique au concept d'un schéma de document et en atténuant la frontière entre la structure et les valeurs d’instance des documents. Il permet également d’indexer automatiquement les documents sans schéma ou index secondaire.

L’indexation dans DocumentDB tire parti du fait que la grammaire JSON permet que les documents soient **représentés sous forme d’arborescences**. Pour qu'un document JSON soit représenté sous forme d'arborescence, il est nécessaire de créer un nœud racine factice qui comporte le reste des nœuds réels du document en dessous. Chaque étiquette incluant les index de tableau d'un document JSON devient un nœud de l'arborescence. La figure ci-dessous illustre un exemple de document JSON et sa représentation correspondante sous forme d'arborescence.

> [!NOTE]
> Le caractère de JSON est autodescriptif, ce qui veut dire que chaque document inclut le schéma (les métadonnées) et les données, par exemple, `{"locationId": 5, "city": "Moscow"}` révèle qu'il existe deux propriétés `locationId` et `city`, et que celles-ci disposent de valeurs de propriétés numériques et de valeurs de propriétés de chaîne. DocumentDB est en mesure de déduire le schéma de documents et de les indexer lorsqu'ils sont insérés ou remplacés, sans que vous ayez besoin de définir les schémas ou les index secondaires.
> 
> 

**Documents JSON sous forme d'arborescences :**

![Documents sous forme d'arborescences](media/documentdb-indexing/DocumentsAsTrees.png)

Par exemple, dans l’illustration ci-dessus :

* La propriété JSON `{"headquarters": "Belgium"}` de l'exemple ci-dessus correspond au chemin /headquarters/Belgium.
* Le tableau JSON `{"exports": [{"city": “Moscow"}`, `{"city": Athens"}]}` correspond aux chemins d’accès `/exports/[]/city/Moscow` et `/exports/[]/city/Athens`.

Avec l’indexation automatique, (1) chaque chemin d’une arborescence de documents est indexé (à moins que le développeur ait configuré explicitement la stratégie d’indexation de manière à exclure certains modèles de chemin). (2) Chaque mise à jour d’un document d’une collection DocumentDB entraîne la mise à jour de la structure de l'index (c.-à-d. l’ajout de causes ou la suppression de nœuds). Une des exigences principales de l'indexation automatique de documents est de veiller à ce que le coût d’indexation et de requête d’un document via une structure fortement imbriquée (à 10 niveaux) soit identique à celui d'un document JSON plat, constitué de paires clé-valeur à un seul niveau d’imbrication. Par conséquent, une représentation normalisée, sous forme de chemin d'accès, constitue la fondation sur laquelle les sous-systèmes de requête et d'indexation automatique ont été créés.

Le fait de traiter des valeurs d’instance et de schéma de la même manière que des chemins d’accès signifie que, tout comme les documents individuels, l’index de deux documents, qui crée un mappage entre les chemins d’accès et les ID de document contenant ce chemin d’accès, peut être logiquement représenté sous forme d’arborescence. DocumentDB utilise ceci pour générer une arborescence d'index qui est créée à partir de l'union de toutes les arborescences qui représentent les documents individuels de la collection. L'arborescence d’index dans les collections DocumentDB augmente à mesure que les documents sont ajoutés à la collection, ou mis à jour.

**Index DocumentDB sous forme d'arborescence :**

![Index sous forme d'arborescence](media/documentdb-indexing/IndexAsTree.png)

Bien qu'ils n#utilisent aucun schéma, les langages de requête SQL et JavaScript de DocumentDB fournissent des filtres et des projections relationnelles, une navigation hiérarchique à travers les documents, des opérations spatiales et l'appel de fonctions définies par l'utilisateur entièrement écrites en JavaScript. L’exécution de requête de DocumentDB est en mesure de prendre en charge ces requêtes puisqu’elle peut fonctionner directement sur cette représentation d’arborescence d'index des données.

La stratégie d'indexation par défaut indexe automatiquement toutes les propriétés des documents et fournit des requêtes cohérentes (l'index est ainsi mis à jour de façon synchrone avec l'écriture du document). Comment DocumentDB prend-il en charge les mises à jour cohérentes de l'arborescence d'index à grande échelle ? DocumentDB utilise une technique de maintenance des index structurée par des journaux, sans verrouillage et optimisée pour l’écriture. Cela signifie que DocumentDB peut prendre en charge un volume soutenu d’écritures rapides, tout en continuant de servir les requêtes cohérentes. 

L’indexation de DocumentDB vous permet de prendre en charge une architecture mutualisée et d’obtenir une efficacité de stockage maximale. Pour des raisons économiques, la surcharge de stockage sur disque de l'index est limitée et prévisible. Les mises à jour de l'index sont également effectuées dans le budget des ressources système allouées par collection DocumentDB.

## <a name="a-namenextstepsa-next-steps"></a><a name="NextSteps"></a> Étapes suivantes
* Télécharger [« Indexation du schéma non spécifié avec Azure DocumentDB »](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)qui sera présenté à la 41e conférence VLDB entre le 31 août et le 4 septembre 2015.
* [Interrogation avec le langage SQL de DocumentDB](documentdb-sql-query.md)
* Pour en savoir plus sur la personnalisation de l'index DocumentDB, cliquez [ici](documentdb-indexing-policies.md)




<!--HONumber=Jan17_HO3-->


