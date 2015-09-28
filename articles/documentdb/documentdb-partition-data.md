<properties      
    pageTitle="Partitionnement et mise à l’échelle de données dans DocumentDB avec un partitionnement | Microsoft Azure"      
    description="Examinez comment mettre à l’échelle des données avec une technique appelée partitionnement. Découvrez les partitions, comment partitionner des données dans DocumentDB, et quand utiliser un partitionnement par hachage, par plage et par recherche."         
    keywords="Scale data, shard, sharding, documentdb, azure, Microsoft azure"
	services="documentdb"      
    authors="arramac"      
    manager="jhubbard"      
    editor="monicar"      
    documentationCenter=""/>
<tags       
    ms.service="documentdb"      
    ms.workload="data-services"      
    ms.tgt_pltfrm="na"      
    ms.devlang="na"      
    ms.topic="article"      
    ms.date="09/14/2015"      
    ms.author="arramac"/>

# Données de partition et d’échelle dans DocumentDB

[Microsoft Azure DocumentDB](../../services/documentdb/) est conçu pour vous aider à atteindre des performances rapides et prévisibles, et pour *monter en charge* en toute transparence parallèlement à l’évolution de votre application. DocumentDB a été utilisé pour faire fonctionner les services de production à grande échelle chez Microsoft, comme le magasin de données utilisateur qui alimente la suite MSN d'applications web et mobiles.

Vous pouvez obtenir une échelle presque illimitée en termes de débit et de stockage pour votre application DocumentDB en partitionnant horizontalement les données, un concept connu sous le nom de **partitionnement**. Les comptes DocumentDB peuvent évoluer linéairement avec les coûts via des unités empilables, ou **collections**. Le meilleur partitionnement de vos données dans les collections dépend du format de vos données et de vos modèles d'accès.

Après avoir lu cet article sur la mise à l’échelle des données, vous serez en mesure de répondre aux questions suivantes :

 - Qu'est le partitionnement par hachage, par plage et par recherche ?
 - Quand utiliser chaque technique de partitionnement et pourquoi ?
 - Comment procéder pour créer une application partitionnée sur Azure DocumentDB ?

Cet article présente certains concepts relatifs au partitionnement. Si vous êtes prêt à écrire du code qui partitionne les données à l’aide du Kit de développement logiciel (SDK) .NET de DocumentDB, examinons le [partitionnement des données avec le Kit de développement logiciel (SDK) .NET de DocumentDB](documentdb-sharding.md).

## Collections = Partitions

Avant d’explorer plus en profondeur les techniques de mise à l’échelle et de partitionnement des données, il est important de comprendre ce qu’est une collection et ce qu’elle n’est pas. Comme vous le savez peut-être déjà, une collection est un conteneur pour vos documents JSON. Les collections de DocumentDB ne sont pas seulement des conteneurs *logiques*, ce sont aussi des conteneurs *physiques*. Elles correspondent à la limite de transaction pour les procédures stockées et les déclencheurs, et le point d'entrée pour les requêtes et les opérations CRUD. À chaque collection est assignée une quantité réservée de débit qui n'est pas partagée avec les autres collections du même compte. Par conséquent, vous pouvez faire évoluer votre application en termes de stockage et de débit en ajoutant des collections, puis répartir vos documents entre ces dernières.

Les collections diffèrent des tables dans les bases de données relationnelles. Elles n'appliquent pas de schéma. Par conséquent, vous pouvez stocker différents types de documents avec différents schémas dans la même collection. Toutefois, vous pouvez choisir d'utiliser des collections pour stocker les objets d'un type unique, comme vous le feriez avec des tables. Le meilleur modèle dépend uniquement de la manière dont les données apparaissent ensemble dans les requêtes et les transactions.

## Partitionnement avec DocumentDB

Deux approches peuvent être utilisées pour le partitionnement de données avec Azure DocumentDB (ou tout système distribué à cette fin), à savoir le *partitionnement par plages de valeurs*, et le *partitionnement de hachage*. Cela implique la sélection d’un seul nom de propriété JSON dans votre document en tant que *clé de partition*, généralement la propriété d’ID naturel, par exemple, « userID » pour le stockage de l’utilisateur, ou « deviceId » pour les scénarios IoT. Pour les données de série chronologique, l’« horodatage » est utilisé comme clé de partition dans la mesure où les données sont généralement insérées et recherchées par plages de temps. S’il est courant d’utiliser une propriété unique, celle-ci peut être différente pour différents types de documents, par exemple, utilisez « id » pour des documents utilisateur, et « ownerUserId » pour des commentaires. L’étape suivante consiste à acheminer toutes les opérations telles que les créations et requêtes vers les regroupements appropriés à l’aide de la clé de partition incluse dans une demande.

Examinons ces techniques plus en détail.

## Partitionnement par plage

Dans le partitionnement par plage, l'affectation des partitions est fonction de la présence de la clé de partition dans une certaine plage. Cette méthode est couramment utilisée pour le partitionnement avec des propriétés d’*horodatage* (par exemple, eventTime entre le 1er février 2015 et le 2 février 2015).

> [AZURE.TIP]Vous devez utiliser le partitionnement par plage si vos requêtes sont limitées à des valeurs de plage spécifiques par rapport à la clé de partition.

Un cas spécial de partitionnement par plage est lorsque la plage est une valeur unique. Cette méthode est couramment utilisée pour le partitionnement par valeurs discrètes telles que la région (par exemple, la partition pour la Scandinavie contient la Norvège, le Danemark et la Suède).

> [AZURE.TIP]Le partitionnement par plage offre le plus haut niveau de contrôle dans la gestion d’une application mutualisée. Vous pouvez affecter plusieurs clients à une seule collection, un client unique à une seule collection ou même un client unique à travers plusieurs collections.

## Partitionnement par hachage

Dans le partitionnement par hachage, les partitions sont affectées en fonction de la valeur d'une fonction de hachage, ce qui vous permet de répartir uniformément les demandes et les données entre un certain nombre de partitions. Cette méthode est généralement utilisée pour partitionner des données produites ou consommées par un grand nombre de clients distincts, et elle s’avère utile pour stocker des profils utilisateur, des éléments des catalogue et des données de télémétrie d’appareil IoT (Internet des objets).

> [AZURE.TIP]Vous devez utiliser le partitionnement par hachage lorsqu'il y a trop d'entités à énumérer via le partitionnement par recherche (par exemple, des utilisateurs ou des appareils) et que le taux de demandes est relativement uniforme entre les entités.

## Choix de la technique de partitionnement appropriée

Quelle est la technique de partitionnement qui vous convient ? Cela dépend du type de données et de vos modèles d'accès courants. Le choix de la technique de partitionnement appropriée au moment de la conception vous permet d'éviter la dette technique et de gérer la croissance en termes de taille des données et de volume des demandes.

- Le **partitionnement par plages de valeurs** est normalement utilisé dans le contexte des dates, car il offre un mécanisme simple et naturel de vieillissement des partitions par horodatage. Il est également utile lorsque les requêtes sont généralement limitées à une plage de temps, dans la mesure où celle-ci est alignée avec les limites de partitionnement. Il permet également de regrouper et d’organiser des jeux de données non ordonnés et indépendants de façon naturelle, par exemple, de regrouper des clients par organisation ou des États par région géographique. La recherche offre également un contrôle précis pour migrer les données entre les collections. 
- Le **partitionnement de hachage** est utile pour assurer un équilibrage de charge uniforme des demandes afin d’utiliser efficacement le débit et le stockage approvisionnés. L’utilisation d’*algorithmes de hachage* cohérents permet de réduire la quantité de données à déplacer lors de l’ajout ou de la suppression d’une partition.

Vous n'êtes pas obligé de choisir une seule technique de partitionnement. Une *combinaison* de ces techniques (partitionnement composite) peut également être utile selon le scénario. Par exemple, si vous stockez des données télémétriques de véhicules, une approche pertinente consisterait à partitionner les données télémétriques des périphériques par plage pour l'horodatage afin de simplifier la gestion des partitions, puis à créer des partitions secondaires pour le numéro d'identification de véhicule (VIN) afin d'assurer la montée en charge pour le débit (partitionnement composite par plage/hachage).

## Développement d'une application partitionnée
Il existe trois zones de conception clés à examiner lors du développement d'une application partitionnée sur DocumentDB.

- Comment vous acheminez vos créations et vos lectures (y compris les requêtes) vers les collections appropriées.
- Comment vous rendez persistante et récupérez la configuration de la résolution des partitions, c'est-à-dire les tables de partitionnement.
- Comment vous ajoutez/supprimez des partitions au fur et à mesure que le volume de vos données et de vos requêtes augmente.

Examinons de plus près chacune de ces zones.

## Routage des créations et des requêtes

Le routage de demandes de création de document est direct pour le partitionnement par hachage et par plage. Le document est créé sur la partition à partir de la valeur de hachage, de recherche ou de plage correspondant à la clé de partition.

La portée des lectures et les requêtes doit normalement être limitée à une seule clé de partition, afin que les requêtes puissent être distribuées uniquement sur les partitions correspondantes. Des requêtes sur toutes les données, cependant, vous obligeraient à *distribuer* la demande sur plusieurs partitions, puis à fusionner les résultats. N'oubliez pas que certaines requêtes peuvent être obligées d'exécuter une logique personnalisée pour fusionner les résultats, par exemple, lors de l'extraction des N premiers résultats.

## Gestion de votre mappage de partitions

Vous devez également décider comment vous souhaitez stocker votre table de partitionnement, comment vos clients la chargeront et recevront les mises à jour lors des changements, et comment elle sera partagée entre plusieurs clients. Si la table de partitionnement ne change pas souvent, vous pouvez simplement l'enregistrer dans votre fichier de configuration d'application.

Sinon, vous pouvez la stocker dans n'importe quel magasin persistant. Un modèle de conception courant que nous avons vu dans la production consiste à sérialiser les tables de partitionnement au format JSON et à les stocker également dans les collections DocumentDB. Les clients peuvent ensuite mettre en cache la table de partitionnement pour éviter des aller et retour supplémentaires, puis interroger régulièrement les modifications. Si vos clients peuvent modifier la carte de partitions (shard map), assurez-vous qu'ils utilisent un schéma d'affectation de noms cohérent et l'accès concurrentiel optimiste (eTags) pour autoriser des mises à jour cohérentes dans le mappage de partitions.

## Ajout et suppression de partitions pour mettre des données à l’échelle

Avec DocumentDB, vous pouvez ajouter et supprimer à tout moment des collections, et les utiliser pour stocker les nouvelles données entrantes ou rééquilibrer les données disponibles sur les collections existantes. Pour connaître le nombre de collections, consultez la page [Limites](documentdb-limits.md). Vous pouvez toujours nous contacter pour augmenter ces limites.

L'ajout et la suppression d'une nouvelle partition avec le partitionnement par recherche et par plage sont des opérations simples. Par exemple, pour ajouter une nouvelle région géographique ou une nouvelle plage de temps pour les données récentes, il vous suffit d'ajouter les nouvelles partitions au mappage de partitions. Le fractionnement d'une partition existante en plusieurs partitions ou la fusion de deux partitions nécessitent un petit effort supplémentaire. Vous devez

- soit mettre la partition hors connexion pour les lectures ;
- soit acheminer les lectures vers les deux partitions en utilisant l'ancienne configuration de partitionnement, ainsi que la nouvelle configuration de partitionnement pendant la migration. Notez que les transactions et les garanties de niveau de cohérence ne seront pas disponibles avant la fin de la migration.

Le hachage est relativement plus compliqué pour l'ajout et la suppression de partitions. Les techniques de hachage simples provoquent des mélanges et nécessitent le déplacement de la plupart des données. L’utilisation d’un **hachage cohérent** garantit que seule une fraction des données doit être déplacée.

Une façon relativement facile d'ajouter de nouvelles partitions sans avoir à déplacer des données consiste à « déverser » vos données dans une nouvelle collection, puis à distribuer les demandes entre les anciennes et les nouvelles collections. Cette approche, cependant, doit être utilisée uniquement dans les rares situations (débordement dans les charges de pointe et stockage temporaire des données jusqu'à ce qu'elles puissent être déplacées, par exemple).

## Étapes suivantes
Dans cet article, nous avons introduit certaines techniques courantes pour le partitionnement des données avec DocumentDB et indiqué quand utiliser telle technique ou combinaison de techniques.

-   Ensuite, examinons cet [article](documentdb-sharding.md) sur la façon dont vous pouvez partitionner les données à l’aide de la résolution de partition avec le Kit de développement logiciel (SDK) DocumentDB. 
-   Téléchargez un des [Kits de développement logiciel (SDK) pris en charge](https://msdn.microsoft.com/library/azure/dn781482.aspx)
-   Contactez-nous via les [forums MSDN de support](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) si vous avez des questions.
   


 

<!---HONumber=Sept15_HO3-->