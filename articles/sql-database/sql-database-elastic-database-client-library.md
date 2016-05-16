<properties
    pageTitle="Conception de bases de données cloud évolutives | Microsoft Azure"
    description="Créez des applications de base de données .NET évolutives avec la bibliothèque cliente de bases de données élastiques"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="ddove;sidneyh"/>

# Conception de bases de données cloud évolutives

La montée en charge des bases de données peut être aisément accomplie à l'aide de fonctionnalités et d'outils évolutifs de bases de données SQL Azure. En particulier, vous pouvez utiliser la **Bibliothèque cliente de bases de données élastiques** pour créer et gérer des bases de données avec montée en charge. Cette fonctionnalité vous permet de développer facilement des applications partitionnées à l'aide de centaines, voire de milliers, de bases de données SQL Azure.

Pour installer la bibliothèque, accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## Documentation
1. [Prise en main des outils de base de données élastiques](sql-database-elastic-scale-get-started.md)
* [Fonctionnalités des bases de données élastiques](sql-database-elastic-scale-introduction.md)
* [Gestion des cartes de partitions](sql-database-elastic-scale-shard-map-management.md)
* [Migration de bases de données existantes pour une mise à l’échelle](sql-database-elastic-convert-to-use-elastic-tools.md)
* [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)
* [Requêtes sur plusieurs partitions](sql-database-elastic-scale-multishard-querying.md)
* [Ajout d’une partition à l’aide des outils de base de données élastique](sql-database-elastic-scale-add-a-shard.md)
* [Applications multi-locataires avec des outils de base de données élastique et la sécurité au niveau des lignes](sql-database-elastic-tools-multi-tenant-row-level-security.md)
* [Mettre à niveau les applications de la bibliothèque cliente](sql-database-elastic-scale-upgrade-client-library.md) 
* [Vue d'ensemble des requêtes élastiques](sql-database-elastic-query-overview.md)
* [Glossaire des outils de base de données élastique](sql-database-elastic-scale-glossary.md)
* [Bibliothèque cliente de la base de données élastique avec Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
* [Bibliothèque cliente de bases de données élastiques avec Dapper](sql-database-elastic-scale-working-with-dapper.md)
* [Outil de fractionnement et de fusion](sql-database-elastic-scale-overview-split-and-merge.md)
* [FAQ des outils de bases de données élastiques](sql-database-elastic-scale-faq.md)

## Fonctionnalités du client

La montée en charge des applications à l’aide du *partitionnement* présente des défis pour le développeur, ainsi que pour l’administrateur. La bibliothèque cliente simplifie les tâches de gestion en fournissant des outils qui permettent à la fois aux développeurs et administrateurs de gérer des bases de données avec montée en charge. Dans un exemple classique, il existe plusieurs bases de données, nommées « partitions », à gérer. Les clients sont colocalisés dans la même base de données et il y a une base de données par client (schéma à locataire unique). La bibliothèque cliente comprend ces fonctionnalités :

1.  **Gestion des cartes de partitions** : une base de données spéciale appelée « gestionnaire des cartes de partitions » est créée. La gestion des cartes de partitions est la capacité d’une application à gérer les métadonnées sur ses partitions. Les développeurs peuvent utiliser cette fonctionnalité pour enregistrer des bases de données, décrire les mappages de clés de partitionnement individuelles ou de plages de clés pour ces bases de données et gérer ces métadonnées en même temps que le nombre et la composition des bases de données évoluent en fonction des changements de capacités. Sans la bibliothèque cliente de base de données élastique, vous passeriez beaucoup de temps à écrire le code de gestion lors de l'implémentation du partitionnement. Pour plus d'informations, consultez [Gestion des cartes de partitions](sql-database-elastic-scale-shard-map-management.md).

* **Routage dépendant des données** : imaginez une requête arrivant dans l'application. Selon la valeur de la clé de partitionnement de la demande, l'application doit déterminer la base de données correcte en fonction de la valeur de clé. Elle ouvre ensuite une connexion à la base de données pour traiter la demande. Le routage dépendant des données fournit la possibilité d'ouvrir des connexions avec un seul appel simple de la carte de partitions de l'application. Le routage dépendant des données est un autre aspect du code d’infrastructure qui est maintenant couvert par les fonctionnalités de la bibliothèque cliente de base de données élastique. Pour plus d'informations, consultez [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md).

* **Requêtes sur plusieurs partitions** : l'interrogation de plusieurs partitions fonctionne lorsqu'une demande implique plusieurs (ou toutes les) partitions. Une requête sur plusieurs partitions exécute le même code T-SQL sur toutes les partitions ou un ensemble de partitions. Les résultats provenant des partitions participantes sont fusionnés en un résultat global défini à l'aide de la sémantique UNION ALL. La fonctionnalité, telle qu’exposée via la bibliothèque cliente, gère de nombreuses tâches, notamment : la gestion des connexions, la gestion des threads, la gestion des erreurs et le traitement des résultats intermédiaires. Les requêtes sur plusieurs partitions peuvent interroger des centaines de partitions. Pour plus d'informations, consultez [Requête sur plusieurs partitions](sql-database-elastic-scale-multishard-querying.md).

De manière générale, les clients utilisant les outils de base de données élastique peuvent s’attendre à obtenir toutes les fonctionnalités T-SQL lors de l’envoi d’opérations de partitions locales, à la différence des opérations entre plusieurs partitions qui ont leur propre sémantique.

## Étapes suivantes

Testez l'[exemple d'application](sql-database-elastic-scale-get-started.md) qui montre les fonctions du client.

Pour installer la bibliothèque, accédez à [Bibliothèque cliente de base de données élastique](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Pour obtenir des instructions sur l'utilisation de l'outil de fusion et de fractionnement, consultez la [vue d'ensemble de l'outil de fusion et de fractionnement](sql-database-elastic-scale-overview-split-and-merge.md).

[La bibliothèque cliente de la base de données élastique est maintenant accessible en open source !](https://azure.microsoft.com/blog/elastic-database-client-library-is-now-open-sourced/)

Utilisez les [Requêtes élastiques](sql-database-elastic-query-overview.md).

La bibliothèque est disponible en tant que logiciel open source sur [GitHub](https://github.com/Azure/elastic-db-tools).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-database-client-library/glossary.png

<!---HONumber=AcomDC_0504_2016-->