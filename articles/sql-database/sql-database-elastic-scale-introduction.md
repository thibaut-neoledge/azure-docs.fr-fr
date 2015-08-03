<properties 
    pageTitle="Base de données SQL Azure - outils de base de données élastique" 
    description="Mettez facilement les ressources de base de données à l’échelle dans le cloud à l’aide des outils de bases de données élastiques." 
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
    ms.date="04/24/2015" 
    ms.author="sidneyh"/>

# Base de données SQL Azure - outils de base de données élastique

## Défis et promesses

La base de données SQL Azure propose des ressources de base de données virtuellement illimitées ainsi qu’une notion d’élasticité pour les charges de travail transactionnelles. L’activation de cette fonctionnalité est facilitée par des fonctionnalités telles que la **bibliothèque de client de base de données élastique** et l’**outil de fusion et fractionnement**. Ces deux fonctions sont conjointement nommées **Outils de base de données élastique**. Ces composants sont conçus pour simplifier le développement et la gestion des solutions de base de données partitionnées, abordées dans cet article.

La mise en œuvre des principes d’élasticité et de mise à l’échelle pour les applications de cloud a été simple pour le calcul et le stockage des objets blob. Elle pose cependant plus de problèmes pour le traitement des données avec état dans les bases de données relationnelles. Nous avons vu ces défis émerger plus particulièrement dans les deux scénarios suivants :

* Agrandissement et réduction des capacités pour la partie base de données relationnelle de votre charge de travail.
* Gestion des zones réactives susceptibles de survenir et d’affecter un sous-ensemble de données spécifique, comme un client final très occupé (locataire).

Traditionnellement, ces types de scénarios ont été résolus en investissant dans des serveurs de base de données à plus grande échelle de manière à prendre en charge l’application. Toutefois, cette option est limitée dans le cloud où tous les traitements se produisent sur un matériel prédéfini. La distribution des données et le traitement sur plusieurs bases de données ayant la même structure (un schéma de montée en charge connu sous le terme « partitionnement ») constituent une alternative intéressante aux approches traditionnelles de montée en charge, à la fois en matière de coût et d’élasticité.

Les fonctionnalités de base de données élastique de la base de données SQL Azure rendent la procédure de montée en charge plus simple et plus flexible que jamais. Si vous développez des applications Software as a Service, les **pools de base de données élastique** permettent de créer facilement des bases de données individuelles pour chacun de vos utilisateurs finaux ou locataires et leur permet d’étendre ou de réduire automatiquement l’encombrement des ressources de manière dynamique, tout en conservant un budget prévisible. Les **tâches de base de données élastique** permettent d’effectuer de manière fiable des opérations de gestion à grande échelle sur un ensemble complet de bases de données en exécutant des scripts T-SQL pour effectuer des modifications de schéma, pour gérer les informations d’identification ou réaliser toute autre opération de maintenance de base de données de votre choix. Pour plus d’informations concernant les tâches de base de données élastique, consultez la rubrique [Vue d’ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md).

Et que vous utilisiez des bases de données distinctes pour chaque locataire ou que vous compressiez plusieurs plages de données dans chaque collection de bases de données, la bibliothèque de client de base de données élastique et l’outil de fusion et de fractionnement permettent de réduire les efforts requis pour créer et gérer des applications qui profitent du partitionnement. Au lieu d’écrire du code pour documenter l’agencement des données sur les différentes bases de données et diriger les connexions vers l’emplacement adéquat, la bibliothèque de client vous permet de vous concentrer sur la logique métier de l’application.

## Mise à l’échelle horizontale et verticale
La figure ci-dessous illustre les dimensions horizontales et verticales de mise à l’échelle.
 
![Comparaison entre la montée en charge horizontale et verticale][4]

La mise à l’échelle horizontale fait référence à l’ajout ou la suppression des bases de données afin d’ajuster les capacités ou les performances globales. Cette procédure est fréquemment appelée « montée en charge ». Le partitionnement est une approche courante de mise à l’échelle horizontale, dans laquelle les données sont partitionnées sur une collection de bases de données structurées de manière identique.

La mise à l’échelle verticale fait référence à l’augmentation ou à la réduction du niveau de performance d’une base de données individuelle et est également appelée « mise à l’échelle vers le haut ».

La plupart des applications de base de données à l’échelle du cloud associent ces deux stratégies. Par exemple, une application Software as a Service peut utiliser la mise à l’échelle horizontale pour approvisionner les nouveaux clients finaux et la mise à l’échelle verticale pour permettre l’augmentation ou la diminution des ressources de la base de données du client final, en fonction des besoins de la charge de travail.

Les outils de base de données élastique simplifient les applications de création qui reposent sur le partitionnement en gérant l’infrastructure de mise à l’échelle horizontale. Et en utilisant un pool de la base de données élastique pour votre famille de bases de données, la mise à l’échelle verticale est gérée automatiquement par le système. Avec les pools, vous êtes responsable de la définition des limites sur le système global et de la plage de variation que vous souhaitez autoriser par base de données. Vous pouvez également modifier manuellement des éditions de base de données ou des niveaux de ressources pour les bases de données qui ne font pas partie des pools de bases de données élastiques. Par exemple, vous pouvez créer une nouvelle partition pour gérer un afflux massif de données à la fin du mois. Tant que de nouvelles données arrivent, la partition est mise à l’échelle, puis réduite quand l’afflux diminue.

Pour plus d’informations concernant les pools de bases de données élastiques, consultez la rubrique [Vue d’ensemble des pools de bases de données élastiques](sql-database-elastic-pool.md).

## Capacités des outils de bases de données élastiques 

Le développement, la mise à l’échelle et la gestion des applications montées en charge à l’aide du partitionnement présentent des défis pour le développeur, ainsi que pour l’administrateur. Les outils de base de données élastique facilitent les procédures pour ces deux rôles. Les chiffres de la figure ci-dessous présentent les principales fonctionnalités fournies par la bibliothèque cliente de base de données élastique et l’outil de fusion et de fractionnement. La figure illustre un environnement avec plusieurs bases de données et chaque base de données correspond à une partition. Les outils simplifient le développement des applications de bases de données SQL Azure partitionnées grâce aux capacités spécifiques suivantes :

Pour obtenir la définition des termes utilisés ici, consultez le [Glossaire des outils de base de données élastique](sql-database-elastic-scale-glossary.md).

![Fonctionnalités de l’infrastructure élastique][1]

1.  **Gestion des cartes de partitions** : la gestion des cartes de partitions est la capacité d’une application à gérer les différentes métadonnées sur ses partitions. La gestion des cartes de partitions est une fonctionnalité de la bibliothèque cliente de bases de données élastiques. Les développeurs peuvent utiliser cette fonctionnalité pour enregistrer des bases de données, décrire les mappages de clés de partitionnement individuelles ou de plages de clés pour ces bases de données et gérer ces métadonnées en même temps que le nombre et la composition des bases de données évoluent en fonction des changements de capacités. La gestion des cartes de partitions constitue une grande partie du code de maquette que les clients devaient écrire dans leurs applications lorsqu'ils implémentaient le partitionnement eux-mêmes. Pour plus d’informations, consultez la rubrique [Gestion des cartes de partitions](sql-database-elastic-scale-shard-map-management.md).
 
* **Routage dépendant des données** : imaginez une demande arrivant dans l’application. Selon la valeur de la clé de partitionnement de la demande, l’application doit déterminer la base de données appropriée qui conserve les données de cette valeur de clé, puis ouvrir une connexion à cette base de données pour traiter la demande. Le routage dépendant des données fournit la possibilité d'ouvrir des connexions avec un seul appel simple de la carte de partitions de l'application. Le routage dépendant des données est un autre aspect du code d’infrastructure qui est maintenant couvert par les fonctionnalités de la bibliothèque cliente de base de données élastique. Pour plus d’informations, consultez la rubrique [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md).

* **Requêtes sur plusieurs partitions** : l’interrogation de plusieurs partitions fonctionne lorsqu’une demande implique plusieurs (ou toutes les) partitions. Une requête sur plusieurs partitions exécute le même code T-SQL sur toutes les partitions ou un ensemble de partitions. Les résultats provenant des partitions participantes sont fusionnés en un résultat global défini à l'aide de la sémantique UNION ALL. La fonctionnalité, telle qu’exposée via la bibliothèque cliente, gère de nombreuses tâches, notamment : la gestion des connexions, la gestion des threads, la gestion des erreurs et le traitement des résultats intermédiaires. Les requêtes sur plusieurs partitions peuvent interroger des centaines de partitions. Pour plus d’informations, consultez la rubrique [Interrogation de plusieurs partitions](sql-database-elastic-scale-multishard-querying.md).


* **Outil de fusion et fractionnement**: si vous choisissez de ne pas utiliser le modèle simple d’attribution des bases de données distinctes dans un pool de bases de données élastiques pour chaque shardlet (locataire), votre application devra peut-être redistribuer de manière flexible les données entre les bases de données lorsque les besoins de capacité varient conjointement à l’inertie d’entreprise. Les outils de base de données élastique incluent un outil de fusion et de fractionnement hébergé par le client pour rééquilibrer la distribution des données et la gestion des zones réactives pour les applications partitionnées dans les situations qui impliquent également le déplacement des données. Ce principe s’appuie sur une capacité sous-jacente à déplacer des shardlets à la demande entre les différentes bases de données et s’intègre à la gestion des cartes de partitions afin de conserver des mappages cohérents et des connexions précises de routage dépendant des données. Pour plus d’informations, consultez la rubrique [Fractionnement et fusion avec les outils de base de données élastique](sql-database-elastic-scale-overview-split-and-merge.md).

De manière générale, les clients utilisant les outils de base de données élastique peuvent s’attendre à obtenir toutes les fonctionnalités T-SQL lors de l’envoi d’opérations de partitions locales, à la différence des opérations entre plusieurs partitions qui ont leur propre sémantique.

## Modèles courants de partitionnement

Le **partitionnement** est une technique permettant de distribuer de grandes quantités de données structurées de façon identique entre plusieurs bases de données indépendantes. Il est particulièrement populaire auprès des développeurs cloud qui créent des offres Software as a Service (SAAS) pour les clients finaux ou les entreprises. Ces clients finaux sont souvent appelés « Locataires ». Le partitionnement peut être nécessaire pour plusieurs raisons :

* La quantité totale de données est trop grande pour tenir dans les contraintes d'une base de données unique 
* Le débit des transactions de la charge de travail globale dépasse les capacités d'une base de données unique 
* Les locataires peuvent nécessiter une isolation physique l'un de l'autre, donc des bases de données distinctes sont nécessaires pour chaque locataire 
* Différentes sections d’une base de données peuvent se trouver dans différentes zones géographiques pour des raisons géopolitiques, de performances ou de conformité. 

Dans d'autres scénarios, telle la réception de données à partir d'appareils distribués, le partitionnement peut servir à remplir un ensemble de bases de données organisées en fonction du temps. Par exemple, une base de données distincte peut être dédiée à chaque jour ou chaque semaine. Dans ce cas, la clé de partitionnement peut être un entier représentant la date (présente dans toutes les lignes des tables partitionnées) et les requêtes qui retournent des informations pour une plage de dates doivent être acheminées par l’application vers le sous-ensemble de bases de données couvrant la plage en question.
 
Le partitionnement fonctionne mieux lorsque toutes les transactions d'une application peuvent être limitées à une seule valeur de clé de partitionnement. Cela permet de garantir que toutes les transactions sont locales à une base de données spécifique.

Certaines applications utilisent l'approche la plus simple consistant à créer une base de données distincte pour chaque locataire. C’est le **modèle de partitionnement par locataire unique** qui offre les fonctionnalités d’isolation, de sauvegarde/restauration et de mise à l’échelle des ressources au niveau de la granularité du locataire. Avec le partitionnement par locataire unique, chaque base de données est associée à une valeur d'identifiant de locataire spécifique (ou la valeur de clé du client), mais il n'est pas nécessaire que cette clé soit toujours présente dans les données elles-mêmes. L’application est responsable de l’acheminement de chaque demande vers la base de données appropriée. Et la bibliothèque cliente de base de données élastique peut simplifier cette procédure.

![Comparaison entre l’architecture à locataire unique et l’architecture mutualisée][3]

D'autres scénarios regroupent plusieurs locataires dans des bases de données, plutôt que de les isoler dans des bases de données distinctes. Il s’agit d’un modèle de **partitionnement à plusieurs locataires** et il peut être requis lorsqu’une application gère un grand nombre de très petits locataires. Dans un partitionnement à plusieurs locataires, les lignes des tables de base de données sont toutes conçues pour comporter une clé identifiant l'ID du locataire ou la clé de partitionnement. Là encore, la couche Application est responsable de l’acheminement de la demande d’un locataire vers la base de données appropriée et la bibliothèque cliente de base de données élastique peut prendre cette procédure en charge. En outre, le dispositif de sécurité au niveau des lignes peut servir à filtrer les lignes auxquelles chaque client peut accéder. Pour plus d’informations, consultez la rubrique [Applications mutualisées avec les outils de base de données élastique et sécurité de niveau de ligne](sql-database-elastic-tools-multi-tenant-row-level-security.md). Il peut être nécessaire de redistribuer les données entre les bases de données à l’aide du modèle de partitionnement à plusieurs locataires. L’outil de fusion et de fractionnement des bases de données élastiques permet de faciliter cette procédure.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-introduction/overview.png
[2]: ./media/sql-database-elastic-scale-intro/tenancy.png
[3]: ./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png
[4]: ./media/sql-database-elastic-scale-introduction/h_versus_vert.png
 

<!---HONumber=July15_HO4-->