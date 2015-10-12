<properties
    pageTitle="Base de données SQL Azure - bibliothèque cliente"
    description="Création de bases de données .NET évolutives"
    services="sql-database"
    documentationCenter=""
    manager="jeffreyg"
    authors="sidneyh"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2015"
    ms.author="sidneyh"/>

# Vue d'ensemble de la bibliothèque cliente de la base de données élastique

La **bibliothèque cliente de base de données élastique** vous aide à facilement développer des applications partitionnées au moyen de centaines, et même de milliers, de bases de données SQL Azure hébergées sur Microsoft Azure. Ces conceptions sont couramment utilisées pour les applications Sofware as Service (SaaS), qui sont généralement des architectures mono-utilisateur où chaque client reçoit une base de données. La création et la gestion de ce type d'application sont un objectif de la bibliothèque. La bibliothèque cliente est une bibliothèque .NET Framework qui peut être installée dans n'importe quel projet d'application à l'aide de [Visual Studio](sql-database-elastic-scale-add-references-visual-studio.md) et de [Nuget](http://go.microsoft.com/?linkid=9862605). La bibliothèque cliente fait partie des outils de base de données élastique. Il s’agit exclusivement d’une [fonctionnalité de base de données élastique](sql-database-elastic-scale-introduction.md).

## Fonctionnalités du client

Le développement, la mise à l’échelle et la gestion des applications montées en charge à l’aide du *partitionnement* (abordé ci-dessous) présentent des défis pour le développeur, ainsi que pour l’administrateur. La bibliothèque cliente facilite les procédures pour ces deux rôles. La figure ci-dessous présente les principales fonctionnalités fournies par la bibliothèque cliente de base de données élastique. La figure illustre un environnement avec plusieurs bases de données et chaque base de données correspond à une partition. Dans cet exemple, de nombreux clients sont colocalisés dans la même base de données à l'aide d'un mappage de plage, bien qu’il aille de même s’il existe une base de données par client (locataire). Les outils simplifient le développement des applications de bases de données SQL Azure partitionnées grâce aux fonctionnalités spécifiques suivantes :

Pour obtenir la définition des termes utilisés ici, consultez le [Glossaire des outils de base de données élastique](sql-database-elastic-scale-glossary.md).

![Fonctionnalités de l’infrastructure élastique][1]

1.  **Gestion des cartes de partitions** : pour gérer une collection de partitions, une base de données spéciale appelée « gestionnaire des cartes de partitions » est créée. la gestion des cartes de partitions est la capacité d’une application à gérer les différentes métadonnées sur ses partitions. Les développeurs peuvent utiliser cette fonctionnalité pour enregistrer des bases de données, décrire les mappages de clés de partitionnement individuelles ou de plages de clés pour ces bases de données et gérer ces métadonnées en même temps que le nombre et la composition des bases de données évoluent en fonction des changements de capacités. Sans la bibliothèque cliente de base de données élastique, vous passeriez beaucoup de temps à écrire le code de gestion lors de l'implémentation du partitionnement. Pour plus d’informations, voir [Gestion des cartes de partitions](sql-database-elastic-scale-shard-map-management.md).

* **Routage dépendant des données** : imaginez une requête arrivant dans l’application. Selon la valeur de la clé de partitionnement de la demande, l’application doit déterminer la base de données appropriée qui conserve les données de cette valeur de clé, puis ouvrir une connexion à cette base de données pour traiter la demande. Le routage dépendant des données fournit la possibilité d'ouvrir des connexions avec un seul appel simple de la carte de partitions de l'application. Le routage dépendant des données est un autre aspect du code d’infrastructure qui est maintenant couvert par les fonctionnalités de la bibliothèque cliente de base de données élastique. Pour plus d’informations, voir [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md).

* **Requêtes sur plusieurs partitions** : l’interrogation de plusieurs partitions fonctionne lorsqu’une demande implique plusieurs (ou toutes les) partitions. Une requête sur plusieurs partitions exécute le même code T-SQL sur toutes les partitions ou un ensemble de partitions. Les résultats provenant des partitions participantes sont fusionnés en un résultat global défini à l'aide de la sémantique UNION ALL. La fonctionnalité, telle qu’exposée via la bibliothèque cliente, gère de nombreuses tâches, notamment : la gestion des connexions, la gestion des threads, la gestion des erreurs et le traitement des résultats intermédiaires. Les requêtes sur plusieurs partitions peuvent interroger des centaines de partitions. Pour plus d’informations, voir [Interrogation de plusieurs partitions](sql-database-elastic-scale-multishard-querying.md).

De manière générale, les clients utilisant les outils de base de données élastique peuvent s’attendre à obtenir toutes les fonctionnalités T-SQL lors de l’envoi d’opérations de partitions locales, à la différence des opérations entre plusieurs partitions qui ont leur propre sémantique.

## Étapes suivantes

Testez l’[exemple d’application](sql-database-elastic-scale-get-started.md) qui montre les fonctions du client.

Pour installer la bibliothèque, accédez à [Bibliothèque cliente de base de données élastique](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Pour obtenir des instructions sur l’utilisation de l’outil de fusion et de fractionnement, voir [vue d’ensemble de l’outil de fusion et de fractionnement](sql-database-elastic-scale-overview-split-and-merge.md).

[La bibliothèque cliente de la base de données élastique est maintenant accessible en open source !](http://azure.microsoft.com/blog/elastic-database-client-library-is-now-open-sourced/)


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-database-client-library/glossary.png

<!---HONumber=Oct15_HO1-->