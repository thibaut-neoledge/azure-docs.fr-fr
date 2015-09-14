<properties
    pageTitle="Base de données SQL Azure - outils de base de données élastique"
	description="Les développeurs de Software as a Service (SaaS) peuvent facilement créer des bases de données élastiques et évolutives dans le cloud à l'aide de ces outils"
	services="sql-database"
	documentationCenter=""
	manager="jeffreyg"
	authors="ddove"
	editor=""/>

<tags
    ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/27/2015"
	ms.author="sidneyh"/>

# Vue d’ensemble des fonctionnalités de base de données élastique

Les fonctionnalités de **base de données élastique** vous permettent d'utiliser les ressources virtuellement illimitées de la **base de données SQL Azure** afin de créer des solutions pour les charges de travail transactionnelles et en particulier les applications Software as a Service (SaaS). Les fonctionnalités de base de données élastique se composent des éléments suivants :

* Outils de base de données élastique : ces deux outils simplifient le développement et la gestion des solutions de base de données partitionnées. Ces outils incluent la [bibliothèque cliente de la base de données élastique](sql-database-elastic-database-client-library.md) et l’[outil de fusion et de fractionnement de la base de données élastique](sql-database-elastic-scale-overview-split-and-merge.md). 
* [Pools de bases de données élastiques](sql-database-elastic-pool-guidance.md) (version préliminaire) : un pool est un ensemble de bases de données auquel vous pouvez à tout moment ajouter ou supprimer des bases de données. Les bases de données du pool partagent une quantité fixe de ressources (également appelées unités de débit ou DTU). Vous payez un prix fixe pour les ressources, ce qui vous permet de calculer facilement les coûts tout en gérant les performances. 
* [Tâches de base de données élastique](sql-database-elastic-jobs-overview.md) (version préliminaire) : utilisez des tâches pour gérer un grand nombre de bases de données SQL Azure. Exécutez facilement les opérations administratives telles que les modifications de schéma, la gestion des informations d’identification, les mises à jour de données de référence, la collecte des données de performances ou la collecte télémétrique du client (customer) à l’aide des tâches.
* [Requête de base de données élastique](sql-database-elastic-query-overview.md) (version préliminaire) : vous permet d’exécuter une requête Transact-SQL qui s’étend sur plusieurs bases de données. Cela permet une connexion à des outils de création de rapports comme Excel, PowerBI, Tableau, etc.

L’illustration ci-dessous montre une architecture qui inclut les **fonctionnalités de base de données élastique** liées à une collection de bases de données.

![Outils de base de données élastique][1]

Pour obtenir une version imprimable de cette illustration, consultez la page [Téléchargement de la vue d'ensemble d’une base de données élastique](http://aka.ms/axmybc).

Dans ce graphique, les couleurs de la base de données représentent des schémas. Les bases de données de même couleur partagent les mêmes schémas.

1. Un ensemble de **bases de données SQL Azure** est hébergé sur Azure avec une architecture de partitionnement. 
2. La **bibliothèque cliente de base de données élastique** sert à gérer un ensemble de partitions.
3. Un sous-ensemble des bases de données est placé dans un **pool de base de données élastique**. (Voir [Maîtrise de la croissance avec les bases de données élastiques](sql-database-elastic-pool.md)). 
4. Une **tâche de base de données élastique** exécute des scripts T-SQL sur toutes les bases de données.
5. L’**outil de fusion et fractionnement** sert à déplacer des données d’une partition à l’autre.
6. La **requête de base de données élastique** vous permet d’écrire une requête qui s’étend sur toutes les bases de données de l’ensemble de partitions.
  
## Défis et promesses

Obtenir l'élasticité et l'échelle pour les applications en cloud a été simple pour le calcul et le stockage blob : il suffit d'ajouter ou de soustraire des unités. Mais cela reste un défi pour le traitement des informations de bases de données relationnelles. Nous avons vu ces défis émerger plus particulièrement dans les deux scénarios suivants :

* Agrandissement et réduction des capacités pour la partie base de données relationnelle de votre charge de travail.
* Gestion des zones réactives susceptibles de survenir et d’affecter un sous-ensemble de données spécifique, comme un client final très occupé (locataire).

Traditionnellement, ces types de scénarios ont été résolus en investissant dans des serveurs de base de données à plus grande échelle de manière à prendre en charge l’application. Toutefois, cette option est limitée dans le cloud où tous les traitements se produisent sur un matériel prédéfini. La distribution des données et le traitement sur plusieurs bases de données ayant la même structure (un schéma de montée en charge connu sous le terme « partitionnement ») constituent une alternative aux approches traditionnelles de montée en charge, à la fois en matière de coût et d’élasticité.

## Mise à l’échelle horizontale et verticale

La figure ci-dessous illustre les dimensions horizontales et verticales de la mise à l'échelle, qui représentent les méthodes de base de mise à l'échelle des bases de données élastiques.

![Comparaison entre la montée en charge horizontale et verticale][2]

La mise à l’échelle horizontale fait référence à l’ajout ou la suppression des bases de données afin d’ajuster les capacités ou les performances globales. Cette procédure est fréquemment appelée « montée en charge ». Le partitionnement est une approche courante de mise à l’échelle horizontale, dans laquelle les données sont partitionnées sur une collection de bases de données structurées de manière identique.

La mise à l’échelle verticale fait référence à l’augmentation ou à la réduction du niveau de performance d’une base de données individuelle et est également appelée « mise à l’échelle vers le haut ».

La plupart des applications de base de données à l’échelle du cloud associent ces deux stratégies. Par exemple, une application Software as a Service peut utiliser la mise à l’échelle horizontale pour approvisionner les nouveaux clients finaux et la mise à l’échelle verticale pour permettre l’augmentation ou la diminution des ressources de la base de données du client final, en fonction des besoins de la charge de travail.

* La mise à l’échelle horizontale est gérée à l’aide de la [bibliothèque cliente de base de données élastique](sql-database-elastic-client-overview.md).

* La mise à l'échelle verticale est effectuée à l'aide des applets de commande Azure PowerShell pour modifier le niveau de service, ou en plaçant des bases de données dans un pool de bases de données élastique.

## Modèles avec un ou plusieurs locataires

Le *partitionnement* est une technique permettant de distribuer de grandes quantités de données structurées de façon identique entre plusieurs bases de données indépendantes. Il est particulièrement populaire auprès des développeurs cloud qui créent des offres Software as a Service (SAAS) pour les clients finaux ou les entreprises. Ces clients finaux sont souvent appelés « locataires ». Le partitionnement peut être nécessaire pour plusieurs raisons :

* La quantité totale de données est trop grande pour tenir dans les contraintes d'une base de données unique
* Le débit des transactions de la charge de travail globale dépasse les capacités d'une base de données unique
* Les locataires peuvent nécessiter une isolation physique l'un de l'autre, donc des bases de données distinctes sont nécessaires pour chaque locataire
* Différentes sections d’une base de données peuvent se trouver dans différentes zones géographiques pour des raisons géopolitiques, de performances ou de conformité.

Dans d'autres scénarios, telle la réception de données à partir d'appareils distribués, le partitionnement peut servir à remplir un ensemble de bases de données organisées en fonction du temps. Par exemple, une base de données distincte peut être dédiée à chaque jour ou chaque semaine. Dans ce cas, la clé de partitionnement peut être un entier représentant la date (présente dans toutes les lignes des tables partitionnées) et les requêtes qui retournent des informations pour une plage de dates doivent être acheminées par l’application vers le sous-ensemble de bases de données couvrant la plage en question.

Le partitionnement fonctionne mieux lorsque toutes les transactions d'une application peuvent être limitées à une seule valeur de clé de partitionnement. Cela permet de garantir que toutes les transactions sont locales à une base de données spécifique.

Certaines applications utilisent l'approche la plus simple consistant à créer une base de données distincte pour chaque locataire. C’est le **modèle de partitionnement par locataire unique** qui offre des fonctionnalités d’isolation, de sauvegarde/restauration et de mise à l’échelle des ressources au niveau de granularité du locataire. Avec le partitionnement par locataire unique, chaque base de données est associée à une valeur d'identifiant de locataire spécifique (ou la valeur de clé du client), mais il n'est pas nécessaire que cette clé soit toujours présente dans les données elles-mêmes. L’application est responsable de l’acheminement de chaque demande vers la base de données appropriée. Et la bibliothèque cliente peut simplifier cette procédure.

![Comparaison entre l’architecture à locataire unique et l’architecture mutualisée][4]

D'autres scénarios regroupent plusieurs locataires dans des bases de données, plutôt que de les isoler dans des bases de données distinctes. Il s’agit d’un modèle type de **partitionnement à plusieurs locataires** et il peut être requis lorsqu’une application gère un grand nombre de très petits locataires. Dans un partitionnement à plusieurs locataires, les lignes des tables de base de données sont toutes conçues pour comporter une clé identifiant l'ID du locataire ou la clé de partitionnement. Là encore, la couche Application est responsable de l’acheminement de la demande d’un locataire vers la base de données appropriée et la bibliothèque cliente de base de données élastique peut prendre cette procédure en charge. En outre, le dispositif de sécurité au niveau des lignes peut servir à filtrer les lignes auxquelles chaque client peut accéder. Pour plus d’informations, voir [Applications mutualisées avec les outils de base de données élastique et sécurité de niveau de ligne](sql-database-elastic-tools-multi-tenant-row-level-security.md). Il peut être nécessaire de redistribuer les données entre les bases de données à l’aide du modèle de partitionnement à plusieurs locataires. L’outil de fusion et de fractionnement des bases de données élastiques permet de faciliter cette procédure.

### Déplacement de données de bases de données à plusieurs locataires vers des bases de données à un seul locataire
Lorsque vous créez une application SaaS, il est courant d'offrir aux clients potentiels une version d'évaluation du logiciel. Dans ce cas, il est plus rentable d'utiliser une base de données à plusieurs locataires pour les données. Toutefois, lorsqu'un prospect devient un client, une base de données à un seul locataire est préférable car elle offre de meilleures performances. Si le client a créé des données pendant la période d’évaluation, utilisez l’[outil de fusion et de fractionnement](sql-database-elastic-scale-overview-split-and-merge) pour déplacer les données de la base de données à plusieurs locataires vers la nouvelle base de données à un seul locataire.

## Étapes suivantes

Pour obtenir un exemple d’application illustrant la bibliothèque cliente, voir [Prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md).

Pour utiliser l’outil de fusion et de fractionnement, vous devez [configurer la sécurité](sql-database-elastic-scale-split-merge-security-configuration,md).

Pour plus de détails sur le pool de base de données élastique, voir [Considérations sur les prix et performances pour un pool de base de données élastique](sql-database-elastic-pool-guidance.md), ou créez un pool à l’aide du [didacticiel](sql-database-elastic-pool-portal.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-introduction/tools.png
[2]: ./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]: ./media/sql-database-elastic-scale-introduction/overview.png
[4]: ./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

<!---HONumber=September15_HO1-->