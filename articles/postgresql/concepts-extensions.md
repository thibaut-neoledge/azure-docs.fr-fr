---
title: "Utiliser les extensions PostgreSQL dans une base de données Azure pour PostgreSQL | Microsoft Docs"
description: "Décrit la capacité à étendre les fonctionnalités d’une base de données à l’aide des extensions de la base de données Azure pour PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: dd16442078cd1d440edf0a16cba32b1494c6db55
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Extensions PostgreSQL dans une base de données Azure pour PostgreSQL
PostgreSQL offre la possibilité d’étendre les fonctionnalités d’une base de données à l’aide des extensions. Les extensions permettent de regrouper plusieurs objets SQL liés dans un package unique ; elles peuvent être chargées ou supprimées de votre base de données d’une seule commande. Une fois chargées dans la base de données, les extensions fonctionnent de la même façon que les fonctions prédéfinies. Pour plus d’informations sur les extensions PostgreSQL, consultez la page [Empaqueter des objets liés dans une extension](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Comment utiliser les extensions PostgreSQL ?
Les extensions PostgreSQL doivent être installées pour votre base de données pour être utilisables. Pour installer une extension donnée, exécutez une simple commande [CRÉER UNE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) dans l’outil psql pour charger les objets empaquetés dans votre base de données.

La base de données Azure pour PostgreSQL prend en charge un sous-ensemble d’extensions de clés (voir la liste ci-dessous) ; ce sont les seules que vous puissiez installer pour votre base de données. Vous ne pouvez pas créer votre propre extension avec le service de base de données Azure pour PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensions prises en charge par la base de données Azure pour PostgreSQL
Voici la liste des extensions PostgreSQL standard actuellement prises en charge par la base de données Azure pour PostgreSQL. Vous pouvez également obtenir ces informations en interrogeant pg\_available\_extensions. 

### <a name="data-types-extensions"></a>Extensions de types de données
| **Extension** | **Description** |
|------------------------------------------------------------------|--------------------------------------------------------|
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fournit un type de chaîne de caractères avec respect de la casse. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fournit un type de données permettant de stocker des ensembles de paires clé/valeur. |

### <a name="functions-extensions"></a>Extensions de fonctions
| **Extension** | **Description** |
|--------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Fournit plusieurs fonctions permettant de déterminer les ressemblances et la distance entre des chaînes. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fournit des fonctions et des opérateurs permettant de manipuler des tableaux d’entiers non Null. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fournit des fonctions de chiffrement. |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gère les tables partitionnées par date ou par ID. |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fournit des fonctions et des opérateurs permettant de déterminer la similarité entre des textes alphanumériques par rapprochement de trigrammes. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Génère des identificateurs uniques universels (UUID). |

### <a name="full-text-search-extensions"></a>Extensions de recherche en texte intégral
| **Extension** | **Description** |
|----------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Dictionnaire de recherche de texte qui supprime les accents (signes diacritiques) des lexèmes. |

### <a name="index-types-extensions"></a>Extensions de types d’index
| **Extension** | **Description** |
|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fournit des exemples de classes d’opérateurs GIN qui implémentent des comportements de type arbre B pour certains types de données. |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fournit des classes d’opérateurs d’index GiST qui implémentent l’arbre B. |

### <a name="language-extensions"></a>Extensions de langage
| **Extension** | **Description** |
|--------------------------------------------------------------------|---------------------------------------|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Langage procédural chargeable PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Extensions diverses
| **Extension** | **Description** |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fournit un moyen d’examiner ce qui se passe dans le cache partagé des tampons en temps réel. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fournit un moyen de charger les données de relation dans le cache des tampons. |
| [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fournit un moyen de suivre les statistiques d’exécution de toutes les instructions SQL exécutées par un serveur. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper de données externes permettant d’accéder aux données stockées dans des serveurs externes PostgreSQL. |

### <a name="postgis"></a>PostGIS
| **Extension** | **Description** |
|--------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
| [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objets spatiaux et géographiques pour PostgreSQL. |
| address\_standardizer, address\_standardizer\_data\_us | Utilisé pour analyser une adresse et la décomposer en éléments constitutifs, généralement afin de prendre en charge l’étape de normalisation des adresses par géocodage. |
| [pgrouting](http://pgrouting.org/) | Étend la base de données géospatiale PostGIS / PostgreSQL pour offrir une fonctionnalité de routage géospatial. |

## <a name="next-steps"></a>Étapes suivantes
Il y a une extension que vous souhaitez utiliser et que vous ne trouvez pas ? Dites-le-nous. Votez pour les demandes existantes ou envoyez de nouveaux commentaires et souhaits dans notre [Forum de commentaires client](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
