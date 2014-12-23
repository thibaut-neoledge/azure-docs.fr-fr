<properties title="Azure SQL Database Elastic Scale" pageTitle="Infrastructure élastique de base de données SQL Azure" description="Easily scale database resources in the cloud using Elastic Scale feature of Azure SQL Database." metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Infrastructure élastique de base de données SQL Azure 
Bienvenue dans la version préliminaire publique de l'infrastructure élastique de base de données SQL Azure 

###Défis et promesses
L'infrastructure élastique de base de données SQL Azure livre la promesse tant attendue de cloud computing et permet une capacité quasiment illimitée ainsi qu'une élasticité sur la plateforme de base de données SQL Azure. À ce jour, les fournisseurs de services cloud ont été en mesure de fournir la plupart des aspects relatifs à la capacité sans limite de calcul et au stockage d'objets blob. Toutefois, l'élasticité reste toujours un défi lorsqu'il s'agit du traitement de données avec état dans le cloud, notamment avec la gestion de bases de données relationnelles. Nous avons vu ces défis émerger plus particulièrement dans les deux scénarios suivants : 

* Agrandissement et réduction des capacités pour la partie base de données relationnelle de votre charge de travail.
* Gestion des zones réactives d'utilisation pour les charges de travail avec état de la base de données et leurs données.

En règle générale, ces scénarios sont résolus par l'achat de matériel qui héberge la couche Données de l'application. Toutefois, cette option est limitée dans le cloud où tous les traitements se produisent sur un matériel prédéfini. Le partitionnement, ou la distribution des données et le traitement sur plusieurs unités d'échelle pour des raisons de capacité, fournit une alternative intéressante aux approches traditionnelles de montée en charge en termes de coût et d'élasticité. Au fil des ans, nous avons découvert que les clients doivent implémenter leurs propres approches de mise à l'échelle en plus de la base de données SQL Azure pour réussir le partitionnement. Pour certains, cela signifiait gérer des centaines voire des milliers de bases de données SQL Azure. Cette approche générait beaucoup de code dans les couches Données qui traitait les subtilités du partitionnement au lieu de la logique métier de l'application. 

En travaillant directement avec les clients, au fil des ans, nous avons vu plusieurs modèles de partitionnement émerger des différents projets des clients. L'infrastructure élastique de base de données SQL Azure fournit des bibliothèques clientes et des offres de service basés sur ces modèles. L'infrastructure élastique permet de développer, mettre à l'échelle et gérer plus facilement les couches Données avec état de vos applications Azure.

Vous pouvez ensuite vous concentrer sur la logique métier de votre application plutôt que sur la conception de l'infrastructure pour le partitionnement.  


##Fonctionnalités 

Le développement, la mise à l'échelle et la gestion des applications évolutives à l'aide du partitionnement présentent des défis pour le développeur, ainsi que pour l'administrateur. L'infrastructure élastique de base de données SQL Azure facilite la vie ces deux rôles. Les nombres du graphique décrivent les fonctionnalités principales livrées avec cette version préliminaire publique. 
La partie inférieure affiche la couche Données de l'application et la distribution des données sur plusieurs bases de données, appelées partitions. Supposons que plusieurs bases de données stockent les données de plusieurs partitions. 

Pour la définition des termes utilisés ici, consultez le [Glossaire de l'infrastructure élastique](./sql-database-elastic-scale-glossary.md).

###Infrastructure élastique avec partitionnement 

![][1]

La figure illustre le développeur et l'administrateur, à gauche et à droite. Les clients peuvent s'attendre à obtenir toutes les fonctionnalités T-SQL lors de l'envoi d'opérations de partition locale, à la différence des opérations entre partitions qui ont leur propre sémantique. 
La version préliminaire publique de l'infrastructure élastique de base de données SQL Azure facilite le développement des applications de base de données SQL Azure partitionnées grâce aux fonctionnalités suivantes : 

* **Gestion des cartes de partitions** : la gestion des cartes de partitions (1) est la capacité d'une application à gérer les différentes métadonnées sur ses partitions. La gestion des cartes de partitions est une fonctionnalité de la bibliothèque cliente de l'infrastructure élastique. Les développeurs peuvent utiliser cette fonctionnalité pour enregistrer des partitions, décrire les mappages de clés de partitionnement individuelles ou de plages de clés pour les partitions et gérer ces métadonnées sous la forme de la disposition des partitions dans la couche Données afin de refléter les modifications de capacité. La gestion des cartes de partitions constitue une grande partie du code de maquette que les clients devaient écrire dans leurs applications lorsqu'ils implémentaient le partitionnement eux-mêmes. Pour plus d'informations, consultez la rubrique [Gestion des cartes de partitions](./sql-database-elastic-scale-shard-map-management.md)
 
* **Routage dépendant des données** : imaginez une demande arrivant dans l'application. Selon la valeur de la clé de partitionnement de la demande, l'application doit déterminer la partition appropriée qui conserve les données de cette valeur de clé de partitionnement, puis ouvrir une connexion à cette partition (2) pour traiter la demande. Le routage dépendant des données fournit la possibilité d'ouvrir des connexions avec un seul appel simple de la carte de partitions de l'application. Le routage dépendant des données était une autre zone du code d'infrastructure, maintenant couvert par les fonctionnalités de la bibliothèque cliente de l'infrastructure élastique. Pour plus d'informations, consultez la rubrique [Routage dépendant des données](./sql-database-elastic-scale-data-dependent-routing.md).

* **Requêtes sur plusieurs partitions** : l'interrogation de plusieurs partitions fonctionne lorsqu'une demande implique plusieurs (ou toutes les) partitions. Une requête sur plusieurs partitions (3) exécute le même code T-SQL sur toutes les partitions ou un ensemble de partitions. Les résultats provenant des partitions participantes sont fusionnés en un résultat global défini à l'aide de la sémantique UNION ALL. La fonctionnalité est accessible via la bibliothèque cliente et gère de nombreuses tâches, notamment : gestion des connexions, gestion des threads, gestion des défaillances et traitement des résultats intermédiaires. Les requêtes sur plusieurs partitions peuvent interroger des centaines de partitions. Pour plus d'informations, consultez la rubrique [Interrogation de plusieurs partitions](./sql-database-elastic-scale-multishard-querying.md).

* **Élasticité des partitions** : cette fonctionnalité permet aux administrateurs d'automatiser la mise à l'échelle verticale (numérotation de haut en bas de l'édition d'une partition unique) et la mise à l'échelle horizontale (ajout de partitions à une carte de partitions ou suppression) de leur environnement partitionné via des scripts PowerShell et le service Azure Automation. Pour plus d'informations, consultez la rubrique [Élasticité des partitions](./sql-database-elastic-scale-elasticity.md).

* **Service de fractionnement/fusion** : lorsque la capacité doit fluctuer en tandem avec l'inertie d'entreprise, les applications doivent redistribuer en souplesse les données entre plusieurs bases de données (4). L'infrastructure élastique offre une expérience de service hébergé par le client pour la croissance et la réduction de la capacité de la couche Données et pour la gestion des zones réactives pour les applications partitionnées dans les situations qui impliquent également le déplacement de données. Elle s'appuie sur une fonctionnalité sous-jacente de déplacement des shardlets à la demande entre les différentes partitions et s'intègre à la gestion des cartes de partitions pour conserver des mappages cohérents et des connexions de routage dépendant des données précis. Pour plus d'informations, consultez la rubrique [Fractionnement et fusion avec l'infrastructure élastique](./sql-database-elastic-scale-overview-split-and-merge.md)

##Modèles courants de partitionnement

**Le partitionnement** est une technique permettant de distribuer de grandes quantités de données structurées de façon identique entre plusieurs bases de données indépendantes. Il est particulièrement populaire auprès des développeurs cloud qui créent des offres Software as a Service (SAAS) pour les clients finaux ou les entreprises. Ces clients finaux sont souvent appelés " Locataires ". Le partitionnement peut être nécessaire pour plusieurs raisons : 

* La quantité totale de données est trop grande pour tenir dans les contraintes d'une base de données unique 
* Le débit des transactions de la charge de travail globale dépasse les capacités d'une base de données unique 
* Les locataires peuvent nécessiter une isolation physique l'un de l'autre, donc des bases de données distinctes sont nécessaires pour chaque locataire 
* Différentes sections d'une base de données doivent se trouver dans différentes zones géographiques pour des raisons géopolitiques, de performances ou de conformité. 

Dans d'autres scénarios, telle la réception de données à partir d'appareils distribués, le partitionnement peut servir à remplir un ensemble de bases de données organisées en fonction du temps. Par exemple, une base de données distincte peut être dédiée à chaque jour ou chaque semaine. Dans ce cas, la clé de partitionnement peut être un entier représentant la date (présente dans toutes les lignes des tables partitionnées) et les requêtes qui retournent des informations pour une plage de dates doivent être acheminées par l'application vers le sous-ensemble de bases de données couvrant la plage en question.
 
Le partitionnement fonctionne mieux lorsque toutes les transactions d'une application peuvent être limitées à une seule valeur de clé de partitionnement. Cela permet de garantir que toutes les transactions sont locales à une base de données spécifique. 

Certaines applications utilisent l'approche la plus simple consistant à créer une base de données distincte pour chaque locataire. C'est le **modèle de partitionnement par locataire unique** qui offre les fonctionnalités d'isolation et de sauvegarde/restauration ainsi que la mise à l'échelle des ressources au niveau de la granularité du locataire. Avec le partitionnement par locataire unique, chaque base de données est associée à une valeur d'identifiant de locataire spécifique (ou la valeur de clé du client), mais il n'est pas nécessaire que cette clé soit toujours présente dans les données elles-mêmes. L'application est responsable de l'acheminement de chaque demande à la base de données appropriée. 

![][2]

D'autres scénarios regroupent plusieurs locataires dans des bases de données, plutôt que de les isoler dans des bases de données distinctes. Il s'agit d'un **modèle de partitionnement à plusieurs locataires** et il peut être requis par des considérations de coût, d'efficacité, ou par le fait qu'une application gère un grand nombre de très petits clients. Dans un partitionnement à plusieurs locataires, les lignes des tables de base de données sont toutes conçues pour comporter une clé identifiant l'ID du locataire ou la clé de partitionnement. Là encore, la couche Application est responsable de l'acheminement de la demande d'un locataire à la base de données appropriée. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-intro/overview.png
[2]:./media/sql-database-elastic-scale-intro/tenancy.png
