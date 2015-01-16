<properties title="Sharding Overview" pageTitle="Présentation du partitionnement" description="Raisons du partitionnement : mettez les ressources de base de données à l'échelle pour augmenter la disponibilité ou les performances." metaKeywords="sharding, scaling, elastic scale, Azure SQL Database" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />
#Présentation du partitionnement 

##Principes de partitionnement 

**Le partitionnement** est une technique permettant de distribuer de grandes quantités de données structurées de façon identique entre plusieurs bases de données indépendantes. Il est particulièrement populaire auprès des développeurs cloud qui créent des offres Software as a Service (SAAS) pour les clients finaux ou les entreprises. Ces clients finaux sont souvent appelés " Locataires ". Le partitionnement peut être nécessaire pour plusieurs raisons : 

* La quantité totale de données est trop grande pour tenir dans les contraintes d'une base de données unique 
* Le débit des transactions de la charge de travail globale dépasse les capacités d'une base de données unique 
* Les locataires peuvent nécessiter une isolation physique l'un de l'autre, donc des bases de données distinctes sont nécessaires pour chaque locataire 
* Différentes sections d'une base de données doivent se trouver dans différentes zones géographiques pour des raisons géopolitiques, de performances ou de conformité. 
 
Le partitionnement fonctionne mieux lorsque toutes les transactions d'une application peuvent être limitées à une seule valeur de clé de partitionnement. Cela permet de garantir que toutes les transactions sont locales à une base de données spécifique. 

Certaines applications utilisent l'approche la plus simple consistant à créer une base de données distincte pour chaque locataire. C'est le **modèle de partitionnement par locataire unique** qui offre les fonctionnalités d'isolation et de sauvegarde/restauration ainsi que la mise à l'échelle des ressources au niveau de la granularité du locataire. Avec le partitionnement par locataire unique, chaque base de données est associée à une valeur d'identifiant de locataire spécifique (ou la valeur de clé du client), mais il n'est pas nécessaire que cette clé soit toujours présente dans les données elles-mêmes. L'application est responsable de l'acheminement de chaque demande à la base de données appropriée. 

![][1]

D'autres scénarios regroupent plusieurs locataires dans des bases de données, plutôt que de les isoler dans des bases de données distinctes. Il s'agit d'un **modèle de partitionnement à plusieurs locataires** et il peut être requis par des considérations de coût, d'efficacité, ou par le fait qu'une application gère un grand nombre de très petits clients. Dans un partitionnement à plusieurs locataires, les lignes des tables de base de données sont toutes conçues pour comporter une clé identifiant l'ID du locataire ou la clé de partitionnement. Là encore, la couche Application est responsable de l'acheminement de la demande d'un locataire à la base de données appropriée. 

Dans d'autres scénarios, tels que la réception de données à partir de périphériques distribués, le partitionnement peut servir à remplir un ensemble de bases de données distribuées durant une période de temps. Par exemple, une base de données distincte peut être dédiée à chaque jour ou chaque semaine. Dans ce cas, la clé de partitionnement peut être un entier représentant la date (présente dans toutes les lignes des tables partitionnées) et les requêtes qui retournent des informations pour une plage de dates doivent être acheminées par l'application vers le sous-ensemble de bases de données couvrant la plage en question.

Quel que soit le modèle de partitionnement utilisé, une structure de données spéciale, nommée **carte de partitions**, sert de table de recherche en associant les valeurs de clé de partitionnement aux bases de données. Cela permet à l'application effectuer l'acheminement des demandes de base de données. Cette méthode est nommée **acheminement dépendant des données** et il s'agit d'une capacité fondamentale, requise pour qu'une application puisse utiliser une couche de données partitionnées. Les [API de client d'architecture élastique](http://go.microsoft.com/?linkid=9862592) fournissent un ensemble complet de fonctionnalités nécessaires à la [gestion des cartes de partitions](http://go.microsoft.com/?linkid=9862595) et à l'activation de [Data-depenendent routing capabilities](http://go.microsoft.com/?linkid=9862596) efficaces et simples d'utilisation dans une application. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-sharding-overview/tenancy.png
